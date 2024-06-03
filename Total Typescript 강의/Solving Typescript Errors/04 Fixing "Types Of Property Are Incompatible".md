![[Pasted image 20240527214023.png]]

```tsx
const routingConfig = {
	routes: [
		{
			path: "home",
			component: "HomeComponent",
		},
		{
			path: "about",
			component: 12,
		},
		{
			path: "contact",
			component: "ContactComponent",
		},
	],
};

  

const createRoutes = (config: {
	routes: {
		path: string;
		component: string;
	}[];
}) => {};

  
createRoutes(routingConfig);
```

### 내 답안
```tsx

// 방법 1
const createRoutes = (config: {
	routes: {
		path: string;
		component: string | number;
	}[];
}) => {};
```

### 팁
- 에러 뒤에서부터 읽는게 좋다!! 

### 방법 1 - 할당해야 하는 위치에 직접 객체를 넣으면 어디서 에러가 났는지 찾기 편하다.

```tsx
// 할당해야 하는 객체 그 자체를 넣어보면 좋다.
// 아래처럼 넣으면 어디서 에러가 나는지 바로 알 수 있다.
createRoutes(
	routes: [
		{
			path: "home",
			component: "HomeComponent",
		},
		{
			path: "about",
			component: 12, // Error!
		},
		{
			path: "contact",
			component: "ContactComponent",
		},
	],
);
```

### 방법 2 - 별도로 타입 지정 
인라인으로 어디에서 에러가 나는지 바로 알 수 있다.
```tsx
type RoutingConfig = {
	routes: {
		path: string;
		component: string;
	}[];
}

const createRoutes = (config:RoutingConfig) => {};

const routingConfig: RoutingConfig = {
	routes: [
		...
	]
}

```

### 방법 3 - `satisfies` 키워드 사용하여 검토
에러 어디서 났는지 바로 알 수 있다.
```tsx
const routingConfig = {
	routes: [
		{
			path: "home",
			component: "HomeComponent",
		},
		{
			path: "about",
			component: 12, // Error!
		},
		{
			path: "contact",
			component: "ContactComponent",
		},
	],
} satisfies RoutingConfig;

```