# 💡 내 머릿속에 쏙 들어오는 자바스크립트 핵심 원리 노트

파일 번호(`04prac`, `prac10-07` 등)를 일일이 외울 필요는 없습니다!
지금까지 연습하신 14개의 예제는 사실 **자바스크립트가 웹 브라우저를 다루는 5가지 핵심 기술**을 반복해서 연습한 것입니다. 각 원리마다 달린 **실전 예시 코드**만 눈으로 익히셔도 어떤 응용 문제가 나와도 스스로 코드를 짜실 수 있습니다.

---

## 1. 브라우저에게 질문하기 (`navigator` & `screen`)

자바스크립트는 웹페이지 안의 글자뿐만 아니라, **지금 이 페이지를 열고 있는 브라우저와 모니터의 정보**도 물어볼 수 있습니다.

### ① "너 무슨 브라우저니?" (`navigator.userAgent`)
* **원리**: 브라우저가 웹사이트에 접속할 때 남기는 신분증(문자열)을 확인하는 기능입니다.
* **이해 포인트**: `indexOf("찾을단어")`는 글자가 들어있으면 그 위치 번호(0 이상)를 알려주고, **없으면 `-1`을 반환**합니다. 그래서 `!= -1`은 *"그 단어가 들어있다면!"*이라는 뜻이 됩니다.
* **예시 코드**:
  ```html
  <script>
    // 브라우저 신분증을 전부 소문자로 바꿈 (대소문자 실수 방지)
    let agent = navigator.userAgent.toLowerCase();

    if (agent.indexOf("chrome") != -1) {
      alert("크롬 브라우저로 접속하셨네요!");
    } else {
      alert("크롬이 아닌 다른 브라우저입니다.");
    }
  </script>
  ```

### ② "모니터 화면 크기가 얼마나 돼?" (`screen`)
* **원리**: 내 웹페이지가 깨지지 않고 잘 보일 만큼 모니터 해상도가 충분한지 검사할 때 씁니다.
* **예시 코드**:
  ```html
  <body onload="checkScreen()">
    <script>
      function checkScreen() {
        // 가로가 1280보다 작거나(OR), 세로가 1024보다 작으면 경고!
        if (screen.width < 1280 || screen.height < 1024) {
          alert("스크린 해상도가 낮아 화면이 잘릴 수 있습니다!");
        }
      }
    </script>
  </body>
  ```

---

## 2. 새 창(팝업) 띄우고 조종하기 (`window.open`)

`window.open("주소", "창이름", "크기/위치")` 함수는 세 칸에 어떤 값을 넣느냐에 따라 전혀 다르게 동작합니다.

### ① 빈 창 열어서 직접 글씨 쓰기
* **이해 포인트**: 주소를 `""`로 비워두면 하얀 빈 창이 열립니다. `write()`로 글씨를 쓴 뒤 **`close()`를 안 해주면 브라우저 탭의 로딩 아이콘이 계속 빙글빙글 돕니다.**
* **예시 코드**:
  ```html
  <body onload="printWelcome()">
    <script>
      function printWelcome() {
        // 주소 없음(""), 이름 없음(""), 가로 200 / 세로 80 크기 창 열기
        let newWin = window.open("", "", "width=200,height=80");
        newWin.document.write("접속 감사합니다!");
        newWin.document.close(); // 다 썼다고 브라우저에 알려주기!
      }
    </script>
  </body>
  ```

### ② 새 창을 계속 열까, 하나만 재활용할까? (두 번째 칸의 비밀)
* **이해 포인트**: 두 번째 칸(창 이름)을 비워두면 클릭할 때마다 새 창이 계속 생기고, `"myWin"`처럼 이름표를 붙여주면 브라우저가 **기존 창을 찾아서 주소만 바꿔줍니다.**
* **예시 코드**:
  ```html
  <select onchange="loadSite(this)">
    <option value="https://www.naver.com">네이버</option>
    <option value="https://www.google.com">구글</option>
  </select>

  <script>
    function loadSite(select) {
      let site = select.options[select.selectedIndex].value;

      // [방식 A] 두 번째 칸이 비어있음 -> 선택할 때마다 새 창이 계속 열림
      // window.open(site, "", "width=500,height=400");

      // [방식 B] "myWin"이라는 이름 지정 -> 창 하나만 띄워놓고 내용만 바뀜
      window.open(site, "myWin", "width=500,height=400");
    }
  </script>
  ```

