---
title: "당신의 Git 히스토리 정리하기"
description: ""
coverImage: "/assets/img/2024-05-17-SanitisingYourGitHistory_0.png"
date: 2024-05-17 20:17
ogImage: 
  url: /assets/img/2024-05-17-SanitisingYourGitHistory_0.png
tag: Tech
originalTitle: "Sanitising Your Git History"
link: "https://medium.com/gitconnected/sanitising-your-git-history-67558cbcc117"
---


2005년에 창설된 이후 Git은 버전 관리 시스템(VCS)의 왕으로 부상했습니다. 빠르고 간단하며 분산 속성을 갖춘 Git은 두 대 혁신적인 클라우드 VCS 제공업체인 GitHub와 GitLab의 채택과 함께 다른 VCS를 대체했습니다.

하지만 좋은 도구도 사용자가 어떻게 관리하는지에 달려 있습니다. 특히 적절한 검토와 강제 사항이 없이 여러 사용자들이 Git 저장소에 기여할 때, 대용량 파일이 많아지며 지나치게 복잡해지는 저장소가 만들어질 수 있습니다. 이는 다른 사용자들이 저장소를 복제하거나 CI 파이프라인을 실행할 때 더 큰 지연을 야기할 수 있습니다.

더 나쁜 상황으로는, 암호와 같은 민감한 파일이 실수로 커밋되어 역사 속에 깊숙이 들어가 있고, 새로운 커밋을 통해 간단히 삭제할 수 없을 수도 있습니다.

Git을 처음 사용하는 경우, 계속하기 전에 여기에서 Git 소개를 읽어보세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-17-SanitisingYourGitHistory_0.png" />

# Git 히스토리 이해하기

Git(또는 다른 VCS)의 아름다움은 버전 내역에 있습니다. 그것이 Git에서 썩은 부분을 정리해야 할 때 발생하는 어려움 역시 그런 아름다움에 속합니다. Git에 실수로 커밋한 큰 파일을 예로 들어보죠.

<img src="/assets/img/2024-05-17-SanitisingYourGitHistory_1.png" />

<div class="content-ad"></div>

- 이 큰 파일은 feature 브랜치 1에서 처음 소개되었고 사용자 1에 의해 마스터 브랜치로 병합되었습니다.
- 마스터 브랜치는 사용자 2에 의해 feature 브랜치 2로 쪼개졌습니다.
- 사용자 1은 나중에 실수를 발견하여 큰 파일을 삭제하고 다시 마스터 브랜치로 병합했습니다.
- 그러나 무심코 남아 있는 사용자 2의 feature 브랜치 2에 여전히 큰 파일이 있고, 사용자 2는 자신의 기능을 해결한 후에도 큰 파일을 마스터 브랜치로 다시 병합합니다.

실수로 커밋할 때나 마스터 브랜치와 같은 공통 브랜치로 병합할 때 복잡성을 볼 수 있습니다. 사용자 2가 feature 2를 커밋하지 않아도 사용자 1이 삭제하고 새 커밋으로 병합한 후에도 큰 파일은 여전히 히스토리에 유지됩니다.

따라서 해당 큰 파일을 제거하려면 원격 저장소의 모든 브랜치의 Git 히스토리를 완전히 지워야 합니다. 또한 제거한 후 로컬 복사본이 있는 다른 개발자들에게 알려주어서 그 파일을 삭제하고 제거 후 다시 저장소를 클론하도록 해야 합니다.

# Git 히스토리 프로파일링

<div class="content-ad"></div>

저장소의 Git 기록은 숨겨진 폴더 .git에 저장됩니다. 로컬로 저장소의 사본을 복제하고 다음 명령을 실행하여 그 크기를 빠르게 확인할 수 있습니다. 이 Git 기록이 약 416Mb임을 알 수 있습니다.

```js
cd .git
du -d 1 -h

416M ./objects
4.0K ./info
 12K ./logs
 60K ./hooks
8.0K ./refs
416M .
```

Git을 프로파일링하는 훌륭한 도구는 git-sizer라는 도구를 사용하는 것입니다. MacOS에서는 brew를 사용하여 설치할 수 있습니다. 다른 운영 체제를 사용하는 경우, GitHub 릴리스 페이지에서 설치 파일을 다운로드할 수 있습니다. (아래 링크를 참조해주세요).

```js
brew install git-sizer
```

<div class="content-ad"></div>

저장소로 이동해서 다음 명령어를 실행해주세요.

```js
cd your/git/repository
git-sizer --verbose
```

이 명령어를 실행하면 Git 기록의 명확한 프로필을 확인할 수 있어요. 저장소의 크기 및 최적화해야 할 관심 수준을 확인할 수 있습니다. 이 경우에는 기록 중 대부분의 저장소가 블롭으로 저장되어 있는 것을 확인할 수 있어요. 블롭은 Git-LFS (Large File Storage)나 다른 저장 영역(예: AWS S3)에 저장해야 합니다.

