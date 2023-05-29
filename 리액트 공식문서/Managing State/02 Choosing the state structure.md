### Principles for structuring state

가능한 한 심플하게 만들어야 한다!

다음 다섯가지 원칙을 고려해 보면 좋다.

>1. **Group related state.** If you always update two or more state variables at the same time, consider merging them into a single state variable.
2. **Avoid contradictions in state.** When the state is structured in a way that several pieces of state may contradict and “disagree” with each other, you leave room for mistakes. Try to avoid this.
3. **Avoid redundant state.** If you can calculate some information from the component’s props or its existing state variables during rendering, you should not put that information into that component’s state.
4. **Avoid duplication in state.** When the same data is duplicated between multiple state variables, or within nested objects, it is difficult to keep them in sync. Reduce duplication when you can.
5. **Avoid deeply nested state.** Deeply hierarchical state is not very convenient to update. When possible, prefer to structure state in a flat way.


### Group related state

- 만약 두개의 state가 항상 동시에 변경된다면, 하나의 객체로 묶는게 유리할 수 있다.

```jsx
const [x, setX] = useState(0);
const [y, setY] = useState(0);

//대신에 아래와 같이 적는게 좋다.
const [position, setPosition] = useState({x:0, y:0});
```

### Avoid contradictions in state

- 반대되는 state 두 개 보다는 하나를 만들어 관리하는게 좋다.

```jsx
export default function FeedbackForm() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  const [isSent, setIsSent] = useState(false);

  async function handleSubmit(e) {
    e.preventDefault();
    setIsSending(true);
    await sendMessage(text);
    setIsSending(false);
    setIsSent(true);
  }

...}

// 위에처럼 작성하는 것보다 아래처럼 status state 하나로 만드는게 더 좋다.
export default function FeedbackForm() {
  const [text, setText] = useState('');
  const [status, setStatus] = useState('typing');

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('sending');
    await sendMessage(text);
    setStatus('sent');
  }

  const isSending = status === 'sending';
  const isSent = status === 'sent';
```

### Avoid redundant state

- 어떤 state나 prop으로부터 또 다른 state를 만들 수 있다면 불필요하게 또 하나의 state를 만들 필요가 없다.

```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState(''); // firstName + lastName 이므로 만들 필요가 없다.
```

### Avoid duplication in state

- 아래 코드에서 selectedItem은 결국 items[0]의 데이터를 중복으로 가지고 있게 된다. 이렇게 만들기보다, selectedId를 state로 저장해서 state 간에 중복된 데이터를 가지지 않도록 하는게 프로그래밍에 더 좋다.

```jsx
const initialItems = [
  { title: 'pretzels', id: 0 },
  { title: 'crispy seaweed', id: 1 },
  { title: 'granola bar', id: 2 },
];

export default function Menu() {
  const [items, setItems] = useState(initialItems);
  const [selectedItem, setSelectedItem] = useState(
    items[0]
  );

  function handleItemChange(id, e) {
    setItems(items.map(item => {
      if (item.id === id) {
        return {
          ...item,
          title: e.target.value,

// 아래처럼 변경하는게 낫다. selectedId로!
const initialItems = [
  { title: 'pretzels', id: 0 },
  { title: 'crispy seaweed', id: 1 },
  { title: 'granola bar', id: 2 },
];

export default function Menu() {
  const [items, setItems] = useState(initialItems);
  const [selectedId, setSelectedId] = useState(0);

  const selectedItem = items.find(item =>
    item.id === selectedId
  );
```

### Avoid deeply nested state

- state가 너무 많이 중첩되어 있으면 state에 변동이 있을때 (삭제 / 추가 / 편집) 조작하기 어렵다. 이럴 때에는 state를 flatten하는게 좋다.