---

## 3. 시간 조종하기: 시계와 카운트다운 (`setTimeout` vs `setInterval`)

자바스크립트에서 시간을 다루는 방법은 딱 두 가지입니다.

| 구분 | `setTimeout(함수, 시간)` | `setInterval(함수, 시간)` |
| :--- | :--- | :--- |
| **비유** | **"1초 뒤에 알람 한 번만 울려줘"** (1회용 예약) | **"1초마다 계속 알람 울려줘"** (무한 반복) |
| **멈추는 법** | `clearTimeout(타이머변수)` | `clearInterval(타이머변수)` |

### ① `setTimeout`으로 만드는 디지털 시계 (꼬리 물기 방식)
* **예시 코드**:
  ```html
  <div id="clock"></div>

  <script>
    function showTime() {
      let now = new Date();
      // 현재 시간을 "오후 4:12:30" 형태로 가져옴
      document.getElementById("clock").innerHTML = now.toLocaleTimeString();

      // 1초(1000ms) 뒤에 나 자신(showTime)을 다시 실행하도록 예약
      setTimeout(showTime, 1000);
    }
    showTime(); // 처음 한 번 시작해주면 계속 꼬리를 물고 실행됨
  </script>
  ```

### ② `setInterval`로 클릭할 때마다 멈췄다/켰다 하기 (토글 스위치)
* **이해 포인트**: `let running = true;` 같은 스위치 변수를 하나 만들어 두고, 클릭할 때마다 상태를 반대로 뒤집어 줍니다.
* **예시 코드**:
  ```html
  <div id="clock">시계 자리</div>

  <script>
    let clock = document.getElementById("clock");
    let running = true; // 현재 시계가 돌아가는 중인지 저장하는 스위치

    function updateClock() {
      clock.innerHTML = new Date().toLocaleTimeString();
    }

    // 1초마다 반복 실행 시작
    let timer = setInterval(updateClock, 1000);

    // 시계를 클릭했을 때 멈춤/재시작 전환
    clock.onclick = function () {
      if (running) {
        clearInterval(timer); // 반복 정지!
        running = false;
        clock.style.color = "grey";
      } else {
        timer = setInterval(updateClock, 1000); // 다시 시작!
        running = true;
        clock.style.color = "black";
      }
    };
  </script>
  ```

### ③ 10초 제한시간 카운트다운 (타이머 겹침 방지 꿀팁)
* **이해 포인트**: "다시" 버튼을 여러 번 누를 때 타이머가 중복 실행되어 시간이 2배속, 3배속으로 줄어드는 것을 막으려면 **시작 직전에 `clearInterval(timer)`를 먼저 호출**해야 합니다.
* **예시 코드**:
  ```html
  <p>남은 시간: <span id="time">10</span>초</p>
  <button onclick="startGame()">다시 시작</button>

  <script>
    let timer = null;
    let remaining = 10;

    function startGame() {
      clearInterval(timer); // ★ 중요: 기존에 돌던 타이머가 있다면 먼저 끄기!
      remaining = 10;
      document.getElementById("time").innerHTML = remaining;

      timer = setInterval(function () {
        remaining--; // 1씩 감소
        document.getElementById("time").innerHTML = remaining;

        if (remaining == 0) {
          clearInterval(timer); // 0초가 되면 타이머 종료
          alert("시간 종료! 자동 채점합니다.");
        }
      }, 1000);
    }
  </script>
  ```

---

## 4. 화면 글자 읽고 스타일 바꾸기 (`DOM` 조작)

산수 퀴즈 예제들의 핵심은 **HTML 태그 안에 적힌 글자를 자바스크립트로 가져와서 계산하고, 결과를 다시 HTML에 반영하는 것**입니다.

