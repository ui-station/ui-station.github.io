---
title: "나의 쿠버네티스 클러스터에 MongoDB No-SQL 데이터베이스를 추가한 경험"
description: ""
coverImage: "/assets/img/2024-06-19-MyexperienceaddingaMongoDBNo-SQLdatabasetomyKubernetescluster_0.png"
date: 2024-06-19 13:00
ogImage: 
  url: /assets/img/2024-06-19-MyexperienceaddingaMongoDBNo-SQLdatabasetomyKubernetescluster_0.png
tag: Tech
originalTitle: "My experience adding a MongoDB No-SQL database to my Kubernetes cluster"
link: "https://medium.com/@martin.hodges/my-experience-adding-a-mongodb-no-sql-database-to-my-kubernetes-cluster-f43fe72fa0ba"
---


## SQL과 No-SQL 데이터베이스 사이를 선택하는 방법에 대해 읽었다면, Kubernetes 클러스터에 No-SQL MongoDB 데이터베이스를 추가할 수 있는지 궁금할 것입니다. 이 글에서는 그것을 어떻게 수행했는지 설명하고 Spring Boot 애플리케이션과 함께 사용하는 방법에 대해 알려드리겠습니다.

![이미지](/assets/img/2024-06-19-MyexperienceaddingaMongoDBNo-SQLdatabasetomyKubernetescluster_0.png)

# 시작하기

일반적으로 Kubernetes 서비스를 개발할 때는, 개발을 위해 로컬 Kind Kubernetes 클러스터에서 시작합니다. Kind를 설정하는 방법에 대해 이전에 썼었고, 이 글에 관련된 GitHub 저장소에는 이를 수행하는 데 필요한 구성 파일이 포함되어 있습니다.

<div class="content-ad"></div>

클론하기 위해 저장소를 다음과 같이 복제할 수 있어요:

```js
git clone git@github.com:MartinHodges/aquarium-with-mongo-db.git
```

# 왜 MongoDB를 사용해야 하나요?

이전 기사에서 SQL 대 No-SQL 결정에 대해 다뤄 보았어요. 여러분이 이 글을 읽고 계신다면 No-SQL을 선택하겠다고 결정하신 거겠죠.

<div class="content-ad"></div>

일단 그 결정이 내렸다면, 이제 No-SQL 데이터베이스를 어떤 것을 선택할지가 문제가 됩니다. MongoDB는 가장 가까운 경쟁상대보다 2배 더 높은 시장 점유율을 보유하고 있습니다. 그것은 매우 정교하며 커뮤니티 버전과 엔터프라이즈 버전 둘 다 가지고 있습니다. 전형적으로 가장 많이 사용되는 No-SQL 데이터베이스입니다.

다른 데이터베이스와의 기술적인 비교는 이 기사의 범위를 벗어나지만, MongoDB가 인기 있는 이유와 일하도록 충분히 할 수 있는 사실에 기반하여 이 기사에서는 MongoDB를 선택했습니다!

# MongoDB 설치

Kubernetes 클러스터에 MongoDB를 설치하는 방법은 다른 응용프로그램과 유사하게 operator를 사용하여 수행됩니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-MyexperienceaddingaMongoDBNo-SQLdatabasetomyKubernetescluster_1.png" />

쿠버네티스 오퍼레이터는 당신을 대신하여 응용 프로그램을 관리합니다. 응용 프로그램의 라이프사이클을 설치하고 관리하며 모니터링하고 필요한 조치를 취할 수 있습니다.

데이터베이스의 경우 데이터베이스 클러스터를 생성하거나 확장하거나 백업하는 등의 작업을 수행할 수 있습니다. 일반적으로 오퍼레이터는 그 자체의 '쿠버네티스 구성 언어'를 제공하는 사용자 정의 리소스 정의 (CRD)를 설치하기에 의존합니다. 이는 클러스터에 사용자 정의 리소스를 추가하기 위한 요청을 감지하고 당신을 대신하여 작동합니다.

