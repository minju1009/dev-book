유사한 컴포넌트를 여러개 표시하고 싶을 때, Javascript 배열 메서드를 이용해 데이터 배열을 조작할 수 있다.  `filter`와  `map`을 먼저 살펴보도록 하자.

- How to render components from an array using JavaScript’s `map()`
- How to render only specific components using JavaScript’s `filter()`
- When and why to use React keys

### 1. 배열에서 데이터 렌더링하기
`map`메서드를 이요해 다음과 같이 반복되는 것을 렌더링 할 수 있다.
```javascript
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];

export default function List() {
  const listItems = people.map(person =>
    <li>{person}</li>
  );
  return <ul>{listItems}</ul>;
}

```

### 2.  항목 배열 필터링하기
`filter`메서드를 이용해 조건에 만족하는 일부 데이터만 렌더링할 수 있다.
```javascript
export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const listItems = chemists.map(person =>
    <li>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

### 3. `key`로 목록의 항목 순서 유지하기
key를 사용하면 형제 항목 사이에서 특정 항목을 고유하게 식별할 수 있다. 잘 선택한 key는 배열 내 위치보다 더 많은 정보를 제공합니다. 만약 재정렬로 인해 어떤 항목의 _위치_가 변경되더라도, 해당 항목이 사라지지 않는 한, React는 `key`를 통해 그 항목을 식별할 수 있습니다.

- **key는 형제간에 고유해야 한다.** _다른_ 배열의 JSX 노드에는 동일한 key를 사용해도 괜찮다.
- **key가 변경되지 않아야 한다.** 그렇지 않으면 목적에 어긋나게 됩니다! 렌더링 중에는 생성하지 말아라!

만약 key를 명시적으로 지정하지 않으면 React는 index로 키를 임의로 지정한다. 그러나 이럴 경우 만약 배열의 순서가 변경되거나 삭제되면 key가 변경되기 때문에 버그가 발생할 수 있다.

또한, `key={Math.random()}`과 같이 렌더링 중에 key를 지정하면 안된다. 렌더링할 때마다 key가 달라지기 때문에 모든 컴포넌트와 DOM이 재생성되기 때문이다. 전체적인 어플리케이션을 느리게 할 뿐만 아니라 사용자의 입력과 같은 정보들을 잃을 수도 있기 때문이다. 데이터에 있는 ID같은 것을 key로 활용해라.