### ① 문제 읽고, 입력값 꺼내서, 틀리면 취소선 긋기
* **예시 코드**:
  ```html
  <table>
    <tr>
      <td class="question">5*6</td>
      <td><input class="answer" type="text" /></td>
    </tr>
  </table>
  <button onclick="check()">채점</button>
  <p>점수: <span id="score">0</span></p>

  <script>
    function check() {
      let q = document.getElementsByClassName("question")[0]; // 문제 태그
      let a = document.getElementsByClassName("answer")[0];   // 입력칸 태그

      // 1. 일반 태그는 innerHTML로 글자를 꺼냄 ("5*6")
      let correct = eval(q.innerHTML); // 30

      // 2. <input> 태그는 반드시 .value로 사용자가 쓴 값을 꺼냄
      if (Number(a.value) == correct) {
        q.style.textDecoration = "none"; // 맞으면 취소선 제거
        document.getElementById("score").innerHTML = 1;
      } else {
        q.style.textDecoration = "line-through"; // 틀리면 글자 가운데 줄 긋기
        document.getElementById("score").innerHTML = 0;
      }
    }
  </script>
  ```

---

## 5. 산수 퀴즈를 똑똑하게 만드는 3가지 계산 트릭

퀴즈 코드에서 가장 재미있고 실용적인 부분입니다.

### ① 문자열 수식을 한 번에 계산하는 마법 (`eval`)
* **원리**: `"7+5*3"`은 그냥 글자(문자열)라서 컴퓨터가 바로 답을 모릅니다. `eval()`에 넣어주면 실제 수학식처럼 곱셈부터 계산해 줍니다.
* **예시 코드**:
  ```javascript
  let text = "7+5*3";
  let result = eval(text); // 곱셈을 먼저 계산해서 숫자 22가 됨!
  console.log(result);     // 출력: 22
  ```

### ② 뺄셈·나눗셈 문제를 깔끔하게 만드는 센스
* **원리**:
  * **뺄셈**: 앞의 숫자가 뒤의 숫자보다 작으면 두 숫자의 자리를 맞바꿔서 마이너스(`-`) 답이 나오지 않게 합니다.
  * **나눗셈**: 두 수를 먼저 곱해서 앞의 숫자로 만들면 소수점 없이 무조건 깔끔한 정수로 떨어집니다.
* **예시 코드**:
  ```javascript
  let num1 = Math.floor(Math.random() * 10) + 1; // 예: 3
  let num2 = Math.floor(Math.random() * 10) + 1; // 예: 8
  let op = "-"; // 연산자가 뺄셈이나 나눗셈이라고 가정

  if (op == "-") {
    // 3 - 8 이 되지 않도록 두 수의 자리를 바꿈 -> 8 - 3
    if (num1 < num2) {
      let temp = num1;
      num1 = num2;
      num2 = temp;
    }
    answer = num1 - num2; // 5
  } 
  else if (op == "/") {
    // 3과 8이 뽑혔다면, 3 * 8 = 24를 먼저 만들어서 "24 / 8"로 문제 출제!
    num1 = num1 * num2; 
    answer = num1 / num2; // 무조건 깔끔한 정수 3이 나옴!
  }
  ```

### ③ 빈칸을 `0`으로 착각하는 함정 피하기 (`.trim()`)
* **원리**: 자바스크립트에서는 아무것도 안 쓴 빈칸 `""`을 숫자로 바꾸면(`Number("")`) `0`이 됩니다. 정답이 `0`인 문제(`5 - 5`)에서 아무것도 안 썼는데 정답 처리가 되는 것을 막으려면 빈칸 검사가 필수입니다.
* **예시 코드**:
  ```javascript
  let userInput = "   "; // 사용자가 스페이스바만 누르거나 아무것도 안 쓴 상태
  let correctAnswer = 0; // 문제의 실제 정답이 0일 때

  // .trim()으로 앞뒤 공백을 다 지웠을 때 빈 문자열("")이 아니어야만 채점 통과!
  if (userInput.trim() != "" && Number(userInput) == correctAnswer) {
    alert("정답입니다!");
  } else {
    alert("오답입니다! (빈칸은 정답으로 인정되지 않습니다)");
  }
  ```