컴포넌트의 가장 큰 장점은 재사용성이다! 컴포넌트를 조합해 또 다른 컴포넌트를 만들 수 있다.

- What a root component file is
- How to import and export a component
- When to use default and named imports and exports
- How to import and export multiple components from one file
- How to split components into multiple files

### 1.  루트 컴포넌트 파일
결국 App.js 라는 루트 컴포넌트 파일안에 다른 컴포넌트를 삽입하는 것이다.

### 2. 컴포넌트 import export 하기
컴포넌트를 여러개로 쪼개 Javacript의 import export 문법을 사용할 수 있다.
이 때, named export와 default export 를 사용할 수 있는데, 하나의 파일에서는 하나의 컴포넌트만 default로 내보낼 수 있고, named export는 여러개가 존재할 수 있다.
### 3. 언제 default를 사용하고 언제 named를 사용하는지
보편적으로 하나의 파일에서 하나의 컴포넌트를 export 할 때는 default export를 사용하고, 여러 개의 컴포넌트를 내보낼 때는 named export를 사용한다. 
