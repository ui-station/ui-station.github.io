---
title: "우분투 2204에서 Apache Hadoop 336 설치 방법"
description: ""
coverImage: "/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_0.png"
date: 2024-06-22 22:14
ogImage: 
  url: /assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_0.png
tag: Tech
originalTitle: "Apache Hadoop 3.3.6 Installation on Ubuntu 22.04"
link: "https://medium.com/@abhikdey06/apache-hadoop-3-3-6-installation-on-ubuntu-22-04-14516bceec85"
---


빅 데이터의 계속 확장되는 세계에서는 대량의 정보를 효율적으로 관리하고 처리하는 것이 비즈니스와 조직에게 중요해지고 있습니다. 이 데이터 혁명의 선두에 서 있는 것이 하둡(Hadoop)입니다. 이 강력한 오픈 소스 프레임워크는 분산 데이터 저장 및 처리의 과제에 대응하기 위해 설계되었습니다. 하둡의 잠재력을 활용하여 데이터 프로젝트를 진행하고 싶지만 설치 과정이 어려워 두려워하지 마십시오! 이 포괄적인 안내서는 여러분이 Ubuntu 시스템에 Hadoop을 설치하는 필수 단계를 안내하여 데이터 분석의 무한한 가능성을 탐험할 수 있도록 도와줄 것입니다.

데이터 전문가로 성장하고자 하는 경험이 풍부한 분이든 데이터 여정을 시작하려는 초보자든, 이 안내서는 이제껏 보다 Hadoop을 접근하기 쉽게 만들고자 합니다. 이 여정을 마치면 분산 데이터 처리 세계를 탐험하고, Hadoop의 능력을 활용하며 데이터 주도형 노력에 대한 잠재력을 최대한 이끌어낼 준비가 되어 있을 것입니다. 그러니 함께 진행하여 빅 데이터 분석의 세계로 흥미진진한 여정을 떠나봅시다.

# 단계 1: Java 개발 키트 설치

기본 Ubuntu 저장소에는 Java 8과 Java 11이 모두 포함되어 있습니다. 저는 이 중 Java 8을 사용하고 있습니다. 다음 명령을 사용하여 Java를 설치하세요.

<div class="content-ad"></div>

```bash
sudo apt update && sudo apt install openjdk-8-jdk
```

# 단계 2: Java 버전 확인하기:

성공적으로 설치했다면, 현재 Java 버전을 확인하세요:

```bash
java -version
```

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_0.png)

# 단계 3: SSH 설치:

Hadoop을 위해 SSH (Secure Shell)를 설치하는 것은 매우 중요합니다. 이를 통해 Hadoop 클러스터의 노드 간 안전한 통신이 가능해지며, 클러스터 전체에 걸쳐 데이터 무결성과 기밀성을 보장하며 데이터의 효율적인 분산 처리를 가능하게 합니다.

```js
sudo apt install ssh
``` 


<div class="content-ad"></div>

# 단계 4: 하둡 사용자 만들기: 

모든 하둡 구성 요소는 Apache 하둡을 위해 만든 사용자로 실행되며 해당 사용자는 하둡의 웹 인터페이스에 로그인하는 데도 사용됩니다. 

다음 명령을 실행하여 사용자를 만들고 비밀번호를 설정하세요:

```js
sudo adduser hadoop
```

<div class="content-ad"></div>

아래는 Markdown 형식을 사용해서 표로 바꿔줄게요.


![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_1.png)

## Step 5: 사용자 전환:

새로 생성된 하둡 사용자로 전환하세요:

```bash
su - hadoop
```


<div class="content-ad"></div>

# 단계 6 : SSH 구성:
이제 새로 만든 하둡 사용자의 비밀번호 없는 SSH 액세스를 구성해 보겠습니다. 파일을 저장하고 암호구 문을 입력하지 않아도 되게 합니다. 먼저 SSH 키페어를 생성합니다:

```js
ssh-keygen -t rsa
```

![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_2.png)

<div class="content-ad"></div>

# 단계 7: 권한 설정:

생성된 공개 키를 권한이 올바르도록 인가된 키 파일에 복사하고 설정하세요:

```js
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys   
chmod 640 ~/.ssh/authorized_keys
```

# 단계 8: 로컬호스트로 SSH 연결하기

<div class="content-ad"></div>

```js
ssh localhost
```

로컬호스트를 인증하려면 알려진 호스트에 RSA 키를 추가하여 호스트를 인증해야 합니다. "yes"를 입력하고 Enter를 눌러 localhost를 인증하세요.

![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_3.png)

# 단계 9: 사용자 전환

<div class="content-ad"></div>

다시 Hadoop으로 전환해주세요.

```bash
su - hadoop
```

# 단계 10: Hadoop 설치하기

