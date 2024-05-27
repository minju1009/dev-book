```tsx
const productPrices = {
	Apple: 1.2,
	Banana: 0.5,
	Orange: 0.8,
}

const getPrice = (productName:string) => {
	return productPrices[productName];
}
```

### 내 답안
```tsx
const productPrices = {
	Apple: 1.2,
	Banana: 0.5,
	Orange: 0.8,
};

type ProductPrice = 'Apple'|'Banana'|'Orange';

const getPrice = (productName: ProductPrice) => {
	return productPrices[productName];
};
```

### 방법 1 - string type 추가 (범위 넓히기)
```tsx
const productPrices: Record<string, number> = {
	Apple: 1.2,
	Banana: 0.5,
	Orange: 0.8,
}
```

### 방법 2 - keyof typeof 사용
```tsx
// 이렇게 사용하거나
const getPrice = (productName: keyof typeof productPrices) => {
 return...
}

// 아래와 같이 사용 하여 강제해줌
const getPrice = (productName:string) => {
	const index = productName as keyof typeof productPrices
	return productPrices[index]
}
```