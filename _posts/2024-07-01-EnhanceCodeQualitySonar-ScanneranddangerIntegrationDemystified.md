---
title: "코드 품질 향상 Sonar-Scanner와 danger 통합 방법 "
description: ""
coverImage: "/assets/img/2024-07-01-EnhanceCodeQualitySonar-ScanneranddangerIntegrationDemystified_0.png"
date: 2024-07-01 16:21
ogImage: 
  url: /assets/img/2024-07-01-EnhanceCodeQualitySonar-ScanneranddangerIntegrationDemystified_0.png
tag: Tech
originalTitle: "Enhance Code Quality: Sonar-Scanner and danger Integration Demystified 🚀"
link: "https://medium.com/@robert.maiersilldorff/enhance-code-quality-sonar-scanner-and-danger-integration-demystified-3aad4c4442d2"
---


이전 게시물 중 하나에서는 npm 피어 종속성 충돌을 해결하는 방법에 대해 이야기했습니다 [1]. 이번에는 코드 문제에 대한 자세한 보고서를 생성하는 방법에 대해 이야기하려고 합니다. 특히 SonarQube를 통합하고 자동화된 코드 리뷰를 위해 danger를 활용하는 방법에 대해 논의하겠습니다.

## 1. Sonar-Scanner

SonarQube Scanner (sonar-scanner)는 여러 프로그래밍 언어에 걸쳐 코드 품질 및 보안을 분석하는 도구입니다. Jenkins와 Maven과 같은 CI/CD 도구와 원활하게 통합되어 코드 문제에 대한 자세한 보고서를 제공하고 유지 관리성을 향상시키며 최상의 코딩 관행을 준수하는 것을 보장합니다.

다음은 sonar-scanner를 사용하는 예시입니다.

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
sonar-scanner
      -Dsonar.projectKey=$sonarQubeProjectKey
      -Dsonar.login=$sonarQubeToken
      -Dsonar.sourceEncoding=UTF-8
      -Dsonar.sources=$npmSonarQubeSources
      -Dsonar.tests=$npmSonarQubeTests
      -Dsonar.exclusions=$npmSonarQubeExclusions
      -Dsonar.test.inclusions=$npmSonarQubeTestInclusions
      -Dsonar.typescript.lcov.reportPaths=$npmSonarQubeLcovReportPath
      -Dcom.itestra.cloneview.enabled=false
```

![Enhance Code Quality](/assets/img/2024-07-01-EnhanceCodeQualitySonar-ScanneranddangerIntegrationDemystified_0.png)

## 1.1 Nx 프로젝트 설정하기

Nx를 사용할 때는 다음 값들로 플레이스홀더를 채울 수 있습니다.

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
// configuration with nx
npmSonarQubeSources = 'apps,libs'
npmSonarQubeTests = 'apps,libs'
npmSonarQubeExclusions = '**/node_modules/**,**/*-api/**,**/test-setup.ts'
npmSonarQubeTestInclusions = '**/*.spec.ts'
npmSonarQubeLcovReportPath = 'coverage/lcov.info'
```

여러 라이브러리 및/또는 애플리케이션이 구성되어 있으므로 커버리지 보고서를 단일 파일로 병합해야 합니다. 이 집계된 결과는 SonarQube로 전송할 수 있습니다.

다음 코드[2]를 사용하여 이를 달성할 수 있습니다. 그러나 최신 glob 버전과 호환되기 위해 일부 수정이 필요합니다. 다음 코드 조각은 이를 어떻게 수정해야 하는지 보여줍니다.

