---
title: "뷰로 퀸 게임 재현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-27-RecreatingQueensGameinVue_0.png"
date: 2024-06-27 19:27
ogImage: 
  url: /assets/img/2024-06-27-RecreatingQueensGameinVue_0.png
tag: Tech
originalTitle: "Recreating Queens Game in Vue"
link: "https://medium.com/@fadamakis/recreating-queens-game-in-vue-d7e3b3013ccb"
---


<img src="/assets/img/2024-06-27-RecreatingQueensGameinVue_0.png" />

최근에 매일 도전하다 보니 Queens라는 게임에 중독되었어요. 링크드인에서 소개된 게임인데 Vue를 사용하여 이 게임의 클론을 구현하는 것은 정말 즐거운 도전이었고 게임을 더 잘 이해하는 데 도움이 되었어요.

# 게임

Queens는 Minesweeper와 Sudoku 요소를 결합한 퍼즐 게임이에요. 8x8 그리드에서 플레이되며, 규칙에 따라 보드 위에 여덟 개의 퀸을 위치시키는 것이 목표예요:

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

- 한 행에 한 퀸
- 한 열에 한 퀸
- 한 색 영역에 한 퀸
- 대각선으로 연속적으로 위치한 셀에 두 개의 퀸을 놓을 수 없음

이 모든 조건을 동시에 만족시키는 것이 과제입니다.

![이미지](/assets/img/2024-06-27-RecreatingQueensGameinVue_1.png)

## 초기 상태 모델링 - 첫 번째 시도

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

제 첫 번째 접근 방식은 초기 보드를 무작위로 생성하는 것이었는데, 이렇게 하면 게임이 너무 쉽다는 것을 깨달았어요. 진정한 도전은 답이 하나뿐인 보드에서 온다고 생각해요. LinkedIn 및 다른 솔루션을 역공학한 후에 그들이 매번 미리 정의된 초기 보드를 사용한다는 것을 깨달았어요.

## 초기 상태 모델링 - 두 번째 시도

더 적합한 방법은 각 셀의 내용이 색상인 2차원 배열을 사용하는 것이에요.

먼저, 각 숫자에 색상을 연결하는 맵을 갖게 될 거에요.

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
cellColors의 속성을 다음과 같이 변경하고

export const cellColors = {
    1: "#007B6C",
    2: "#D18B00",
    3: "#C75D00",
    4: "#0044CC",
    5: "#CC0000",
    6: "#CCCC00",
    7: "#008B8B",
    8: "#8B008B",
  };

그런 다음 초기 상태를 나타내는 2 차원 배열을 생성합니다.

const sectionGrid = [
  [1, 1, 2, 2, 2, 3, 3, 3],
  [1, 1, 2, 2, 2, 3, 3, 3],
  [4, 1, 2, 2, 2, 3, 3, 3],
  [4, 1, 5, 5, 5, 5, 3, 3],
  [4, 1, 5, 5, 5, 5, 6, 6],
  [4, 5, 5, 7, 7, 6, 6, 6],
  [4, 8, 7, 7, 7, 6, 6, 6],
  [8, 8, 8, 7, 7, 6, 6, 6]
];

그리고 나서 다음 이미지를 참고하세요. <img src="/assets/img/2024-06-27-RecreatingQueensGameinVue_2.png" />
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

우리는 이제 초기 상태를 나타내는 것을 가지고 게임 보드를 시작할 수 있어요.

## 게임 보드

보드를 만들기 전에 각 셀에 대한 작은 구성 요소를 만들어 봅시다.

