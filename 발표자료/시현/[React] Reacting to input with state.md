<출처>
https://react.dev/learn/reacting-to-input-with-state  
https://react-ko.dev/learn/reacting-to-input-with-state

## Reacting to input with state

컴포넌트의 다양한 시각적 state ('초기 state', '입력 state', '성공 state')별로
표시하려는 UI를 구현한 다음, 사용자 입력에 대한 응답으로 state 변경을 trigger 한다.
이는 디자이너가 UI에 대한 생각 방식과 유사하다.

[You will learn]

- 선언형 UI 프로그래밍, 명령형 UI 프로그래밍 차이점
- 컴포넌트가 있을 수 있는 다양한 시각적 state를 열거하는 방법
- 코드에서 다른 시각적 state 간의 변경을 trigger 하는 방법

</br>

#### 선언형 UI, 명령형 UI 차이점

UI interactions을 디자인할때, 사용자의 행동에 따라 UI가 어떻게 변하는지에 대해 고민  
사용자가 답변을 제출할 수 있는 양식을 고려해보자.

1) form에 무언갈 입력할때, "Submit" 버튼이 활성화 됨.
2) "Submit" 버튼을 누르면, form과 button이 비활성화가되고, 스피너가 나타남.
3) 네트워크 요청이 성공하면 form은 숨겨지고, "Thank you" 메세지가 나타남.
4) 네트워크 요청이 실패하면 오류메시지가 뜨고, form 은 다시 활성화가 됨.
  
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbPzoVy%2FbtsiaBirxny%2F0JwPrfhUtYIR4BQz1PnzSk%2Fimg.png">

**명령형 프로그래밍**에서는  
방금 일어난 일에 따라 UI를 조작하기 위한 정확한 지침을 작성.  
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FHySFn%2Fbtsic6OS3z8%2FxrnwMM5khdtys4BZGu4eAk%2Fimg.png">  

ex) 운전자는 자동차를 타고 가는사람이 어디로 가고 싶은지 모른채 명령만 따른다.  
(지시를 잘못하면 잘못된 장소에 가게 됨)  
</br>
즉, 컴퓨터에게 스피너부터 버튼까지 각 요소에 "command"를 해서 컴퓨터에 어떻게 UI를 업데이트할 내용을 지시해야한다.  
이를 "명령형"이라 부른다.  
</br>
리액트에서는 직접 UI를 조작하지 X.  
즉, 컴포넌트를 직접 활성화하거나 비활성화 하지도, 보여주거나 숨기지도 않는다.  
대신 표시할 내용을 선언하면 React가 UI를 업데이트 할 방법을 알아낸다.  

택시를 타고 기사에게 정확히 어디서 꺾어야 할지를 알려주는 대신 어디로 가고싶은지만 말한다고 생각!  
목적지까지 데려다주는 것은 택시기사의 몫이며,  
기사는 우리가 미처 생각하지 못한 지름길을 알고 있을 수도 있다.  

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdQ0f6R%2FbtsicMJYJ2B%2FAzb2ubdWtKjhY28tHXwpA1%2Fimg.png">  


#### Thinking about UI declaratively (UI를 선언적인 방식으로 생각)

위에 예시에선 form을 명령형으로 구현하는 방법이었다면,  
React로 사고하는 방법을 이해하기 위해,  
이 UI를 React로 다시 구현하는 과정.  

1) 컴포넌트의 다양한 시각적 상태를 식별함.
2) 상태 변화를 촉발하는 요소를 파악함.
3) useState를 사용해, 메모리의 상태를 표현함.
4) 비필수적인 state 변수를 제거함.
5) 이벤트 핸들러를 연결하여 state를 설정함.

</br>
 
**1) 컴포넌트의 다양한 시각적 상태를 식별함.**

먼저 사용자에게 표시될 수 있는 UI의 다양한 "상태"를 모두 시각화해야함.

· Empty : form의 "Submit" 버튼은 비활성화  
· Typing : form의 "Submit" 버튼이 활성화  
· Submitting : form은 완전히 비활성화되어있고, Spinner가 표시됨.  
· Success : form 대신 "Thank you" 메세지가 표시됨.  
· Error : '입력중' 상태와 동일하지만, 추가로 오류 메세지가 표시됨.  
 

