# 문제 해결 패턴

- [빈도수 세기 패턴](#빈도수-세기-패턴)
- [다중 포인터 패턴](#다중-포인터-패턴)
- [기준점 간 이동 배열 패턴](#기준점-간-이동-배열-패턴)
- [분할과 정복 패턴](#분할과-정복-패턴)

<br>

## 빈도수 세기 패턴

> 보통 자바스크립트의 객체를 사용해서 다양한 **값과 빈도를 수집**하는 것

<br>

예제) 2개의 배열 a, b 를 입력받고 a의 각 원소의 제곱이 b의 원소이면 true (순서는 무시하지만 빈도는 동일해야함)

```javascript
same([1, 2, 3], [4, 1, 9]); // true
same([1, 2, 3], [1, 9]); // false
same([1, 2, 1], [4, 4, 1]); //false
```

- 중첩된 loop를 이용한 naive solution

```javascript
function same(arr1, arr2) {
  if (arr1.length !== arr2.length) {
    return false;
  } // 즉시 확인이 가능함
  for (let i = 0; i < arr1.length; i++) {
    // indexOf는 배열에서 지정된 원소를 발견하고 첫번째 인덱스를 반환(없으면 -1 반환)
    // 그 자체로서 loop임
    let correctIndex = arr2.indexOf(arr1[i] ** 2);
    if (correctIndex === -1) {
      return false;
    }
    arr2.splice(correctIndex, 1); // 반환 후엔 배열의 길이가 1씩 줄어듬
  }
  return true;
}
```

> 시간복잡도 : O(N^2) → naive solution

<br>

- 리팩토링한 풀이

> 각 배열에 한 번씩 개별적으로 루프를 적용할 수 있다.

> 💡 두 개의 루프가 두 개의 중첩 된 개별 루프보다 훨씬 낫다!

```javascript
function same(arr1, arr2) {
  if (arr1.length !== arr2.lenth) {
    return false;
  }
  let frequencyCounter1 = {};
  let frequencyCounter2 = {};
  for (let val of arr1) {
    // arr1의 원소 처음부터 끝까지를 val에 반복 저장
    // frequencyCounter[val]이 있으면 그 값에 +1, 없으면 0 할당 후 +1
    frequencyCounter1[val] = (frequencyCounter1[val] || 0) + 1;
  }
  for (let val of arr2) {
    frequencyCounter2[val] = (frequencyCounter2[val] || 0) + 1;
  }
  for (let key in frequencyCounter1) {
    // 객체에서는 of 대신 in을 씀
    if (!(key ** 2 in frequencyCounter2)) {
      return false;
    }
    if (frequencyCounter2[key ** 2] !== frequencyCounter1[key]) {
      return false;
    }
  }
  return true;
}
```

> 대략 3n 이며 n은 배열의 길이 → O(n)의 복잡성으로 단순화

<br>
<br>

- ANAGRAMS 도전과제

문자열 a, b를 입력받고 anagram이면 true를 출력하는 프로그램 작성!

앞전 코드를 활용한 풀이

```javascript
function validAnagram(str1, str2) {
  if (str1.length !== str2.length) {
    return false;
  }
  let frequencyCounter1 = {};
  let frequencyCounter2 = {};
  for (let char of str1) {
    frequencyCounter1[char] = (frequencyCounter1[char] || 0) + 1;
  }
  for (let char of str2) {
    frequencyCounter2[char] = (frequencyCounter2[char] || 0) + 1;
  }
  for (let key in frequencyCounter1) {
    if (!(key in frequencyCounter2)) {
      return false;
    }
    if (frequencyCounter1[key] !== frequencyCounter2[key]) {
      return false;
    }
  }
  return true;
}
console.log(validAnagram("bape", "beap")); // true
console.log(validAnagram("bapze", "beap")); // false
```

<br>

- 리팩토링

```javascript
function validAnagram(first, second) {
  if (first.length !== second.length) {
    return false;
  }
  const lookup = {}; // 빈도 카운터 객체
  for (let i = 0; i < first.length; i++) {
    let letter = first[i];
    lookup[letter] ? (lookup[letter] += 1) : (lookup[letter] = 1);
  } // 문자열을 세분화한 객체
  for (let i = 0; i < second.length; i++) {
    let letter = second[i];
    if (!lookup[letter]) {
      // 알파벳 키값의 밸류값이 0인 것이 있으면 false
      return false;
    } else {
      lookup[letter] -= 1;
    }
  }
  return true;
}
// {a: 3, n: 1, g: 1, r: 1, m: 1}
console.log(validAnagram("anagram", "nagaram")); // true
```

<br>
<br>
<br>

## 다중 포인터 패턴

> 인덱스나 위치에 해당하는 포인터나 값을 만든 다음 특정 조건에 따라 중간 지점에서 부터 시작 지점이나 끝 지점이나 양쪽 지점을 향해 이동시키는 것

<br>

예제) 한 숫자를 가져와 다른 숫자에 더하면 0이 되는 첫 번째 쌍을 찾는다. (오름차순으로 정렬된 배열이어야 함)

```javascript
sumZero([-3, -2, -1, 0, 1, 2, 3]); // [-3, 3]
sumZero([-1, 0, 2, 3]); // undefined
```

<br>

- naive solution

> ❗ 중첩 반복문 사용으로 O(n^2)의 시간복잡도를 가짐

```javascript
function sumZero(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] + arr[j] === 0) {
        return [arr[i], arr[j]];
      }
    }
  }
}
```

<br>

- 리팩토링

> O(n)으로 시간복잡도 줄임!

```javascript
function sumZero(arr) {
  let left = 0;
  let right = arr.length - 1; // 마지막 index
  // left와 right가 중앙으로 점점 다가오다가 맞닥뜨릴 때까지 진행하는 while loop
  // 맞닥뜨렸는데도 합 0 아니면 리턴값이 없어서 undefined 반환됨
  while (left < right) {
    let sum = arr[left] + arr[right];
    if (sum === 0) {
      return [arr[left], arr[right]];
    } else if (sum > 0) {
      right--;
    } else {
      left++;
    }
  }
}
```

<br>

- 다중 포인터 : 고유값 세는 도전과제

오름차순 정렬된 정수 배열을 입력받고, 고유값(unique value)의 갯수를 반환  
(빈 배열은 0을 반환)

<br>

> O(n) 선형 시간이 적용됨

```javascript
function countUniqueValues(arr) {
  if (!arr.length) {
    return 0;
  }
  let i = 0;
  for (let j = 1; j < arr.length; j++) {
    if (arr[i] !== arr[j]) {
      i++;
      arr[i] = arr[j];
    }
    console.log(i, j);
  }
  return i + 1;
}
// [1,1,2,3,3,4,5,6,6,7]
```

<br>

> 💡 투 포인터를 사용한 풀이 (모범답안보다 범용성 좋음)

```javascript
function countUniqueValues(arr) {
  if (!arr.length) {
    return 0;
  }
  let left = 0;
  let right = 1;
  let count = 1;
  while (right < arr.length) {
    if (arr[left] === arr[right]) {
      right++;
    } else if (arr[left] !== arr[right]) {
      left = right;
      count++;
    }
  }
  return count;
}
console.log(countUniqueValues([1, 1, 1, 2, 3, 3, 4, 4, 4, 4])); // 4
```

<br>
<br>
<br>

## 기준점 간 이동 배열 패턴

> **sliding window** → window 는 어떤 **조건**을 만족하는지 아닌지를 판별하는 **집합 단위**라고 생각하면 된다

> 배열이나 문자열과 같은 일련의 데이터를 입력하거나 특정 방식으로 연속적인 해당 데이터의 하위 집합을 찾는 경우에 유용

<br>

예제) 배열과 숫자 하나를 전달하고 서로 마주한 두 숫자의 가장 큰 합계를 찾아라

```
ex) maxSubarraySum([1,2,5,2,8,1,5], 2) // 10
ex) maxSubarraySum([1,2,5,2,8,1,5], 4) // 17
ex) maxSubarraySum([4,2,1,6], 1) // 6
ex) maxSubarraySum([], 4) // null
```

```javascript
function maxSubarraySum(arr, num) {
  if (num > arr.length) {
    return null;
  }
  var max = -Infinity; // 음의 무한대 → 음수만 있는 배열
  // arr.length - num + 1 → window 수
  for (let i = 0; i < arr.length - num + 1; i++) {
    temp = 0;
    // 개별의 window
    for (let j = 0; j < num; j++) {
      // arr[i + j]를 통해 i++ 될 때마다 연속하는 배열을 구할 수 있다
      temp += arr[i + j];
    }
    if (temp > max) {
      max = temp;
    }
  }
  return max;
}

//maxSubarraySum([2, 6, 9, 2, 1, 8, 5, 6, 3], 3)
```

> ❗ 아주 큰 배열일 경우 빅오 표기법을 사용하여 무한으로 연장할 수 있음 → 매우 비효율적임

<br>

- 💡 Refactor ) **sliding window** 예시

