---
title: "맥OS에서 RVM 설치하기 단계별로"
description: ""
coverImage: "/assets/img/2024-05-17-InstallRVMinmacOSstepbystep_0.png"
date: 2024-05-17 17:46
ogImage:
  url: /assets/img/2024-05-17-InstallRVMinmacOSstepbystep_0.png
tag: Tech
originalTitle: "Install RVM in macOS (step by step)"
link: "https://medium.com/@nrogap/install-rvm-in-macos-step-by-step-d3b3c236953b"
---

오직 나 한테만 말하고 싶어서 죄송한데, 덕분에 글쓰기를 멈출 수가 없어 😛

# 필수 준비물

- Homebrew 🍺

# 시작해봅시다!

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

1. GnuPG 설치

```bash
$ brew install gnupg
```

2. GPG 키 설치 (첫 번째 방법 또는 두 번째 방법 중 선택)

2.1 첫 번째 방법

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

$ gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

2.2 Second way

$ gpg --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

3. Install RVM

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
$ \curl -sSL https://get.rvm.io | bash
```

4. 콘솔에서 감사 메시지 🙏를 받게 됩니다.

5. 모든 터미널을 종료하세요.

6. 새 터미널을 열고 이겼어요! 해보세요.

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
$ rvm 리스트
```

7. 이 메시지가 나타납니다.

```js
# 아직 rvm 루비가 설치되지 않았습니다. 'rvm 도움말 설치'를 시도해보세요.
```

8. 2.7.1과 같은 몇 가지 루비 버전을 설치하세요.

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

- 예전 버전인 2.3.1 등을 사용하고 싶다면 아래 팁 항목을 확인해주세요 😉
- 버전 3 이상을 사용하고 싶다면 아래 루비 버전 3+에 대한 참고사항을 확인해주세요 😁

```bash
$ rvm install 2.7.1
```

9. 설치가 완료된 후 사용 가능한 루비 버전을 확인해보세요.

```bash
$ rvm list
ruby-2.7.1 [ x86_64 ]
# 기본 루비가 설정되지 않았습니다. 'rvm alias create default <ruby>'를 시도해보세요.
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

10. 기본 루비 버전을 만듭니다

```js
$ rvm alias create default 2.7.1
```

11. 그게 다에요! 즐겨주세요 🎉

# 팁 💡

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
루비의 이전 버전을 사용하는 경우, openssl과 관련된 아래와 같은 오류 메시지가 발생할 수 있습니다.

```

/Users/pagorn/.rvm/src/rubygems-3.0.8/lib/rubygems/core_ext/kernel_require.rb:54:in `require': cannot load such file -- openssl (LoadError)

따라서, rvm에서 openssl을 설치한 다음 이 openssl을 사용하여 이전 버전의 루비를 설치해야 합니다.

$ rvm pkg install openssl
$ rvm install 2.3.1 --with-openssl-dir=$HOME/.rvm/usr

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

# Ruby 버전 3+에 대한 참고 사항

루비 v3를 설치할 때는 특정 openssl 위치를 지정해야 할 수 있습니다.

설치하기 전에 openssl 위치를 확인해주세요.

- 위치 A

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
$ ls /usr/local/opt | grep openssl
openssl
openssl@1.1
openssl@3
openssl@3.1
```

```js
$ rvm install ruby-3.1.0 --with-openssl-dir=/usr/local/opt/openssl@3.1
```

- 위치 B

```js
$ ls /opt/homebrew/opt | grep openssl
openssl
openssl@1.1
openssl@3
openssl@3.1
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
$ rvm install ruby-3.1.0 --with-openssl-dir=/opt/homebrew/opt/openssl@3.1
```

# 참고 자료

- https://formulae.brew.sh/formula/gnupg
- https://rvm.io/
- https://stackoverflow.com/questions/54895263/how-to-install-gpg2-via-homebrew
- https://github.com/rvm/rvm/issues/4607#issuecomment-619422100
- https://github.com/rvm/rvm/issues/4607#issuecomment-621343322
- https://mac.install.guide/homebrew/3.html