- Hadoop 3.3.6 다운로드하기

<div class="content-ad"></div>

```js
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```

- 파일을 다운로드한 후에 압축을 풀어주세요.

```js
tar -xvzf hadoop-3.3.6.tar.gz
```

- 추출된 폴더를 버전 정보를 제거하여 이름을 변경해주세요. 이 단계는 선택 사항입니다. 이름을 변경하지 않으려면 구성 경로를 조정해주세요.

<div class="content-ad"></div>


mv hadoop-3.3.6 hadoop


![Installation Guide](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_4.png)

- Next, you will need to configure Hadoop and Java Environment Variables on your system. Open the `~/.bashrc` file in your favorite text editor. Here I am using the nano editor. To paste the code, use `Ctrl+Shift+V`, for saving the file use `Ctrl+X` and `Ctrl+Y`, then hit `Enter`:

```bash
nano ~/.bashrc
```

<div class="content-ad"></div>

```js
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```

![Apache Hadoop](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_5.png)

- 현재 환경에서 위의 구성을 로드합니다.

<div class="content-ad"></div>

```js
source ~/.bashrc
```

- 하둡 환경 변수 파일인 hadoop-env.sh 파일에서 JAVA_HOME을 구성해야 합니다. 텍스트 편집기에서 Hadoop 환경 변수 파일을 편집하세요:

```js
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

"export JAVA_HOME"을 검색하고 구성하세요.

<div class="content-ad"></div>

```js
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

![Image](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_6.png)

# 11단계 : 하둡 구성하기:

- 먼저, 하둡 사용자 홈 디렉터리 내에서 namenode 및 datanode 디렉터리를 생성해야합니다. 다음 명령을 실행하여 두 디렉터리를 생성하십시오:

<div class="content-ad"></div>

```js
cd 하둡/
```

```js
mkdir -p ~/hadoopdata/hdfs/{namenode,datanode}
```

<img src="/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_7.png" />

- 다음으로 core-site.xml 파일을 편집하고 시스템 호스트 이름으로 업데이트하세요:

<div class="content-ad"></div>

```js
나노 $HADOOP_HOME/etc/hadoop/core-site.xml
```

아래 이름을 시스템 호스트 이름에 맞게 변경하세요:

```js
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

<img src="/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_8.png" />

<div class="content-ad"></div>

파일을 저장하고 닫으세요.

- 그런 다음, hdfs-site.xml 파일을 편집하세요:

```js
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```

- 다음과 같이 NameNode 및 DataNode 디렉토리 경로를 변경하세요:

<div class="content-ad"></div>

```js
<구성>
    <속성>
        <이름>dfs.replication</이름>
        <값>1</값>
    </속성>
    <속성>
        <이름>dfs.namenode.name.dir</이름>
        <값>file:///home/hadoop/hadoopdata/hdfs/namenode</값>
    </속성>
    <속성>
        <이름>dfs.datanode.data.dir</이름>
        <값>file:///home/hadoop/hadoopdata/hdfs/datanode</값>
    </속성>
 </구성>
```

<img src="/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_9.png" />

- 이후, mapred-site.xml 파일을 편집합니다:

```js
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

<div class="content-ad"></div>

- 다음 변경 사항을 적용해주세요:

```js
<configuration>
   <property>
      <name>yarn.app.mapreduce.am.env</name>
      <value>HADOOP_MAPRED_HOME=$HADOOP_HOME/home/hadoop/hadoop/bin/hadoop</value>
   </property>
   <property>
      <name>mapreduce.map.env</name>
      <value>HADOOP_MAPRED_HOME=$HADOOP_HOME/home/hadoop/hadoop/bin/hadoop</value>
   </property>
   <property>
      <name>mapreduce.reduce.env</name>
      <value>HADOOP_MAPRED_HOME=$HADOOP_HOME/home/hadoop/hadoop/bin/hadoop</value>
   </property>
</configuration>
```

![Apache Hadoop Installation on Ubuntu 22.04](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_10.png)

- 그런 다음, yarn-site.xml 파일을 편집하세요.

<div class="content-ad"></div>

```js
나노 $HADOOP_HOME/etc/hadoop/yarn-site.xml
```

- 다음 변경 사항을 적용하세요:

