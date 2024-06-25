---
title: "Ruby on Rails로 OCR 시스템 구축하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-HowIbuiltanOCRSystemwithRails_0.png"
date: 2024-06-22 22:27
ogImage:
  url: /assets/img/2024-06-22-HowIbuiltanOCRSystemwithRails_0.png
tag: Tech
originalTitle: "How I built an OCR System with Rails"
link: "https://medium.com/@lcgarcia/how-i-built-an-ocr-system-with-rails-525535da8995"
---

친구와의 대화 중에 이 아이디어가 떠올랐어요. 친구가 문서를 스캔하고 분석해야 할 일이 많다는데, 시간을 내기가 어려워한다는 걸 언급했더라구요. 그 일이 어려운 작업으로 들리고, 스트레스를 받고 있다는 걸 알 수 있었어요. 그래서 "어쩌면 해결책을 찾을 수도 있겠네요" 라고 말했죠. 그 순간, Ruby와 RTesseract를 사용하여 작은 스크립트를 만들기로 결심했어요. 이 스크립트는 그의 문서 스캔을 도와주는 것뿐만 아니라 이미지에서 텍스트를 추출하기 위한 OCR 작업도 수행했답니다. 이 작업은 상당히 유용했고, 다른 사람들도 이 아이디어의 더 강력한 버전에서 혜택을 받을 수 있을 거라 생각했어요. 그래서 이렇게 Rails 어플리케이션이 탄생했죠.

![OCR 시스템을 Rails로 어떻게 만들었는지](/assets/img/2024-06-22-HowIbuiltanOCRSystemwithRails_0.png)

# 사용할 도구

## 이 프로젝트에서 사용할 도구는 다음과 같습니다:

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

- 루비 온 레일즈: 난 올드 스쿨이라서.
- Active Storage: 빠르니까.
- Tailwind CSS: 요즘 누가 안 하는가?
- Tesseract OCR: 마술사처럼 사진을 단어로 바꿔주지.
- RTesseract: 우리 앱과 친구되게 만들어 주는 루비 젬이야.

# 단계 1: 재료 준비하기

먼저 레일즈 앱이 필요해. 하나 만들기 위해 다음 명령어를 실행해봐

```js
rails new ocr
cd ocr
rails active_storage:install
rails db:migrate
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

다음으로, Tailwind가 필요합니다.

```js
bundle add tailwindcss-rails
rails tailwindcss:install
```

이걸로 대부분의 작업이 끝날 거예요. 더 자세히 알고 싶다면, Tailwind의 문서를 살펴보세요.

# 단계 2: 모델 생성하기

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

모델을 생성하여 파일 첨부 기능이 포함된 모델을 만들어봅시다. 예시를 위해 Document 모델을 생성해보겠습니다:
원하시는 다른 열을 포함시킬 수 있습니다. 저는 제목 열만을 포함하겠습니다.

```js
rails generate model Document title:string
rails db:migrate
```

# 단계 3: 모델 업데이트

파일 첨부를 다루기 위해 Active Storage를 사용할 것입니다. 이를 통해 모델에 파일을 쉽게 첨부할 수 있습니다. Document 모델을 수정하여 파일 첨부를 포함하도록 해야 합니다.

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

다음은 단계별로 설명해 드리겠습니다:

- 모델 생성: 이미 제목 속성이 있는 문서 모델을 생성했습니다.
- Active Storage 관계 추가: 문서 모델을 업데이트하여 하나의 첨부 파일이 있다는 것을 나타냅니다. 이는 Active Storage가 제공하는 has_one_attached 메서드를 사용하여 수행됩니다.
- 모델 정의: app/models/document.rb에 있는 문서 모델 파일을 열고 다음 코드를 추가하세요:

```ruby
class Document < ApplicationRecord
  has_one_attached :file
