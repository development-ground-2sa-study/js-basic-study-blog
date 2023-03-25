# BigO

BigO는 하나의 문제를 해결할 수 있는 여러 접근 방법(해결 방법)이 있을 때, 여러 접근 방법들을 비교해서 더 좋은 방법을 선택하기 위한 비교 기준으로, 함수에서 입력 값과 연산 수의 관계를 표기한 것

코드 성능을 측정하는 기준은 목적과 환경에 따라 여러가지 있을 수 있지만 BigO는 연산 회수를 기준으로 알고리즘의 성능을 측정한다.

> - 처리 속도가 빠름(시간 복잡도)
> - 메모리 사용률이 적거나 안정적(공간 복잡도)
> - 기타 목적과 환경에 맞는 방법

<O(n(연산 수)), O(1), O(log n), O(n \* log n), O(n^2)과 같이 사용한다.>

---

## **예시 문제**

---

양의 정수 n을 입력 받아서 1부터 n까지의 정수를 전부 합산하는 함수를 만들기

### **풀이01** (for문을 이용한 루프)

```js
function addUpTo(n) {
  let sum = 0;
  for (let i = 1; i <= n; i++) {
    sum += i;
  }
  return sum;
}
```

### **풀이02** (수학 공식을 이용한 풀이)

```js
function addUpTo(n) {
  return (n * (n + 1)) / 2;
}
```

입력 값이 같을 때 두 풀이의 결과는 같다. 하지만 연산 회수 는 "풀이02"가 더 적다.

---

## **O(n) 예시**

```js
const findIndex = (items, match) => {
  for (let i = 0, total = items.length; i < total; i++) {
    if (items[i] == match) return i;
  }

  return -1;
};
```

## **O(n^2) 예시**

```js
const buildSquareMatrix = (items) => {
  let matrix = [];

  for (let i = 0, total = items.length; i < total; i++) {
    matrix[i] = [];

    for (let j = 0, total = items.length; j < total; j++) {
      matrix[i].push(items[j]);
    }
  }

  return matrix;
};
```

## **O(n \* log n) 예시**

배열을 정렬하는 함수인데 자바스크립트의 sort() 메소드도 유사한 연산과정을 거침

```js
const quickSort = (list) => {
  if (list.length < 2) return list;

  let pivot = list[0];
  let left = [];
  let right = [];

  for (let i = 1, total = list.length; i < total; i++) {
    if (list[i] < pivot) {
      left.push(list[i]);
    } else {
      right.push(list[i]);
    }
  }

  return [...quickSort(left), pivot, ...quickSort(right)];
};
```

참조: [Big O Notation in JavaScript](https://medium.com/cesars-tech-insights/big-o-notation-javascript-25c79f50b19b)

---

## **push() vs unshift()**

배열의 앞에 1씩 늘어나는 값을 n번 추가하는 같은 코드

**push()** [O(1)]

```js
var a = [];
var start = new Date();
for (var i = 0; i < 100000; i++) {
  a.push(i);
}

a.reverse();
var end = new Date() - start;
console.log(`Push and reverse time: ${end}`);
console.log(a);
```

**unshift()** [O(n)]

```js
var a = [];
var start = new Date();
for (var i = 0; i < 100000; i++) {
  a.unshift(i);
}
var end = new Date() - start;
console.log(`Unshift time: ${end}`);
console.log(a);
```

참조: [Time complexity of unshift() vs. push() in Javascript](https://stackoverflow.com/questions/12250697/time-complexity-of-unshift-vs-push-in-javascript)
