---
title: "Django를 사용하여 SaaS 애플리케이션용 동적 가격 테이블 만드는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_0.png"
date: 2024-07-01 16:34
ogImage: 
  url: /assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_0.png
tag: Tech
originalTitle: "How to Create a Dynamic Pricing Table for Your SaaS Application in Django"
link: "https://medium.com/@rcmisk/how-to-create-a-dynamic-pricing-table-for-your-saas-application-in-django-39003c8e7647"
---


당신이 개발자신 것을 발견해보세요. Django 기반 SaaS 애플리케이션을 위한 동적 요금표를 만드는 단계별 프로세스를 알려드릴게요.

저는 Ideaverify를 만들고 있어요! Ideaverify는 #indiehackers를 위해 아이디어 검증을 자동화하는 데 도움을 줄 거예요! 각 사용자는 자신의 서브도메인에 연결된 랜딩 페이지를 만들 수 있을 거예요. 일반적인 SaaS 랜딩 페이지에 있는 모든 인기 있는 섹션들이 있을 거예요. 제가 여러분과 함께 공유하고 싶은 한 가지 섹션은 SaaS를 위한 동적 요금표를 만들고 Django에서 데이터를 저장하고 검색하는 방법이에요.

- 기존의 Django 프로젝트를 사용하여 아래 코드를 추가할 수 있어요. 또는 처음부터 시작하세요.
- 처음부터 시작하는 경우: cd Desktop
- Django가 설치되어 있는지 확인하세요. 안 되어 있다면 pip install Django
- 그 다음 django-admin startproject landingpage
- cd landingpage 그리고 아래와 같이 입력하세요.
- pip install virtualenv
- python3.8 -m venv env
- source env/bin/activate
- pip install Django
- 우선 models.py 파일에 모델을 추가해봐요.

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

```python
from django.db import models

class PricingDescription(models.Model):
    description = models.CharField(max_length=256)
    order = models.PositiveIntegerField()

    def __str__(self):
        return self.description

    class Meta:
        ordering = ["order", "pk"]


class Pricing(models.Model):
    # payment interval enum
    class Interval(models.IntegerChoices):
        MONTHLY = 1, "월간"
        YEARLY = 2, "연간"

    # usage type enum
    class Type(models.IntegerChoices):
        RECURRING_PAYMENT = 1, "반복결제"
        ONE_TIME_PAYMENT = 2, "일시불"

    name = models.CharField(max_length=256)
    descriptions = models.ManyToManyField(
        to=PricingDescription, related_name="pricing_description"
    )
    price = models.PositiveIntegerField()
    interval = models.PositiveSmallIntegerField(
        choices=Interval.choices, null=True, blank=True
    )
    type = models.PositiveSmallIntegerField(
        choices=Type.choices, default=Type.RECURRING_PAYMENT
    )
    order = models.PositiveIntegerField()

    def __str__(self):
        return self.name

    class Meta:
        ordering = ["order", "pk"]

class LandingPage(models.Model):
    primary_hero_title = models.CharField(max_length=256)
    pricing = models.ManyToManyField(to=Pricing, related_name="landingpage_pricing")

    def __str__(self):
        return self.primary_hero_title
```

위의 3가지 모델을 통해 요금 정보를 정의하고 랜딩 페이지와 연결할 수 있습니다.

루트 디렉토리 내에 static_files 폴더를 추가해주세요.

static_files 안에 css 폴더를 추가해주세요.

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

루트 디렉토리 안에 템플릿 디렉토리를 추가해주세요.

프로젝트 구조는 아래와 같이 보여야 합니다.

![Project Structure](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_1.png)

이제 `python manange.py makemigrations` 명령을 실행하세요 (새로운 프로젝트라면 변경사항이 감지되지 않아야 합니다).

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

이제 Python manage.py migrate를 실행하세요.

![이미지](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_2.png)

이제 python manage.py makemigrations landingpage을 실행한 뒤 python manage.py migrate landingpage을 실행하세요.