중첩된 루프로 연속적으로 루프를 진행할 필요 X

→ 큰 관점에서 O(n^2) 가 아닌, O(n) !!

```javascript
function maxSubarraySum(arr, num) {
  let maxSum = 0;
  let tempSum = 0;
  if (arr.length < num) return null;
  for (let i = 0; i < num; i++) {
    maxSum += arr[i]; // 우선 첫 번째 합을 maxSum에 저장
  }
  tempSum = maxSum;
  for (let i = num; i < arr.length; i++) {
    // arr[0] 빼고 arr[num] 더하면 새로운 window.
    // 이것을 반복
    tempSum = tempSum - arr[i - num] + arr[i];
    maxSum = Math.max(maxSum, tempSum);
  }
  return maxSum;
}
maxSubarraySum([2, 6, 9, 2, 1, 8, 5, 6, 3], 3);
```

<br>
<br>
<br>

## 분할과 정복 패턴

> Divide and Conquer (분할 정복 패턴) → 주로 배열이나 문자열 같은 큰 규모의 데이터셋을 작은 조각들로 나누고 그 하위집합을 처리하는 프로세스를 반복한다.
> 💡 시간복잡도를 크게 낮출 수 있다.

<br>

예제) **정렬**된 숫자 배열에서 특정 숫자의 인덱스를 리턴한다 (없으면 -1 리턴)

```js
search([1, 2, 3, 4, 5, 6], 4); // 3
search([1, 2, 3, 4, 5, 6], 11); // -1
```

<br>

- 선형 탐색 (naive ver.)

시간 복잡도 : O(n)

```js
function search(arr, num) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === val) {
      return i;
    }
  }
  return -1;
}
```

<br>

- Binary Search (이진 탐색) (refactor ver.)

시간복잡도 : O(log n)

```js
function search(array, val) {
  let min = 0;
  let max = array.length - 1;
  while (min <= max) {
    let middle = Math.floor((min + max) / 2);
    let currentElement = array[middle];
    if (array[middle] < val) {
      min = middle + 1;
    } else if (array[middle] > val) {
      max = middle - 1;
    } else {
      return middle;
    }
  }
  return -1;
}
```
