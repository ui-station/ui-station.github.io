---
title: "프론트엔드 개발자를 위한 간단하면서도 재미있는 공룡 게임 만들기 가이드"
description: ""
coverImage: "/assets/img/2024-07-01-BuildingaSimpleYetEngagingDinoGameAFront-EndDevelopersGuide_0.png"
date: 2024-07-01 16:30
ogImage: 
  url: /assets/img/2024-07-01-BuildingaSimpleYetEngagingDinoGameAFront-EndDevelopersGuide_0.png
tag: Tech
originalTitle: "Building a Simple Yet Engaging Dino Game: A Front-End Developer’s Guide"
link: "https://medium.com/@utkarshbansal01/building-a-simple-yet-engaging-dino-game-a-front-end-developers-guide-4018b62ebd30"
---


프로젝트를 작업 중에 인터넷 장애를 겪은 적이 있나요? 그러면 크롬 디노 게임을 하게 되는 경우가 많죠. 이 게임은 간단하면서 중독성이 강하며 웹 기반 게임의 훌륭한 예입니다. 이 기사에서는 핵심 프론트엔드 개발 최상의 실천법을 강조하면서 디노 게임의 자신만의 버전을 만드는 방법을 안내해 드릴 거에요.

![이미지](/assets/img/2024-07-01-BuildingaSimpleYetEngagingDinoGameAFront-EndDevelopersGuide_0.png)

# 핵심 프론트엔드 개발 실천법

## 1. 간결함

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

크롬 디노 게임은 간단하지만 재미있습니다. 미니멀한 디자인으로 빠른 로딩 시간과 쉬운 게임 플레이를 보장합니다. 우리도 이 같은 원칙을 게임에 적용할 것입니다.

## 2. 반응형 디자인

미디어 쿼리를 사용하여 모든 기기에서 게임이 잘 보이도록 합니다. 화면 크기에 따라 게임 컨테이너의 높이가 조정됩니다.

## 3. 성능 최적화

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

에셋을 최소화하고 코드를 최적화하면 성능을 크게 향상시키고 Dino 게임이 즉시로드되고 저전력 장치에서도 원할하게 작동하도록 할 수 있습니다. 이러한 목표를 달성하는 방법에 대해 알아보겠습니다.

## 4. 오프라인 기능

Chrome Dino와 같은 게임은 오프라인에서 실행되어 인터넷 연결 없이도 즐길 수 있습니다. 기본 HTML, CSS, 그리고 JavaScript를 사용하여 게임이 오프라인에서도 플레이 가능하도록 만들어 보겠습니다.

## 5. 사용자 참여

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

사용자가 계속해서 방문하는 것은 매력적인 경험을 제공할 때입니다. 우리는 게임에 게임화 요소를 추가할 거에요.

## 6. Lazy Loading

게임이 자원을 효율적으로 로드하도록 보장할 거예요. 이는 성능에 중요한 요소입니다.

# 모두 결합하기

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

여기에는 장애물, 점수 및 게임 오버 시나리오가 있는 간단하면서 매력적인 Dino 게임을 만드는 완전한 예제가 있습니다.

데모 사이트: https://dinogamedemo.netlify.app