2. 이제는 admin.py에 몇 가지 관리자 테이블을 추가해봅시다. 이렇게 하면 UI를 통해 데이터를 추가할 수 있습니다.

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

```python
from django.contrib import admin
from .models import (
    LandingPage,
    Pricing,
    PricingDescription,
)

class LandingPageAdmin(admin.ModelAdmin):
    model = LandingPage
    list_display = (
        "primary_hero_title",
    )


admin.site.register(LandingPage, LandingPageAdmin)

class PricingAdmin(admin.ModelAdmin):
    model = Pricing
    list_display = ("id", "name")

admin.site.register(Pricing, PricingAdmin)

class PricingDescriptionAdmin(admin.ModelAdmin):
    model = PricingDescription
    list_display = ("id", "description")

admin.site.register(PricingDescription, PricingDescriptionAdmin)
```

3. 이제 views.py에 내용을 추가해 봅시다.

```python
from django.shortcuts import render
from .models import LandingPage

def index(request):
    landing_page = LandingPage.objects.all()[0] # 1개의 랜딩 페이지가 있다고 가정합니다.
    context = {"landing_page": landing_page}
    
    return render(request, "index.html", context)
```

4. templates 폴더 안에 index.html을 추가해보세요. 이전에 생성되지 않았다면 다음과 같은 HTML 코드를 넣어주세요.

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

```html
{ load static }
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8" />
    <title>landingpage</title>
    <meta content="width=device-width, initial-scale=1" name="viewport" />
    <link href="{ static 'css/landingpage.css' }" rel="stylesheet"
        type="text/css" />
</head>

<body>
    <section class="rl_section_pricing18">
        <div class="rl-padding-global-2">
            <div class="rl-container-large-2">
                <div class="rl-padding-section-large-2">
                    <div class="rl_pricing18_component">
                        <div class="rl_pricing18_heading-wrapper">
                            <h2 class="rl-heading-style-h2">Pricing plan</h2>
                        </div>
                        <div class="rl_pricing18_spacing-block-3"></div>
                        <div class="rl_pricing18_plans_{landing_page.pricing.all|length}">
                            { for price in landing_page.pricing.all }
                            <div class="rl_pricing18_plan">
                                <div class="rl_pricing18_plan-content">
                                    <div class="rl_pricing18_plan-content-top">
                                        <div class="rl_pricing18_price-wrapper">
                                            <div class="rl-heading-style-h6">{price.name}</div>
                                            <div class="rl_pricing18_spacing-block-4"></div>
                                            <div class="rl-heading-style-h1-2">${price.price}<span
                                                    class="rl-heading-style-h4">/{ if price.interval == 1 }mo
                                                    { else}yr{ endif }</span></div>
                                            <div class="rl_pricing18_spacing-block-4"></div>
                                        </div>
                                        <div class="rl_pricing18_spacing-block-5"></div>
                                        <div class="rl_pricing18_feature-list">
                                            { for description in price.descriptions.all }
                                            <div class="rl_pricing18_feature">
                                                <div class="rl_pricing18_icon-wrapper">
                                                    <div class="rl_pricing18_icon w-embed"><svg fill="none"
                                                            height=" 100%" viewbox="0 0 24 24" width=" 100%"
                                                            xmlns="http://www.w3.org/2000/svg">
                                                            <path
                                                                d="M19.8501 7.25012L9.2501 17.8501C9.15621 17.9448 9.02842 17.998 8.8951 17.998C8.76178 17.998 8.63398 17.9448 8.5401 17.8501L3.1501 12.4601C3.05544 12.3662 3.0022 12.2384 3.0022 12.1051C3.0022 11.9718 3.05544 11.844 3.1501 11.7501L3.8501 11.0501C3.94398 10.9555 4.07178 10.9022 4.2051 10.9022C4.33842 10.9022 4.46621 10.9555 4.5601 11.0501L8.8901 15.3801L18.4401 5.83012C18.6379 5.63833 18.9523 5.63833 19.1501 5.83012L19.8501 6.54012C19.9448 6.634 19.998 6.7618 19.998 6.89512C19.998 7.02844 19.9448 7.15623 19.8501 7.25012Z"
                                                                fill="currentColor"></path>
                                                        </svg></div>
                                                </div>
                                                <div class="rl-text-style-regular-2">{ description.description }</div>
                                            </div>
                                            { endfor }
                                        </div>
                                        <div class="rl_pricing18_spacing-block-6"></div>
                                    </div>
                                    <a class="rl-button-2 w-button" href="#">Get started</a>
                                </div>
                            </div>
                            { endfor }
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</body>
</html>
```

