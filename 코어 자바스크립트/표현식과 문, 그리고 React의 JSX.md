## 1.  표현식(Expression)
> 표현식이란 값으로 평가될 수 있는 문(statement)이다. 표현식이 평가되면 새로운 값을 생성하거나 기존 값을 참조한다.

예를 들어, 다음과 같은 문들은 모두 표현식이다.
```
100; 
50 + 50; // 리터럴 & 연산자로 이루어져 있는데 평가되어 값 100을 생성하므로 표현식
score; // 변수 식별자를 참조하면 변수 값으로 평가된다. 
// 식별자 참조는 값을 생성하지는 않지만 값으로 평가되므로 표현식이다. 
```

```js
// 리터럴 표현식
// 자바스크립트 엔진에 의해 평가되어 값을 생성하므로 그 자체로 표현식
10
'Hello'

// 식별자 표현식(선언이 이미 존재한다고 가정)
// 식별자 참조는 값을 생성하지는 않지만 참조하고 있는 값으로 평가되므로 표현식
sum
person.name
arr[1]

// 연산자 표현식
10 + 20
sum = 10
sum !== 10

// 함수/메서드 호출 표현식(선언이 이미 존재한다고 가정)
square()
person.getName()
```

### Quiz!
다음 아래 JS 코드 청크에 몇 개의 표현식이 있을까?
```js
(5 + 1) * 2
```
답 : 총 5개이다!
1) 5
2) 1
3) 2
4) 6
5) 12



## 2. 문 (statements)
> 문(statements)은 프로그램을 구성하는 기본 단위이자 최소 실행 단위다. 문의 집합으로 이뤄진 것이 프로그램이며, 문을 작성하고 순서에 맞게 나열하는 것이 프로그래밍이다.

> 문은 여러 토큰으로 구성된다. `토큰`이란 문법적인 의미를 가지며, 문법적으로 더 이상 나눌 수 없는 코드의 기본 요소를 의미한다. 

예를 들어, 키워드, 연산자, 리터럴, 세미콜론이나 마침표 등의 특수 기호는 문법적인 의미를 가지며, 문법적으로 더 이상 나눌 수 없는 코드의 기본 요소이므로 모두 토큰이다. 

문을 명령문이라고도 부른다. 즉, 문은 컴퓨터에 내리는 명령이다. 문이 실행되면 명령이 실행되고 무슨 일인가가 일어나게 된다. 문은 `선언문, 할당문, 조건문, 반복문` 등으로 구분할 수 있다. 
```js
// 변수 선언문
var x;

// 할당문
x = 5;

// 함수 선언문
function foo () {}

// 조건문
if (x > 1) {console.log(x);}

// 반복문
for (var i = 0; i < 2; i++) { console.log(i); }
```

## 3. 표현식과 문을 구분하는 법
### 1. 변수에 할당해보기
```js
var x = var x;  // 변수에 할당가능한가? 아니다! 고로 선언문은 표현식이 아님
```
```js
var x = 100;  // 변수에 할당가능한가? 그렇다! 고로 할당문은 표현식이다.
```

### 2. console.log 찍어보기
- `모든 함수 인수가 표현식이어야 하기 때문에` 동작한다. 표현식은 값을 생성하고 그 값이 함수에 전달되기 때문!

```js
console.log(여기에 찍어보기!)

console.log(if(~~~))  // Unexpected token 'if'
console.log(1+2) // 3
```

### 3. 완료 값(completion value)
- 크롬 개발자 도구에서 표현식이 아닌 문을 실행하면 언제나 undefined를 출력한다. 이를 완료 값이라고 하는데, 완료 값은 표현식의 평가 결과가 아니다. 따라서 다근 값과 같이 변수에 할당할 수 없고 참조할 수도 없다. 

자, 다음 변수 선언문과 조건문은 'undefined'를 반환한다.
![[Pasted image 20230218112008.png]]

반면, 표현식인 문을 실행하면 언제나 평가된 값을 반환한다.
![[Pasted image 20230218112322.png]]

