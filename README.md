# RxSwiftStudy

## 함수형 프로그래밍

- 모든 것이 불변 상태이므로 실제로 변경할 수 없다
- 일급 객체와 고차 함수

### Pure function

- 같은 입력을 주면 항상 같은 결과가 나오는 함수
- 부작용이 없는 함수

> RxSwift, in its essence, simplifies developing asynchronous programs by allowing your code to react to new data and process it in sequential, isolated manner.

## ReactiveX

- 옵저버블 스트림을 이용해서 비동기 프로그래밍을 하기 위한 API

## iOS에서 비동기 작업

### 1. 내부 프레임워크

- Notification Center
- Delegate pattern
- GCD
- Closure

일반적으로 대부분의 클래스들은 비동기적으로 작업을 수행하고 모든 UI 구성요소들은 본질적으로 비동기적으로 진행된다. 따라서 어떤 코드를 작성했을 때 정확히 어떤 순서 동작하는지 판단하는 것은 불가능. 코드는 유저 입력, 네트워크 또는 os 이벤트와 같은 다양한 외부 요인에 따라서 완전히 다른 순서로 실행될 수 있다.

### 2. PromiseKit

- PromiseKit을 이용하여 Promise라는 객체를 return에서 이용

### 3. RxSwift

- Promise와 비슷한 형태인데 Observable을 리턴하고 done 대신 subscribe