## HTML: (index.html)

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Challenging Dino Game</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #e0e0e0;
            font-family: Arial, sans-serif;
        }
        #game-container {
            width: 100%;
            height: 50vh;
            position: relative;
            overflow: hidden;
            background-color: #f0f0f0;
            border: 2px solid #000;
        }
        #dino, .obstacle {
            position: absolute;
            bottom: 0;
        }
        #dino {
            width: 50px;
            height: 50px;
            background-color: green;
        }
        .obstacle {
            background-color: red;
        }
        #score {
            position: absolute;
            top: 10px;
            left: 10px;
        }
        @media (min-width: 600px) {
            #game-container {
                height: 70vh;
            }
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="dino"></div>
        <div id="score">Score: 0</div>
    </div>
    <script>
        let isJumping = false;
        let score = 0;
        let gameSpeed = 4;
        const dino = document.getElementById('dino');
        const scoreDisplay = document.getElementById('score');
        
        document.addEventListener('keydown', function(event) {
            if (event.code === 'Space' && !isJumping) {
                jump();
            }
        });

        function jump() {
            if (isJumping) return;
            isJumping = true;
            let upInterval = setInterval(() => {
                if (dino.style.bottom === '100px') {
                    clearInterval(upInterval);
                    let downInterval = setInterval(() => {
                        if (dino.style.bottom === '0px') {
                            clearInterval(downInterval);
                            isJumping = false;
                        }
                        dino.style.bottom = `${parseInt(dino.style.bottom) - 5}px`;
                    }, 20);
                }
                dino.style.bottom = `${parseInt(dino.style.bottom) + 5}px`;
            }, 20);
        }

        function createObstacle() {
            const obstacle = document.createElement('div');
            obstacle.classList.add('obstacle');
            obstacle.style.width = `${Math.random() * 40 + 20}px`;
            obstacle.style.height = `${Math.random() * 40 + 20}px`;
            obstacle.style.right = '0';
            document.getElementById('game-container').appendChild(obstacle);

            moveObstacle(obstacle);

            // Randomly decide to create another obstacle shortly after the current one
            if (Math.random() < 0.3) {
                setTimeout(createObstacle, Math.random() * 2000);
            }
        }

        function moveObstacle(obstacle) {
            let obstaclePosition = parseInt(obstacle.style.right);
            if (obstaclePosition >= window.innerWidth) {
                obstacle.remove();
                score++;
                scoreDisplay.innerText = 'Score: ' + score;
                gameSpeed += 0.1;  // Increase game speed slightly with each obstacle passed
                createObstacle();
            } else {
                obstacle.style.right = `${obstaclePosition + gameSpeed}px`;
                if (checkCollision(dino, obstacle)) {
                    alert('Game Over! Your score is ' + score);
                    location.reload();  // Reload the game
                    return;
                }
                requestAnimationFrame(() => moveObstacle(obstacle));
            }
        }

        function checkCollision(dino, obstacle) {
            const dinoRect = dino.getBoundingClientRect();
            const obstacleRect = obstacle.getBoundingClientRect();
            return (
                dinoRect.right > obstacleRect.left &&
                dinoRect.left < obstacleRect.right &&
                dinoRect.bottom > obstacleRect.top
            );
        }

        dino.style.bottom = '0';
        createObstacle();
    </script>
</body>
</html>
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

# 설명

- HTML 구조: 기본 HTML 구조에서는 게임 컨테이너, 공룡 및 점수 표시가 설정됩니다.
- CSS 스타일링: CSS는 게임이 반응형이 되고 모든 기기에서 잘 보이도록 보장합니다. 미디어 쿼리는 화면 크기에 따라 게임 컨테이너의 높이를 조절합니다.
- JavaScript 기능:

- 점프 메커니즘: 점프 함수는 스페이스 바를 누르면 공룡이 점프합니다.
- 장애물 생성: createObstacle 함수는 무작위 크기의 장애물을 생성합니다. 때로는 여러 장애물이 서로 가까이 나타나 더 큰 도전 요소가 됩니다.
- 장애물 이동: moveObstacle 함수는 장애물을 오른쪽에서 왼쪽으로 이동시키며, 각각 이후 약간 속도를 높입니다.
- 충돌 감지: checkCollision 함수는 공룡이 장애물과 충돌하는지 확인하고, 게임 오버 경고를 트리거합니다.

# 성능 최적화

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

게임이 저전력 장치에서도 원할하게 실행되도록 하기 위해 CSS와 JavaScript를 최소화할 수 있습니다. 다음은 방법입니다:

## CSS 최소화

- cssminifier.com과 같은 온라인 도구를 사용하여 CSS 코드를 최소화합니다.
- 최소화된 버전으로 `style` 태그 안에 있는 CSS를 대체합니다.

## JavaScript 최소화

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

- 자바스크립트 코드를 최소화하려면 javascript-minifier.com과 같은 온라인 도구를 사용하실 수 있어요.
- `script` 태그 안에 있는 자바스크립트를 최소화된 버전으로 바꿔주세요.

# Lazy Loading

성능을 더 향상시키기 위해 자산들에 대한 lazy loading을 구현할 수 있어요. 이 간단한 게임은 이미지나 외부 자산이 없지만, 기본적인 방법을 살펴보겠습니다:

```js
<script>
document.addEventListener('DOMContentLoaded', () => {
    const lazyElements = document.querySelectorAll('.lazy');
    const lazyLoad = (target) => {
        const observer = new IntersectionObserver((entries, observer) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    const element = entry.target;
                    // Load element here
                    observer.disconnect();
                }
            });
        });
        observer.observe(target);
    };

    lazyElements.forEach(lazyLoad);
});
</script>
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

# 결론

이러한 모범 사례를 따르면 반응형이고 최적화되며 재미있는 간단한 게임을 만들 수 있습니다. 이 프로젝트는 주요 프론트엔드 개발 개념을 학습하고 적용하는 좋은 방법입니다. 웹 애플리케이션이 사용자 친화적이고 성능이 우수해지도록 보장합니다. 즐거운 코딩되세요!