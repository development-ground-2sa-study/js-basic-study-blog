props는 컴포넌트에서 데이터를 다루고 전달하기 위해 사용된다.   
<br>
> **Props란?** 
 
* properties의 약자. 즉, 컴포넌트의 속성값

* props는 컴포넌트에서 컴포넌트로 전달하는 데이터.

* props는 해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서만 설정 가능.   

   prop의 값은 컴포넌트 내부에서 변경할 수 없고, 읽기 전용.
   
* 따라서, 부모 컴포넌트에서 자식 컴포넌트로 데이터를 전달할때 사용

<br> 

 
> **Props 지정하기** 
 

* props는 <ComponentName prop1={propValue1} prop2={propValue2} ... /> 형태로 컴포넌트를 부를 때 함께 지정

 
```
<Message name="John" age={40} />
<Message name="Jay" age={35} />
 ```

* 같은 타입의 컴포넌트에 다른 props 값을 주어서, 패턴이 반복되는 형태로 컴포넌트의 효율적인 재사용 가능.

* props에는 문자열, 불리언 (true, false), 숫자, 배열, 객체와 같은 다양한 형태의 데이터를 담을 수 있다.

  또한 함수도 전달할 수 있다.

* props에 있는 데이터는 문자열인 경우 제외 => 모두 중괄호 {} 로 값을 감싸야함.

 
<br>

> **props 사용하기** 
 

* props는 읽기 전용.

  컴포넌트 내부에서 props를 변경 X.

 
```
// Message 컴포넌트에서 props를 사용하여 name 값을 읽어옵니다.
const Message = (props) => {
  return <div>Hello, {props.name}!</div>;
};
``` 

'props.name'을 통해, 부모 컴포넌트에서 전달한 값을 사용할 수 있다.

따라서, Hello, John! 이 출력이 된다.

<br>
부모 컴포넌트에서 전달된 prop의 값이 변경되면,

React는 자동으로 해당 자식 컴포넌트를 리렌더링하여 변경된 prop의 값을 반영.

 
```
import React, { useState } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <ChildComponent count={count} />
      <button onClick={handleClick}>클릭</button>
    </div>
  );
}
``` 
``` 
function ChildComponent(props) {
  return (
    <div>
      <p>{props.count}</p>
    </div>
  );
}
``` 
<br>
부모 컴포넌트에 있는 'handleClick' 함수가 실행되어, 'count' 값을 증가시키는 코드다.

이렇게 증가시켜 변경된 'count'값이 자식 컴포넌트에 전달된다.


따라서, 부모 컴포넌트에서 전달된 prop값이 변경될때마다 자식 컴포넌트가 리렌더링 되어지는것을 볼 수 있다.

<br>

**문제점**

* 불필요한 리렌더링

  : 부모 컴포넌트의 리렌더링이 발생하면 자식 컴포넌트도 함께 리렌더링이 되어,

  의도치않게 리렌더링 되거나 성능 저하를 발생시킬 수 있다.