```js
const { glob } = require('glob');
const fs = require('fs');
const path = require('path');

const getLcovFiles = function (src) {
    return new Promise((resolve) => {
        glob(`${src}/**/lcov.info`)
            .then((result) => resolve(result))
            .catch(() => resolve([]));
    });
};

(async function () {
    const files = await getLcovFiles('coverage');
    const mergedReport = files.reduce((mergedReport, currFile) => (mergedReport += fs.readFileSync(currFile)), '');

    await fs.writeFile(path.resolve('./coverage/lcov.info'), mergedReport, (err) => {
        if (err) throw err;
        console.log('파일이 저장되었습니다!');
    });
})();
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

위의 스크립트를 사용하려면 해야 할 일은 glob를 의존성으로 추가하는 것 뿐입니다.

```js
npm install glob --save
```

## 1.2. 기타 유형의 프로젝트 설정

Nx 없이도 다음 값을 사용하여 자리 표시자를 채울 수 있습니다.

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
// nxnpmSonarQubeSources = 'src'이라는 설정 없이
npmSonarQubeTests = 'src'
npmSonarQubeExclusions = '**/node_modules/**,**/*-api/**'
npmSonarQubeTestInclusions = '**/*.spec.ts'
npmSonarQubeLcovReportPath = 'coverage/lcov.info'
```

## 1.3 Jest 설정

jest를 사용할 때 (karma 대신) jest와 함께 nx를 사용하면, coverageReports 옵션을 lcov로 설정하여 쉽게 커버리지 보고서를 생성할 수 있습니다.

```js
// package.json 내의 스크립트

"coverage:merge": "node merge-coverage.js",
"test": "nx run-many --all --target=test --code-coverage --coverageReporters=lcov --parallel=2 --runInBand && npm run coverage:merge",
"test:ci": "npm run test"
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

아래는 사용할 수 있는 jest.config의 예시입니다.

```js
export default {
    displayName: 'common',
    preset: '../../../jest.preset.js',
    setupFilesAfterEnv: ['<rootDir>/src/test-setup.ts'],
    coverageDirectory: '../../../coverage/libs/common',
    transform: {
        '^.+\\.(ts|mjs|js|html)$': [
            'jest-preset-angular',
            {
                tsconfig: '<rootDir>/tsconfig.spec.json',
                stringifyContentPathRegex: '\\.(html|svg)$',
            },
        ],
    },
    transformIgnorePatterns: ['node_modules/(?!.*\\.mjs$|@datorama/akita)'],
    snapshotSerializers: [
        'jest-preset-angular/build/serializers/no-ng-attributes',
        'jest-preset-angular/build/serializers/ng-snapshot',
        'jest-preset-angular/build/serializers/html-comment',
    ],
    moduleNameMapper: {
        '^lodash-es$': 'lodash',
    },
};
```

## 1.4 Karma 설정

그러나 jest 대신 karma를 사용할 경우, 각 karma.conf 파일에 lcovonly를 리포터로 포함해야 합니다.

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
module.exports = () => {
    return {
        basePath: '',
        frameworks: ['jasmine', '@angular-devkit/build-angular'],
        plugins: [
            require('karma-jasmine'),
            require('karma-chrome-launcher'),
            require('karma-jasmine-html-reporter'),
            require('karma-coverage-istanbul-reporter'),
            require('@angular-devkit/build-angular/plugins/karma'),
        ],
        client: {
            clearContext: false, // 브라우저에서 Jasmine Spec Runner 출력을 보이게 유지
        },
        jasmineHtmlReporter: {
            suppressAll: true, // 중복된 추적을 제거
        },
        coverageIstanbulReporter: {
            dir: require('path').join(__dirname, '../../../coverage/common″'),
            reports: ['html', 'lcovonly'],
            fixWebpackSourcePaths: true,
        },
        reporters: ['progress', 'kjhtml', 'coverage-istanbul'],
        ...
}
```

그리고 코드 커버리지 옵션이 package.json에 설정되어야 합니다.

```js
"test": "ng test",
"test:ci": "npm run test -- --watch false --code-coverage=true",
```

## 2. Danger


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

TypeScript 프로젝트에서는 danger 의존성을 사용하여 eslint 규칙을 확인하여 코드 리뷰를 자동화합니다. 이는 pull request 에서 linting 문제를 자동으로 식별하여 코드 품질을 유지하는 데 도움이 됩니다. danger 를 통합하려면 Dangerfile 을 설정하여 규칙과 검사를 정의하고 일관된 코딩 표준을 유지하며 리뷰 프로세스를 간소화해야 합니다.

