---
title: "M3 Pro Mac에서 RVM을 사용하여 Ruby 설치하기"
description: ""
coverImage: "/assets/img/2024-06-19-InstallRubywithRVMonanM3ProMac_0.png"
date: 2024-06-19 10:27
ogImage: 
  url: /assets/img/2024-06-19-InstallRubywithRVMonanM3ProMac_0.png
tag: Tech
originalTitle: "Install Ruby with RVM on an M3 Pro Mac"
link: "https://medium.com/@nelliemckesson/install-ruby-with-rvm-on-an-m3-pro-mac-784718bdb72a"
---


![Ruby 설치에 문제가있는 이미지](/assets/img/2024-06-19-InstallRubywithRVMonanM3ProMac_0.png)

StackOverflow 및 GitHub 이슈를 한 시간 정도 읽어본 후에 M3 Pro 맥북에서 RVM을 설정하는 방법에 대한 빠르고 간단한 안내서를 제공해드릴게요. 물론, 모두의 시스템이 다를 수 있고 이 문제를 해결할 수 있는 보장은 없지만 도움이 되길 바랍니다.

한 가지 주의할 점은, 초기 RVM 설치(\curl -sSL https://get.rvm.io | bash -s stable — ruby) 중에 Ruby를 실제로 설치하는 단계에 도달했을 때 실패가 발생했습니다. Ruby 설치에 실패했음에도 불구하고 RVM이 이 과정 중에 설치되었습니다. "configuring...." 단계 이후 비슷한 실패가 발생하면 터미널을 재시작하고 rvm list를 실행하여 RVM 설치가 작동하는지 확인해보세요.

이제 Ruby로 넘어가겠습니다! 다음 해결책의 대부분은 GitHub 사용자 mayur-kambariya의 훌륭한 조언에서 왔지만, openssl 설치에 약간의 수정이 필요했습니다. 제가 한 작업은 다음과 같습니다:

<div class="content-ad"></div>

- 터미널을 종료하세요. 그런 다음 Finder에서 Application → Utilities로 이동하세요. 터미널을 마우스 오른쪽 버튼으로 클릭하고 정보 가져오기를 선택한 다음 "Rosetta 사용하여 열기" 상자를 체크하세요.

![image](/assets/img/2024-06-19-InstallRubywithRVMonanM3ProMac_1.png)

2. Terminal을 열고 Homebrew를 제거하세요:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"
rm -rf /opt/homebrew/*
sudo rm -rf /opt/homebrew
```

<div class="content-ad"></div>

3. Homebrew 다시 설치하기:
```markdown
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

4. 터미널을 종료하고 다시 시작하세요.

5. Homebrew가 제대로 동작하는지 확인하세요: `brew doctor`

6. openssl을 *낮은 버전으로 다시 설치하기*: `brew install openssl@1.1` . 다음 단계를 위해 openssl이 설치된 위치를 알아야 할 수도 있습니다. openssl이 설치된 위치는 `brew info openssl@1.1` 명령을 실행하여 확인할 수 있습니다.

<div class="content-ad"></div>

7. 혹시나를 대비해 터미널을 종료하고 다시 시작했어요.

8. 루비 설치하기: rvm install 2.7.7 --with-openssl-dir=/usr/local/opt/openssl@1.1 (또는 필요한 루비 버전).