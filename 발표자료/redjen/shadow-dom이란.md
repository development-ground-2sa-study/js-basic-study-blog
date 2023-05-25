
뷰 캡슐화를 위해 꼭 필요한 개념 중 하나인 shadow DOM은 무엇일까?

https://developer.mozilla.org/ko/docs/Web/API/Web_components/Using_shadow_DOM

## 목적

웹 컴포넌트의 가장 중요한 측면은 캡슐화이다. 
캡슐화를 통해 마크업 구조, 스타일, 동작을 숨기고 페이지의 다른 코드와 분리하여 서로 다른 컴포넌트와 충돌하는 일이 없도록 하고, 클린한 코드를 유지할 수 있다.

>Shadow DOM API는 캡슐화의 핵심 파트이고, '숨겨진, 분리된' 각각의 DOM을 요소에 부착하는 방법을 제공한다.

## 핵심

shadow DOM은 숨겨진 DOM 트리가 통상적인 DOM 트리에 속한 요소에 부착될 수 있도록 한다.
- shadow DOM 트리는 shadow root로부터 시작한다.
- shadow DOM 트리는 원하는 모든 element 안에 부착할 수 있다.
- shadow DOM 트리를 부착하는 방법은 일반 DOM을 부탁하는 방법과 동일하다.

https://developer.mozilla.org/ko/docs/Web/API/Web_components/Using_shadow_DOM/shadowdom.svg

## 용어 정리

- shadow host : shadow DOM이 부착되는 DOM 노드
- shadow tree : shadow DOM 내부의 DOM 트리
- shadow boundary : shadow DOM이 끝나고, 우리가 아는 통상적인 DOM이 시작되는 경계
- shadow root : shadow 트리의 root 노드

## 특징

### 일반 DOM 트리와 똑같아요 

shadow DOM 노드에 대해, shadow가 아닌 DOM 트리의 노드에 가할 수 있는 똑같은 작업을 수행할 수 있다.
- append children
- property set
- `element.style` 정의를 통한 노드 꾸미기
- `<style>` 엘리먼트 내부에 있는 전체 shadow DOM 트리에 스타일 추가

### 일반 DOM 트리와는 이게 조금 달라요

shadow DOM 내부의 코드 중 어떤 것도 shadow DOM 외부의 모든 것에 영향을 줄 수는 없다.

즉 shadow DOM을 최소 단위의 컴포넌트로 구성하게 되면 캡슐화를 편리하게 달성할 수 있다.

### 흔하게 접할 수 있는 예시로는?

`<video>` 태그를 생각해보자.

DOM 내부에서 보이는 모든 것은 `<video>` 엘리먼트이지만,
- 일련의 버튼들과는 완전히 다른 컨트롤 패널을 가진다.
- 그리고 이 패널의 조작은 shadow DOM 내부에 포함되어 있다.

shadow DOM spec은 아주 잘 정의되어 있기 때문에, 사용자 커스텀의 shadow DOM을 조작할 수 있다.

## shadow DOM 맛보기

`Element.attachShadow()`를 통해서 `document`의 어떤 엘리먼트에라도 shadow root를 붙일 후 있다.

`attachShadow()`는 인자로 `mode`라는 하나의 옵션을 받는데,
- `mode: open`
- `mode: closed` 의 값을 가질 수 있다.

### mode: open일 경우의 shadow DOM

open mode의 shadow DOM은 shadow DOM 외부에서 자바스크립트를 사용하여 shadow DOM에 접근할 수 있다.

`someElementRef.shadowRoot` 와 같이 `shadowRoot` 속성을 통해 접근할 수 있는 것이다.

### mode: closed 일 경우의 shadow DOM

closed mode의 shadow DOM은 (명세 상으로는) 외부로부터 shadow DOM에 접근할 수 없다.

`someElementRef.shadowRoot`는 null을 반환한다. 

> 하지만 closed mode의 shadow DOM을 외부에서 우회 접근하는 것은 그리 어렵지는 않다..

### 외부에서 closed mode의 shadow DOM 접근하기

https://blog.revillweb.com/open-vs-closed-shadow-dom-9f3d7427d1af

```js
class MyWebComponent extends HTMLElement {  
    constructor() {  
        super();  
        this._root = this.attachShadow({ mode: "closed" });
    }  
    connectedCallback() {  
        this._root.innerHTML = `  
            <p>I'm in the closed Shadow Root!</p>  
        `;  
    }  
}

window.customElements.define("my-web-component", MyWebComponent);
```

위와 같은 예시에서..

```js
const $myWebComponent = document.querySelector("my-web-component");  
$myWebComponent.shadowRoot // null  
$myWebComponent._root // shadow-root (closed)
```

위와 같이 접근하게 되면 shadow root에 대한 ref를 대신 커스텀하게 가짐으로써 마치 open mode인 shadow DOM을 가진 것처럼 조작할 수 있다.

```js
const $myWebComponent = document.querySelector("my-web-component");  
$myWebComponent._root.querySelector("p").innerText = "Modified!";
```

이런 방식의 접근을 막기 위해서 `WeakMap`을 사용해 저장한 ref를 잃게 만드는 방식으로 shadow root를 저장하는 것을 막을 수는 있긴 한데,,

```js
(function(){  
	const _shadows = new WeakMap();
	class MyWebComponent extends HTMLElement {  
		constructor() {  
			super();  
			_shadows.set(this, this.attachShadow({ mode: "closed" }));  
		}
		 
		connectedCallback() {  
		_shadows.get(this).innerHTML = `<p>I'm in the closed Shadow Root</p>`;  
		}  
	}  
  
	window.customElements.define("my-web-component", MyWebComponent);  
})();
```

아래와 같이 ref를 따로 저장하지 않고 객체의 prototype 속성을 재정의함으로써 shadow root 접근을 open mode로 접근하게 할 수도 있다. (즉 근본적으로 막기는 매우 힘들다)

```js
Element.prototype._attachShadow = Element.prototype.attachShadow;  
Element.prototype.attachShadow = function () {  
	return this._attachShadow( { mode: "open" } );  
};
```

위 코드는 `attachShadow` 네이티브 메서드를 재정의함으로써 모든 `attachShadow` 메서드를 open mode로써 만들어버리게 한다..

기본적으로는 shadow DOM을 closed mode로 생성해 놓고, 다시 외부에서 접근할 일이 생긴다면 위와 같은 방법을 사용해서 내부 DOM 트리에 접근할 수 있을 것 같다.