5. 이제 static_files/css 디렉토리 안에 있는 landingpage.css에 몇 가지 CSS를 추가해 보겠습니다.

```css
.rl-button-2 {
    border: 1px solid black;
    background-color: black;
    color: white;
    text-align: center;
    padding: 0.75rem 1.5rem;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
      Noto Sans, Ubuntu, Cantarell, Helvetica Neue, Oxygen, Fira Sans, Droid Sans,
      sans-serif;
    font-size: 1rem;
}
.rl-text-style-regular-2 {
    color: black;
    margin-top: 0;
    margin-bottom: 0;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
      Noto Sans, Ubuntu, Cantarell, Helvetica Neue, Oxygen, Fira Sans, Droid Sans,
      sans-serif;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
}
.rl-heading-style-h4 {
    color: black;
    margin-top: 0;
    margin-bottom: 0;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
      Noto Sans, Ubuntu, Cantarell, Helvetica Neue, Oxygen, Fira Sans, Droid Sans,
      sans-serif;
    font-size: 2rem;
    font-weight: 700;
    line-height: 1.3;
}
  
.rl-heading-style-h1-2 {
    color: black;
    margin-top: 0;
    margin-bottom: 0;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
      Noto Sans, Ubuntu, Cantarell, Helvetica Neue, Oxygen, Fira Sans, Droid Sans,
      sans-serif;
    font-size: 3.5rem;
    font-weight: 700;
    line-height: 1.2;
}
  
.rl-heading-style-h6 {
    color: black;
    margin-top: 0;
    margin-bottom: 0;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
      Noto Sans, Ubuntu, Cantarell, Helvetica Neue, Oxygen, Fira Sans, Droid Sans,
      sans-serif;
    font-size: 1.25rem;
    font-weight: 700;
    line-height: 1.4;
}
.rl-heading-style-h2 {
    color: black;
    margin-top: 0;
    margin-bottom: 0;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
      Noto Sans, Ubuntu, Cantarell, Helvetica Neue, Oxygen, Fira Sans, Droid Sans,
      sans-serif;
    font-size: 3rem;
    font-weight: 700;
    line-height: 1.2;
}
  
.rl-padding-section-large-2 {
  padding-top: 7rem;
  padding-bottom: 7rem;
}
  
.rl-container-large-2 {
  width: 100%;
  max-width: 80rem;
  margin-left: auto;
  margin-right: auto;
}

.rl-padding-global-2 {
  padding-left: 5%;
  padding-right: 5%;
}

.rl_pricing18_spacing-block-6 {
  width: 100%;
  padding-bottom: 2rem;
}

.rl_pricing18_icon {
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 1.5rem;
  height: 1.5rem;
  display: flex;
}

.rl_pricing18_icon-wrapper {
  color: black;
  flex: none;
  align-self: flex-start;
}

.rl_pricing18_feature {
  grid-column-gap: 1rem;
  grid-row-gap: 1rem;
  display: flex;
}

.rl_pricing18_feature-list {
  grid-column-gap: 1rem;
  grid-row-gap: 1rem;
  grid-template-rows: auto;
  grid-template-columns: 1fr;
  grid-auto-columns: 1fr;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  display: grid;
}
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

```js
django.conf.settings에서 settings를 가져옵니다
django.conf.urls.static에서 static을 가져옵니다
django.contrib에서 admin을 가져옵니다
django.urls에서 path를 가져옵니다

from .views에서 *을 가져옵니다

