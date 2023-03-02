
## 반복자 (iterator) 패턴이란

https://refactoring.guru/ko/design-patterns/iterator

어플리케이션 전체에서 어떤 도메인 모델의 순회가 일어나는 경우 코드의 중복을 줄일 수 있는 디자인 패턴
- 코드가 다른 데이터 구조들을 순회하길 원할 때
- 순회할 구조들의 타입 또는 정보를 미리 전부 알 수 없을 때 사용한다.

### 사용 방법

Iterator 인터페이스 선언. Iterator 인터페이스는 몇 가지 구성 요소를 가질 수 있다.
1. (필수) 컬렉션에서 다음 요소를 가져오는 `getNext()` 같은 메서드
2. (선택 사항) 컬렉션에서 전 요소를 가져오는 메서드
3. (선택 사항) 컬렉션에서 현재 위치를 추적하는 메서드
4. (선택 사항) iterator를 사용한 순회의 끝을 확인하는 메서드

iterator를 구현한 구현체를 통해 특정 객체에 대한 반복을 손 쉽게 할 수 있다는 장점이 있다.

### 장점

1. SRP : 그래프와 같은 순회 알고리즘 작성 시 순회 대상을 별도의 클래스로 추출하여 코드를 정리할 수 있다.
2. OCP : 신규 유형의 컬렉션과 Iterator들을 구현함으로써 신규 유형에 대한 순회도 쉽게 할 수 있다.
3. 동일 컬렉션 내 병렬 순회 가능 : 각각의 Iterator 객체 내에는 순회 중인 상태를 저장하기 때문에 큰 그래프의 탐색 같은 경우 다수의 worker에 순회를 위임시켜 결과를 병합할 수 있다.
4. 순회를 잠시 멈추거나 중개하는 등의 흐름 제어가 가능 

### 단점

1. 단순한 컬렉션에 대한 순회만 사용한다면 굳이?
2. Iterator 사용 시 때로는 직접 컬렉션의 요소를 순회하는 것보다 비효율적일 수 있음 (RB 트리 같은 경우?)

## Javascript의 Iteration Protocol

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols

자바스크립트는 객체들이 어떤 (커스텀) iteration 동작을 정의하는 것을 허용한다.

- 어떤 빌트인 타입들 (`Object`) 은 iteration이 불가능하지만
- 어떤 빌트인 타입들 (`Array`, `Map`)은 iteration이 가능하다..

차이는 무엇일까?

> Iterable 하기 위해서는 `@@iterator` 메서드를 구현해야 한다.

어떤 객체가 iterable하게 호출된다고 하는 것 == 객체의 `@@iterator` 메서드가 **인수 없이** 호출되고 , 반환된 iterator가 다시 반복을 통해 획득할 값들을 얻을 때 사용된다.

### 자세히 들여다 보면..

ES6 (ECMAScript 2015) 부터는 값들의 sequence를 만드는 표준 방법을 정의하고, 이를 iterator protocol라고 부른다.

객체가 `next()` 메서드를 가지고 있고, 아래 규칙에 따라 구현되었다면 그 객체는 iterator이다.
- `next()` : 아래 2개의 속성을 가진 `Object`를 반환하는 파라미터 없는 함수
	- `done` : boolean 타입을 리턴한다.
		- iterator가 마지막 반복 작업 마쳤을 경우 true 반환
		- iterator가 반환할 값이 아직 남아있다면 false 반환
		- `done` 을 정의하지 않은 경우 false
	- `value` : iterator로부터 반환되는 모든 자바스크립트의 값.
		- `done` 이 `true`인 경우 생략 가능

### 예시

자바스크립트의 `String` 타입은 빌트인 iterator 객체이다.
```js
let someStr = "hi";
typeof someStr[Symbol.iterator]; // "function"

let iterator = someStr[Symbol.iterator]();

iterator + ""; // "[object String Iterator]"
iterator.next(); // {value : "h", done: false}
iterator.next(); // {value : "i", done: false}
iterator.next(); // {value : undefined, done: true}
```

[Spread 연산자]() 또한 iteration protocol을 사용하기 때문에 다음과 같이 전개될 수 있다.

