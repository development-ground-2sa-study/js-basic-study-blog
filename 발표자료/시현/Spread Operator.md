> Spread Operator(스프레드연산자)는 ES6 문법이며, ... (점3개)를 사용합니다.  

스프레드연산자를 잘 익혀두면 배열이나 객체 전체 부분이나 일부분을 쉽게 복사할 수 있다는 점이 큰 장점입니다.

<br>

- (배열) 괄호제거를 해줍니다.
   
```js
let array = ['hi','hello'];
console.log(array);
// ['hi', 'hello']
console.log(...array);
// hi hello
```
- (문자열) 문자를 하나씩 펼쳐줍니다.
```js
let a = 'hi';
console.log(a);
// hi
console.log(...a);
// h i
```
<br>

### 활용방법

**1. 배열 (Array) 에서의 Spread Operator**

- array 합치기
```js
let a = [1,2,3];
let b = [4,5];
let c = [...a, ...b];
console.log(c);
// [1, 2, 3, 4, 5]
```

- array 복사    
array(배열), object(객체) 즉 **reference data type (참조타입)** 인 경우에 변수를 복사하게 되면 동일한 값을 공유합니다.  
(*) 참조타입에 대해 좀 더 알고싶다면 [이 사이트](https://velog.io/@surim014/JavaScript-Primitive-Type-vs-Reference-Type)를 참고해주세요

```js
let a = [1,2,3];
let b = a;

console.log(a);
console.log(b);
```
[1, 2, 3] 으로 똑같은 값을 반환합니다.   
하지만, 이렇게 b = a 등호를 이용해 복사를 하면 안됩니다!   
a에 값을 추가하거나 제거했을때, b의 값도 같이 바뀌게 되기 때문입니다.   

따라서, 값을 공유하지 않고 각각 독립적인 값을 저장하도록 복사하려면 spread 연산자를 사용하면 됩니다.   
```js
let a = [1,2,3];
let b = [...a];
```
이렇게 sparead 연산자를 사용하게 되면은  
a값을 바꾸거나 해도 b의 값은 변하지 않게 됩니다.  

<br>

**2. 객체 (Object) 에서의 Spread Operator**
- Object 합치기

```js
let a = { name: '이름', species: '종'};
a = { ...a, age: '나이'}; 

console.log(a)
// {name: '이름', species: '종', age: '나이'}
```

+) 객체의 key값이 중복되면?  
ex)  
```js
let a = {name: '이름', species: '종'};
let b = {name: '자바', ...a};
console.log(b);
// {name: '이름', species: '종'}
```

name이라는 값이 중복이 발생하면 **무조건 뒤에 오는 name**이 출력됩니다.   

(주의점)  
```js
...a
```
이런식으로 그냥 쓰면 안됩니다.     
함수 소괄호나 object 중괄호, array 대괄호 안에서 사용해야합니다.   

<br>

**3. array를 파라미터형태로 집어넣을때**

```js
function add(a,b,c,d) {
  console.log(a+b+c+d);
}

let arr = [5,6,7,8];
```

Q. arr 값이 주어졌을때, arr[0] ~ arr[3]의 값을 a,b,c,d 파라미터 값으로 넣고 싶다면?   
```js
add(arr[0],arr[1],arr[2],arr[3]);
```

이때, spread 연산자를 사용하면 더 간편하고 깔끔하게 작성할 수 있습니다.  
```js
add(...arr);
// 26
```
<br>

cf) 스프레드연산자 잘 사용한 문제풀이 예시 : [링크](https://github.com/sihyun10/Programmers/commit/13e56a7e52a2d081f641f1a086969c30b6c66f21?short_path=7ba4904#diff-7ba4904942cbfea636960f1fefd4c5947e8d87d4df1ddb903f28a8b0813e7dcc)
