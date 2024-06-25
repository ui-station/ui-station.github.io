---
title: "Rails에서 낙관적 락킹은 무엇인가요"
description: ""
coverImage: "/assets/img/2024-06-19-Whatisoptimisticlockinginrails_0.png"
date: 2024-06-19 22:17
ogImage:
  url: /assets/img/2024-06-19-Whatisoptimisticlockinginrails_0.png
tag: Tech
originalTitle: "What is optimistic locking in rails?"
link: "https://medium.com/@imvishalpandey/what-is-optimistic-locking-in-rails-66fef33c3c19"
---

낙관적 락킹은 데이터베이스 시스템에서 동시에 여러 사용자가 데이터에 동시 액세스를 관리하는 동시성 제어 메커니즘입니다. 충돌이 드물고 트랜잭션이 일반적으로 서로 간섭하지 않을 것으로 가정합니다. 비관적 락킹과 달리 최적적 락킹은 데이터를 처음 액세스할 때 잠그는 대신, 트랜잭션이 커밋하려고 할 때만 충돌을 확인합니다.

다음은 작동 방식입니다:

- 트랜잭션 시작: 트랜잭션이 레코드를 읽을 때 해당 레코드와 연관된 버전 번호 또는 타임스탬프도 검색합니다.
- 트랜잭션 처리: 트랜잭션은 데이터를 로컬로 변경합니다.
- 커밋: 커밋하기 전에 트랜잭션은 데이터베이스에서 레코드의 버전 번호 또는 타임스탬프를 확인합니다:

  - 트랜잭션이 시작되었을 때 버전 번호나 타임스탬프가 변경되지 않았다면, 트랜잭션은 변경 사항을 커밋합니다.
  - 버전 번호나 타임스탬프가 변경되었다면 (다른 트랜잭션이 레코드를 수정했다는 것을 나타냄), 해당 트랜잭션은 중지되고 다시 시도해야 합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 낙관적 락킹 예시

간단한 예시로 온라인 상점의 제품을 위한 데이터베이스 테이블을 고려해 봅시다:

## 제품 테이블:

![Products Table](/assets/img/2024-06-19-Whatisoptimisticlockinginrails_0.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 시나리오

- 사용자 A는 버전 번호가 1인 "위젯"(ProductID=1)의 제품 세부 정보를 읽습니다.
- 사용자 B는 동일한 제품 "위젯"의 버전 번호가 1인 제품 세부 정보를 읽습니다.
- 사용자 A가 "위젯"의 가격을 $12.00으로 업데이트하고 트랜잭션을 커밋하려고 합니다:

- 시스템은 데이터베이스 내 "위젯"의 현재 버전 번호를 확인합니다(아직 1).
- 버전 번호가 일치하므로 업데이트가 진행되고, 가격이 $12.00으로 설정되고, 버전 번호가 2로 증가합니다.

4. 이제 사용자 B가 "위젯"의 가격을 $11.00으로 업데이트하려고 합니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 시스템은 데이터베이스에서 "위젯"의 현재 버전 번호를 확인합니다 (현재 2).
- 버전 번호가 변경되었으므로 다른 트랜잭션이 레코드를 수정했다는 것을 나타내어, 사용자 B의 트랜잭션이 실패합니다.
- 사용자 B는 업데이트된 레코드(버전 2)를 읽어 트랜잭션을 다시 시도해야 합니다.

# 장단점

장점:

- Non-blocking: 낙관적 잠금은 잠금을 유지하는 오버헤드를 피함으로써 여러 사용자가 대기없이 동일한 데이터로 작업할 수 있습니다.
- 확장성: 충돌 비율이 낮은 환경에서 더 확장 가능하며, 이는 리소스에 대한 경합을 줄여줍니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

단점:

- 다시 시도 필요: 충돌이 커밋 시간에 감지되면 트랜잭션이 다시 시도될 수 있습니다.
- 고 갈등 환경에 적합하지 않음: 고 갈등 환경에서는 충돌 가능성이 증가하여 더 자주 다시 시도하게 되고 성능이 감소할 수 있습니다.

다음은 루비 온 레일즈에서 낙관적 잠금을 구현하는 예시입니다.

우리가 제품 테이블을 가지고 있다고 가정해봅시다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```ruby
class CreateProducts < ActiveRecord::Migration[6.1]
  def change
    create_table :products do |t|
      t.string :name
      t.decimal :price, precision: 8, scale: 2
      t.integer :lock_version, default: 0, null: false

      t.timestamps
    end
  end
end
```

## 모델 정의

다음으로 Product 모델을 정의하세요. lock_version 열은 Rails에서 낙관적 락킹에 자동으로 사용됩니다.

```ruby
class Product < ApplicationRecord
end
```

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 예시 사용법

담아 두고 있는 상황에서는 어떻게 낙관적 락을 사용할 수 있을까요:

## 사용자 A와 사용자 B가 동일한 레코드를 읽고 있는 경우

```js
# 사용자 A
user_a_product = Product.find(1) # id가 1인 제품을 읽음
# user_a_product.lock_version은 0입니다

# 사용자 B
user_b_product = Product.find(1) # 동일한 제품을 읽음
# user_b_product.lock_version도 0입니다
```

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 사용자 A가 레코드를 업데이트합니다

```js
# 사용자 A가 가격을 업데이트합니다
user_a_product.price = 12.00
user_a_product.save
# 이로써 lock_version이 1로 증가합니다
```

## 사용자 B가 레코드를 업데이트하려고 합니다

```js
# 사용자 B가 가격을 업데이트하려고 합니다
user_b_product.price = 11.00
begin
  user_b_product.save
rescue ActiveRecord::StaleObjectError
  puts "이전 정보가 감지되었습니다."
  # 충돌을 처리하기 위해 레코드를 다시 불러와서 재시도할 수 있습니다
  user_b_product.reload
  # 이제 user_b_product.lock_version은 1입니다
  # 사용자 B가 업데이트를 다시 시도할지 결정할 수 있습니다
end
```

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 충돌 처리

ActiveRecord::StaleObjectError가 발생하면, 해당 레코드가 읽힌 후 다른 트랜잭션에 의해 수정되었음을 나타냅니다. 이를 처리하기 위해 레코드를 다시 불러오고 사용자에게 알릴 수도 있으며, 트랜잭션을 다시 시도하거나 애플리케이션 로직에 따라 변경 사항을 병합할 수 있습니다.

## 완전한 예제

```js
class ProductsController < ApplicationController
  def update
    @product = Product.find(params[:id])
    @product.assign_attributes(product_params)

    begin
      @product.save
      flash[:notice] = "제품이 성공적으로 업데이트되었습니다."
    rescue ActiveRecord::StaleObjectError
      flash[:alert] = "제품이 다른 사용자에 의해 업데이트되었습니다. 변경 사항을 검토하고 다시 시도해주세요."
      @product.reload
      # 선택적으로 사용자의 변경 사항을 다시 적용하고 통합된 양식을 사용자에게 제시할 수 있음
    end

    redirect_to @product
  end

  private

  def product_params
    params.require(:product).permit(:name, :price)
  end
end
```

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 결론

이 예는 Ruby on Rails 애플리케이션에서 낙관적 잠금을 사용하는 방법을 보여줍니다. lock_version 열을 포함하고 Active Record의 내장 메커니즘을 사용하여 Rails는 버전 확인을 자동으로 처리하고 충돌이 감지되면 예외를 발생시킵니다. 이를 통해 애플리케이션이 적절하게 처리할 수 있도록 합니다.
