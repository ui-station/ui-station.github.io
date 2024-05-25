---
title: "Rust로 코딩하는 커맨드 라인 Connect 4 게임 배우기"
description: ""
coverImage: "/assets/img/2024-05-20-LearnRustbycodingacommandlineConnect4game_0.png"
date: 2024-05-20 16:00
ogImage: 
  url: /assets/img/2024-05-20-LearnRustbycodingacommandlineConnect4game_0.png
tag: Tech
originalTitle: "Learn Rust by coding a command line Connect 4 game"
link: "https://medium.com/gitconnected/learn-rust-by-coding-a-command-line-connect-4-game-673c2bd2edf7"
---


![Rust Connect 4](/assets/img/2024-05-20-LearnRustbycodingacommandlineConnect4game_0.png)

안녕하세요! 이번 글에서는 Rust가 어떻게 우리가 Connect 4의 간단한 버전을 만들며 명령줄 응용 프로그램을 작성하는 데 도움이 되는지 살펴볼 거에요.

최근 Rust를 배우는 도전에 도전했고, 제 자신의 조언을 따르기 위해 개인 프로젝트에서 Rust를 사용해 손을 더럽히며 배운 것을 정리하려고 노력하고 있어요. Connect 4는 아주 좋은 도전 과제에요. 오후 하루 동안 코딩할 수 있는 것은 간단하지만 잘 하려면 약간의 생각이 필요한 복잡한 문제이죠.

여기 제 마지막 결과물이 포함된 Rust 플레이그라운드가 있어요. 아래에서는 거기에 이르기까지 취한 정확한 단계를 살펴볼 거에요. 따라오고 싶으시면 Rust가 설치되어 있는지 확인한 후 터미널에서 `cargo new rust_connect_4_tutorial`을 실행해서 프로젝트를 열고 좋아하는 편집기에서 src/main.rs로 이동해서 시작해봐요!

<div class="content-ad"></div>

혹은 비디오를 통해 학습하는 것을 선호한다면, 해당 튜토리얼의 비디오 버전을 확인해보세요:

# 우리의 타입 정의하기

Rust에서 시작하는 가장 좋은 방법 중 하나는 종종 우리의 타입을 정의하는 것이므로, 우리는 정확히 그것을 할 것입니다!

먼저, 게임에 사용할 보드를 정의해 보겠습니다. Connect 4의 클래식 버전은 7개의 슬롯이 가로로 있고 6개의 슬롯이 세로로 있기 때문에, 이들 정수를 상수로 저장하고, 그 상수를 사용하여 두 차원 배열의 길이를 정의하는 데 사용할 수 있습니다. Board 타입으로 정의합니다:

<div class="content-ad"></div>

```rust
const BOARD_WIDTH: usize = 7;
const BOARD_HEIGHT: usize = 6;

type Board = [[u8; BOARD_WIDTH]; BOARD_HEIGHT];
```

플레이어를 추적할 방법이 필요할 것입니다. 이를 enum으로 구현할 수 있습니다. 우리는 두 명의 플레이어만 지원하지만, 한 명의 피스가 없는 슬롯을 나타내는 None 변형과 게임이 무승부로 끝날 때 처리하는 데 유용할 것입니다.

```rust
#[derive(Clone, Copy, Debug, PartialEq)]
#[repr(u8)]
enum Player {
    One = 1,
    Two = 2,
    None = 0,
}
```

