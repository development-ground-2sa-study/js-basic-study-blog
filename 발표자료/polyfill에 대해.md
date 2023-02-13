자바스크립트 === 언어 명세이고, 자바스크립트 구현체는 각 브라우저 엔진에 따라 완전히 똑같은 동작을 보장해주지는 않는다.

그렇다면, 브라우저 위에서 동작하는 자바스크립트 코드가 특정 브라우저 위에서만 동작하는 코드를 만나게 되면 어떻게 될까?

https://www.programmerinterview.com/html5/html5-polyfill/

> polyfill은 현대 브라우저에서, 지원하는 특정 기능을 다른 브라우저에서도 지원하기 위해서 사용된다.

## forward-compability

Polyfilling은 구형 브라우저의 호환성 뿐만 아니라 신규 브라우저에 대한 호환성을 위해서도 적용되는 개념이다.

신규 브라우저들이라고 HTML5 표준의 모든 기능들을 구현한 채로 공개되는 것이 아니기 때문이다.

HTML5 어플리케이션을 작성할 때 대중적인 브라우저에서 지원하지 않는 HTML5 피쳐를 구현한다면 polyfill을 사용해야만 한다.

## polyfill은 HTML5 표준일까?

polyfill은 특정 브라우저에서만 동작하는 코드를 설명하기 위해 사용되는 비공식 용어이다. 

**polyfill 자체가 HTML5 표준이 아님에 유의해야 한다!**

polyfill은 HTML5에서만 사용할 필요가 없고, CSS3에 대한 호환을 위해 일부 CSS 코드에도 사용할 수 있기 떄문이다.

## polyfill의 어원

polyfill의 어원은 구멍나고, 금이 간 벽에 덧칠을 해서 메우는 것에서 유래했다고 한다.

브라우저 === 벽
polyfill === 브라우저를 다른 브라우저처럼 보이게 하는 덧칠

### 자바스크립트 밖에서의 polyfill

자바스크립트가 polyfill을 주로 사용하는 기술이긴 하지만, polyfill이 자바스크립트에만 국한된 것은 아니다.

poly === many를 뜻하고,
many는 다시 수많은, 서로 다른 기술들에서 사용될 수 있음을 의미한다.

## polyfill의 예시

특정 유저 세션에 대한 데이터를 저장하는 HTML5 표준 프로퍼티인 `sessionStorage` 가 있다고 가정하고, 이 프로퍼티가 브라우저 내에서 네이티브하게 작동하는지 확인하기 위해서는 아래와 같은 코드를 작성할 수 있다.

```javascript
typeof window.sessionStorage !== 'undefined'
```

하지만 구형 파이어폭스 브라우저에서는 상기 코드가 실행되었을 때 exception을 발생시킨다. 이러한 exception을 피하기 위해서 아래와 같이 '체크 후에' polyfill을 사용할 수 있다.

```javascript
var isThereSessionStorage = (function() {
  try {
    return typeof window.sessionStorage !== 'undefined';
  } catch (e) {
    return false;
  }
})(); 

if(!isThereSessionStorage)
// sessionStorage를 구현하는 polyfill 코드
```