---
title: "아마존 데이터 분석가를 위한 SQL 인터뷰 질문"
description: ""
coverImage: "/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_0.png"
date: 2024-05-23 16:00
ogImage: 
  url: /assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_0.png
tag: Tech
originalTitle: "Amazon SQL Interview Question for Data Analyst"
link: "https://medium.com/@mail2asimmanna/amazon-sql-interview-question-for-data-analyst-c99b574c42b3"
---


<img src="/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_0.png" />

아마존에서 데이터 분석가 직군 면접 때 물어본 문제입니다. 병원에는 직원들이 여러 번 들어오고 나갈 수 있습니다.

이제 병원 안에 있는 직원을 찾아내야 합니다.

<img src="/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_1.png" />

<div class="content-ad"></div>

요기 데이터 있어요. 이제 병원 안에 있는 직원의 emp_id를 찾아야 해요.

이 질문은 두 가지 방법으로 해결할 수 있어요.

방법 1

여기서 우리는 각 직원의 최신 출근 시간과 최신 퇴근 시간을 찾을 거예요. 직원은 최신 출근 시간이 최신 퇴근 시간보다 늦거나 최신 퇴근 시간이 알려지지 않은 경우에 병원에 있을 거예요.

<div class="content-ad"></div>

아래 결과를 통해 emp_id 2, 3, 4가 조건을 만족시킨다는 것을 확인할 수 있습니다. 따라서 이들 직원들은 병원 안에 있습니다.

최종 쿼리는 다음과 같습니다:

<img src="/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_3.png" />

<div class="content-ad"></div>

## 방법 2

여기서는 각 직원의 최신 활동 시간을 찾은 다음, 해당 시간에 직원의 활동이 무엇이었는지 알아낼 것입니다. 그 후에 해당 직원을 필터링할 것입니다.

![image](/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_4.png)

이제 해당 시간에 직원의 활동을 찾아보겠습니다.

<div class="content-ad"></div>

```markdown
<img src="/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_5.png" />

지금은 최신 활동이 "in"인 것을 필터링할 것입니다.

<img src="/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_6.png" />

방법 3: 여기서 우리는 각 emp_id의 행 번호를 내림차순으로 시간 순서대로 생성할 것입니다. 그런 다음 CTE를 생성한 다음, 행 번호가 =1이고 활동이 'in'인 emp_id를 추출할 것입니다.
```

<div class="content-ad"></div>

```js
SELECT *,ROW_NUMBER() OVER(PARTITION BY emp_id ORDER BY time DESC) AS rnk
FROM hospital) 
SELECT *
FROM x
WHERE rnk=1 AND action='in';
```

![Image](/assets/img/2024-05-23-AmazonSQLInterviewQuestionforDataAnalyst_7.png)

Please clap if you find the solution helpful.

Let's connect on LinkedIn! 🤝
```

<div class="content-ad"></div>

포트폴리오를 확인해보세요.