end
```

- has_one_attached :file: 이 코드는 Rails에게 각 문서 인스턴스가 첨부 파일을 하나 가질 수 있다고 알려줍니다. Active Storage가 첨부를 우리 대신 관리하며, 메타데이터는 데이터베이스에, 실제 파일은 구성된 저장 서비스(로컬 디스크, Amazon S3 등)에 저장됩니다.

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

우리 모델에 has_one_attached :file를 추가함으로써, 이제 생성할 컴포넌트가 또 하나 생겼습니다: 컨트롤러.

# 단계 4: 컨트롤러 생성

이제 모델을 설정했으니, 문서를 관리하고 파일 업로드를 처리하는 컨트롤러를 생성할 때입니다.

```js
rails generate controller Documents
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

다음으로, 문서를 업로드하고 표시하는 데 필요한 작업을 정의하겠습니다. app/controllers/documents_controller.rb에 위치한 DocumentsController 파일을 열어 다음 코드로 업데이트하세요:

```js
class DocumentsController < ApplicationController
  def new
    @document = Document.new
  end

  def create
    @document = Document.new(document_params)
    if @document.save
      redirect_to @document, notice: '문서가 성공적으로 업로드되었습니다.'
    else
      render :new
    end
  end

  def show
    @document = Document.find(params[:id])
  end

  private

  def document_params
    params.require(:document).permit(:title, :file)
  end
end
```

- new 액션: 이 액션은 새 Document 객체를 초기화합니다. 이것은 당신의 걸작을 위한 빈 캔버스를 설정하는 것과 같습니다. 여기서 미래의 OCR을 위해 파일을 업로드할 것입니다.
- create 액션: 이 액션은 새 문서를 생성하는 작업을 처리합니다. 폼 (제목 및 파일)에서 매개변수를 가져와 새 Document 객체를 만들고 저장을 시도합니다. 저장에 성공하면 문서의 표시 페이지로 이동하여 성공 메시지가 표시됩니다. 실패한 경우 새 문서 양식을 다시 렌더링하여 실수를 수정할 수 있습니다. 이 액션에서 또한 파일 업로드를 처리합니다.
- show 액션: 이 액션은 ID로 문서를 찾아서 표시합니다. 여기에서 마법이 일어날 것이지만, 현재는 파일을 표시만 합니다.

# 단계 5: 뷰 생성

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

다음으로 뷰를 생성할 차례에요.

아래 내용을 app/views/documents/new.html.erb에 추가해주세요:

```js
<!-- app/views/documents/new.html.erb -->
<div class="min-h-screen bg-gray-100 flex items-center justify-center">
  <div class="bg-white p-8 rounded-lg shadow-md w-full max-w-md">
    <h2 class="text-2xl font-bold mb-6 text-center">새 문서 업로드</h2>

    <%= form_with model: @document, local: true, class: "space-y-6" do |form| %>
      <% if @document.errors.any? %>
        <div class="bg-red-100 text-red-700 p-4 rounded-lg">
          <h3 class="font-bold">제출 과정에 오류가 있습니다:</h3>
          <ul class="list-disc list-inside">
            <% @document.errors.full_messages.each do |message| %>
              <li><%= message %></li>
            <% end %>
          </ul>
        </div>
      <% end %>

      <div class="space-y-2">
        <%= form.label :title, class: "block font-medium text-gray-700" %>
        <%= form.text_field :title, class: "block w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500" %>
      </div>

      <div class="space-y-2">
        <%= form.label :file, class: "block font-medium text-gray-700" %>
        <%= form.file_field :file, class: "block w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500" %>
      </div>

      <div>
        <%= form.submit "문서 업로드", class: "w-full bg-blue-500 text-white font-bold py-2 px-4 rounded-lg hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500" %>
      </div>
    <% end %>
  </div>
</div>
```

그리고 app/views/documents/show.html.erb에 다음 내용을 추가해주세요.

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

<p>
  <strong>Title:</strong>
  <%= @document.title %>
</p>

