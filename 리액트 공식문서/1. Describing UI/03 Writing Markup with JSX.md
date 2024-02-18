> JSX는 Javascript를 확장한 문법으로 Javascript 파일 안에 HTML과 유사한 마크업을 작성할 수 있도록 해준다.

- Why React mixes markup with rendering logic
- How JSX is different from HTML
- How to display information with JSX

### 1. JSX : Javascript에 마크업 넣기
웹이 점점 더 인터렉티브해지면서 로직이 컨텐츠를 결정하는 경우가 많아졌다. 그래서 Javascript가 점점 HTML을 담당하게 되었고, 이것이 바로 React에서 렌더링 로직과 마크업이 같은 컴포넌트 안에 있게 된 이유이다.

JSX는 HTML과 비슷해보이지만 조금 더 엄격하며, 동적으로 정보를 표시할 수 있다.

### 2. HTML을 JSX로 변환하기
#### HTML
```javascript
<h1>Hedy Lamarr's Todos</h1>  
<img  
src="https://i.imgur.com/yXOvdOSs.jpg"  
alt="Hedy Lamarr"  
class="photo"  
>  
<ul>  
<li>Invent new traffic lights  
<li>Rehearse a movie scene  
<li>Improve the spectrum technology  
</ul>
```

#### JSX
```javascript
export default function TodoList(){
	return (
		<>
		<h1>Hedy Lamarr's Todos</h1>  
		<img src="https://.." alt="" className="" />
		<ul>
			<li>Invent new traffic lights</li>
			<li>Rehearse a movie scene</li>
			<li>Improve the spectrum technology</li>
		</ul>
		</>
	)
}
```

### 3. JSX 규칙
#### 1. 단일 루트 엘리먼트를 반환하기
컴포넌트에서 여러 엘리먼트를 반환하려면, **하나의 부모 태그로 감싸야한다.**
`<div>`태그를 사용하거나 `<></>``Fragment`를 활용하여 하나의 객체를 반환하도록 할 수 있다.

***중요!!!***
JSX는 HTML처럼 보이지만 내부적으로는 Javascript 객체로 변환되기 때문인데, 하나의 함수에서 두개의 객체로 반환하려면 반드시 배열로 묶여있어야 하기 때문이다. 

#### 2. 모든 태그를 닫아야 한다.
`<img /> <li></li>`와 같이 모든 태그를 닫아줘야 한다.

#### 3. 거의 대부분이 카멜케이스이다.
JSX는 결국 Javascript로 바뀌는데, 이 때 작성한 attribute들이 Javascript 객체의 키가 된다. 따라서 대부분 카멜케이스로 작성되며 class와 같은 예약어 대신 className과 같은 속성으로 작성한다.

`aria-*, data-*`의 어트리뷰트는 HTML과 동일하게 대시를 이용하여 작성한다.