```js
<configuration>
   <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_11.png)

<div class="content-ad"></div>

파일을 저장하고 닫아주세요.

# 단계 12: Hadoop 클러스터 시작하기:

- Hadoop 클러스터를 시작하기 전에 Namenode를 hadoop 사용자로 포맷해야 합니다.
- 다음 명령을 실행하여 Hadoop Namenode를 포맷합니다:

```js
hdfs namenode -format
```

<div class="content-ad"></div>

- Namenode 디렉토리를 hdfs 파일 시스템으로 성공적으로 포맷한 후에는 "Storage directory /home/hadoop/hadoopdata/hdfs/namenode has been successfully formatted" 메시지가 표시됩니다.

![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_12.png)

- 그런 다음 다음 명령어로 Hadoop 클러스터를 시작합니다.

```js
start-all.sh
```

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_13.png)

- 이제 jps 명령어를 사용하여 모든 Hadoop 서비스의 상태를 확인할 수 있습니다:

```js
jps
```

![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_14.png)


<div class="content-ad"></div>

# 단계 13: 하둡 네임노드 및 리소스 매니저에 액세스하기:

- 먼저 우리의 IP 주소를 알아야 합니다. Ubuntu에서는 ipconfig 명령을 실행하기 위해 net-tools를 설치해야 합니다. net-tools를 처음으로 설치하는 경우 기본 사용자로 전환하세요:

```js
sudo apt install net-tools
```

- 그런 다음 ifconfig 명령을 실행하여 우리의 IP 주소를 알아낼 수 있습니다:

<div class="content-ad"></div>

```js
ifconfig
```

![Image](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_15.png)

여기서 내 IP 주소는 192.168.1.6입니다.

- 네임노드에 액세스하려면 웹 브라우저를 열고 다음 URL을 방문하십시오. http://your-server-ip:9870. 다음 화면이 표시됩니다:

<div class="content-ad"></div>

http://192.168.1.6:9870

![Apache Hadoop Installation on Ubuntu 22.04](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_16.png)

- 리소스 관리자에 액세스하려면 웹 브라우저를 열고 URL http://your-server-ip:8088을 방문하십시오. 다음 화면이 표시됩니다:

http://192.168.1.6:8088

<div class="content-ad"></div>

<img src="/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_17.png" />

# 단계 13: Hadoop 클러스터 확인하기:

이 시점에서 Hadoop 클러스터가 설치되고 구성되었습니다. 다음으로, HDFS 파일 시스템에 몇 가지 디렉토리를 생성하여 Hadoop을 테스트할 것입니다.

- 다음 명령을 사용하여 HDFS 파일 시스템에 몇 가지 디렉토리를 생성해 봅시다.

<div class="content-ad"></div>

```js
hdfs dfs -mkdir /test1
hdfs dfs -mkdir /logs
```

- 다음은 위의 디렉토리를 나열하는 다음 명령어를 실행하십시오:

```js
hdfs dfs -ls /
```

다음과 같은 출력이 나와야 합니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_18.png" />

- 또한, 하둡 파일 시스템에 파일을 추가하세요. 예를 들어, 호스트 머신의 로그 파일을 하둡 파일 시스템에 넣어보세요.

```js
hdfs dfs -put /var/log/* /logs/
```

해당 파일 및 디렉터리를 하둡 웹 인터페이스에서도 확인할 수 있습니다.

<div class="content-ad"></div>

웹 인터페이스로 이동하셔서 'Utilities'를 클릭하신 후 '파일 시스템 찾아보기'를 선택해주세요. 여러분이 이전에 만든 디렉토리들이 다음 화면에서 보일 것입니다:

![이미지](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_19.png)

# 14 단계: Hadoop 서비스 중지하기 :

Hadoop 서비스를 중지하려면, hadoop 사용자로 다음 명령어를 실행해주세요:

<div class="content-ad"></div>

```js
stop-all.sh
```

![Image](/assets/img/2024-06-22-ApacheHadoop336InstallationonUbuntu2204_20.png)

이 튜토리얼은 우분투 22.04 리눅스 시스템에 Hadoop를 설치하고 구성하는 방법을 단계별로 설명해줍니다.

# 결론:

<div class="content-ad"></div>

요약하자면, 여러분은 이제 우분투 시스템에 Hadoop을 설치하는 지식과 기술을 갖추었으며, 빅 데이터 분석의 엄청난 잠재력을 발휘하기 위한 첫걸음을 내딛었습니다. 설치 과정을 정복함으로써, 분산 데이터 처리와 분석의 방대한 세계를 탐험할 수 있는 길을 열었습니다.

Hadoop을 활용하면 대규모 데이터셋을 처리하고 관리하는 도전에 대처할 강력한 도구를 갖게 됩니다. 데이터 전문가로서 전문성을 확장하려는 숙련된 분이든, 데이터의 세계에 입문한 초보자든, 이 안내서는 Hadoop을 저절로 접근 가능하게 만들어주었습니다.

그러므로 빅 데이터 분석의 세계로 여행을 떠날 때, 이것이 시작에 불과하다는 것을 기억하세요. 발견하고 활용할 수 있는 무한한 가능성이 기다리고 있습니다. 즐겁게 탐험하시고, 데이터 중심의 모험을 통해 가치 있는 통찰과 혁신적인 발견을 이루시기를 기대합니다!