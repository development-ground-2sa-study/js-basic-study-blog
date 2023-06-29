브라우저간에 통일된 스타일을 주기위해  
특정 스타일을 맨 위에 작성하고 css 코드를 짜기 시작하면 좋다.  

브라우저마다 <button> 스타일이나 line-height (줄간격) 등등  
같은 코드를 짰을때, 다른 브라우저에서 봤을때는 이상하게 보일 수 있다.  

(참고하기 좋은 링크) _ 브라우저간에 다르게 보이는 문제들을 미리 해결할 수 있음.  
https://github.com/necolas/normalize.css/blob/master/normalize.css  

<br>

ex)
```
.box {
  width: 70px;
  background: #eee;
  margin: 20px; // 바깥여백
  padding: 10px; // 안쪽여백
  border-radius: 5px;
}
```
여기서 width는 실제 content 영역의 너비를 의미.  
하지만 padding을 주게 되면 박스 폭이 늘어나게 된다.  
그렇다면 정확하게 실제 보이는 박스 사이즈를 측정하기 어렵다.  

```
.box {
  ...
  box-sizing: border-box;
}
```

**box-sizing: border-box;** 를 적어줌으로써,  
padding, border 포함한거까지 고려해 실제 width값인 70px;을 우리 눈에 보여지게 된다.  

+) **box-sizing: content-box; => padding,border 포함 X**  
<br>
따라서 처음 css 코드를 짜기 전에 미리 설정해두면은 div 만들때마다 하나하나 border-box를 적어줄 필요가 없다.  
```
div {
  box-sizing: border-box;
}
```

<br>
대표적인 예시로 페이지가 기본 margin이 설정되어 있기에 초기화 시켜두고 시작하면 좋다. 

```
body {
  margin: 0px;
}
```

------------

> **CSS normalize, CSS reset**

크롬, 파이어폭스 등 브라우저들 간에 스타일이 다르게 보일 수 있는 문제가 발생할 수 있다.  
그 이유는 모두 동일한 스타일을 제공하지 않기 때문이다.  
이 브라우저간에 호환성 문제를 해결할 수 있는 방법으로 "CSS normalize, CSS reset'이 있다.  

[크롬, 파이오폭스 차이점을 잘 설명해주고 있는 블로그 글](https://velog.io/@kjewt/%ED%81%AC%EB%A1%9C%EC%8A%A4-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A7%95%ED%95%98%EA%B8%B0-Safari%EC%99%80-Chrome)

- **CSS reset**
모든 브라우저 형태에서 기본적으로 제공하는 스타일을 전부 초기화.   
(마지막 업데이트가 2011년 _ 오래되었기도 하고, 유용한 스타일도 초기화되어있어 비효율적)  

- **CSS normalize**
모든 브라우저의 스타일을 똑같이 유지.  
위 css reset처럼 전부 초기화하지않고, 필요한 부분만 작성.  


(읽어보면 좋을 것 같은 글) https://elad.medium.com/normalize-css-or-css-reset-9d75175c5d1e 
