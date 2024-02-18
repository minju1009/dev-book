- What a component is
- What role components play in a React application
- How to write your first React component

## 1. Component
- reusable UI elements for your app
- **앱의 재사용 가능한 UI 요소**
- 리액트 컴포넌트는 마크업으로 추가 동작을 할 수 있게 하는 자바스크립트 함수다.

## 2. 컴포넌트를 빌드하는 방법
#### 1. 컴포넌트 내보내기
-  Javascript의 import export 구문을 활용하여 내보내기 할 수 있다.
#### 2.  함수 정의하기
- `function Profile(){}` 과 같이 대문자를 사용하여 정의할 수 있다.
-  리액트 컴포넌트는 Javascript 함수이지만, 이름은 대문자로 시작하여야 한다!!
	- 꼭 어떤 함수명이 대문자일 필요는 없지만, JSX 구문 안에서는 대문자로 시작해야 한다. 따라서 만약 함수명을 소문자로 만들었다면, import 시에 as로 대문자 이름으로 바꿔주거나 혹은 컴포넌트 내부나 외부에서 대문자로 된 변수명을 만들어 컴포넌트를 할당한 후, JSX 안에서 대문자로 쓰이도록 하면 된다. 
		
		
```javascript
import DefaultProfile from "./ExportedDefaultProfile";

import {
  NamedExportedProfileOne,
  namedExportProfileTwo as NamedTwo,
  namedExportProfileThree
} from "./Profiles";

const NamedThree = namedExportProfileThree;

const NamedThree = namedExportProfileThree;

export default function App() {
  const NamedFour = namedExportProfileThree;
  return (
    <>
      <DefaultProfile user={{ ...user, name: "DefaultProfile" }} />
      <NamedExportedProfileOne user={{ ...user, name: "NamedExported" }} />
      <NamedTwo user={{ ...user, name: "namedExported2" }} />
      <NamedThree user={{ ...user, name: "NamedThree" }} />
      <NamedFour user={{ ...user, name: "NamedFour" }} />
    </>
  );
}
```

#### 3. 마크업 추가하기
`return <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />;`
위와 같은 구문의 `<img />`태그는 HTML처럼 작성되었지만, 실제로는 Javascript이다. 
> - JSX란 자바스크립트와 XML을 결합한 문법으로, JSX를 이용하면 인간이 이해하기 쉬운 방식으로 돔 조작과 자바스크립트의 사용을 한 공간에서 손쉽게 작성할 수 있다. 또한 선언적인 방식으로 작성하기에 가독성도 좋다.

### 3. 컴포넌트 사용하기
컴포넌트는 일반 JavaScript 함수이므로 같은 파일에 여러 컴포넌트를 포함할 수 있다.
경우에 따라 부모 컴포넌트와 자식 컴포넌트로 이름 지어서 부르기도 하며, 여러 번 줕첩하여 사용할 수도 있다.