```js
<!-- GridCell.vue -->
<script setup>
defineProps(["content", "color"]);
</script>

<template>
  <div class="cell" :style="{ backgroundColor: color }">
    <img v-if="content === 'queen'" src="@/assets/crown.png" class="queen" />
    <span v-if="content === 'marked'">×</span>
  </div>
</template>

<style scoped>
.cell {
  font-size: 24px;
  border: 1px solid #000;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  user-select: none;
}

.queen {
  width: 24px;
  height: 24px;
}
</style>
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

이 프레젠테이션 컴포넌트는 두 가지 매개변수를 받습니다. 셀 내용은 퀸, X 표시 또는 빈 경우에는 null이 될 수 있습니다. 또한 셀의 색상을 받습니다.

이제 게임 보드를 만들려면 GridCell 컴포넌트 주위에 두 개의 중첩된 루프가 필요합니다. 게임이 종료되고 WinMessage 컴포넌트가 표시될 때 그리고 보드를 지우는 버튼이 있을 때입니다.

대부분의 복잡성은 나중에 처리할 createGame 컴포저블 내부에 추상화되어 있습니다.

이 보드는 정렬을 위해 CSS 그리드를 사용합니다.

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
<!-- GameBoard.vue -->
<script setup>
import GridCell from "@/features/game/components/GridCell.vue";
import { createGame } from "@/features/game/composables/createGame";
import WinMessage from "@/features/game/components/WinMessage.vue";
import AppButton from "@/components/AppButton.vue";
import { cellColors } from "@/features/game/data/cellColors.js";

const {
  boardState,
  gameWon,
  isValidQueen,
  toggleCell,
  clearBoard,
} = createGame();
</script>

<template>
  <div class="game-board">
    <template v-for="(row, rowIndex) in boardState">
      <GridCell
        v-for="(cell, cellIndex) in row"
        :key="`${rowIndex}-${cellIndex}`"
        :content="cell.content"
        :color="cellColors[cell.section]"
        :invalid="isValidQueen(rowIndex, cellIndex)"
        @click="toggleCell(rowIndex, cellIndex)"
      />
    </template>
  </div>
  <WinMessage v-if="gameWon" />
  <AppButton @click="clearBoard">Clear Board</AppButton>
</template>

<style scoped>
.game-board {
  display: grid;
  justify-content: center;
  grid-template-columns: repeat(8, 42px);
  grid-template-rows: repeat(8, 42px);
  border: 1px solid #000;
}
</style>
``` 

다음으로 createGame 컴포저블을 다루어 봅시다.

먼저, 각 셀의 상태를 저장할 방법이 필요합니다. 이를 위해 board와 동일한 구조인 boardState라는 새 변수를 만들 것입니다. 또한 가능한 값으로 [Null, marked, or queen]을 갖는 content 속성을 추가할 것입니다.

또한, 보드 상의 모든 퀸의 좌표를 추적할 수 있는 배열이 필요합니다. 이는 퀸이 유효한지 확인하기 위해 모든 셀을 검색할 필요 없이 작은 최적화입니다.


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

toggleCell 함수는 별도의 배열에서 퀸을 추적하는 기능을 추가하여 셀의 세 가지 가능한 상태를 회전합니다. 각 변경 후에 보드가 다시 유효성을 검사합니다. 현재 유효성 검사 코드는 생략되었습니다.

clearBoard 함수는 boardState와 퀸을 재설정합니다.

isValidQueen 함수는 셀의 좌표를 인수로 받아 유효한 퀸이 있는지 여부를 반환합니다.

마지막으로 게임이 종료되었는지 확인하려면 해당 배열 내의 유효한 퀸 수를 세면 됩니다. 이것은 계산된 값이어야 합니다.

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
// composables/createGame.js
import { ref, computed } from 'vue'
import { sectionGrid } from '@/features/game/data/sectionGrid'

function createBoard() {
  return sectionGrid.map((row) =>
    row.map((section) => ({
      content: null,
      section
    }))
  )
}

