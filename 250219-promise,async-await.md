# Promise

- 비동기 처리 상태와 처리결과를 관리하는 객체이다.
- 성공시 resolve, 실패시 reject promise생성 직후엔 pending상태
- 후속처리 메소드로 then, catch, finally가 있다.
- then은 보통 성공시 처리, catch는 에러시 처리, finally는 성공과 실패상관없이 공통적으로 어떤 작업을 한번만 수행하기위해 사용 / 모두다 promise를 반환한다.

## promise 가 나오게 된 배경
 - 콜백 헬을 해결하기 위해서 나왔다도 맞지만...
 - 콜백 같은 경우 코드 실행 순서와 실제 실행 순서가 다르기때문에 매우 불편하다. 이걸 해결 하기 위해 나왔다.
 - promise는 그에 반해 코드 순서를 실행 순서와 맞출 수 있다.
 - ```typescript
   // callback같은 경우 나중에 할일이 다음 할일보다 먼저 코드 순서가 오지만, 실제 실행순서는 다음할일이 먼저 실행 된 후 나중에할일이 실행됨
   fecthCallback(url,(data)=> {
      console.log('나중에 할일');
   });
   console.log("다음 할일");

   // promise의 경우 내가 원할때 실행에 흐름에 맞게 코드를 작성할 수 있다.
   const p = fetch(url);
   console.log("다음 할일");
   console.log("다음 할일2");
   console.log("다음 할일3");
   p.then((data)=>{})
   ```

### async await, promise.all의 차이
 - async await는 순차적으로 실행하는 개념 / promise.all은 동시의 개념이다.

### promise.all vs  promise.allSettled
 - promise all의 경우 여러개의 요청 중 하나라도 reject되면 다른 resolve된 결과를 알수가없음.
 - 하지만, 속도는 promise all이 더 빠름.
 - ```typescript
   const a = Promise.reslove('a')
   const b = Promise.reject('b')
   const c = Promise.reslove('c')
   await Promise.all([a,b,c]);
   // b에서 오류 난다는 에러 발생

    const a = Promise.reslove('a')
   const b = Promise.reject('b')
   const c = Promise.reslove('c')
   await Promise.allSettled([a,b,c]);
   // [
   //  {status: 'fullfilled, value: 'a'},
   //  {status: 'rejected, value: 'b'},
   // {status: 'fullfilled, value: 'c'},
   // ] 와 같이 전체 각각의 결과를 다 볼 수 있다.
   ```


## async await
 - 프로미스를 기반으로 동작하며, 후속 처리할 필요없이 동기 처리처럼 프로미스를 사용할 수 있다.
 - ```typescript
   function getData() {
      return new Promise((resolve) => {
        setTimeout(() => resolve("데이터 로드 완료"), 1000);
      });
    }
    
    getData()
      .then((result) => {
        console.log(result);
        return getData();
      })
      .then((result2) => {
        console.log(result2);
        return getData();
      })
      .then((result3) => console.log(result3))
      .catch((error) => console.error("에러 발생:", error));
   ```
   이 예제처럼 then 체이닝을 통해 코드가 깔끔해졌고, catch를 통해 에러 처리를 하나로 통합 할 수 있다.
   하지만, 계속 then체이닝이 길어지면 가독성이 떨어지고, 비동기 흐름을 직관적으로 알기가 어렵다.
- 위 문제를 해결하기 위해 등장한 async await -> 동기 코드처럼 작성 가능해 가독성이 좋다.
- ```typescript
  async function fetchData() {
    try {
      const result1 = await getData();
      console.log(result1);
  
      const result2 = await getData();
      console.log(result2);
  
      const result3 = await getData();
      console.log(result3);
    } catch (error) {
      console.error("에러 발생:", error);
    }
  }
  
  fetchData();
  ```
- Promise의 장점을 유지하면서 더 깔끔한 코드이면서 try-catch로 에러처리 할수있음.
- 단점 : 병렬 실행은 어려움, await를 만나면 일단 기다리기때문에 불필요하게 순차적으로 실행되면 성능 저하가 올수있다.