## 개발용 쿠버네티스 클러스터 생성

<div class="content-ad"></div>

Kind를 설치했다고 가정하면, 다음 구성을 사용하여 Kind 클러스터를 만들 수 있습니다:

kind/kind-config.yml

```js
apiVersion: kind.x-k8s.io/v1alpha4
kind: Cluster
nodes:
- role: control-plane
  extraPortMappings:
  # apis
  - containerPort: 30080
    hostPort: 30080
- role: worker
- role: worker
- role: worker
```

이렇게 하면 1개의 컨트롤러 및 3개의 워커로 구성된 4개 노드 클러스터가 생성됩니다. 또한 개발 머신의 포트 30080을 사용할 수 있습니다. 이를 사용하여 로컬 Kubernetes 클러스터를 생성할 수 있습니다:

<div class="content-ad"></div>

```shell
kind create cluster --config kind/kind-config.yml
```

## 오퍼레이터 설치

Helm을 사용하여 커뮤니티 지원 오퍼레이터를 설치할 수 있습니다.

먼저 다음과 같이 로컬 리포지토리에 Helm 링크를 추가하세요:

<div class="content-ad"></div>

```js
helm repo add mongodb https://mongodb.github.io/helm-charts
```

아래 명령어로 이 리포지토리가 추가한 차트를 확인할 수 있어요:

```js
helm search repo mongo
```

리스트에서 커뮤니티 오퍼레이터를 확인할 수 있을 거에요. 이것을 사용할 거에요.```

<div class="content-ad"></div>

저희는 오퍼레이터와 데이터베이스를 별도의 네임스페이스로 mongo라는 이름으로 분리해서 배치할 겁니다. 다음과 같이 생성해보겠습니다:

```js
kubectl create namespace mongo
```

이제 다음 명령으로 오퍼레이터를 설치할 수 있어요:

```js
helm install community-operator mongodb/community-operator -n mongo
```

<div class="content-ad"></div>

다음 명령어를 사용하여 준비 상태가 1/1로 Running인지 확인할 수 있어요:

```sh
kubectl get pods -n mongo
```

이제 운영자가 작동 중인 것을 볼 수 있습니다. 설치된 CRD는 다음을 통해 확인할 수 있어요:

```sh
kubectl get crds
kubectl describe crd mongodbcommunity.mongodbcommunity.mongodb.com 
```

<div class="content-ad"></div>

이제 MongoDB 클러스터를 생성할 준비가 되었습니다.

## 클러스터 생성

오퍼레이터가 설치되었으므로 MongoDB 데이터베이스를 생성하는 요청을 대기 중입니다. 우리는 오퍼레이터에 의해 로드된 CRD를 사용하여 쿠버네티스 클러스터에 MongoDB 매니페스트를 적용하여 요청을 할 수 있습니다.

이를 하기 전에 데이터베이스 사용자의 비밀번호를 쿠버네티스 시크릿으로 설정해야 합니다.

<div class="content-ad"></div>

다음과 같이 비밀을 생성하세요 (‘…’를 선택한 비밀번호로 교체하세요):

```js
kubectl create secret generic my-user-password -n mongo --from-literal="password=<당신의 비밀번호>"
```

다음 명령어로 확인할 수 있어요:

```js
kubectl get secrets -n mongo my-user-password -o jsonpath={.data.password} | base64 -d; echo
```

<div class="content-ad"></div>

모든 쿠버네티스 시크릿은 base64로 인코드되어 있기 때문에 비밀번호를 디코딩하는 데 base64 -d를 사용하는 것을 알 수 있습니다. 우리가 --from-literal을 사용하였기 때문에 create secret 명령어에 의해 비밀번호가 자동으로 base64로 인코드되었습니다.

이제 비밀번호가 준비되었으니, 이 비밀번호를 사용하는 관리자 사용자가 있는 MonogoDB 클러스터와 데이터베이스를 생성할 수 있습니다.

매니페스트 파일을 생성해 보세요:

k8s/my-mongo-db.yml

<div class="content-ad"></div>

```yaml
apiVersion: mongodbcommunity.mongodb.com/v1
kind: MongoDBCommunity
metadata:
  name: my-mongo-db
  namespace: mongo
