# React를 구현해보자

```typescript
const React = (function() {
  let hooks = [];  // 상태 저장소(배열형태로)
  let idx = 0;  // 상태 인덱스 관리하는 변수

 function useState(initVal) {
  const _idx = idx;  // 클로저로 인덱스를 기억
  const state = hooks[_idx] ?? initVal;  // 값이 있으면 가져오고, 없으면 초기값
  const setState = (newVal) => {
    hooks[_idx] = newVal;  // 상태 배열에 값을 넣는다.
  }
  idx++; //  다음 상태를 위해 인덱스 증가
  return [state, setState]; // 상태와 상태 변경함수 반환
}

function render(Component) {
  idx = 0;  // 인덱스 초기화
  const C = Component();  // 컴포넌트 실행하고
  C.render();   //화면에 출력
  retrun C;  
}

 return {useState, render}
)}
```
- 컴포넌트가 렌더링 될떄마다 idx를 0으로 초기화 시키고 다시 쭉 증가하면서 상태를 찾아간다.

- ### 클로저

- 함수 안에 또 다른 함수를 만들고
- 내부 함수가 외부 함수의 변수에 접근하고
- 그 내부 함수를 나중에 실행할 때도 여전히 외부 변수에 접근할 수 있다!
- 이조건을 만족하면 클로저 상황
- 
