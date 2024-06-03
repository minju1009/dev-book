```tsx
const somethingDangerous = () => {
	if(Math.random() > 0.5){
		throw new Error("Oh dear!");
	}
}

try{
	somethingDangerous();
} catch (error){
	console.log(error.message);
}
```

### 방법 - Assertion을 사용
- unknown 과 any 는 다르다.
- unknown은 type checking을 꺼주지 않는다. (any 는 다 꺼버림)
```tsx
	try {
		somethingDangerous();
	} catch (error){
		console.log((error as Error).message)
	}
```

### 방법 2 - 더 구체적으로 narrowing
```tsx
	try{
		somethingDangerous();
	}catch(error){
		if(error instanceof Error){
			console.log(error.message);
		}else{
			throw Error;
		}
	}
```
