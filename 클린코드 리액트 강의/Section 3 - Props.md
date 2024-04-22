## 불필요한 Props 복사 및 연산
```jsx
//불필요한 Props 복사

function CopyProps({value}): Element {
	const [copyValue] = useState(값비싸고_무거운_연산(value));

	return value;
}
```

## Curly Braces
```jsx
	// Curly Braces를 언제 사용할까?
	// 문자열인 경우 생략
	// 값이 계산되어 들어가는 경우는 반드시 curlyBraces에
	// 혹은 일반객체를 넣을 때에는 curly braces에

<Image
	alt = {'image'}
	src = "image.jpg"
	style = {{width : 100}}
	className = 'clean-div'
/>
```

## Shorthand Props
```jsx
// 단축구문 props 언제 사용할까?

// ❌
function component(props){
	<HeaderComponent hasPadding={props.hasPadding}>
		<ChildComponent isDarkMode={props.isDarkMode} isLogin={props.isLogin} />;
	</HeaderComponent>
}


// ✅
function component({hasPadding, ...props}){
	<HeaderComponent hasPadding>
		<ChildComponent {...props} />;
	</HeaderComponent>
}
```

## Single Quotes vs Double Quotes
```jsx
1. 팀에서 일반적인 규칙 => 일관성을 지키기 위함
2. HTML? JavaScript? 둘의 차이를 두어 구분하면 좋다.
3. 결론적으로 규칙을 정하고 맥락을 파악하고 공유하자 => Lint, Prettier등의 도구의 힘을 빌리자.

// 이런식으로 정리하고, 사용하면 좋은데
javascript는 singleQuote
jsx 내부의 html attribute 는 doubleQuote


// 일일히 하기 어려우므로 esLint를 통해 설정하면 좋다.

```

## 인라인 스타일 주의하기
```jsx

const myButtonStyle = {
	warning : {backgroundColor: 'red', fontSize: '14px'},
	danger : {backgroundColor: 'yellow', fontSize: '24px},
} 
	  

function InlineStyle(){

	return(
		<button style={myButtonStyle.warning}>Clean Code</button>
	)
}
```

## 인라인 스타일 지양하기
```jsx
// css in Js를 사용할 때
- 외부로 분리하면 렌더링될때마다 직렬화되지 않고(객체가 밖에 있으므로) => 한번만 된다.
- 동적인 스타일을 실수로 건드는 확률이 적어진다.
- 스타일 관련 코드를 분리해서 로직에 집중하고 JSX를 볼 때 조금 더 간결하게 볼 수 있다.
- export해서 사용할 수도 있다.
```

## 객체 props 지양하기
```js
Object.is(
{hello: "world"}, // 초기 렌더링
{hello : "world"} // 두번째 렌더링
);

// false, false
사람의 눈에는 같지만 주소값이 달라 리액트 입장에서는 다르기 때문에 렌더링을 다시한다.
```

```jsx
/**해결 방법
	- 변하지 않는 값일 경우 컴포넌트 외부로 드러내기
	- 필요한 값만 객체를 분해해서 Props로 내려준다.
**/

function SomeComponent() {
	return(
		<ChildComponent
			propObj = {{}} // 리액트 입장에서는 객체가 변경된 줄 모른다!!
			arrProps = {[]}
		/>
	)
}
```

```jsx
propsArr.at(0)
propsArr.at(-3)

// 해당 인덱스의 값을 내려준다.
```

## HTML Attribute 주의하기
HTML의 기본 속성이 내가 만든 props명이랑 겹치지 않는지! 확인 반드시 해야함.

## ...props 주의할 점
```jsx
spread Operator로 하위 컴포넌트에 props를 넘길때..!
- 코드를 예측하기 어렵다. props를 찾으려면 올라가고 올라가고 올라가야 해서 예측하기 어렵다.
- higher Order Component를 사용할 때는 내릴 수는 있지만.. 아래처럼 하면 좋다.

const ParentComponent = (props) => {
	const { 관련_없는_props, 관련_있는_props, ...나머지_props} = props;

	return (<ChildOrHOCComponent
		관련_있는_propsj = {관련_있는_props}
		{...나머지_props}
	/>)
}
```

## 많은 Props 일단 분리하기
```jsx
// 너무 많은 Propsfmf sjarlsms ruddn
// 결과보다는 일단 실행 => 분리의 대상?
// Tanstack Query, Form Library, 상태 관리자, Context API, 등 이용
// 1. 가장 먼저 one depth라도 분리해보기
// 2. 확장성을 위한 분리를 위해 도메인 로직을 다른 곳으로 모아넣는다.

// ❌
const App = () => {
	return(
		<JoinForm
			user={user}
			auth={auth}
			location={location}
			favorite={favorite}
			handleSubmit={handleSubmit}
			handleReset={handleReset}
			handleCancel={handleCancel}
		/>
	
	)
}


//✅ One depth 분리하는 시도부터 꼭 해보기!
return(
	<JoinForm
		onSubmit={handleSubmit}
		onReset={handleReset}
		onCancel={handleCancel}
	>
	<CheckBoxForm formData={user} />
	<CheckBoxForm formData={auth} />
	<RadioButtonForm formData={favorite} />
	<SectionForm formData={favorite} />
	</JoinForm>
)
```

## 객체보다는 단순한 Props의 장점
```jsx
const UserInfo = ({ user }) => {
	return(
		<div>
			<img src={user.imgUrl} />
			<h3>{user.userName}</h3>
		</div>
	
	)

}

통으로 전달하는 것보다, 분해해서 전달받는게 좋다.
객체 전체를 내리면 불필요한 정보까지 같이 내리게 된다.
```