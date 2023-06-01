XSS는 가장 오래되었지만 또 현재에도 가장 간단하게 웹 어플리케이션에 가해질 수 있는 공격 기법 중 하나이다.

고등학생 때에도 XSS 실습을 해봤었지만, 현재에도 여전히 유효한 공격 방법 중 하나인 XSS의 종류와 대응 방안을 알아보자.

## XSS란

https://owasp.org/www-community/attacks/xss/

XSS는 크로스 사이트 스크립팅 공격의 줄임말이다.

XSS는 인젝션 공격 중 하나로, 악의적인 스크립트가 상대적으로 신뢰성 있는 웹 사이트들에서 실행되어 웹 서비스를 정상적으로 사용하는 다른 사용자들이 공격자가 심어놓은 악의적인 스크립트를 실행하게 되는 공격이다. 

- 때문에 공격자는 다수의 익명의 사용자를 대상으로 공격을 실행할 수 있다.
- 피해자는 스크립트가 실행되는지 자체를 눈치 못챌 수 있다. (상대적으로 익숙한 웹 사이트를 방문하므로)
- 공격자는 피해자의 다양한 정보를 갈취할 수 있다. (쿠키, 세션, 토큰과 같은 계정 정보를 포함하는 다른 민감한 정보도 가능하다)

## XSS의 종류

종류에는 어떤 것들이 있을까?

https://owasp.org/www-community/Types_of_Cross-Site_Scripting

### Reflected XSS

파라미터 쿼리에 스크립트를 삽입해서 컨텐츠를 열람하는 사용자가 의도하지 않은 스크립트가 실행되도록 하는 공격 방법이다.

가능한 공격 시나리오로는..
1. 공격자가 피해자에게 Reflected XSS를 실행할 웹 페이지의 쿼리 파라미터에 공격 스크립트가 포함된 링크를 포함해서 보낸다. (가령 `http://somepage.com/notice/1?page=<script>alert(1)</script>` 과 같은 링크)
2. 피해자는 링크에 접속하고, 쿼리 파라미터를 웹 페이지에서 그대로 보여주는 취약한 웹 사이트는 공격자가 사전에 링크에 심어놓은 스크립트를 그대로 실행한다.
3. 피해자는 본인의 환경에서 의도치 않게 실행된 공격자의 스크립트에 의해 피해를 입는다.

> Reflected XSS는 URL을 통해 웹 페이지에 보여지는 데이터를 직접 조작할 수 있기 때문에 발생한다. (query param, path variable 등..)

최근에는 피싱 사이트를 만들어서 피해를 끼치는 것이 트렌드라면 예전에는 이런 형태의 공격이 더 흔했다. (최근에는 공격자의 개발 생산성도 많이 증가했기 때문인가? ㅋㅋ)

물론 피해자의 입장에서는 알 수 없는 링크를 클릭하지 않는 것이 가장 좋지만, 웹 사이트를 설계하고 개발하는 입장에서는 취약한 웹 사이트를 만들지 않게 해서 우리 사이트를 이용하는 고객들이 억울하게 피해를 입지 않게 하는 것이 개발자의 책임이다.

Reflected XSS는 non persistent한 XSS 공격이다. 공격이 단일 요청 - 응답의 사이클에 기인해서 일어나기 때문이다.

## Stored XSS

공격자가 입력한 스크립트가 DB에 저장되고, 저장된 컨텐츠를 열람하는 사용자가 의도치 않은 스크립트가 실행되도록 하는 공격 방법이다.

가능한 공격 시나리오로는..
1. 공격자가 회원가입과 같이 DB에 저장되는 로직을 타는 api에 스크립트를 삽입한 데이터를 포함하여 전송한다.
2. 공격자가 전송한 데이터가 서버를 거쳐 DB에 저장된다.
3. 피해자가 정상적으로 공격자가 삽입한 데이터를 열람하려 할 때 삽입된 스크립트가 불러와지며 사용자의 환경에서 실행되는 스크립트에 의해 피해를 입는다.

> Stored XSS는 사용자가 입력한 데이터를 바로 DB에 저장하고, DB에 저장한 데이터를 바로 불러오기 때문에 발생한다.

즉 전송되는 데이터가 어차피 DB에 저장되더라도 최소한 이에 대한 방어 로직이 어딘가 한 군데에는 존재해야 한다.

저장된 데이터가 계속 존재하는 한 사용자가 서버로 요청을 할 때마다 공격이 발생하기 때문에 Stored XSS는 Persistent XSS라고 불리기도 한다.

## DOM based XSS

Reflected XSS와 Stored XSS 공격은 페이로드가 서버 쪽 애플리케이션의 취약점 때문에 발생하는 반면 DOM based XSS는 순수하게 클라이언트 사이드에 존재하는 취약점 때문에 발생한다.

DOM based XSS는 공격자에 의해 HTTP 응답으로 받아온 페이지 자체는 변하지 않지만, 페이지에 포함된 클라이언트 사이드의 코드가 삽입된 DOM 환경에 의해 악의적으로 변화하여 동작하는 공격이다.

