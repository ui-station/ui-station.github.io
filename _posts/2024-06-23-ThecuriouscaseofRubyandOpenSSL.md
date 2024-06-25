---
title: "루비와 OpenSSL의 미스터리한 이야기"
description: ""
coverImage: "/assets/img/2024-06-23-ThecuriouscaseofRubyandOpenSSL_0.png"
date: 2024-06-23 20:55
ogImage:
  url: /assets/img/2024-06-23-ThecuriouscaseofRubyandOpenSSL_0.png
tag: Tech
originalTitle: "The curious case of Ruby and OpenSSL"
link: "https://medium.com/@computers-art/the-curious-case-of-ruby-and-openssl-e018b703f8d"
---

![image](/assets/img/2024-06-23-ThecuriouscaseofRubyandOpenSSL_0.png)

만약 당신이 컴퓨터에 익숙한 사용자라면, 토런트를 통해 게임을 다운로드하고 크랙을 사용하거나 라이브러리를 설치하는 경우와 같이 일부 상황에서는 오류를 만날 수밖에 없습니다. 그럴 때 여러 웹사이트를 찾아다니거나 여러 YouTube 동영상을 보며 해결책을 찾는 긴 여정을 겪게 될 수 있습니다. 그런 상황에서는 당신의 노력이 가치 있는지 의심하게 될 수도 있지만, 결국 그 문제를 해결했을 때의 기분은 정말로 훌륭합니다. 제 경험을 들어보면 PC용 콜 오브 듀티 블랙 옵스 II를 수정하거나 Wii의 임의의 CD를 허용하기 위해 크랙을 적용하거나 Arch Linux를 설치하는 등 여러 번 이런 상황을 겪었습니다. 그리고 지금은 Ruby용 webauthn 젬을 설치하는 과정에서 OpenSSL 오류를 해결하고 있습니다. StackOverflow의 답변을 여러 개 찾아보고 관련 블로그 글을 여러 개 읽은 결과, 처음에는 문제 해결에 실패했지만 약 한 시간을 투자한 끝에 성공했습니다. 비슷한 문제가 다시 발생할 경우를 대비해 나의 해결책을 문서로 남겨두기로 했습니다.

webauthn을 설치하려고 하는 과정에서 에러 메시지가 나왔습니다. sudo gem install webauthn 명령을 입력하자 다음과 같은 응답이 왔습니다: ERROR: While executing gem ... (Gem::Exception) Unable to require openssl, install OpenSSL and rebuild ruby (preferred) or use non-HTTPS sources..

두 가지 잠재적인 해결책을 살펴보았습니다. 먼저, 몇몇 온라인 소스에서 제안한 대로 기본 Ruby를 제거하고 다시 설치해보기로 결정했습니다. rvm remove ruby-2.6.10 명령을 사용하여 버전을 성공적으로 제거했지만 rvm install ruby-2.6.10 --with-openssl-dir=`/usr/local/opt/openssl` 명령을 사용하여 재설치하려고 시도했을 때 추가 문제가 발생했습니다. Ruby를 설치했지만 webauthn을 설치하려고 할 때 문제가 지속되었습니다. 나중에 Ruby gem을 업데이트해야 문제를 해결할 수 있다는 것을 발견했습니다. sudo gem update --system 명령을 사용하여 시도했지만 또 다른 오류가 발생했습니다: ruby: No such file or directory -- setup.rb (LoadError). 여러 해결책을 조사한 끝에 gem install rubygems-update --source http://production.s3.rubygems.org/ update_rubygems와 같은 방법으로 시도해도 문제를 해결할 수 없었습니다. 결국 다시 Ruby를 제거하고 brew install을 통해 설치하니 gem을 성공적으로 업데이트할 수 있었습니다.

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

OpenSSL 오류를 해결하면서 루비와 비슷한 방법을 시도하기로 결정했습니다. homebrew를 사용하여 OpenSSL 1.0을 설치하기로 결정해서 brew install rbenv/tap/openssl@1.0을 실행했고, 이전 버전의 OpenSSL을 참조하여 루비를 다시 설치했습니다: rvm reinstall 2.6.10 --with-openssl-dir=`/usr/local/opt/openssl@1.0`. 놀랍게도 작동했습니다! 이것이 제 첫 번째 해결책이었고, 이 아이디어를 제공해준 데 대해 감사를 표합니다.

그러나 OpenSSL 오류를 해결한 뒤 webauthn을 설치하려고 시도할 때 추가 오류가 발생했습니다: ERROR: While executing gem ... (Gem::FilePermissionError) You don`t have write permissions for the /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/gems/2.6.0 directory.. 조사를 한 결과, 이는 Mac의 미리 설치된 Ruby 버전에 직접 gem을 설치하는 것에 대한 Apple의 제한 때문이었음을 알게 되었습니다. 좀 더 깊이 파고들어 알아보니 Apple이 macOS의 10.15 버전인 Catalina에서 macOS에 내장된 각종 스크립트 언어 런타임을 공식으로 사용 중단했다는 것을 알게 되었습니다. macOS Catalina 10.15 릴리스 노트에서 관련 정보를 찾아볼 수 있습니다. 더 깊이 조사한 결과 두 번째 해결책을 찾게 되었습니다.

루비 2.6.10을 사용하는 것이 내가 겪은 문제들의 근본 원인이었음을 알게 되었습니다. 이 문제를 해결하기 위해 최신 버전의 루비를 설치하여, rvm install ruby-3.2.1을 실행하고, sudo gem install webauthn으로 webauthn을 설치했습니다. 어려움 없이 작동했고, 다음 메시지가 표시되었습니다: Done installing documentation for openssl-signature_algorithm, bindata, tpm-key_attestation, jwt, safety_net_attestation, cbor, cose, awrence, android_key_attestation, webauthn after 1 seconds. 10 gems installed.

전반적으로 상당히 흥미로운 오류였으며 해결하는 것이 즐거웠습니다.

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

## 부가 정보:

- 때때로, RVM이 이상하게 행동할 수 있습니다. 그냥 삭제하고 다시 설치하세요. rm -rf ~/.rvm를 입력한 후, curl -L https://get.rvm.io | bash -s stable을 실행하세요.
- 젬을 설치할 때 경로를 제공해야 합니다. 이렇게: sudo gem install -n /usr/local/bin GEM_NAME. 이걸 매번 하기 귀찮다면, 먼저 이렇게 하세요: echo "gem: -n/usr/local/bin" `` ~/.gemrc. 다음에는 그냥 sudo gem install GEM_NAME을 실행하세요.