spec:
  members: 3
  type: ReplicaSet
  version: "7.0.11"
  security:
    authentication:
      modes: ["SCRAM"]
  users:
    - name: my-user
      db: admin
      passwordSecretRef: # a reference to the secret that will be used to generate the user's password
        name: my-user-password
        key: password
      roles:
        - name: clusterAdmin
          db: admin
        - name: userAdminAnyDatabase
          db: admin
      scramCredentialsSecretName: my-user-scram
  additionalMongodConfig:
    storage.wiredTiger.engineConfig.journalCompressor: zlib
```

이제 다음과 같이 적용할 수 있습니다:

```bash
kubectl apply -f k8s/my-mongo-db.yml 
```

그리고 진행 상황을 다음과 같이 확인할 수 있습니다:
```

<div class="content-ad"></div>

```js
kubectl get pods -n mongo
```

3개의 인스턴스가 생성될 때까지 기다리고 있어요. 제 MacBook Pro(M2 Max Apple 실리콘)에서 4노드 Kind 클러스터를 사용하면, 모든 3개의 인스턴스를 시작하는 데 약 5분 정도 걸렸어요.

시작되고 나면, 다음 명령어로 서비스가 정상적으로 작동하는지 확인할 수 있어요:

```js
kubectl get svc -n mongo
```

<div class="content-ad"></div>

이렇게 하면:

```js
NAME              TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)     AGE
my-mongo-db-svc   ClusterIP   None         <none>        27017/TCP   6m
```

## 데이터베이스 테스트

우리 애플리케이션에서는 쿠버네티스 내부에서 직접 데이터베이스에 연결할 것입니다. 데이터베이스의 서비스를 이용해 DNS 이름으로 연결하려고 하지만, 테스트 목적으로는 로컬 개발 머신에서 연결하고 싶습니다.

<div class="content-ad"></div>

처음으로 이를 시도할 때 로컬 개발 머신으로 MonogoDB 파드 중 하나를 포워딩하기 위해 포트 포워딩을 사용했고, 어떤 변경을 시도했을 때 다음과 같은 오류 메시지를 받았습니다:

```js
MongoServerError[NotWriteablePrimary]: not primary
```

이것은 포트 포워딩한 파드가 클러스터의 주 파드가 아니기 때문에 발생한 문제입니다. 보조 파드는 읽기 전용 복사본이기 때문에 모든 쓰기 작업은 주 파드를 통해 이루어져야 합니다.

이 문제를 피하려면 주 파드에 연결해야 합니다.

<div class="content-ad"></div>

어떤 노드가 기본 노드인지 알고 싶다면 다음 노드 중 하나의 로그를 조사하면 됩니다:

```js
kubectl logs my-mongo-db-0 -n mongo -c mongod | grep "\"primary\":"
```

만약 결과가 없다면, 기본 노드에 도달한 것입니다.

만약 결과를 얻는다면, 몇 줄만 출력될 수 있지만, 그것들은 매우 길고 읽기 어려울 수 있습니다. JSON pretty printer 같은 것(jq와 같은)을 가지고 있다면 다음을 사용할 수 있습니다:

<div class="content-ad"></div>

```js
kubectl logs my-mongo-db-0 -n mongo -c mongod | grep "\"primary\":" | jq
```

그러면 다음과 같은 줄을 볼 수 있습니다:

```js
...
"primary": "my-mongo-db-1.my-mongo-db-svc.mongo.svc.cluster.local:27017",
...
```

여기에 연결해야 하는 pod의 이름이 나옵니다 (제 경우: my-mongo-db-1). 이제 해당 pod를 포트 포워드할 수 있습니다:

<div class="content-ad"></div>

```js
kubectl port-forward my-mongo-db-1 -n mongo 27017:27017
```