**2) 상태 변화를 trigger하는 요소를 파악함.**

두가지 입력에 대한 응답으로 상태 변경을 trigger 할 수 있다.

· **사람의 입력** : 버튼 클릭, 필드 입력, 링크 이동 등  
· **컴퓨터의 입력** : 네트워크에서 응답 도착, 시간 초과, 이미지 로딩 등  

두 경우, 모두 **state 변수를 설정**해야 UI를 업데이트 할 수 있다.  
몇가지 다른 입력에 따라 state를 변경해야한다.  

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbTtoBm%2FbtsidmjXat3%2FMXhaqtEZ3JXC2na6iZt7i0%2Fimg.png">  

· **사람) text 입력 변경**

text box 가 비어있는지의 여부에 따라  
비어있는 state -> 입력하고 있는 state로 전환.  

· **사람) 제출 버튼 클릭**

 제출 중 state로 전환.

 
· **컴퓨터) 네트워크 응답 성공**  
 성공 state로 전환.  

· **컴퓨터) 네트워크 요청 실패**  
 오류메시지와 함께 오류 state로 전환.  
 
</br>

**3) useState를 사용해, 메모리의 상태를 표현함.**

메모리에서 컴포넌트의 시각적 상태를 useState로 표현해야함. (단순함이 핵심)  
각 상태 조각은 "움직이는 조각". 가능한 적은 수의 "움직이는 조각"을 원함.  
</br>
반드시 필요한 state 부터 시작.  
ex) 입력에 대한 answer를 저장하고, 가장 마지막에 발생한 error(존재함) 저장해야함.  
```
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
```
  
어떤 상태를 표시할지를 나타내는 state 변수가 필요하다.   
가장 좋은 방법은 가능한 모든 시각적 상태를 다룰 수 있게 충분한 state를 추가해라.  
  
```
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```
</br>

 **4) 비필수적인 state 변수를 제거함.**

state가 사용자에게 보여주기를 원하는 유효한 UI를 나타내지 않는 경우를 방지하는 것이다.  
ex) 오류 메시지를 표시하며 동시에 입력을 비활성화한다면 사용자는 오류를 수정 X.  

</br>
Q. state가 모순을 야기하나요?  

 isTyping과 isSubmitting은 동시에 true일 수 없다.  
이러한 모순은 일반적으로 state가 충분히 제약되지 않았음을 의미함.   

'불가능한' state를 제거하려면 세가지 값을 하나의 status로 결합하면 된다. => typing, submitting, success

</br>
Q. 다른 state 변수에 이미 같은 정보가 있나요?  

 isEmpty와 isTyping은 동시에 ture가 될수 X.  
이를 각 state 변수로 분리하면 동기화되지않아 버그가 발생할 위험이 있다.  

isEmpty를 제거하고 대신 answer.length === 0으로 확인할 수 있다.

</br>
Q. 다른 state 변수를 뒤집으면 동일한 정보를 얻을 수 있나요?

isError는 error !== null을 대신 확인할 수 있기 때문에 필요하지 X.  


</br>
이렇게 3개의 필수 state 변수만 남게 된다.  
  
```
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
const [status, setStatus] = useState('typing'); // 'typing', 'submitting', or 'success'
```
</br>

 **5) 이벤트 핸들러를 연결하여 state를 설정함.**

state 변수를 설정하는 이벤트 핸들러를 생성한다.  

</br>

#### 요약

· 선언형 프로그래밍은 각 시각적 상태에 대해 UI를 기술하는것을 의미.  

· 컴포넌트를 개발할때,  

 1) 모든 시각적 상태를 식별하기
 2) 사람 및 컴퓨터가 상태 변화를 trigger하는 요인을 결정하기
 3) useState로 상태를 모델링하기
 4) 버그와 모순을 피하려면 비필수적인 state를 제거하기.
 5) 이벤트 핸들러를 연결하여 state를 설정하기.
---------------

<블로그에 정리>  
링크 : https://codingstudy00.tistory.com/248
