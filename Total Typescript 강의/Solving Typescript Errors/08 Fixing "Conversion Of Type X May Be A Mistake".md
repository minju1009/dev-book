```tsx
interface Dog {
	bark: boolean;
}

let cat = {purr: true};
let dog = cat as Dog
```

### 해결방법
```tsx
interface Dog {
	bark: boolean;
	purr?: boolean;
}

let cat = {purr: true};
let dog = cat as Dog
```
