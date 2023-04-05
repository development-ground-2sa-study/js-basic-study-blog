자바스크립트에서 정말 선택받은 몇몇을 제외하고 모든 것은 객체이다. 

그럼 익명의 객체가 어떤 타입인지 알기 위해서는 어떤 방법을 사용할 수 있을까?

## typeof

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/typeof

`typeof object` 형태로 사용한다.
`typeof`의 반환 값은 사전에 정의된다.
type | result
--- | ---
Undefined | "undefined"
Null | "object"
Boolean | "boolean"
Number | "number"
BigInt | "bigint"
String | "string"
Symbol | "symbol"
Function | "function"
다른 모든 객체 | "object"

### typeof는 언제 사용할까

객체의 undefined 검사에서 사용될 수 있다. `if (typeof object === 'undefined')` 처럼..
자바스크립트의 변수가 어떤 값을 가지는지 모호할 때 사용한다 (`var` 키워드 등)

`typeof`를 사용해서 nullish 체크를 할 수 있지만 옵셔널 체이닝과는 완전히 다른 개념이다.

### 왜 null은 object type일까?

주의해야 할 점은, `typeof null`은 항상 "object"로 반환된다는 점이다.
MDN 설명에 따르면..

> 자바스크립트가 처음 구현될 때 자바스크립트의 값은 타입 태그와 값으로 표시되었습니다.
> 
> object의 타입 태그는 0이었고, `null`은 Null Pointer (0x00)으로 표기되었습니다.
> 
> 그 결과 null은 타입 태그로 0을 가지고, `typeof null`은 object를 반환합니다.

변경 요청이 거절되었는데, 거절된 사유도 그럴듯하다.

>  This proposal has been rejected. It was implemented in V8 but it turned out that it broke a lot of existing sites. In the spirit of One JavaScript this is not feasible.
>
> V8엔진에서 구현되었지만 기존 사이트들이 정상적으로 작동하지 않는 원인이 되었고, 이는 하나의 자바스크립트 정신에 위배되기 때문에 기각한다.

typescript에서도 `typeof null`은 "object"라고 표시된다.

내가 본 자바스크립트의 기능 중 최악이다 ㅋㅋ
```javascript
var myNullVar = null;
alert(typeof myNullVar); // alerts the string "object"
alert(myNullVar  instanceof Object); // alerts "false"
```

## instanceof

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/instanceof

> `instanceof` 연산자는 생성자의 `prototype` 속성이 객체의 프로토타입 체인 내에 존재하는지 판별한다.

```javascript
// 생성자 정의
function C(){}
function D(){}

var o = new C();

// true, 왜냐하면 Object.getPrototypeOf(o) === C.prototype
o instanceof C;

// false, 왜냐하면 D.prototype이 o 객체의 프로토타입 체인에 없음
o instanceof D;

o instanceof Object; // true, 왜냐하면
C.prototype instanceof Object // true

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

// false, 왜냐하면 C.prototype이
// 더 이상 o의 프로토타입 체인에 없음
o instanceof C;

D.prototype = new C(); // C를 D의 [[Prototype]] 링크로 추가
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true, 왜냐하면 이제 C.prototype이 o3의 프로토타입 체인에 존재
```

`String`과 `Date` 객체도 `Object` 타입이기 때문에 `typeof`를 사용해서는 제대로 된 객체 판별이 불가능하다.

```javascript
var simpleStr = "This is a simple string";
var myString  = new String();
var newStr    = new String("String created with constructor");
var myDate    = new Date();
var myObj     = {};

simpleStr instanceof String; // returns false, prototype chain을 확인하고, undefined를 찾는다.
myString  instanceof String; // returns true
newStr    instanceof String; // returns true
myString  instanceof Object; // returns true

myObj instanceof Object;    // returns true, undefined prototype 임에도 불구하고 true.
({})  instanceof Object;    // returns true, 위의 경우와 동일.

myString instanceof Date;   // returns false

myDate instanceof Date;     // returns true
myDate instanceof Object;   // returns true
myDate instanceof String;   // returns false
```

## 언제, 무엇을 사용해야 할까?

`typeof`와 `instanceof` 모두 객체가 '어떤 타입인지' 알려주는 연산자이다.