** 질문, `var num = 10`은 변수 선언문이자 할당문인데 왜 `undefined를 반환할까?`
사실,  위의 문은 변수 선언과 할당을 하나의 문으로 단축 표현해 놓은 것이다. 자바스크립트 엔진은 변수 선언과 값의 할당을 2개의 문으로 나누어 각각 실행한다. 또한 이 때 주의할 점은 변수 선언과 값의 할당 시점이 다르다는 것인데, 변수 선언은 런타임 이전, 값의 할당은 런타임에 실행된다.

위에서  undefined가 나온것은 var foo 이 부분만 먼저 실행되었기 때문이 아닐까...😮 ? (질문하기)


## 4. React에서 값 / 표현식의 의미
React 공식문서에서 `expression`을 검색해보면, JSX의 curly braces({ }) 안에서 표현식이 사용가능하다는 말이 나와있다. 

>Any JavaScript expression will work between curly braces, including function calls like `formatDate()`:

그리고 [if 문을 사용하고 싶다면, 다음과 같이 conditional Rendering](https://beta.reactjs.org/learn/conditional-rendering#conditionally-returning-jsx)을 하도록 해줘야 한다고 되어 있다.

```js
if (isPacked) {  

return <li className="item">{name} ✔</li>;  

}  

return <li className="item">{name}</li>;
```

보통 위와 같은 조건부 렌더링에서 우리는 [Conditional(ternary) operator(? :)](https://beta.reactjs.org/learn/conditional-rendering#conditional-ternary-operator--) 를 사용하고 있다.
```js
return (  

<li className="item">  

{isPacked ? name + ' ✔' : name}  

</li>  

);
```

위에서 살펴봤던 대로, if문은 '문'이며 ternary operator는 값으로 평가되는 '표현식'이다. 즉, React JSX에서 { }중괄호 안에 넣을 수 있는 것은 표현식 뿐이라는 소리인데, 이건 왜 그럴까? 

### JSX 는 사실 문법적 설탕이다!
[공식문서](https://ko.reactjs.org/docs/jsx-in-depth.html)에 보면, 아래와 같이 적혀있다.
>근본적으로, JSX는 `React.createElement(component, props, ...children)` 함수에 대한 문법적 설탕을 제공할 뿐입니다.

사실 JSX로 쓴 코드들은 내부적으로 `React.createElement(component, props, ...children)`이라는 함수를 거쳐 우리가 보는 컴포넌트들이 만들어 지는 것이다. 위에서 표현 식과 문을 구분할 때, console.log로 찍어볼 수 있는 것은 표현식, 아닌 것은 문이라고 했다. 이에 대한 이유는 `함수는 값을 인자로 받기 때문` 이라고 했다. 여기서도 같은 맥락이다. `{ }로 작성한 내용은 ...children parameter에 해당하게 되는데, 값을 넘겨야 하는데, 이 부분에 문이 들어가면 함수 실행에 오류`가 나는 것이다. 

`React.createElement`를 코드로 살펴보면 다음과 같다.
```js
<MyButton color="blue" shadowSize={2}>
	Click Me
</MyButton>

React.createElement(MyButton, {color:'blue', shadowSize:2}, 'Click me');


<div className = "sidebar" />
React.createElement('div', {className: 'sidebar'})

```

`Click Me`가 createElement함수의 세 번째 인자로 들어간 것을 볼 수 있다. 
[Babel compiler](https://babeljs.io/repl/#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8BAjAAycvuID8iAOQBbGGABWIIYgBcwgIZkYGIQF9aAekasA3ETVEgA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=react&prettier=false&targets=&version=7.20.15&externalPlugins=&assumptions=%7B%7D)에서 한번 더 살펴보았다.
```js
function hello() {
  return <div>{100>1 ? 'minju' : 'ariel'}</div>;
}

function hello() {
  return /*#__PURE__*/React.createElement("div", null, 100 > 1 ? 'minju' : 'ariel');
}
```

따라서 { }안에는 오로지 표현식만 넣을 수 있고, 문을 넣을 수 없다! 