# 문제 해결 접근법

<br>

## 알고리즘은 무엇인가?

```
특정 작업을 달성하기 위한 과정이나 일련의 단계
```

- 문제 해결을 위한 계획을 수립하자
- 일반적인 문제 해결 패턴을 파악하자

<br>

## 문제해결 단계

**1. 문제 이해**

**2. 구체적인 예시 알아보기**

**3. 문제 세분화**

**4. 문제를 해결하고 단순화**

**5. 회고, 재구성**

<br>

## 1단계 : 문제 이해

- 질문이 무엇인지, 나만의 언어(방식)로 이해하기
- 어떤 입력값이 주어지는가?
- 어떤 값을 출력해야 하는가?
- 입력값에 의해 출력값이 결정되는가?
- 문제해결과정에서 중요한 데이터를 어떻게 라벨링할 것인가?

```
// ===============================================================
// Write a function which takes two numbers and returns their sum.
// ===============================================================
// 1. Can I restate the problem in my own words?
"implement addition"
// 2. What are the inputs that go into the problem?
"ints? floats? large numbers?"
// 3. What are the outputs that should come from the solution to the problem?
"ints? floats? large numbers?"
// 4. Can the outputs be determined from the inputs? In other words, do I have enough information to solve the problem?
// 5. How should I label the important pieces of data that are a part of the problem?
```

<br>

## 2단계 : 구체적인 예제들

- 간단한 예시로 시작하기
- 복잡한 예시로 나아가기
- 빈 입력값 넣어보기
- 유효하지 않은 입력 넣어보기

<br>

## 3단계 : 문제 세분화

- **주석**을 활용하여 적어나간다

<br>

## 4단계 : 해결/단순화

- 단순한 부분부터 구현

<br>

## 5단계 : 회고, 재구성(Refactor)

> 리팩토링을 위한 질문들

- 결과를 확인할 수 있는가?
- 결과를 다른 방식으로 도출할 수 있는가?
- 작성한 코드가 한눈에 이해되는가? (+가독성)
- 작성한 코드를 다른 문제를 해결하기 위해 활용할 수 있는가? (해결한 문제가 다른 문제에 대한 해결책을 열어주기도 함)
- 솔루션의 성능을 향상시킬 수 있는가? (시간복잡도, 공간복잡도)
- 리팩토링할 다른 방식을 생각할 수 있는가? (회사의 코드 컨벤션 등에 맞는지)
- 다른 사람들은 어떻게 문제를 해결했는지 확인했는가?

<br>

```javascript
// 리팩토링 전 for loop 사용

function charCount(str) {
  var obj = {};
  for (var i = 0; i < str.length; i++) {
    var char = str[i].toLowerCase();
    if (/[a-z0-9]/.test(char)) {
      // 참고로 브라우저나 환경에 따라 정규식의 수행속도가 조금씩 다르다(크롬에서 이슈있었음)
      obj[char]++;
    } else {
      obj[char] = 1;
    }
  }
  return obj;
}
```

<br>

```javascript
// for of 문 사용하여 리팩토링 후

function charCount(str) {
  var obj = {};
  for (var char of str) {
    // str에서 순서대로 1개씩 char에 할당
    if (isAlphaNumeric(char)) {
      char = char.toLowerCase();
      obj[char] = ++obj[char] || 1; // 정규식을 만족하면 obj[char]에 +1 증가한 수를 할당하고 아니면 1을 할당
    }
  }
  return obj;
}
// 분리된 함수를 통해 가독성 향상
function isAlphaNumeric(char) {
  var code = char.charCodeAt();
  if (
    !(code > 47 && code < 58) &&
    !(code > 64 && code < 91) &&
    !(code > 96 && code < 123)
  ) {
    return false;
  }
  return true;
}
```