위에서는 나중에 유용할 몇 가지 트레잇을 구현하기 위해 derive를 사용하고, enum의 메모리 레이아웃을 제어하기 위해 repr을 사용했습니다. 가능한 모든 값을 알기 때문에 u8로 제한할 수 있습니다.```

<div class="content-ad"></div>

다음으로, 우리는 게임을 나타내는 struct를 만들 것입니다. 여기에는 추적해야 할 모든 상태가 포함될 것입니다.

```javascript
struct Game {
    current_move: u8,
    current_player: Player,
    board: Board,
    is_finished: bool,
    winner: Player,
}
```

current_move는 플레이어가 선택한 열을 나타내는 정수입니다. 우리에게는 승자 필드가 있지만, 게임이 비김으로 끝날 때 별도로 is_finished 부울도 유용할 것입니다!

마지막으로, Game에 대한 기본 함수를 구현할 것입니다. 이 함수에는 초기 상태가 포함될 것입니다:

<div class="content-ad"></div>

```rust
impl Game {
    fn default() -> Game {
        Game {
            current_move: 0,
            current_player: Player::One,
            board: [
                [0, 0, 0, 0, 0, 0, 0],
                [0, 0, 0, 0, 0, 0, 0],
                [0, 0, 0, 0, 0, 0, 0],
                [0, 0, 0, 0, 0, 0, 0],
                [0, 0, 0, 0, 0, 0, 0],
                [0, 0, 0, 0, 0, 0, 0],
            ],
            is_finished: false,
            winner: Player::None,
        }
    }
}
```

보드에 대해 조금 이야기해 봅시다. 여기서 0은 빈 칸을 나타내며, 1은 Player One이 차지한 칸을 나타내고, 2는 Player Two가 차지한 칸을 나타냅니다.

BOARD_WIDTH와 BOARD_HEIGHT를 사용하여 동적으로 생성할 수도 있었지만, 코드를 작성하는 동안 전체 배열을 보는 것이 도움이 되는 시각적 도구라는 것을 발견했습니다. 같은 이유로, 정수가 아닌 Player 열거형을 사용하지 않았습니다. 텍스트가 더 많아지면 코드를 한눈에 이해하기 어려워지기 때문입니다.

이 방법의 (약간의) 단점은 나중에 정수를 Player로 변환해야 한다는 것입니다. 하지만 Player에 from_int 함수를 구현함으로써 쉽게 해결할 수 있습니다.```

<div class="content-ad"></div>

```js
구현 Player {
    fn from_int(int: u8) -> Player {
        match int {
            1 => Player::One,
            2 => Player::Two,
            _ => Player::None,
        }
    }
}
```

# 보드 표시

디버깅을 위해, 보드를 시각화하는 좋은 방법을 빠르게 코드화하는 것이 도움이 될 것입니다. 명령줄에 출력하기 위해 크레이트를 사용하는 대신에 간단한 색을 나타내기 위해 16진수 이스케이프 문자열을 사용할 것입니다.

```js
const RESET: &str = "\x1b[0m";
const ORANGE: &str = "\x1b[93m";
const RED: &str = "\x1b[0;31m";
```

<div class="content-ad"></div>

아래의 코드에서는 맵을 사용하여 Connect 4 토큰과 유사한 이모지로 슬롯을 변환할 것입니다. 게임이 종료되면 우승자를 발표하는 좋은 장소로 보입니다.

```js
impl Game {
    // 다른 함수들

    fn display_board(&self) {
        println!("{}--------------------{}", ORANGE, RESET);
        println!("{}CONNECT 4 (Move {}){}", ORANGE, self.current_move, RESET);
        println!("{}--------------------{}", ORANGE, RESET);

        for row in self.board {
            let row_str: String = row
                .iter()
                .map(|&cell| match cell {
                    1 => "🔴",
                    2 => "🟡",
                    _ => "⚫",
                })
                .collect::<Vec<&str>>()
                .join(" ");

            println!("{}", row_str);
        }

        println!("{}--------------------{}", ORANGE, RESET);

        if self.is_finished {
            match self.winner {
                Player::One => println!("{}🔴 Player 1이 승리했습니다!{}", ORANGE, RESET),
                Player::Two => println!("{}🟡 Player 2가 승리했습니다!{}", ORANGE, RESET),
                Player::None => println!("{}무승부입니다!{}", ORANGE, RESET),
            }

            println!("{}--------------------{}", ORANGE, RESET);
        }
    }
}
```

우리는 이것을 언제든지 메인 함수 내에서 호출하여 보드의 상태를 확인할 수 있습니다.

```js
fn main() {
    let mut game = Game::default();
    game.display_board();
}
```

<div class="content-ad"></div>

조금 지루하지만 수를 두지 않고는 재미가 없죠. 그래서 이제 수를 두는 방법을 프로그래밍해 봅시다!

# 수 두기

우리의 play_move 함수를 첫 번째로 살펴보면, 이 함수는 이차원 배열에서 올바른 항목을 변경하고, 수를 증가시킨 다음 현재 플레이어를 변경해야 합니다.

```rust
impl Game {
    // other functions

    fn play_move(&mut self, column: usize) {
        if let Some(row) = (0..BOARD_HEIGHT)
            .rev()
            .find(|&row| self.board[row][column] == 0)
        {
            self.board[row][column] = self.current_player as u8;

            self.current_move += 1;

            self.current_player = match self.current_player {
                Player::One => Player::Two,
                _ => Player::One,
            };
        }
    }
}
```