```js
// Create a generator function with multiple yields
function* generatorFunction() {
  yield 'Neo'
  yield 'Morpheus'
  yield 'Trinity'

  return 'The Oracle'
}

const generator = generatorFunction()

// Create an array from the values of a Generator object
const values = [...generator]

console.log(values) // (3) ["Neo", "Morpheus", "Trinity"]
```

## 자바스크립트의 제네레이터 함수

제네레이터 함수는 generator 객체를 반환하는 함수이다.

또한 몇 가지 특징을 가지고 있는데,
1. `function*` 으로 선언해서 사용한다.
2. 빠져나갔다가 나중에 다시 돌아올 수 있는 함수
	1. 이 때 함수 내부의 컨텍스트는 출입 과정에서 변경된 상태로 저장된다.
3. 호출되어도 즉시 시행되지 않고, 함수를 위한 iterator 객체가 반환
	1. 이 iterator의 `next()` 메서드가 호출되면 generator 함수가 실행
	2. `yield`문을 만날 때까지 진행
		1. `yield*` 표현식을 마주치면 다른 제네레이터 함수가 실행을 위임받는다.
	3. 해당 표현식이 명시하는 iterator의 반환 (`value`) 값을 리턴
	4. 다음 `next()` 메서드가 호출되면 멈췄던 위치에서부터 재실행

```js
function* idMaker(){
  var index = 0;
  while(index < 3)
    yield index++;
}

var gen = idMaker();

console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
console.log(gen.next().value); // undefined
```

아래 예제처럼 `yield*`를 사용해서 제네레이터 함수 안에서 다른 제네레이터로의 위임이 가능하다.
```js
function* anotherGenerator(i) {
  yield i + 1;
  yield i + 2;
  yield i + 3;
}

function* generator(i){
  yield i;
  yield* anotherGenerator(i);
  yield i + 10;
}

var gen = generator(10);

console.log(gen.next().value); // 10
console.log(gen.next().value); // 11
console.log(gen.next().value); // 12
console.log(gen.next().value); // 13
console.log(gen.next().value); // 20
```


### yield

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield

yield 키워드는 제너레이터 함수를 중지하거나 재개하는데 사용된다.
- 제네레이터 함수 안에서 `yield` 키워드를 사용한 곳들을 기준으로 코드가 잘려서 수행된다.
- 특정 처리까지 한 후에 잠시 멈췄다가 나중에 다시 해당 상태에 접근할 때 유용하게 사용할 수 있다.

### React의 Redux Saga에서의 제네레이터 함수의 사용

