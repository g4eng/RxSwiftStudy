# RxSwiftStudy

RxSwift

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

## 비동기 프로그래밍 용어들
### 1. State, specifically, shared mutable state
state는 정의하기 어려운 개념이라 예를 들어서 이해해보자
* 처음 시동한 랩탑은 잘 작동함
* 하지만 시간이 지날수록 반응이 느려지거나 반응을 멈추는 상황이 발생한다... 왜 그래?
* 하드웨어와 소프트웨어는 그대로지만 변한건 state
* 랩탑을 재시동하거나 사용할 수록 메모리상의 데이터, 디스크에 저장된 내용을 포함한 온갖 찌꺼기 파일들은 랩탑에 남게 됨. 이것이 랩탑의 state라고 할 수 있음.
* 쉽게 얘기해서 사용하면 할 수록 데이터의 교환 등으로 인해 이루어지고 또 남는 것들이 생기면서 state가 변화한다는 뜻
> 앱의 State를 관리하는 것은 RxSwift를 통해 배울 수 있는 중요한 포인트 중 하나

### 2. 명령형 프로그래밍 (imperative programming)
명령형 프로그래밍은 선언형 프로그래밍과 반대되는 개념으로, 프로그래밍의 상태와 상태를 변경시키는 구문의 관점에서 연산을 설명하는 프로그래밍 패러다임의 일종이다.
*출처: [위키피디아](https://ko.wikipedia.org/wiki/%EB%AA%85%EB%A0%B9%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)*