<div class="content-ad"></div>

위의 if let 코드에서는 열의 맨 아래부터 시작하여 하나씩 올라가면서 빈 칸을 찾을 때까지 진행합니다. 빈 칸을 찾으면 그 위치에 현재 플레이어를 나타내는 정수로 변경하고, 현재 이동을 증가시킨 후 match 표현식을 사용하여 플레이어를 변경합니다.

이 시점에서 코드를 통해 움직임을 실행하고 터미널에서 결과를 확인할 수 있습니다:

```js
fn main() {
    let mut game = Game::default();
    
    game.play_move(3);
    game.play_move(3);
    game.play_move(4);
    game.play_move(3);

    game.display_board();
}
```

# 오류 처리

<div class="content-ad"></div>

지금까지 우리는 보드 바깥으로 이동을 시도하는 잠재적인 오류를 처리하지 않고 있습니다. 따라서 세 가지 가능한 오류를 처리해 봅시다:

- 제공된 열이 허용 범위를 벗어납니다.
- 제공된 열이 이미 가득 찼습니다.
- 게임이 끝났습니다.

(사용자 입력을 받기 시작하면 추가적인 오류 처리가 필요할 것입니다. 나중에 처리하겠습니다.)

세 가지 오류 유형을 포함한 enum을 만들어 봅시다.

<div class="content-ad"></div>

```js
enum MoveError {
    GameFinished,
    InvalidColumn,
    ColumnFull,
}
```

우리는 에러를 명령줄에 출력하기를 원하기 때문에, 우리의 에러 메시지를 포함하는 사용자 정의 Display 트레이트를 구현해야 합니다. 예를 들어:

```js
impl std::fmt::Display for MoveError {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        match self {
            MoveError::ColumnFull => write!(f, "column is full"),
            MoveError::InvalidColumn => write!(f, "column must be between 1 and 7"),
            MoveError::GameFinished => write!(f, "game is already finished"),
        }
    }
}
```

이제 이를 사용하여 play_move 함수를 수정할 수 있습니다. 이제 우리는 움직임이 성공적이면 아무것도 반환하지만, 그렇지 않은 경우 에러가 반환되는 Result enum을 반환합니다.


<div class="content-ad"></div>

```rust
impl Game {
    // 다른 함수들

    fn play_move(&mut self, column: usize) -> Result<(), MoveError> {
        if self.is_finished {
            return Err(MoveError::GameFinished);
        }

        if column >= BOARD_WIDTH {
            return Err(MoveError::InvalidColumn);
        }

        if let Some(row) = (0..BOARD_HEIGHT)
            .rev()
            .find(|&row| self.board[row][column] == 0)
        {
            self.board[row][column] = self.current_player as u8;
        } else {
            return Err(MoveError::ColumnFull);
        }

        self.current_move += 1;

        self.current_player = match self.current_player {
            Player::One => Player::Two,
            _ => Player::One,
        };

        Ok(())
    }
}
```

디버깅을 위해 이제 play_move의 결과를 인쇄할 수 있으며, 예상치 못한 입력이 있는 경우 enum으로부터 에러를 볼 수 있습니다. 

우리가 여기에 있는 동안, 명령줄에 에러 메시지를 인쇄하는 도우미 함수를 추가해 봅시다.

```rust
impl Game {
    // 다른 함수들

    fn display_error(&self, error: String) {
        self.display_board();
        println!("{}에러: {}{}", RED, error, RESET);
    }
}
```

<div class="content-ad"></div>

# 게임에서 승리했는지 계산하기

누군가가 이겼는지 확인하기 위해 2차원 배열을 여러 방향으로 탐색해야 합니다. 이 Leetcode 스타일 문제는 재미있는 도전이었고, 결과물을 얻기까지 몇 번 시도해야 했어요.

누군가가 4칸을 연달아 얻을 수 있는 네 가지 방향이 있어요:

- 수평,
- 수직,
- 역 슬래시 대각선 (왼쪽 위에서 오른쪽 아래로),
- 순방향 슬래시 대각선 (왼쪽 아래에서 오른쪽 위로).

<div class="content-ad"></div>

