```tsx
const searchParams = new URLSearchParams(window.location.search);

const id = searchParams.get("id");

console.log(id.toUpperCase());  // Error
```

### 내 답안
```tsx
// 방법 1 - optional chaining
console.log(id?.toUpperCase());

// 방법 2 - early return
if(!id) return;
```

### 방법 1 - optional chaining

### 방법 2 - non-nullable assertion operation 사용
절대 없을리 없다!! 라고 가정해줌 -> 그러나 안전한 방법은 아님
```tsx
console.log(id!.toUpperCase());
```

### 방법 3  - narrowing 
```tsx
if(id){
  console.log(id.toUpperCase());
}


// 혹은
if(!id){
  throw new Error("Id not found);
}
```