[Redux Saga란? - 벨로퍼트님 글](https://react.vlpt.us/redux-middleware/10-redux-saga.html)

> Redux-Saga란 액션을 모니터링하고 있다가, 특정 액션 발생 시 이에 따른 특정 작업을 실행한다.

```js
import { delay, put, takeEvery, takeLatest } from 'redux-saga/effects';

// 액션 타입
const INCREASE = 'INCREASE';
const DECREASE = 'DECREASE';
const INCREASE_ASYNC = 'INCREASE_ASYNC';
const DECREASE_ASYNC = 'DECREASE_ASYNC';

// 액션 생성 함수
export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });
export const increaseAsync = () => ({ type: INCREASE_ASYNC });
export const decreaseAsync = () => ({ type: DECREASE_ASYNC });

function* increaseSaga() {
  yield delay(1000); // 1초를 기다립니다.
  yield put(increase()); // put은 특정 액션을 디스패치 해줍니다.
}
function* decreaseSaga() {
  yield delay(1000); // 1초를 기다립니다.
  yield put(decrease()); // put은 특정 액션을 디스패치 해줍니다.
}

export function* counterSaga() {
  yield takeEvery(INCREASE_ASYNC, increaseSaga); 
  // 모든 INCREASE_ASYNC 액션을 처리
  yield takeLatest(DECREASE_ASYNC, decreaseSaga); 
  // 가장 마지막으로 디스패치된 DECREASE_ASYNC 액션만을 처리
}

// 초깃값 (상태가 객체가 아니라 그냥 숫자여도 상관 없습니다.)
const initialState = 0;

export default function counter(state = initialState, action) {
  switch (action.type) {
    case INCREASE:
      return state + 1;
    case DECREASE:
      return state - 1;
    default:
      return state;
  }
}
```


https://caileb.tistory.com/197

Redux Saga/effects 에서 제공되는 함수들 (`call`, `put`)을 사용한다면
- `function*` 함수를 호출 할 때 `next()` 가 아닌 액션을 통해 호출한다.
- 리액트에서는 액션을 통해 `function*` 함수를 한번 호출하고 난 후에
	- 내부에 있는 여러 `yield` 함수를 차례로 호출한다.

Redux Saga의 `put` 함수를 통해 새로운 액션을 디스패치하여 비동기로 실행할 수 있다.

- `all` 함수는 redux에서 비동기 처리가 필요한 함수들을 배열 형태로 전달, 동시에 병행으로 처리를 수행한다.
- `takeEvery` 함수는 특정 액션 타입에 대해 디스패치되는 모든 액션을 처리한다.
- `takeLatest` 함수는 특정 액션 타입에 대해 디스패치된 가장 마지막 액션만을 처리한다.
	- 즉 지정한 액션이 실행되는 동안 동일 타입의 새 액션이 디스패치되면 기존에 하던 작업은 무시하고 새 작업 시작 (따닥 방지 등)

스프링의 AOP과도 개념이 약간 비슷한듯..?

## 왜 제네레이터 + yield 인가

모던 웹 트렌드 : 끊기지 않는 데이터 스트림의 활용이 사용자들에게 보다 깊은 인상을 줌 (주관적인 생각)

제네레이터 등장 이전에 무한한 데이터 스트림을 처리하려면..? 반복 어케하죠? (무한 스크롤, 연속된 음성 재생 등)

promise / async / await 사용해서 API 호출 시 페이지네이션을 적용할 수 있지만.. 패러다임의 불일치 때문에 뭔가 자연스럽지 않다!

### 제네레이터 함수의 장점

Lazy Eval : 표현식의 실행을 실제로 필요할 때까지 미룰 수 있다.
- 이에 따른 메모리 효율성 증대 : 보다 빠른 코드의 실행. 브라우저 최적화 -> UX 개선

데이터 패러다임의 불일치를 어느정도 해결
- Collection / Stream 타입 데이터를 분리해서 처리해보자!
- 제네레이터 함수를 사용해서 컬렉션 타입의 데이터도 처리할 수 있지만..
- Stream 타입 데이터를 처리할 때 비로소 진가가 나타난다

[제네레이터를 사용한 무한 스크롤 구현](https://ashishshubham.medium.com/infinite-scroll-using-generators-bbacbf6cafd9) 

### 무한 스크롤이 가지는 장점 

https://www.nngroup.com/articles/infinite-scrolling-tips/

1. 인터럽션 감소 : 사용자 입장에서 잠깐의 인터럽션을 줄임으로써 잔류 시간을 늘리는데에 큰 역할
2. 인터랙션 비용 감소 : 페이지네이션을 사용했을 때 버튼을 클릭하는 등의 부가적인 인터랙션 비용을 줄여, 사용자 입장에서 좀 더 덜 피곤한 웹 페이지를 만들 수 있다.
3. 모바일 기기에서의 활약 : 모바일 환경에서 스크롤은 가장 빈번하게 일어나는 인터랙션. 사용자들은 이미 스크롤을 하고 있기 때문에 무한 스크롤 지원 시 모바일에서의 UX가 가장 크게 개선된다

### 무한 스크롤이 가지는 단점

1. 한번 봤던 내용을 다시 보기 어려움 : 내용을 다시 찾으려 할 때 다시 스크롤을 올려야 함
2. 페이지의 끝에 도달하기 어려움 : 사용자 입장에서 컨텐츠가 끝났다는 것을 인지하기 어렵다
3. Footer 접근 어려움 : 무한 스크롤 사용 시 사용자에게는 Footer가 노출되기 어렵다
4. 접근성 문제 : 키보드만 사용하는 (vim만 사용한다거나) 사용자들에게는 너무 불편하다
5. 페이지 로드 증가 : 더 많은 정보를 불러와야 하기 때문에, 정보 전달이 지연된다는 느낌을 줄 수 있다.
6. SEO 성능 감소 : 무한 스크롤은 SEO 성능을 감소시킨다. 검색 엔진은 첫번째 섹션 다음의 컨텐츠에 항상 접근할 수 있는 것이 아니기 때문에, 노출 수가 중요한 페이지의 경우 사용을 고민해야


