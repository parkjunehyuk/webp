            var             let         const     
값 변경      가능             가능       불가능(고정값)
재선언    가능(버그원인)    불가능(에러)    불가능(에러)
유효범위    함수단위        블록 단위       블록 단위

왜 var 대신 let을 쓰게 되었을까?

①중복 선언 문제 (var는 방지하지 못함)

var name = "Kim";
var name = "Lee"; // 버그가 발생하기 쉬움

// let은 동일한 이름으로 재선언하면 에러를 냅니다.
let age = 20;
let age = 30;




② 유효 범위 문제 (Scope)
var (함수 스코프): if문이나 for문 안에서 선언해도 블록{}을 뚫고 밖으로 나옵니다.

let (블록 스코프): 자신이 선언된 {} 블록 안에서만 유효합니다.

if (true) {
  var x = 10;
  let y = 20;
}

console.log(x); // 10 (밖에서도 접근 가능)
console.log(y); // ReferenceError: y is not defined (접근 불가)