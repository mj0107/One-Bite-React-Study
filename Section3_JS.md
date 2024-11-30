# JavaScript 심화

## Truthy와 Falsy

### JavaScript에서의 Truthy와 Falsy

- **Falsy 값**: JavaScript에서 `false`로 평가되는 값
  - `false`, `0`, `""` (빈 문자열), `null`, `undefined`, `NaN`
- **Truthy 값**: Falsy 값이 아닌 모든 값

### JSX에서 0 렌더링의 이유

- JSX는 JavaScript 표현식의 결과를 렌더링합니다.
- JavaScript에서 `0`은 Falsy한 값이지만, JSX에서 Falsy 여부와 관계없이 렌더링 시 표현식의 결과를 그대로 출력합니다.

#### 예제

```js
const items = [];

// JSX에서 0이 렌더링되는 경우
<div>{items.length && items.map((item) => <div>{item}</div>)}</div>;
```

- `items.length`가 `0`일 경우:
  - `items.length && ...`는 `0`이 반환됩니다.
  - JSX는 이 값을 그대로 렌더링하므로 falsy한 값이지만 화면에 `0`이 출력됩니다.

#### 해결 방법

Falsy한 값을 명시적으로 처리하여 `0`이 렌더링되지 않도록 해야 합니다.

```javascript
<div>{items.length > 0 && items.map((item) => <div>{item}</div>)}</div>
```

- `items.length > 0`이 `false`일 경우 아무것도 렌더링되지 않습니다.

<br>

---

<br>

## Spread 연산자와 2차원 배열의 얕은 복사

### Spread 연산자(`...`)란?

- 배열이나 객체를 펼쳐서 새로운 배열이나 객체를 생성할 때 사용됩니다.
- 복사를 할 때 **얕은 복사**(shallow copy)가 이루어집니다.

### 2차원 배열에서의 얕은 복사

- Spread 연산자를 사용하면 1차원 배열의 요소들은 복사되지만, 그 내부의 참조형 데이터(예: 객체, 배열)는 복사되지 않고 원본을 참조합니다.

#### 예제

```javascript
const matrix = [
  [1, 2, 3],
  [4, 5, 6],
];

const shallowCopy = [...matrix];

// 변경 전
console.log(shallowCopy[0]); // [1, 2, 3]

// 원본 배열의 첫 번째 행을 수정
matrix[0][0] = 99;

// 얕은 복사된 배열도 영향을 받음
console.log(shallowCopy[0]); // [99, 2, 3]
```

### 얕은 복사의 특징

- 최상위 레벨의 요소는 복사되지만, 하위 레벨의 참조형 데이터는 동일한 메모리 주소를 공유합니다.
- 원본 배열을 변경하면 얕은 복사 배열도 영향을 받습니다.

### 깊은 복사를 원할 때

- 2차원 이상의 배열에서 깊은 복사를 수행하려면 `structuredClone`이나 `lodash` 같은 유틸리티를 사용하거나, 혹은 `JSON.parse(JSON.stringify())`, 반복문이나 재귀적으로 복사해야 합니다.

#### `JSON.stringify()`

`JSON.stringify()`는 자바스크립트 객체를 JSON 문자열로 변환하는 함수입니다.  
이 과정에서 객체의 구조와 데이터만 복사되고, 참조는 복사되지 않습니다.

**동작 방식**

- 객체나 배열을 순회하면서 해당 값들을 문자열로 변환
- 객체 안에 포함된 중첩된 객체나 배열도 재귀적으로 변환
- `undefined`, `function`, 순환 참조, `Symbol`과 같은 직렬화할 수 없는 값들은 제외

#### `JSON.parse()`

`JSON.parse()`는 JSON 문자열을 자바스크립트 객체로 변환하는 함수입니다. 이 과정에서 새로운 객체나 배열이 생성됩니다.

#### 깊은 복사 예제

```javascript
// JSON.stringify와 JSON.parse를 이용한 깊은 복사
const deepCopy = JSON.parse(JSON.stringify(matrix));

// 원본 배열 수정
matrix[0][0] = 42;

// 깊은 복사된 배열은 영향을 받지 않음
console.log(deepCopy[0]); // [99, 2, 3] -> JSON 방식으로 복사한 값 유지
console.log(matrix[0]); // [42, 2, 3]
```

<br>

---

<br>

## for...in

### for...in은 어떻게 동작하는가?

- `for...in`은 객체의 **열거 가능한 속성**(enumerable properties)을 순회하는 구문입니다.
- 배열은 객체의 일종으로, **인덱스가 키(key)**로 동작하기 때문에 **배열에서도 사용할 수 있습니다.**

#### 예제: for...in과 배열

```javascript
const arr = [10, 20, 30];

for (const key in arr) {
  console.log(key, arr[key]);
}
// 출력:
// 0 10
// 1 20
// 2 30
```

- `key`는 배열의 인덱스를 의미합니다.
- `arr[key]`를 통해 해당 인덱스의 값을 가져올 수 있습니다.

### for...in이 배열에 적합하지 않은 이유

1. **순서가 보장되지 않음**: `for...in`은 키의 순서를 보장하지 않습니다.
2. **프로토타입 속성 포함 가능성**: 배열에 추가된 프로토타입 속성도 순회 대상이 될 수 있습니다.

### 배열 순회에 권장되는 방법

1. **for...of**: 배열의 값을 순회할 때 사용

   ```javascript
   const arr = [10, 20, 30];

   for (const value of arr) {
     console.log(value);
   }
   // 출력:
   // 10
   // 20
   // 30
   ```

2. **고차 함수**: `forEach`, `map` 등

   ```javascript
   arr.forEach((value) => console.log(value));
   // 출력:
   // 10
   // 20
   // 30
   ```

### 요약

- 배열은 객체의 일종이며, 인덱스는 키(key), 요소는 값(value)로 동작합니다.
- `for...in`은 배열에서도 동작하지만, 배열의 순회에는 적합하지 않습니다.
- **배열 순회에는** `for...of`나 고차 함수(`forEach`, `map` 등)를 사용하는 것이 더 안전하고 효율적입니다.