다음은 Dangerfile 을 구성할 수 있는 예시입니다.

```js
import {danger, fail, schedule, warn} from 'danger';
import {istanbulCoverage} from 'danger-plugin-istanbul-coverage';
import {ESLint} from 'eslint';

// 코드 커버리지 확인
schedule(istanbulCoverage({
    entrySortMethod: 'least-coverage',
    numberOfEntries: 30,
    coveragePath: {path: './coverage/lcov.info', type: 'lcov'},
    reportFileSet: 'createdOrModified',
    reportMode: 'warn',
    threshold: {
        statements: 80,
        branches: 80,
        functions: 80,
        lines: 80
    }
}));

const enum EslintSeverity {
    WARNING = 1,
    ERROR = 2
}

// ESLint 규칙 검사
schedule(async () => {
    try {
        const filesToLint = danger.git.created_files.concat(danger.git.modified_files);
        const eslint = new ESLint({});

        const results = await eslint.lintFiles(filesToLint);

        results.forEach(({filePath, messages}) => {
            messages.forEach(message => {
                if (message.fatal) {
                    warn(`Fatal error linting ${filePath} with eslint.`);
                    return;
                }

                // 무시된 파일 건너뛰기
                if (message.severity === EslintSeverity.WARNING && (message.message || '').startsWith('File ignored')) {
                    return;
                }

                const fn = reporterFor(message.severity);

                fn(`${filePath} line ${message.line} – ${message.message} (${message.ruleId})`);
            });
        });
    } catch (error) {
        console.error(error);
    }
});

function reporterFor(severity: number): (message: string) => void {
    switch (severity) {
        case EslintSeverity.WARNING:
            return warn;
        case EslintSeverity.ERROR:
            return fail;
        default:
            return () => { /* 아무것도 수행하지 않음 */
            };
    }
}
```

danger 및 코드 커버리지 도구를 사용하려면 danger 및 danger-plugin-istanbul-coverage가 모두 의존성으로 추가되어야 합니다.

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
// 일부 구버전
"danger": "10.5.3",
"danger-plugin-istanbul-coverage": "^1.6.2",
```

그리고 다음 스크립트를 package.json에 추가할 수 있습니다.

```js
"danger:ci": "danger ci",
"danger:local": "danger local"
```

Gitlab과 함께 danger를 통합하려면 다음 스크립트를 CI/CD 파이프라인에 사용할 수 있습니다.

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

```bash
export DANGER_GITLAB_HOST=${dangerGitlabHost}
export DANGER_GITLAB_API_BASE_URL=${dangerGitlabApiBaseUrl}
                        
git branch temp_${BRANCH_NAME}
git checkout -b ${dangerBaseBranch}
git checkout temp_${BRANCH_NAME}
npm run danger:local -- --base ${dangerBaseBranch}

export NODE_TLS_REJECT_UNAUTHORIZED=0
npm run danger:ci
export NODE_TLS_REJECT_UNAUTHORIZED=1
```

## 마무리

저는 SonarScanner를 Nx 및 다양한 유형의 프로젝트에 구성하는 방법과 Jest와 Karma를 사용하여 테스팅하는 방법의 차이점을 강조했습니다. 각 도구의 구체적인 구성 및 모범 사례를 예를 통해 설명하여 개발자들이 테스트 및 코드 품질 전략에 대해 정보를 얻고 결정하는 데 도움이 되기를 바라며 이 비교를 제공했습니다.

게다가, danger의 사용법을 보여주고 이를 CI/CD 파이프라인에 통합하는 방법을 설명했습니다. danger는 eslint 규칙을 강제로 적용하여 코드 리뷰를 자동화하고 일관된 코딩 표준을 유지하며 리뷰 프로세스를 간소화하기 위해 PR에서 이슈를 플래그 처리함으로써 개발자들을 도와줍니다.

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

# 링크

[1] <https://medium.com/@robert.maiersilldorff/resolving-npm-peer-dependency-conflicts-70d67f4ca7dc>  

[2] <https://yonatankra.com/how-to-create-a-workspace-coverage-report-in-nrwl-nx-monorepo/>  