`typeof`는 unary operator이고, `instanceof`는 binary operator인 것 이외에 차이점이 있을까?
- 사실 unary vs binary 차이가 크긴 하다
- unary는 코드 작성 시점에 어떤 내용이 들어올 지 모를 때 사용하고
- binary는 코드 작성 시점에 어떤 내용이 대충 들어올지 아는 상태에서 정말 이 타입의 인스턴스가 맞나? 체크

https://stackoverflow.com/questions/899574/what-is-the-difference-between-typeof-and-instanceof-and-when-should-one-be-used

### 1. 커스텀 타입에 대해서는 `instanceof`를 사용

```javascript
var ClassFirst = function () {};
var ClassSecond = function () {};
var instance = new ClassFirst();
typeof instance; // object
typeof instance == 'ClassFirst'; // false
instance instanceof Object; // true
instance instanceof ClassFirst; // true
instance instanceof ClassSecond; // false 
```

### 2. 간단한 빌트인 타입에 대해서는 `typeof`를 사용

```javascript
'example string' instanceof String; // false
typeof 'example string' == 'string'; // true

'example string' instanceof Object; // false
typeof 'example string' == 'object'; // false

true instanceof Boolean; // false
typeof true == 'boolean'; // true

99.99 instanceof Number; // false
typeof 99.99 == 'number'; // true

function() {} instanceof Function; // true
typeof function() {} == 'function'; // true
```

### 3. 복잡한 빌트입 타입에 대해서는 `instanceof`를 사용

```javascript
/regularexpression/ instanceof RegExp; // true
typeof /regularexpression/; // object

[] instanceof Array; // true
typeof []; //object

{} instanceof Object; // true
typeof {}; // object
```

### 상세 비교

`instanceof` 연산자는 객체의 prototype 속성의 constructor에 대한 테스트를 진행한다.

때문에 `instanceof` 는 객체 타입에만 적용할 수 있다. 

대부분의 경우, `instanceof someObject` 가 true이면 객체가 해당 생성자를 통해 생성되었거나 그 서브클래스의 생성자를 통해 생성되었다는 것을 의미한다.

하지만 프로토타입은 `Object.setPrototypeOf()` 를 통해서 명시적으로 설정될 수 있기 때문에 항상 정확한 것은 아니다. (물론 성능때문에 권장되는 행동은 아니다)

대부분의 경우에는 생성자를 사용해서 string이나 number를 생성하지 않는다. 이 경우에는 좀 헷갈리지만 주의해서 사용해야 한다.

```javascript
let num = new Number(5);
console.log(num instanceof Number); // print true
console.log(typeof num); // print object
num++; //num is object right now but still can be handled as number
//and after that:
console.log(num instanceof Number); // print false
console.log(typeof num); // print number
```

개인적으로는 나도 `instanceof`를 선호하는데.. string 간 비교는 깔끔하지 않다고 생각이 들기 때문이다.

하지만 `typeof`를 사용해야만 하는 케이스가 좀 있어 그 경우를 기억해보도록 해야 겠다. 

별개로 `instanceof`는 또한 동일한 윈도우 안에 있는 객체들 간에서만 사용할 수 있다는 단점도 존재한다.
- iframe 이나 iframe을 사용한 팝업 윈도우의 경우에는 별개의 윈도우로 인식된다.
- 때문에 서로 다른 iframe 안에서 `instanceof`를 호출할 경우 정상적으로 작동하지 않는다.
- 하지만 `typeof`는 모든 케이스에서 동일한 결과를 리턴할 것을 보장하기 때문에 좀 더 안전하다.

### typescript를 쓰면 되지 않느냐?

typescript는 컴파일 시점에 타입에 대한 체킹을 강제하는 기능이 더해진 자바스크립트라고 생각한다.
- 자바의 제네릭에 대한 정보가 컴파일 시점에 지워지는 것처럼, 타입스크립트 또한 컴파일 이후에는 자바스크립트가 되기 때문에 타입에 대한 오류는 검사하지 않는다.

safe coding을 하기 위해서는 여전히 런타임에 인스턴스의 타입 정보를 가져와야 할 일이 있기 때문에 사용할 필요가 없다는 말은 성립하지 않는다.