urlpatterns = [
    path("admin/", admin.site.urls),
    path("index/", index),
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

7. settings.py가 설정되어 추가되었는지 확인합니다.

```js
os에서 import
from pathlib에서 Path import

#########################################
##  SITE_NAME - 고유 이름으로 수정 ##
#########################################
SITE_NAME = "랜딩 페이지"

# 이런 식으로 프로젝트 내에서 경로를 작성하세요: os.path.join(BASE_DIR, ...)
BASE_DIR = Path(__file__).resolve().parent.parent
# 보안 경고: 프로덕션 시 사용할 비밀 키를 비밀로 유지하세요!
SECRET_KEY = "비밀_키"

# 보안 경고: 프로덕션에서 디버그를 실행하지 마세요!
DEBUG = True
#########################################
##  애플리케이션 정의 ##
#########################################

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "landingpage",
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = "landingpage.urls"

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [
            BASE_DIR,
            "templates/",
        ],
        "APP_DIRS": True,
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.debug",
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",
            ],
        },
    },
]

WSGI_APPLICATION = "landingpage.wsgi.application"


#########################################
##  데이터베이스 ##
#########################################
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

#########################################
##  비밀번호 검증 ##
#########################################

AUTH_PASSWORD_VALIDATORS = [
    {
        "NAME": "django.contrib.auth.password_validation.UserAttributeSimilarityValidator",
    },
    {
        "NAME": "django.contrib.auth.password_validation.MinimumLengthValidator",
    },
    {
        "NAME": "django.contrib.auth.password_validation.CommonPasswordValidator",
    },
    {
        "NAME": "django.contrib.auth.password_validation.NumericPasswordValidator",
    },
]

#########################################
##  지역화 ##
#########################################

LANGUAGE_CODE = "en-us"

TIME_ZONE = "America/New_York"

USE_I18N = True

USE_L10N = True

USE_TZ = True

DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"

STATIC_ROOT = os.path.join(BASE_DIR, "staticfiles")
STATIC_URL = BACKEND_URL + "/static/"
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static_files"),
]
```

8. 이제 python manage.py createsuperuser를 실행하고 사용자 이름, 이메일, 비밀번호를 입력하여 Django 관리자 UI에 로그인하세요


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

9. python manage.py runserver 명령을 실행하세요.

10. http://127.0.0.1:8000/admin/로 이동하세요.

11. 사용자 이름과 비밀번호로 로그인하세요.

12. http://127.0.0.1:8000/admin/landingpage/pricingdescription/로 이동하세요.

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

13. Basic, Pro 및 Premium에 대한 설명을 추가하세요.

![Dynamic Pricing Table](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_3.png)

14. http://127.0.0.1:8000/admin/landingpage/pricing/ 로 이동하세요.

15. Basic, Pro 및 Premium에 대한 설명이 포함된 3가지 요금을 추가하세요.

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


![2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_4](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_4.png)

![2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_5](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_5.png)

![2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_6](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_6.png)

이제 가격 표는 3개의 가격을 가져야 합니다.


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


![Image 1](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_7.png)

16. 이제 http://127.0.0.1:8000/admin/landingpage/landingpage/ 로 이동하여 랜딩 페이지를 추가하십시오.

![Image 2](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_8.png)

17. http://localhost:8000/index/ 로 이동하여 동적 가격 테이블을 확인할 수 있습니다.


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

마크다운 형식으로 테이블 태그를 변경하세요.


![How to Create a Dynamic Pricing Table for Your SaaS Application in Django](/assets/img/2024-07-01-HowtoCreateaDynamicPricingTableforYourSaaSApplicationinDjango_9.png)

그게 다에요! 즐기셨기를 바래요.

만약 이 튜토리얼이 마음에 들었고 도움이 되었다면, 저를 팔로우해주세요. 코딩, 공개로 개발하기, 인디핵커, 자동화, 스타트업 또는 기업가정신에 대해 배우는 것을 즐기신다면:

rcmisk.com


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

https://twitter.com/rcmisk

https://indiehackers.com/rcmisk

https://dev.to/rcmisk

내 소식을 계속해서 받고 배우기 위해 뉴스레터에 가입해주세요!