# js 기초

1. 호이스팅(Hoisting) 발생 원리를 알아보자

2. [var / let / const 차이점 정리 (변수 호이스팅)](#2-var--let--const-차이점-정리-변수-호이스팅)

3. 재미있게 확실히 이해하는 null / undefined / NaN 차이

4. [콜백함수, ajax](#4-콜백함수-ajax)

---


## 2. var / let / const 차이점 정리 (변수 호이스팅)

### 3. scope
- `var` : 함수 레벨 스코프
- `let`, `const` : 블록 레벨 스코프


#### 예시
변수 `y`가 `var`일 때와 `let 또는 const`일 때 `scope`이 어떻게 차이나는지 보겠습니다.

1. 함수 레벨 스코프 (var)
```js
function exampleFunction() {
    var x = 10; // 함수 내에서 선언된 변수 x
    if (true) {
        var y = 20; // 함수 내에서 선언된 변수 y
        console.log(x); // 함수 내부에서 변수 x에 접근 가능
        console.log(y); // 함수 내부에서 변수 y에 접근 가능
    }
    console.log(x); // 함수 내부에서 변수 x에 접근 가능
    console.log(y); // 함수 내부에서 변수 y에 접근 가능
}

exampleFunction(); 
// 출력: 
// 10
// 20
// 10
// 20
console.log(x); // 에러: x는 함수 내부에서만 정의되었으므로 외부에서 접근할 수 없음
console.log(y); // 에러: y는 함수 내부에서만 정의되었으므로 외부에서 접근할 수 없음
```

2. 블록 레벨 스코프 (let, const)
```js
function exampleFunction() {
    var x = 10; // 함수 내에서 선언된 변수 x
    if (true) {
        let y = 20; // 블록 내에서 선언된 변수 y
        console.log(x); // 함수 내부에서 변수 x에 접근 가능
        console.log(y); // 함수 내부에서 변수 y에 접근 가능
    }
    console.log(x); // 함수 내부에서 변수 x에 접근 가능
    console.log(y); // 에러: y는 블록 내에서만 정의되었으므로 외부에서 접근할 수 없음
}

exampleFunction(); 
// 출력: 
// 10
// 20
// 10
// 에러: y는 블록 내에서만 정의되었으므로 외부에서 접근할 수 없음
console.log(x); // 에러: x는 함수 내부에서만 정의되었으므로 외부에서 접근할 수 없음
```


---

## 4. 콜백함수, ajax

### 콜백함수
- 정의 : (함수에) 파라미터로 들어가는 함수
- 용도 : 순차적으로 실행하고 싶을 때 사용

```js
function first(파라미터){
    console.log(1);
    파라미터();
}

function second(){
    console.log(2);
}

first(second);
```

`first`라는 함수의 파라미터로 들어간 `second`라는 함수를 보고 `콜백함수`라고 합니다.


#### 예시
이벤트 리스너, setTimeout 함수를 보면 콜백함수를 파라미터로 넣어 사용하는 걸 볼 수 있습니다.

#### 1. 이벤트 리스너
```js
document.querySelector('.button').addEventListener('click', function (){
    // 버튼을 클릭했을 때 실행할 코드
})
```

```js
function 콜백함수(){
    // 실행하고 싶은 코드
}

document.querySelector('.button').addEventListener('click', 콜백함수);
```

#### 2. setTimeout
```js
setTimeout(function (){
    // 실행하고 싶은 코드
}, 1000)
```

```js
function 콜백함수(){
    // 실행하고 싶은 코드
}

setTimeout(콜백함수, 1000)
```

### 콜백 지옥 (callback hell)
여러 개의 비동기 작업을 순차적으로 수행하게끔 코드를 짜다 보면 콜백함수가 중첩되어 코드의 깊이가 깊어지는 현상이 발생합니다.
이를 `콜백 지옥`이라고 부릅니다.

예를 들어, 숫자 n 을 파라미터로 받아와서 다섯번에 걸쳐 1초마다 1씩 더해서 출력하는 작업을 setTimeout 비동기 함수로 구현해보겠습니다.

```js
function increaseAndPrint(n, callback) {
  setTimeout(() => {
    const increased = n + 1;
    console.log(increased);
    if (callback) {
      callback(increased); // 콜백함수 호출
    }
  }, 1000);
}
```

#### 콜백 지옥 예시
함수 실행 횟수가 많아질수록 `콜백 함수`로 인해 들여쓰기의 깊이가 점점 더 깊어지는 걸 볼 수 있습니다.
```js
increaseAndPrint(0, n => {
  increaseAndPrint(n, n => {
    increaseAndPrint(n, n => {
      increaseAndPrint(n, n => {
        increaseAndPrint(n, n => {
          console.log('끝!');
        });
      });
    });
  });
});
```

### Promise 객체로 개선된 콜백 지옥
자바스크립트에 새로 추가된 `Promise 객체`를 이용한다면 함수 실행 횟수가 많아져도 위와 같은 콜백 지옥 현상을 완화시킬 수 있습니다.

```js
function increaseAndPrint(n) {
  return new Promise((resolve, reject)=>{ // Promise 객체를 이용!
    setTimeout(() => {
      const increased = n + 1;
      console.log(increased);
      resolve(increased);
    }, 1000)
  })
}
```

#### Promise 객체 사용으로 완화된 콜백 지옥 현상
```js
increaseAndPrint(0)
  .then((n) => increaseAndPrint(n))
  .then((n) => increaseAndPrint(n))
  .then((n) => increaseAndPrint(n))
  .then((n) => increaseAndPrint(n)); // 체이닝 기법
```

#### Promise란?
자바스크립트의 `Promise`는 비동기 프로그래밍의 근간이 되는 기법 중 하나로 기존의 콜백 함수를 대체하고, 비동기 작업 흐름을 쉽게 제어할 수 있게 되었습니다.
참고로 `Promise`는 **비동기 작업의 최종 완료 또는 실패를 나타내는 객체**로 `Array`나 `Object`처럼 자바스크립트의 독자적인 객체입니다.


### ajax 
서버와 비동기적으로 데이터를 주고받는 자바스크립트 기술
- A : Asynchronous
- J : JavaScript
- A : And
- X : XML

#### 자바스크립트로 ajax 요청을 하는 방법

#### 1. 옛날 JS 방식
```js
var ajax = new XMLHttpRequest();
ajax.onreadystatechange = function (){
    if(this.readyState == 4 && this.status == 200){
        console.log(ajax.responseText);
    }
};
ajax.open("GET", "비동기로 요청할 URL", true);
ajax.send();
```

#### 2. 요즘 JS 방식
(1) `Fetch API` 이용
```js
fetch('비동기로 요청할 URL')
    .then((response) => { // 콜백함수
        if(!response.ok){ // 에러 처리
            throw new Error('400 아니면 500 에러남')
        }
        return response.json()
    })
    .then((결과) => {
        console.log(결과)
    })
    .catch(() => { // 에러 처리를 하고 싶을 때 catch() 이용
        console.log('에러남')
    })
```

(2) async/await 문법 
<br/>
위처럼 `then()` 함수를 사용하는 경우에는 여전히 코드 길이가 길고 복잡해지는 문제가 있습니다. 
`async/await` 문법을 이용한다면 보다 더 간결하게 비동기 처리 코드를 작성할 수도 있습니다.
```js
async function 데이터가져오는함수(){
    let response = await fetch('비동기로 요청할 URL')
    if(!response.ok){
        throw new Error('400 아니면 500 에러남');
    }
    let result = await response.json();
    console.log(result);
}

데이터가져오는함수().catch(() => {
    console.log('에러남');
})
```

참고로 async/await 키워드는 ES8에서 도입된 비동기 처리를 위한 문법입니다. 
Promise를 기반으로 하지만 then과 catch 메서드를 사용하지 않고 비동기 작업을 수행할 수 있습니다.

#### 3. 외부 라이브러리 방식
(1) jQuery 라이브러리의 `$.ajax()`

(2) axios 라이브러리
<br/>
리액트나 뷰 환경에서는 `axios` 라이브러리를 설치하여 많이 사용합니다.
```js
axios.get('비동기로 요청할 URL')
    .then((result) => {
        console.log(result.data);
    })
    .catch(() => {
        console.log('에러남');
    })
```

## Reference
- https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%8A%A4%EC%BD%94%ED%94%84-%ED%95%A8%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85
- https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-var-let-const-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85
- https://inpa.tistory.com/entry/%F0%9F%93%9A-null-undefined-NaN
- https://www.youtube.com/watch?v=-iZlNnTGotk&t=109s
- https://www.youtube.com/watch?v=nKD1atl6cAw&t=304s