export function createGame() {
  const boardState = ref(createBoard())
  const queens = ref([])

  function toggleCell(rowIndex, cellIndex) {
    const cell = boardState.value[rowIndex][cellIndex];
  
    if (!cell.content) {
      cell.content = 'marked';
    } else if (cell.content === 'marked') {
      cell.content = 'queen';
      queens.value.push({ row: rowIndex, col: cellIndex, valid: true });
    } else {
      cell.content = null;
      queens.value = queens.value.filter(
        (queen) => queen.row !== rowIndex || queen.col !== cellIndex
      );
    }
  
    validateBoard();
  }

  function validateBoard() {
    // TODO
  }

  function clearBoard() {
    boardState.value = boardState.value.map((row) =>
      row.map((cell) => ({ ...cell, content: null }))
    )
    queens.value = []
  }

  function isValidQueen(rowIndex, cellIndex) {
    return queens.value.some(
      (queen) => queen.row === rowIndex && queen.col === cellIndex && !queen.valid
    )
  }

  const gameWon = computed(() => {
    if (queens.value.length !== sectionGrid.length) {
      return false
    }

    return queens.value.every((queen) => queen.valid)
  })

  return {
    boardState,
    toggleCell,
    queens,
    isValidQueen,
    clearBoard,
    gameWon
  }
}
```

## Validations

이제 애플리케이션의 가장 복잡한 측면인 유효성 검사를 구현해 보겠습니다. 각 변경 사항 후에는 승리 조건이 위배되었는지 확인하고 해당 퀸을 무효로 표시해야 합니다. validateRow, validateColumn, validateSection 및 checkDiagonalConflicts와 같이 네 가지 다른 함수가 있습니다. 각 함수는 게임 규제 중 하나를 확인하는 역할을 합니다.

예를 들어 validateRow 함수는 같은 행에 두 개의 퀸이 존재하지 않도록하고 그렇다면 두 퀸을 모두 무효로 표시합니다.

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
// composables/createGame.js
// ...

function validateBoard() {
  resetValidations()

  for (const queen of queens.value) {
    const { row, col } = queen
    const cell = boardState.value[row][col]
    const rowValid = validateRow(row)
    const columnValid = validateColumn(col)
    const sectionValid = validateSection(cell.section)
    const diagonalValid = checkDiagonalConflicts(queen)

    queen.valid = rowValid && columnValid && sectionValid && diagonalValid
  }
}

function resetValidations() {
  queens.value.forEach((queen) => (queen.valid = true))
}

function validateRow(rowIndex) {
  const queensInRow = queens.value.filter((queen) => queen.row === rowIndex)

  if (queensInRow.length > 1) {
    queensInRow.forEach((queen) => (queen.valid = false))
    return false
  }
  return true
}

function validateColumn(columnIndex) {
  // TODO
}

function validateSection(section) {
  // TODO
}
function checkDiagonalConflicts(queen) {
  // TODO
}
```

## 타이머

애플리케이션의 또 다른 기능은 각 게임판의 완료까지 걸린 시간을 추적하는 것입니다. 경과된 시간을 보여주는 작은 프레젠테이션 컴포넌트를 만들 것입니다.

```js
<!-- AppTimer.vue -->
<template>
  <div class="timer">⏱ 시간: { formattedTime }</div>
</template>

<script setup>
import { useTimer } from "../composables/useTimer";

const { formattedTime } = useTimer();
</script>

<style scoped>
.timer {
  font-size: 13px;
  margin: 10px 0;
}
</style>
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

한 번 더 composable이 사용되어 상태를 관리합니다. 타이머를 시작, 중지 및 재설정할 수 있는 기능을 노출합니다.

```js
import { ref, computed } from 'vue'

const time = ref(0)

export function useTimer() {
  let timerInterval = null

  const startTimer = () => {
    if (timerInterval) return
    timerInterval = setInterval(() => {
      time.value++
    }, 1000)
  }

  const stopTimer = () => {
    clearInterval(timerInterval)
    timerInterval = null
  }

  const resetTimer = () => {
    time.value = 0
  }

  const formattedTime = computed(() => {
    const minutes = Math.floor(time.value / 60)
    const seconds = time.value % 60
    return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
  })

  return {
    time,
    formattedTime,
    startTimer,
    stopTimer,
    resetTimer
  }
}
```

게임 보드 컴포넌트를 이 타이머를 사용하도록 업데이트해야 합니다. 최종 구현은 다음과 같습니다.

컴포넌트가 마운트될 때 타이머를 시작하고, 게임이 이겼을 때 중지합니다.

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


<script setup>에서 <script>태그를 Markdown 형식으로 바꿔보세요.

이 최종 결과물은 퀸의 게임의 기능적인 클론입니다. 여기에서 확인할 수 있어요.

<img src="/assets/img/2024-06-27-RecreatingQueensGameinVue_3.png" />

## 결론


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

여기 있습니다! Vue와 같은 반응형 프런트엔드 프레임워크는 대부분의 중요한 작업을 처리하여 상태를 동기화하는 데 도움이 되지만 여전히 대부분의 로직을 직접 구현해야 합니다. 이 기사에서 가치를 찾았고 Vue를 사용하여 이와 같은 게임을 만드는 방법에 대해 더 잘 이해할 수 있기를 바랍니다.

다음 단계는 여러 수준, 힌트 및 일일 도전 과제를 구현하는 것입니다. 이를 향한 다음 기사에서 그것을 할 것입니다. 반드시 구독하세요!

게임을 플레이하거나 GitHub에서 코드베이스를 살펴볼 수 있습니다.

![게임 이미지](/assets/img/2024-06-27-RecreatingQueensGameinVue_4.png)