https://owasp.org/www-community/attacks/DOM_Based_XSS

간단한 다국어로 열람이 가능한 게시판 예시를 생각해보자. 쿼리 파라미터로 `default` 기본 언어 설정을 하도록 하여 많은 사람들이 해당 게시판을 사용하고 있다고 가정해보자. 아래와 같은 코드를 통해 기본 언어를 선택하는 코드를 작성하였다.

```js
// Select your language
<script>
document.write("<OPTION value=1>"+decodeURIComponent(document.location.href.substring(document.location.href.indexOf("default=")+8))+"</OPTION>");

document.write("<OPTION value=2>English</OPTION>");
</script>
```

선량한 사람들은 `http://somepage.com/page.html?default=English`와 같이 기본 언어를 설정하여 게시판을 사용한다.

공격자는 피해자에게 `http://somepage.com/page.html?default=<script>alert(document.cookie</script>`와 같은 링크를 전송하고, 피해자는 링크에 접속하였을 때 DOM Based XSS가 이뤄진다.

서버에 공격이 이뤄지면 보다 탐지하기 쉽지만, DOM based XSS는 서버에 공격 여부를 전달하지 않기 위해서 꼼수를 쓸 수도 있다.

URI 뒤에 `#` 프래그먼트를 붙이면 브라우저는 페이로드를 서버로 전송하지 않는다. 때문에 서버에서는 공격이 일어난 사실을 탐지할 수 없게 된다. 

## XSS를 막아보자

XSS 자체가 워낙 전통적인 공격 방법이다 보니 현대의 프레임워크 레벨에서도 막아주기도 한다. 앵귤러 컴포넌트 내부에서 `<script>` 태그의 삽입 자체는 불가능하다. (아마 뷰나 리액트도 비슷한 기능이 있을지도?)

하지만 이런 프레임워크 레벨의 방어만 믿고 개발을 하다 보면 소홀해지는 요소가 있기 마련이다. 그 예시로는
- 이스케이프 문자열을 통해 직접 DOM을 조작하는 코드를 작성한다던지
- 리액트의 `dangerouslySetInnerHTML`을 검증 없이 사용한다던지 (innerHTML을 직접적으로 조작하는 행위는 웬만하면 하지 않는 것이 좋다)
- 리액트에서 `javascript:`나 `data:` URL을 검증 없이 사용한다던지
- 앵귤러의 `bypassSecurityTrustAs*` 함수들을 사용한다던지
- 템플릿을 삽입하는 기능을 검증 없이 만든다거나
- deprecated된 프레임워크 플러그인이나 컴포넌트를 사용한다던지

개발자의 부주의 때문에 발생할 수 있는 취약점이지만, XSS는 대처법이 다른 취약점들에 비해 상대적으로 간단한 편이다.

XSS가 무엇인가? 공격자가 입력한 (스크립트가 포함된) 데이터를 그대로 실행하게 되어서 발생하는 공격이다.

때문에 Reflected XSS와 Stored XSS를 방어하기 위해서는 입력을 받는 모든 변수에 대해서 검증을 완벽히 수행하는 것이 대응 방법이다. 구체적으로는..
1. 검증을 대신 수행해주는 라이브러리를 사용한다던지
   1. 프로젝트 의존성이 복잡해지긴 하지만 비즈니스 로직의 수정 없이 대응할 수 있긴 하다
2. HTML 어트리뷰트, js, css 컨텍스트들에 대해 인코딩 치환을 사용한다던지
   1. 특히 js 내부에서 변수를 위치시킬 수 있는 안전한 위치는 따옴표로 묶인 데이터 값 내부임에 유의하자. (`'someVariable'`)
3. URL에 파라미터를 urlencode해서 a 태그를 통해 검증 없이 이동시키는 것은 절대 하지 말아야 할 실수이다.
   1. 정 필요한 로직이라면 최소한 `window.encodeURIComponent(url)`을 사용해야 한다.
4. 안전한 HTML 싱크만 사용한다. 안전한 태그들은 변수들을 텍스트로 인식하여 실행하지 않는다.
   1. `innerHTML`과 같이 변수들을 실행할 수 있는 싱크들을 사용하지 말아야 한다.

### Based XSS를 방어하려면 어떻게 해야 할까?

#### 실행 컨텍스트 내에서 신뢰할 수 없는 데이터를 HTML 하위 컨텍스트에 삽입하기 전 HTML 이스케이프 + 자바스크립트 이스케이프 사용

어려운 말 같지만 사실 `innerHTML`, `outerHTML` 애트리뷰트를 적용할 때나 `<html>` 태그를 삽입할 때 이스케이프를 하라는 말이다.

나쁜 예
```js
element.innerHTML = "<HTML> Tags and markup";
element.outerHTML = "<HTML> Tags and markup";

document.write("<HTML> Tags and markup");
document.writeln("<HTML> Tags and markup");
```