게시판의 모든 슬롯을 반복하여 네 방향 중 하나에서 테스트할 수 있습니다. (게시판이 작기 때문에 무차별 대입 방식을 사용하는 것이 부담이 되지 않습니다. 그러나 더 나은 방법이 있는지 궁금합니다!)

게시판의 주어진 슬롯에서 시작합니다. 플레이어의 토큰이 들어있는 경우, 첫 번째 방향으로 한 단계 이동합니다. 이 새로운 슬롯도 같은 토큰을 포함하는 경우, 이 방향으로 또 다른 단계를 이동합니다. 이를 반복하여 같은 토큰이 있는 네 개의 슬롯을 찾을 때까지 진행합니다. 그렇지 않으면 다른 방향으로 이동합니다.

각 방향은 정수들의 튜플로 나타낼 수 있습니다 - 행당 거리에 대한 값과 열당 거리에 대한 값 하나씩 - 이 정보를 배열에 저장할 수 있습니다.

```js
let directions = [
    (0, 1),  // 가로
    (1, 0),  // 세로
    (1, 1),  // 대각선 (왼쪽 위에서 오른쪽 아래)
    (-1, 1), // 대각선 (왼쪽 아래에서 오른쪽 위)
];
```

<div class="content-ad"></div>

이 튜플들이 무엇을 나타내는지 알려드릴게요:

- 수평으로 이동하려면 행은 변하지 않고(0), 열만 변해야 합니다(1).
- 수직으로 이동하려면 행이 변해야 하고(1), 열은 변하지 않아야 합니다(0).
- 역 슬래시 대각선으로 이동하려면 행과 열이 둘 다 증가해야 합니다(1, 1).
- 마지막으로, 슬래시 대각선으로 이동하려면 열은 증가하고 행은 감소해야 합니다(-1, 1).

각 방향의 모든 슬롯을 테스트하려면 아래와 같이 세 개의 루프가 필요합니다.

```rust
impl Game {
    // 다른 함수들

    fn calculate_winner(&mut self) -> Player {
        for row in 0..BOARD_HEIGHT {
            for col in 0..BOARD_WIDTH {
                let cell = self.board[row][col];

                if cell != 0 {
                    let directions = [
                        (0, 1),  // 수평
                        (1, 0),  // 수직
                        (1, 1),  // 대각선 (왼쪽 위에서 오른쪽 아래로)
                        (-1, 1), // 대각선 (왼쪽 아래에서 오른쪽 위로)
                    ];

                    for (row_step, col_step) in directions {
                        // TODO - 주어진 방향으로 보드를 탐색하고
                        // 승자를 찾았다면 해당 플레이어를 반환하세요
                    }
                }
            }
        }
        Player::None
    }
}
```

<div class="content-ad"></div>

우리는 연속 카운트를 유지하고 — 4에 도달하면 승자가 나온 것을 알 수 있습니다.

그럼, 최종 중첩된 루프에서는 시작 토큰부터 슬롯별로 이동을 시도할 것입니다. 그때까지…

- 시작 토큰과 다른 토큰을 만날 때 (그러한 경우 루프를 종료합니다);
- 보드의 가장자리에 도달할 때 (그러한 경우 루프를 종료합니다);
- 동일한 토큰의 4연속을 만날 때 (그러한 경우 게임을 종료하고 승자를 지정합니다).

```js
impl Game {
    // 다른 함수들

    fn calculate_winner(&mut self) -> Player {
        for row in 0..BOARD_HEIGHT {
            for col in 0..BOARD_WIDTH {
                let cell = self.board[row][col];

                if cell != 0 {
                    let directions = [
                        (0, 1),  // 가로
                        (1, 0),  // 세로
                        (1, 1),  // 대각선 (왼쪽 위에서 오른쪽 아래로)
                        (-1, 1), // 대각선 (왼쪽 아래에서 오른쪽 위로)
                    ];

                    for (row_step, col_step) in directions {
                        let mut consecutive_count = 1;
                        let mut r = row as isize + row_step;
                        let mut c = col as isize + col_step;

                        while r >= 0
                            && r < BOARD_HEIGHT as isize
                            && c >= 0
                            && c < BOARD_WIDTH as isize
                        {
                            if self.board[r as usize][c as usize] == cell {
                                consecutive_count += 1;

                                if consecutive_count == 4 {
                                    self.is_finished = true;
                                    return Player::from_int(cell);
                                }
                            } else {
                                break;
                            }
                            r += row_step;
                            c += col_step;
                        }
                    }
                }
            }
        }

        Player::None
    }
}
```

