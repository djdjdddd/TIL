# js 기초

1. [호이스팅(Hoisting) 발생 원리를 알아보자](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%8A%A4%EC%BD%94%ED%94%84-%ED%95%A8%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)

2. [var / let / const 차이점 정리 (변수 호이스팅)](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-var-let-const-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)

3. [재미있게 확실히 이해하는 null / undefined / NaN 차이](https://inpa.tistory.com/entry/%F0%9F%93%9A-null-undefined-NaN)

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