좋은 예
```js
var ESAPI = require('node-esapi');
element.innerHTML = "<%=ESAPI.encoder().encodeForJavascript(ESAPI.encoder().encodeForHTML(untrustedData))%>";
element.outerHTML = "<%=ESAPI.encoder().encodeForJavascript(ESAPI.encoder().encodeForHTML(untrustedData))%>";
document.write("<%=ESAPI.encoder().encodeForJavascript(ESAPI.encoder().encodeForHTML(untrustedData))%>");
document.writeln("<%=ESAPI.encoder().encodeForJavascript(ESAPI.encoder().encodeForHTML(untrustedData))%>");
```

#### 실행 컨텍스트 내에서 신뢰할 수 없는 데이터를 HTML 애트리뷰트 하위 컨텍스트에 삽입하기 전에 자바스크립트 이스케이프 

js의 실행 컨텍스트 내에서 html 엘리먼트를 생성하고, 애트리뷰트를 설정할 때 이스케이프하라는 말이다.

좋은 예
```js
var ESAPI = require('node-esapi');
var x = document.createElement("input");
x.setAttribute("name", "company_name");
x.setAttribute("value", '<%=ESAPI.encoder().encodeForJavascript(companyName)%>');
var form1 = document.forms[0];
form1.appendChild(x);
```

#### 실행 컨텍스트 내에서 이벤트 핸들러 및 자바스크립트 코드 하위 컨텍스트에 신뢰할 수 없는 데이터 삽입 시 주의하기

js 인코딩은 다른 인코딩과 비교하였을 때 인코딩된 데이터의 의미가 다르기 때문에 (브라우저가 이를 해석하기 때문에)( js 코드 내에 동적으로 데이터를 넣을 때 조금 더 주의해야 할 필요가 있다.

많은 경우에 자바스크립트 인코딩은 실행 컨텍스트 내에서 이뤄지는 공격을 막지는 못한다. 인코딩된 자바스크립트 문자열 또한 실행되는 경우가 있다.

때문에 XSS를 피하기 위해서는 동적으로 입력을 받는 데이터는 전부 신뢰하지 않는 것이 포인트이다.

```js
var x = document.createElement("a");
x.href="#";
x.setAttribute("onclick", "\u0061\u006c\u0065\u0072\u0074\u0028\u0032\u0032\u0029");
var y = document.createTextNode("Click To Test");
x.appendChild(y);
document.body.appendChild(x);
```

https://dencode.com/ 에서 위 코드를 decode 해보면 `alert(22)`를 x의 애트리뷰트로 설정한 것을 알 수 있고, `setAttribute` 메서드는 암묵적으로 두 번째 인자를 DOM 애트리뷰트 데이터 타입으로 강제한다.

위 예제에서 애트리뷰트 이름은 자바스크립트 이벤트 핸들러이기 때문에 애트리뷰트 값 자체는 암묵적으로 자바스크립트 코드로 변환되어 eval된다. 즉 위 예제에서 자바스크립트 인코딩은 DOM based XSS를 막지 못한다.

```js
for(var \u0062=0; \u0062 < 10; \u0062++){
    \u0064\u006f\u0063\u0075\u006d\u0065\u006e\u0074
    .\u0077\u0072\u0069\u0074\u0065\u006c\u006e
    ("\u0048\u0065\u006c\u006c\u006f\u0020\u0057\u006f\u0072\u006c\u0064");
}
\u0077\u0069\u006e\u0064\u006f\u0077
.\u0065\u0076\u0061\u006c
\u0064\u006f\u0063\u0075\u006d\u0065\u006e\u0074
.\u0077\u0072\u0069\u0074\u0065(111111111);
```

위 코드 또한 유효한 자바스크립트 코드이며, 실행이 가능하다. (해보자)

자바스크립트는 국제 표준이고, (ECMAScript) 자바스크립트 인코딩은 (한글을 포함한) 다른 언어의 인코딩을 지원하기 때문에 이런 일이 발생한다.

HTML 인코딩은 어떨까? HTML 태그 엘리먼트들은 잘 정의되어 있고 동일한 태그에 대해 여러 표현이 필요하지 않기 때문에 HTML 인코딩은 `<a>` 태그에 대한 대체 표현이 존재하지 않는다.

```html
<a href="..." > // 이건
&#x3c;a href=... &#x3e; // 이것과 다르다.. 요놈은 동작하지 않는다.
```

## 요약해보자

내용이 너무 많다..! ㅎㅎ 직접 owasp.org에 방문해서 xss 관련 글은 한 번쯤은 쭉 정독해보는 것을 권한다. 

그러나 요약하기는 어렵진 않을 것 같다. 
- XSS는 기본적으로 입력 받은 데이터를 신뢰하는 개발자의 오만한 행위로부터 생길 수 있다.
- 때문에 서버 사이드던 클라이언트 사이드던 입력 받은 데이터를 신뢰하지 않고 검증하는 과정이 필요하다.
- 입력 받은 데이터에 대한 이스케이프 처리는 기본적으로 해주자. (많은 모던 라이브러리 / 프레임워크에서 기본적으로 지원하는 추세긴 하지만)
- 이스케이프를 별도로 하지 않는다면 정규식 등을 통해 엄격한 검사를 수행하자.