```js
블롭 처리 중: 1368개
트리 처리 중: 1957개
커밋 처리 중: 518개
트리에 해당하는 커밋: 518개
주석이 달린 태그 처리 중: 1개
참조 처리 중: 24개
| 이름                         | 값        | 관심 수준                         |
| ---------------------------- | --------- | ------------------------------ |
| 전체 저장소 크기             |           |                                |
| * 커밋                      |           |                                |
|   * 총 개수                  |   518     |                                |
|   * 총 크기                 |   158 KiB |                                |
| * 트리                      |           |                                |
|   * 총 개수                  |  1.96 k   |                                |
|   * 총 크기                 |   686 KiB |                                |
|   * 총 트리 항목 수         |  17.7 k   |                                |
| * 블롭                      |           |                                |
|   * 총 개수                  |  1.37 k   |                                |
|   * 총 크기                 |   453 MiB |                                |
| * 주석이 달린 태그           |           |                                |
|   * 개수                    |     1     |                                |
| * 참조                      |           |                                |
|   * 개수                    |    24     |                                |
|     * 브랜치                |     1     |                                |
|     * 태그                  |    15     |                                |
|     * 원격 추적 참조         |     8     |                                |
|                              |           |                                |
| 가장 큰 객체들               |           |                                |
| * 커밋                      |           |                                |
|   * 최대 크기             [1] |   771 B   |                                |
|   * 최대 부모 수          [2] |     2     |                                |
| * 트리                      |           |                                |
|   * 최대 엔트리 수        [3] |    23     |                                |
| * 블롭                      |           |                                |
|   * 최대 크기             [4] |   430 MiB | !!!!!!!!!!!!!!!!!!!!!!!!!!!!!! |
|                              |           |                                |
| 기록 구조                    |           |                                |
| * 최대 기록 깊이            |   346     |                                |
| * 최대 태그 깊이        [5] |     1     |                                |
|                              |           |                                |
| 가장 큰 체크아웃들           |           |                                |
| * 디렉터리 수          [6] |   108     |                                |
| * 최대 경로 깊이       [7] |     9     |                                |
| * 최대 경로 길이       [7] |   134 B   | *                              |
| * 파일 수              [3] |   283     |                                |
| * 파일 총 크기        [8] |   442 MiB |                                |
| * 심볼릭 링크 수       [9] |     1     |                                |
| * 서브모듈 수         [10] |     2     |                                |
```

<div class="content-ad"></div>

# Git 히스토리 삭제하기

프로파일링 후 지우고 싶은 내용에 대한 명확한 아이디어가 생겼으니, Git 히스토리에서 해당 내용을 삭제해 보겠습니다. 이를 위해 git-filter-repo라는 Python 도구를 설치해야 합니다 (아래 링크를 참조하세요). 나중에 필요할 때를 대비해 참조용 원격 origin을 목록에 표시해 두세요.

```js
# 설치
pip install git-filter-repo

# 원격 origin 목록 표시
git remote -v
>> origin git@gitlab.com:jake/test.git (fetch)
>> origin git@gitlab.com:jake/test.git (push)
```

여기에 파일이나 텍스트를 삭제하는 몇 가지 일반적인 예시 명령어를 포함했습니다.

<div class="content-ad"></div>

- 특정한 절대 파일 이름을 가진 파일을 삭제하는 방법
- (*)를 사용하여 특정 유형의 모든 파일을 삭제하는 방법
- 실수로 패스워드를 커밋한 경우와 같이 파일이나 스크립트에서 특정 텍스트를 대체하는 방법.

```js
# 파일 삭제
git filter-repo --invert-paths --path 'myfilename.jpg' --force

# 글로브(*)를 사용하여 모든 파일 삭제
git filter-repo --invert-paths --path-glob '*.jpg' --force
git filter-repo --invert-paths --path-glob '*.jpg' --path-glob '*.png' --force

# "mypassword"를 "REPLACED"로 대체하는 방법
git filter-repo --replace-text <(echo "mypassword==>REPLACED") --force
```

이전에 삭제한 것이 더 이상 남아 있지 않은지 확인하여 버전이나 브랜치를 검사하여 정상 작동하는지 확인할 수 있습니다.

완료되면 정리된 복사본을 원격 저장소에 푸시할 수 있습니다. 이 도구는 안전상의 이유로 git 원격 URL을 자동으로 제거합니다. 따라서 다시 추가해야 합니다. 그런 다음 기존 브랜치를 원격에 강제로 푸시할 수 있습니다. 다른 브랜치가 있는 경우 체크아웃하여 그것들도 푸시해야 합니다.

<div class="content-ad"></div>

```js
# git filter-repo를 실행한 후 원격 저장소를 다시 추가하세요
git remote add origin git@gitlab.com:jake/test.git

# 원격 저장소로 푸시하세요
git push --set-upstream origin main --force

# 다른 브랜치가 있으면 변경사항을 푸시하세요
git checkout <branch>
git push --set-upstream origin <branch> --force
```

# 주의사항

각 Git 버전은 Git 해시라는 고유한 ID를 가지고 있습니다. git-filter-repo나 기타 도구를 사용하여 Git 히스토리의 커밋을 변경하면 해시가 변경됩니다.

Git 해시는 병합/풀 요청, Git 태그 또는 릴리스와 같은 VCS 플랫폼에서 참조로 사용됩니다. 이들은 작동하지 않고 특히 Git 히스토리에서 깊숙이 포함된 것을 제거하고 싶은 경우 문제가 될 수 있습니다. 중요하다면 저장소의 복사본을 만드는 것이 좋습니다.


<div class="content-ad"></div>

# 요약

git-sizer와 git-filter-repo는 Git 히스토리를 프로파일링하고 정리하는 데 사용되는 인기 있는 신뢰할 수 있는 오픈 소스 도구입니다. 이러한 도구를 사용하면 필요에 따라 간편하게 리포지토리의 상태를 확인하고 정리하여 유지할 수 있습니다.

# 참고