<p>
  <strong>File:</strong>
  <%= link_to @document.file.filename.to_s, rails_blob_path(@document.file, disposition: "attachment") %>
</p>

# Step 6: 라우트 추가

이 부분은 Rails가 대부분 처리하기 때문에 작은 단계입니다. config/routes.rb에 다음을 추가해보세요:

```ruby
Rails.application.routes.draw do
  resources :documents, only: [:new, :create, :show]
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

# 단계 7: 스토리지 서비스 구성하기

기본적으로 Active Storage는 파일을 저장하기위해 로컬 디스크를 사용합니다. config/storage.yml에서 Amazon S3, Google Cloud Storage 및 Microsoft Azure Blob Storage와 같은 다른 스토리지 서비스를 구성할 수 있습니다.

Active Storage를 사용하면 구성 파일을 업데이트하여 이러한 스토리지 서비스 간에 쉽게 전환할 수 있습니다. 이 유연성을 통해 필요에 가장 적합한 스토리지 솔루션을 선택하고 애플리케이션이 성장함에 따라 확장할 수 있습니다.

여기 우리 앱에서 사용할 구성입니다.

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

```js
지금 이 작업을 해보세요. 터미널에 가서 앱을 시작해보세요.

./bin/dev

다음과 같은 내용이 나와야 합니다:
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

<table> 태그를 Markdown 형식으로 변경하십시오.

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

당신의 Gemfile에 Gem을 추가해보세요.

```js
gem 'rtesseract'
```

# 단계 9: OCR 메소드 구현하기

문서 모델에 OCR을 수행하는 메소드를 만들어보세요. 아래와 같이 할 수 있어요!

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

```rb
class Document < ApplicationRecord
  has_one_attached :file

  def perform_ocr
    return unless file.attached?

    file_path = ActiveStorage::Blob.service.send(:path_for, file.key)
    image = RTesseract.new(file_path)
    image.to_s
  end
end
```

# 단계 10: 컨트롤러 업데이트하여 OCR 사용

OCR 수행을 위한 액션을 포함하도록 DocumentsController를 수정합니다:

```rb
class DocumentsController < ApplicationController
  def new
    @document = Document.new
  end

  def create
    @document = Document.new(document_params)
    if @document.save
      redirect_to @document, notice: '문서가 성공적으로 업로드되었습니다.'
    else
      render :new
    end
  end

  def show
    @document = Document.find(params[:id])
    @ocr_text = @document.perform_ocr
  end

  private

  def document_params
    params.require(:document).permit(:title, :file)
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

# 단계 11: OCR 텍스트 표시를 위한 뷰 업데이트

show.html.erb 뷰를 수정하여 OCR 텍스트를 표시합니다:

```js
<p>
  <strong>제목:</strong>
  <%= @document.title %>
</p>

<p>
  <strong>파일:</strong>
  <%= link_to @document.file.filename.to_s, rails_blob_path(@document.file, disposition: "attachment") %>
</p>

<% if @ocr_text.present? %>
  <p>
    <strong>OCR 텍스트:</strong>
    <pre><%= @ocr_text %></pre>
  </p>
<% end %>
```

# 단계 12: Tesseract가 설치되었는지 확인하기

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

시스템에 Tesseract OCR이 설치되어 있는지 확인해주세요. 다음 명령어를 사용하여 운영 체제에 맞게 설치할 수 있습니다:

```js
macOS:
brew install tesseract

Ubuntu:
sudo apt-get install tesseract-ocr
```

앱을 실행한 후에는 다음과 같은 결과가 나타납니다:

![OCR 결과](/assets/img/2024-06-22-HowIbuiltanOCRSystemwithRails_2.png)

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

텍스트에는 개선할 여지가 많이 있어요. 댓글로 의겢나 생각을 알려주세요.

또한, 제 Github에서 전체 소스 코드를 확인하세요: https://github.com/luizcg/ocr