<div class="content-ad"></div>

우리는 이동 횟수가 일곱 미만인 경우 이러한 확인 작업을 수행하지 않고 최적화를 조금 추가할 수 있습니다. 그때까지는 게임에서 이기는 것이 불가능하기 때문입니다. 그래서 함수 맨 위에 이를 추가해 봅시다:

```js
if self.current_move < 7 {
    return Player::None;
}
```

그리고 보드가 가득 찼지만 승자가 없는 경우(즉, 무승부인 경우)에 게임을 종료로 표시해야 합니다. 이를 반환문 위에 추가해 줍시다:

```js
if self.current_move >= BOARD_HEIGHT as u8 * BOARD_WIDTH as u8 {
    self.is_finished = true;
}
```

<div class="content-ad"></div>

마지막으로, 새로운 calculate_winner 함수를 play_move 함수에 추가할 수 있습니다. 이번에는 아무것도 반환하는 대신, 성공인 Result enum을 반환하거나 실패인 경우에는 오류를 반환합니다.

```js
impl Game {
    // 다른 함수들

    fn play_move(&mut self, column: usize) -> Result<(), MoveError> {
        if self.is_finished {
            return Err(MoveError::GameFinished);
        }

        if column >= BOARD_WIDTH {
            return Err(MoveError::InvalidColumn);
        }

        if let Some(row) = (0..BOARD_HEIGHT)
            .rev()
            .find(|&row| self.board[row][column] == 0)
        {
            self.board[row][column] = self.current_player as u8;
            self.current_move += 1;
        } else {
            return Err(MoveError::ColumnFull);
        }

        let calculated_winner = self.calculate_winner();

        if calculated_winner != Player::None {
            self.winner = calculated_winner;
        } else {
            self.current_player = match self.current_player {
                Player::One => Player::Two,
                _ => Player::One,
            };
        }

        Ok(())
    }
}
```

우리의 Connect 4 게임 엔진이 완성되었습니다! 사용자로부터 입력을 받는 방법만 남았습니다...

# 사용자 입력 받기

<div class="content-ad"></div>

우리 명령 줄 게임의 마지막 부분에서는 주 함수로 돌아가 사용자 입력을 읽을 수 있도록 할 것입니다.

먼저, Rust의 표준 I/O 라이브러리를 가져오겠습니다.

```js
use std::io;
```

사용자 입력을 읽는 논리는 다음과 같이 보일 수 있습니다. 여기서는 컴퓨터와 달리 0이 아닌 1부터 시작하여 카운트하게 됩니다!

<div class="content-ad"></div>

```js
let mut user_move = String::new();

io::stdin()
    .read_line(&mut user_move)
    .expect("Failed to read line");

let user_move: usize = match user_move.trim().parse() {
    Ok(num) => {
        if num < 1 || num > BOARD_WIDTH as u8 {
            game.display_error(MoveError::InvalidColumn.to_string());
            continue;
        } else {
            num
        }
    }
    Err(err) => {
        game.display_error(err.to_string());
        continue;
    }
};

match game.play_move(user_move - 1) {
    Ok(_) => {
        game.display_board();
    }
    Err(err) => {
        game.display_error(err.to_string());
    }
}
```

먼저, 우리는 빈 문자열을 정의한 뒤 사용자로부터 텍스트를 받기 위해 stdin 메소드를 사용합니다.

다음으로 이 입력을 구문 분석을 시도합니다. 다음 중 하나라도 해당하는 경우 오류를 표시합니다:

- 구문 분석이 실패하는 경우 (예: 입력이 숫자가 아닌 경우).
- 구문 분석이 성공하지만 숫자가 원하는 범위를 벗어난 경우.```

<div class="content-ad"></div>

만약 오류가 표시되지 않는다면, 우리는 선택한 수를 둘 것입니다. 그러나 해당 시나리오에서 오류가 발생할 수도 있으므로, 그런 경우에는 오류를 표시할 것입니다.

그러나 위의 코드는 한 번만 실행됩니다. 게임이 끝날 때까지 루프를 실행해야 합니다. 아래는 그 반복문을 추가한 main 함수와 사용자를 도와줄 몇 가지 유용한 메시지입니다:

```js
fn main() {
    let mut game = Game::default();
    game.display_board();

    while !game.is_finished {
        println!("\n");

        match game.current_player {
            Player::One => println!("플레이어 1"),
            Player::Two => println!("플레이어 2"),
            _ => (),
        };

        println!("1부터 7 사이의 열을 입력하세요:");

        let mut user_move = String::new();
        io::stdin()
            .read_line(&mut user_move)
            .expect("라인을 읽는 데 실패했습니다");

        let user_move: usize = match user_move.trim().parse() {
            Ok(num) => {
                if num < 1 || num > 7 {
                    game.display_error(MoveError::InvalidColumn.to_string());
                    continue;
                } else {
                    num
                }
            }
            Err(err) => {
                game.display_error(err.to_string());
                continue;
            }
        };

        match game.play_move(user_move - 1) {
            Ok(_) => {
                game.display_board();
            }
            Err(err) => {
                game.display_error(err.to_string());
            }
        }
    }
}
```

# 여러 판을 플레이하기

<div class="content-ad"></div>

마지막으로, 사용자가 게임을 끝내면 게임을 다시 시작할 수 있도록 추가 코드를 추가하고 싶어요. 매번 실행 파일을 다시 시작해야 하는 번거로움은 피하고 싶으니까요!

이를 위해 새로운 루프를 만들 것입니다. 이전과 동일한 방법을 사용하여 사용자의 입력을 읽을 수 있습니다. "R"을 누르면 새 게임을 시작하고, "Q"를 누르면 루프를 중단하고 코드를 끝까지 실행합니다.

```js
fn main() {
    let mut game = Game::default();
    game.display_board();

    loop {
        while !game.is_finished {
            // 코드 변경 없음
        }

        println!("Press 'R' to restart or 'Q' to quit the game.");

        let mut user_input = String::new();

        io::stdin()
            .read_line(&mut user_input)
            .expect("Failed to read line");

        match user_input.trim() {
            "R" | "r" => {
                game = Game::default();
                game.display_board();
            }
            "Q" | "q" => {
                println!("Quitting...");
                break;
            }
            _ => game.display_error("잘못된 입력".to_string()),
        }
    }
}
```

마지막으로, 차례 사이에 터미널을 지우기 위한 ASCII 이스케이프 코드를 사용할 수 있습니다.

<div class="content-ad"></div>

```js
fn clear_screen(&self) {
    print!("{}[2J", 27 as char);
}
```

게임 보드를 표시할 때마다 이 작업을 수행할 수 있습니다.

```js
fn display_board(&self) {
    self.clear_screen();

    // 코드 변경 없음
}
```

# 모두 함께 가져오기

<div class="content-ad"></div>

그것이 커넥트 4의 간단한 명령줄 게임을 위해 필요한 모든 것입니다! 이 작업 중인 파일을 사용하는 작동 버전을 시험하려면 Rust 플레이그라운드를 확인해보세요.

우리가 지금까지 만든 전체 파일은 아래와 같습니다:

```js
use std::io;

const RESET: &str = "\x1b[0m";
const ORANGE: &str = "\x1b[93m";
const RED: &str = "\x1b[0;31m";

const BOARD_WIDTH: usize = 7;
const BOARD_HEIGHT: usize = 6;

type Board = [[u8; BOARD_WIDTH]; BOARD_HEIGHT];

#[derive(Clone, Copy, Debug, PartialEq)]
#[repr(u8)]
enum Player {
    One = 1,
    Two = 2,
    None = 0,
}

impl Player {
    fn from_int(int: u8) -> Player {
        match int {
            1 => Player::One,
            2 => Player::Two,
            _ => Player::None,
        }
    }
}

#[...]

# 결론

<div class="content-ad"></div>

지금까지 따라오셨다면 프로젝트를 더 나아가고 싶을 것입니다. 여러 게임을 거치면서 승리를 추적해 볼 수도 있고, 어떨까요? 어플리케이션을 웹 서버로 전환하여 웹사이트가 HTTP를 통해 Connect 4를 플레이할 수 있게끔 만들어 보는 것은 어떤가요?

이번 명령줄 어플리케이션을 Rust로 개발하는 방법에 대한 유용하고 재미있는 통찰력으로 여기시길 바랍니다. 아직 Rust 여행을 시작한 지 얼마 되지 않았기 때문에, 어떠한 비평이나 더 많은 Rust 다운 방식으로 어플리케이션을 작성하는 아이디어가 있다면 꼭 알려주세요. 함께 고민해보고 싶습니다.