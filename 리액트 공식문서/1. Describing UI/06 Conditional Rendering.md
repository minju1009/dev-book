React에서는 `if` 문, `&&`, `? :` 연산자 같은 JavaScript 문법을 사용해 조건부로 JSX를 렌더링할 수 있다.

- How to return different JSX depending on a condition
- How to conditionally include or exclude a piece of JSX
- Common conditional syntax shortcuts you’ll encounter in React codebases

### 1. 조건부로 반환하는 JSX
`if..else`구문을 활용하여 조건부로 JSX를 반환할 수 있다.
```javascript
if (isPacked) {  
	return <li className="item">{name} ✔</li>;  
}  
	return <li className="item">{name}</li>;
```

또는 아무것도 반환하고 싶지 않을 때는 `null`을 반환하도록 할 수 있으나, 이는 일반적이지 않다. 
```javascript
if (isPacked) {  
	return null
}  
	return <li className="item">{name}</li>;
```

### 2. 조건을  포함한 JSX
위와 같이 작성했을 때, `<li className="item">{name}</li>`부분이 중복된다는 것을 알 수 있다. 따라서 더 나아가서 다음과 같이 작성할 수 있다.

#### 조건(삼항) 연산자(`? :`)
```javascript
return (  
	<li className="item">  
		{isPacked ? name + ' ✔' : name}  
	</li>  
);
```

#### 논리 AND 연산자(`&&`)
`그렇지 않으면`아무것도 렌더링되지 않도록 할 때 사용된다.
```javascript
return (  
	<li className="item">  
		{name} {isPacked && '✔'}  
	</li>  
);
```

#### 변수에 조건부로 JSX 할당하기
다음과 같이 변수를 이용해 조건부 할당을 할 수도 있다.
```javascript
let itemContent = name;

if (isPacked) {  
	itemContent = name + " ✔";  
}

<li className="item">  
	{itemContent}  
</li>
```