이 포트 포워딩이 설정되면 데이터베이스에 연결해야 합니다. MongoDB Compass 클라이언트를 사용할 수 있습니다. 해당 클라이언트는 https://www.mongodb.com/try/download/compass 에서 다운로드할 수 있습니다.

설치 후 데이터베이스에 연결할 수 있어야 합니다. 연결 문자열(mongodb://localhost:27017)이 제안됩니다만, 몇 가지 설정을 변경해야합니다.

고급 연결 옵션을 클릭하고 직접 연결을 클릭하십시오 (이 설정을 변경하지 않으면 내부 쿠버네티스 주소를 사용하려고 시도하여 찾을 수 없는 주소가 발생합니다).```

<div class="content-ad"></div>

인증 탭을 클릭해주세요. 사용자 이름/비밀번호를 선택하고 이전에 선택한 사용자 이름(my-user)과 비밀번호를 입력해주세요. Admin을 데이터베이스로 추가하고 SCRAM-SHA-256 인증 메커니즘을 선택해주세요 (필요하다면 아래로 스크롤).

저장 및 연결을 클릭하고 연결 이름을 지정한 후, 데이터베이스에 연결된 Compass 콘솔이 표시됩니다.

클러스터 내에서 admin, config 및 local 데이터베이스가 생성된 것을 확인하실 수 있습니다.

여기까지 오셨다면, MongoDB 클러스터가 정상적으로 실행 중임을 의미합니다.

<div class="content-ad"></div>

# 애플리케이션 사용자 생성

우리의 MongoDB에 연결할 모든 애플리케이션이 우리가 생성한 my-user를 사용할 수 있을 것이라고 생각할 수 있습니다. 하지만, 이 사용자는 실제로 데이터베이스 유지 관리를 위한 것이기 때문에 그렇지 않습니다.

애플리케이션이 데이터베이스 클러스터를 사용할 수 있도록하려면 데이터베이스와 해당 데이터에 액세스할 사용자를 생성해야 합니다.

Compass 창의 맨 아래에 `_MONGOSH` 프롬프트가 나타납니다. 이를 클릭하여 명령줄에 액세스할 수 있습니다.

<div class="content-ad"></div>

이제 다음과 같이 사용자를 생성할 것입니다:

```js
use aquarium
db.createUser( { user: "my-app-user",
              pwd: "<password>",
              roles: [ {db: "aquarium", role: "dbOwner"} ] } )
```

알아둬야 할 몇 가지 사항이 있습니다. 첫번째로, 생성되기 전에 존재하지 않는 데이터베이스(aquarium)로 전환합니다. 이는 사용하기 전에 아무 것도 정의할 필요가 없다는 원칙에 부합합니다. 데이터베이스 및 모든 컬렉션은 문서를 추가할 때 처음 생성됩니다.

두번째는 새 데이터베이스에 할당된 역할입니다. MongoDB에는 사용자에게 부여할 수 있는 소수의 기본 역할이 있습니다. 이 경우 dbOwner 역할은 사용자가 데이터베이스를 읽고 쓰고 관리할 수 있도록 합니다. 실제 운영에서는 사용자 권한을 적절히 제한해야 합니다.

<div class="content-ad"></div>

```js
...
ok: 1,
...
```

사용자를 확인하기 위해 새 Compass 연결을 열어보세요. 이는 메뉴를 통해 할 수 있습니다. 혹은 MacOS에서는 Cmd N을 누르세요. 창이 열릴 때까지 몇 초가 걸릴 수 있는데, 아무런 표시가 없으므로 한 번만 누르세요!

새 연결 창이 나타나면, 이전에 저장한 연결을 복제하는 것이 더 쉽다고 생각합니다(연결 옆의 ... 메뉴를 사용하세요).```

<div class="content-ad"></div>

사용자 이름과 비밀번호를 변경해주세요. 또한 Authentication Database를 aquarium으로 변경해주세요. 그런 다음 연결하세요.

이제 새로운 aquarium 데이터베이스를 확인할 수 있어야 합니다. "fishes"라는 collection을 생성해보면서 테스트해 볼 수 있습니다. 데이터베이스에 문서 형태로 데이터를 추가할 수 있습니다.

```js
{
  "_id": 123,
  "fish": "Guppy"
}
```

이 시점에서 Spring Boot 애플리케이션과 함께 사용할 준비가 된 MongoDB가 준비되었습니다.

<div class="content-ad"></div>

# 스프링 부트 애플리케이션 만들기

간단한 데이터베이스 지원 예제를 만들 때는 제가 제일 먼저 수족관 애플리케이션을 사용합니다. REST API를 사용하여 물고기와 수족관을 만들고 관리할 수 있습니다. 그런 다음 물고기를 여러분의 수족관 중 하나에 추가할 수 있습니다.

## 코드

저는 코드를 여기에 포함하려는 의도는 없지만 관련된 GitHub 저장소에서 확인할 수 있습니다.

<div class="content-ad"></div>

## 종속성

Spring Boot 애플리케이션을 시작하는 것은 항상 https://start.spring.io/에서 Spring Initializr를 사용하는 것이 더 쉽습니다. 사용 방법을 알고 있다고 가정합니다.

이 프로젝트에서 Spring Web과 Spring Data MongoDB를 종속성으로 추가하고 프로젝트를 생성합니다.

## 패키지 구조

<div class="content-ad"></div>

내가 만드는 애플리케이션에 따라, 패키지 구조를 구성하는 데 컴포넌트 유형(예: 컨트롤러, 서비스 및 리포지토리)에 기반을 둘 수도 있고, 비즈니스 도메인에 기반을 둘 수도 있습니다.

물고기와 수조 두 가지 비즈니스 도메인만 있는 작은 애플리케이션인 경우, 이 프로젝트를 이러한 도메인을 기반으로 해서 다음과 같이 만들 것입니다:

```js
fishes
  FishController
  FishService
  FishRepository
fishtanks
  FishTankController
  FishTankService
  FishTankRepository
```

보시다시피, 컨트롤러, 서비스 및 리포지토리 레이어를 사용하여 표준 계층 구조를 따르고 있습니다.

<div class="content-ad"></div>

## API 엔드포인트

이 컨트롤러들은 각각의 API에 대해 생성, 조회, 업데이트 및 삭제 (CRUD) 엔드포인트를 제공합니다.

## 엔티티 및 문서

만약 JPA와 Postgres와 같은 SQL 데이터베이스에 익숙하다면, 엔티티와 리포지토리로 익숙할 것입니다.

<div class="content-ad"></div>

No-SQL 데이터베이스에서는 테이블이 컬렉션으로 대체되고, 테이블 내의 행은 문서로 대체됩니다.

이는 No-SQL 데이터베이스를 위한 리포지토리가 SQL 데이터베이스와는 조금 다르다는 것을 의미합니다.

No-SQL 데이터베이스는 어떤 구조든 다룰 수 있기 때문에, 엔티티(또는 문서)는 간단한 Plain Old Java Objects (POJOs)가 됩니다. 이는 우리 예시 애플리케이션에서 다음과 같이 엔티티를 생성할 수 있다는 것을 의미합니다:

aquarium/fishes/Fish.java

<div class="content-ad"></div>

```js
...

@Setter
@Getter
@Document("fishes")
@NoArgsConstructor
public class Fish {

  @Id
  public UUID id;

  public String type;

  public Fish(String type) {
      this.id = UUID.randomUUID();
      this.type = type;
  }
  ...
}
```

친구야, 여기 몇 가지 주의할 점이 있어요:

- @Entity를 정의하는 대신 컬렉션의 이름을 사용하는 @Document를 정의하고 있어요.
- 자체 UUID Id를 관리할 수 있도록 @mongoId 대신에 (필수는 아니지만 MongoDB가 제공하지 않은 경우 MongoDB로 제공할 수 있기 때문에) @Id를 사용하고 있어요.
- Lombok(예: @Getter)을 사용하여 보일러플레이트 코드 일부를 제거하는 것을 좋아해요.

이제 비슷한 방식으로 물고기 수조를 만들 수 있어요:```

<div class="content-ad"></div>

수족관/fishtanks/FishTank.java

```java
@Setter
@Getter
@Document("fish tanks")
@NoArgsConstructor
public class FishTank {

    @Id
    public UUID id;

    public String name;

    public FishTank(String name) {
        this.id = UUID.randomUUID();
        this.name = name;
    }

    @Override
    public String toString() {
        return String.format(
                "FishTank[id=%s, type='%s']",
                id.toString(), name);
    }
}
```

## Repositories

자, 이제 우리의 문서들이 준비되었어요. 이제 이들에 어떻게 접근할까요?

<div class="content-ad"></div>

제가 보여드릴 것은 우리 저장소의 변경 사항입니다. 물고기 저장소를 예로 들어보겠습니다:

```js
...
public interface FishRepository extends MongoRepository<Fish, UUID> {

    public List<Fish> findAll();

    public Optional<Fish> findFirstById(UUID id);

    public Optional<Fish> findFirstByType(String type);
}
...
```

이것이 SQL 데이터베이스에서 찾을 수 있는 Repository 유형과 거의 동일하다는 것을 알 수 있습니다. 유일한 차이점은 인터페이스가 CrudRepository가 아닌 MongoRespository를 확장한다는 것뿐입니다.

한 대 다 및 다른 매핑 주제는 다른 기사로 미루겠습니다. 그래서 현재로서는 물고기와 어항을 생성하고 관리할 수 있을 것입니다.

<div class="content-ad"></div>

## 어플리케이션 속성

데이터베이스와 작업할 때는 어플리케이션이 어떻게 연결해야 하는지를 알려줘야 합니다. 우리는 SQL 데이터베이스와 마찬가지로 어플리케이션 속성을 통해 이를 수행합니다.

나는 Spring Boot 속성 파일에 YAML 파일을 사용하는 것을 선호하며, 내 구성은 다음과 같이 보입니다 (나의 값으로 ` ` 필드를 교체해주시기 바랍니다):

resources/application.yml

<div class="content-ad"></div>

```yaml
spring:
  application:
    name: aquarium-with-mongo-db

  data:
    mongodb:
      host: localhost
      port: 27017
      database: aquarium
      username: my-app-user
      password: <password>
```

나중에 프로필에 대해 이야기할 때 다시 돌아올게요.

## 컨트롤러 및 서비스

이제 SQL 데이터베이스와 마찬가지로 컨트롤러와 서비스를 추가할 수 있습니다. GitHub 저장소에서 이용 가능하므로 별도로 제시하지 않겠습니다.

<div class="content-ad"></div>

## 애플리케이션 테스트

코드를 완성하거나(또는 제 저장소를 복제)하여 IDE 내에서 애플리케이션을 실행하십시오. 여전히 주 서버로 포트 포워딩 중인 경우, 애플리케이션이 시작되어야 합니다.

그런 다음 다음 curl 명령을 사용하여 테스트할 수 있습니다:

```js
curl localhost:8080/api/v1/fishes -H "Content-Type: application/json" -d '{"type": "guppy2"}' 
curl localhost:8080/api/v1/fish-tanks -H "Content-Type: application/json" -d '{"name": "big one"}' 
curl localhost:8080/api/v1/fishes
curl localhost:8080/api/v1/fish-tanks
```

<div class="content-ad"></div>

이제 컴퍼스 클라이언트로 이동하여 아쿠아리움 데이터베이스를 새로 고침하면 fishes 및 fish tanks 두 개의 컬렉션이 표시됩니다. 이러한 컬렉션 내에는 만든 fishes 및 fish tanks가 표시됩니다.

# 최종 단계

이 시점에서 저희는 쿠버네티스 클러스터에서 실행 중인 MongoDB에 연결된 Spring Boot 애플리케이션을 갖추었습니다. 이제 해야 할 마지막 단계, 즉 Spring Boot 애플리케이션을 쿠버네티스 클러스터에 로드하는 것이 남았습니다.

이를 위해 다음을 수행해야 합니다:

<div class="content-ad"></div>

- 팻 JAR 파일을 생성합니다 (모든 종속성이 포함됨)
- 해당 JAR에서 Docker 이미지를 생성합니다
- 이미지를 Docker 저장소에 업로드합니다
- 배포 매니페스트 파일을 생성합니다
- 배포 매니페스트를 Kubernetes 클러스터에 적용합니다

제가 Kind를 사용하고 있기 때문에, 3단계를 간단한 로드 단계로 대체할 수 있습니다. 이렇게 하면 Docker 저장소를 사용할 필요가 없습니다.

## 프로필

JAR 파일을 생성하기 전에 Spring Boot 프로필 두 개를 생성하는 것이 유용합니다. 이를 통해 애플리케이션을 연결된 모드 (지금까지 한 것처럼) 및 Kubernetes 클러스터 내에서 실행할 수 있습니다. Spring Boot 프로필 두 개를 생성하겠습니다:

<div class="content-ad"></div>

- `connected` ... 클러스터 외부에서 실행 중일 때 사용되는 모드
- `local-cluster` ... 클러스터 내부에서 실행 중일 때 사용되는 모드

현재 실행 중인 모드는 첫 번째입니다. 이는 우리가 간단히 application.yml(또는 application.properties) 파일을 application-connected.yml로 복사할 수 있다는 것을 의미합니다. 그런 다음 JVM 명령줄에 다음 JVM 인수를 추가할 수 있습니다:

```js
-Dspring.profiles.active=connected
```

로컬 클러스터 파일에 대해서도 동일한 작업을 수행하지만 이번에는 변경이 필요합니다.

<div class="content-ad"></div>

```js
...
  data:
    mongodb:
      host: my-mongo-db-svc.mongo.svc.cluster.local
      port: 27017
...
```

DNS 이름을 사용하여 올바른 팟에 연결할 수 있습니다. 팟에서 DNS 검색 규칙이 설정되어 있어 my-mongo-db-svc.mongo.svc와 같은 이름 일부를 생략할 수 있습니다. 이를 통해 다른 클러스터로 배포하고도 응용 프로그램이 작동할 수 있습니다.

## 이미지 생성

이제 이미지를 만드는 방법을 살펴보겠습니다. GitHub에 있는 프로젝트가 Gradle 프로젝트이므로 루트 프로젝트 폴더에서 다음과 같이 JAR 파일을 생성할 수 있습니다:```

<div class="content-ad"></div>

```js
gradle build 
```

gradle.build에 아래 내용이 추가되었음을 유의해주세요. 이는 manifest가 주 애플리케이션 파일을 가리키도록 합니다:

gradle.build

```js
jar {
    manifest {
        attributes "Main-Class": "com.requillion_solutions.aquarium.AquariumWithMongoDbApplication"
    }
}
```

<div class="content-ad"></div>

이렇게 하면 jar 파일이 생성됩니다: build/libs/aquarium-with-mongo-db-0.0.1-SNAPSHOT.jar.

도커 이미지를 만들기 위해서는 도커 파일이 필요합니다. 아래 내용대로 만들어보세요:

Dockerfile

```js
FROM openjdk:17.0.2-slim-buster
RUN addgroup --system spring && useradd --system spring -g spring
USER spring:spring
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
EXPOSE 8080
```

<div class="content-ad"></div>

이는 Java 17 기반 이미지를 시작으로 합니다 (이것은 롬복과의 문제를 피하기 위해 필요합니다) 그리고 새 사용자 (spring)를 추가하여 루트로 실행하지 않도록 합니다. 그런 다음 JAR 파일이 이미지로 복사되고 응용 프로그램을 실행하는 엔트리포인트가 생성됩니다.

다음 명령어로 도커 이미지를 생성하세요:

```bash
docker build -t aquarium .
```

그리고 만약 Kind를 사용 중이라면, 다음 명령어로 직접 Kubernetes 클러스터에 로드하세요:

<div class="content-ad"></div>

```js
kind load docker-image aquarium
```

이 작업이 완료되면 클러스터에서 실행하기 위한 배포 매니페스트를 생성할 준비가 되었습니다.

## 배포 매니페스트

이제 쿠버네티스 클러스터에 도커 이미지를 로드했으므로 배포 매니페스트를 사용하여 배포할 수 있습니다. 다음 파일을 만들어주세요:```

<div class="content-ad"></div>

k8s/deployment.yml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aquarium
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aquarium
  template:
    metadata:
      labels:
        app: aquarium
    spec:
      containers:
      - name: aquarium
        image: aquarium
        imagePullPolicy: IfNotPresent
        ports:
          - containerPort: 8080
        env:
          - name: SPRING_PROFILES_ACTIVE
            value: local-cluster
---
apiVersion: v1
kind: Service
metadata:
  name: aquarium
  namespace: default
spec:
  selector:
    app: aquarium
  type: NodePort
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30080
```

알아두어야 할 사항이 몇 가지 있어요:

- 어플리케이션이 default 네임스페이스에 배포되었어요 (네임스페이스가 지정되지 않으면 사용되는 곳이죠)
- 레플리카는 1개뿐이에요
- 이미지는 이전에 불러왔으므로, 이미지가 없을 때만 불러와요
- 프로필은 local-cluster로 설정돼요
- 서비스가 생성되어 어플리케이션의 포트 8080을 개발 머신의 포트 30080으로 매핑돼요

<div class="content-ad"></div>

이제 다음과 같이 배포할 수 있습니다:

```js
kubectl apply -f k8s/deployment.yml
```

시작이 성공적으로 이루어졌는지 확인해보세요:

```js
kubectl get pods
```

<div class="content-ad"></div>

한 번이 배포되면 API는 이전에 사용한 것과 동일한 curl 명령으로 테스트할 수 있습니다. 단, 포트를 30080으로 변경해 주세요.

```js
curl localhost:30080/api/v1/fishes -H "Content-Type: application/json" -d '{"type": "guppy2"}' 
curl localhost:30080/api/v1/fish-tanks -H "Content-Type: application/json" -d '{"name": "big one"}' 
curl localhost:30080/api/v1/fishes
curl localhost:30080/api/v1/fish-tanks
```

Compass UI에서 새 문서를 확인할 수도 있습니다 (포트 포워드가 여전히 유지되는지 확인해 주세요).

# 요약

<div class="content-ad"></div>

이 기사는 Kind Kubernetes 클러스터로 MongoDB를 설치하고 Spring Boot 애플리케이션과 통합하는 과정에 대해 다루었습니다.

이 연습은 꽤 간단하지만 그냥 어떻게 하는지 보여주는 것뿐입니다. 실제로는 보안, 백업 및 장애 조치에 작업이 필요할 것입니다.

다른 기사에서는 문서간의 관계를 어떻게 관리할 수 있는지도 보여드릴 예정입니다.

이 연습을 통해 No-SQL 데이터베이스가 Kubernetes와 Spring Boot와 간단하게 사용될 수 있다는 것을 보여줬으면 좋겠습니다.

<div class="content-ad"></div>

이 글을 즐겁게 읽으셨기를 바라며, 새로운 것을 배우며 기술을 향상시켰기를 바랍니다. 작은 거라도 새로운 지식을 얻었다면 좋겠네요.

이 글이 유익하게 느껴진다면, 박수 한 번 부탁드립니다. 그렇게 하면 미래에 어떤 글을 써야 하는지 파악할 수 있고, 다음 글을 결정하는 데 도움이 됩니다. 개선 사항이나 제안 사항이 있다면 메모나 답글로 추가해 주세요.