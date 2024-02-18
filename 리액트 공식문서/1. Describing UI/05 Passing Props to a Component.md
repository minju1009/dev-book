React는 props를 통해 서로 통신한다. 부모 컴포넌트는 props를 통해 자식 컴포넌트에게 함수, 객체, 배열을 포함한 모든 Javascript값을 전달해 줄 수 있다.

- How to pass props to a component
- How to read props from a component
- How to specify default values for props
- How to pass some JSX to a component
- How props change over time

### 1. 컴포넌트에 props 전달하기
props를 사용하면 부모 컴포넌트와 자식 컴포넌트를 독립적으로 생각할 수 있다. 부모 컴포넌트는 자식컴포넌트가 props를 어떻게 사용하는지 생각할 필요없이 props를 변경할 수 있고, 마찬가지로 자식컴포넌트는 부모 컴포넌트를 보지 않고도 props를 사용하는 방식을 바꿀 수 있다.
```javascript
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
    </div>
  );
}

```

### 2. prop의 기본값 지정하기
값이 지정되지 않았을때, 기본으로 prop에 값을 주길 원한다면 =와 함께 기본값을 넣어 구조분해할당을 해 줄 수 있다.
```javascript
function Avatar({person, size = 100}){

...
}
```

### 3. JSX 전개 구문으로 props 전달하기
prop을 전달받아 그대로 쓸 때에는 전달받은 prop을 일일이 달아주기가 귀찮을 때가 있다 이럴때는 전개구문을 활용하여 그대로 컴포넌트에 전달해주면 된다.
```javascript
function Profile({ person, size, isSepia, thickBorder }) {  
return (  
	<div className="card">  
		<Avatar  
			person={person}  
			size={size}  
			isSepia={isSepia} 
			thickBorder={thickBorder}  
		/>
	</div>  
	);  
}


function Profile(props){
	return(
		<div className="card">
			<Avatar {...props} />
		</div>
	)
}
```

### 4. 자식을 JSX로 전달하기
컴포넌트를 중첩하게 되면, 부모컴포넌트에 자식 컴포넌트가 `children`이란 prop으로 전달된다.
아래의 경우 Card는 children 내부에서 무엇이 렌더링 될것인지 알 필요가 없다. 따라서 이러한 패턴은 그리드, 패널 등의 Wrapper로서의 역할을 만들 때에 주로 사용된다.

```javascript
<Card>  
	<Avatar />  
</Card>
```

```javascript
export function Card({children}){
	return(
		<div>
			{children}
		</div>		
	)
}
```

### 5. 시간에 따라 props가 변하는 방식
prop은 고정된 값이 아니라 상황에 따라 계속 변할 수 있다. 유저의 인터렉션이나 여러가지 조건 하에 prop은 변경될 수 있는 값을 가지고 있다는 것이다.

***중요!***
prop은 불변값이다. 만약 유저의 인터렉션에 따라 다른 prop값을 내려줘야 한다면! 항상 새로운 값, 새로운 객체가 전달되고, 이전의 값은 GC를 통해 버려진다. 이전 값과의 참조가 끊어지고 새로운 값에 대한 참조를 하게 된다!


