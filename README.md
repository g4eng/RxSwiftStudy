# RxSwift * Reactive Programming with Swift
## Section I: Getting Started with RxSwift
### Chapter 1: Hello, RxSwift!
#### Ⅰ. What is RxSwift?
> **RxSwift***is a library for composing asynchronous and event*based code by using observable sequences and functional style operators, allowing for parameterized execution via schedulers*.  

책에는 이렇게 정의되어 있다.
여기서 주목할만한 단어들은 `observable(관찰가능한)`, `asynchronous(비동기)`, `functional(함수의)`, `via schedulers(스케줄러를 통해)`이다.
이해하기 쉽게 다시 표현해보면 

> RxSwift는 본질적으로 코드가 **새로운 데이터에 반응**하고 **순차적이고 분리된** 방식으로 처리할 수 있도록 함으로써 **비동기** 프로그램 개발을 단순화합니다.

#### Ⅱ. Cocoa and UIKit asynchronous APIs
Apple은 iOS SDK 내에서 비동기 코드를 작성할 수 있도록 다양한 API를 제공하고 있다. 많이 사용하는 방법은 네가지가 있다.

* **Notification center**: 사용자가 디바이스의 방향을 변경하거나 소프트웨어 키보드가 화면에 표시되거나 숨겨지는지 등의 이벤트가 발생할 때마다 코드를 실행
* **Delegate pattern**: 다른 개체를 대신하거나 다른 개체와 협력하여 작동하는 개체를 정의할 수 있다. 예를 들어 앱 delegate에서 새 원격 알림이 도착할 때 발생해야 하는 작업을 정의하지만 언제 실행될지 또는 몇 번 실행될지 알 수 없다.
* **Grand Central Dispatch(GCD)**: 작업 실행을 추상화하는 데 도움이 된다. 코드가 직렬 큐에서 순차적으로 실행되도록 예약하거나 우선 순위가 다른 여러 대기열에서 동시에 다수의 작업을 실행할 수 있다.
* **Closures**: 코드에서 전달할 수 있는 분리된 코드 블럭을 생성하여 다른 객체가 실행 여부, 횟수, 컨텍슽를 결정할 수 있도록 한다.

대부분의 일반적인 코드는 일부 작업을 비동기로 수행하고 모든 UI 이벤트는 본질적으로 비동기기 때문에, 앱 코드 전체가 어떤 순서로 실행될지 가정하는 것은 불가능하다.

> 문제는 Apple의 SDK가 제공하는 다양한 API 때문에 복잡한 비동기 코드를 작성하는 것이 부분적으로 매우 어려워진다는 것이다.

#### Ⅲ. Asynchronous programming glossary
RxSwift의 언어 중 일부는 비동기, 반응형 및/또는 함수형 프로그래밍에 너무 밀접하게 묶여 있어서 먼저 다음과 같은 기본 용어를 이해하면 더 쉬울 것입니다.

일반적으로, RxSwift는 다음과 같은 문제를 해결하려고 시도한다:
###### 1. State, and specifically, shared mutable state
State(상태)는 정의하기 어려운 개념이니 예를 들어 이해해보도록 합시다.

* 처음 부팅한 랩탑은 잘 작동한다.
* 시간이 지날수록 반응이 느려지거나 멈추는 상황 발생
* 하드웨어와 소프트웨어는 그대로만 상태가 변경됨
* 랩탑을 재부팅하거나 사용할 수록 메모리 상의 데이터, 디스크에 저장된 내용을 포함한 온갖 파일들은 랩탑에 남게 됨. 이것들을 랩탑의 상태라고 할 수 있음.
* 간단하게 사용하면 할 수록 데이터의 교환 등이 이루어지고 또 남는 것들이 생기면서 상태가 변화한다는 뜻이다.

> 앱의 상태를 관리하는 것(특히 여러 비동기 구성 요소를 공유할 때)은 RxSwift를 통해 배울 수 있는 중요한 점 하나다!

###### 2. Imperative programming
명령형 프로그래밍은 선언형 프로그래밍과 반대되는 개념으로, 프로그래밍의 상태와 상태를 변경시키는 구문의 관점에서 연산을 설명하는 프로그래밍 패러다임의 일종이다.
*출처: [위키피디아](https://ko.wikipedia.org/wiki/%EB%AA%85%EB%A0%B9%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)*

* 명령형 프로그래밍은 강아지와 노는 것과 비슷하다. (누워!, 죽은 척!)
* 앱에게 정확히 **’언제’ '무엇을'** 하라고 말하고 싶을 때 명령형 코드를 쓸 수 있다.
* 명령형 코드는 컴퓨터가 이해하는 코드와 유사하다. CPU가 하는 모든 일은 간단한 명령의 긴 시퀀스를 따르는 것이다.
* 문제는 인간이 복잡한 비동기 앱을 위한 명령형 코드를 작성하는 것이 어려워진다는 것이다!
* 예를 들어 iOS ViewController의 viewDidAppear(_:)를 확인해 보자

	```swift
	override func viewDidAppear(_ animated: Bool) {
 		super.viewDidAppear(animated)
 	
 		setupUI()
 		connectUIControls()
	 	createDataSource()
 		listenForChanges()
	}
	```

	* 이렇게 봐서는 각각의 메소드들이 무선 동작을 하는지 전혀 알 수 없다.
	* 뷰컨트롤러 자체의 프로퍼티를 업데이트하는지, 그렇지 않은지도 알 수 없다.
	* 각각의 메소드가 순서대로 잘 실행될지도 보증할 수 없다.
	* 누군가 실수로 메소드의 순서를 바꾼다면 이로 인해 앱이 다르게 동작할 수 있다.


###### 3. Side effects
* Side effect란 현재 scope를 벗어난 상태의 모든 변경 사항을 칭한다.
* 예를 들어 위의 코드에서 `connectUIControls()`라는 메소드는 아마 어떤 UI 구성요를 제어하기 위한 `event handler`일 것이다. 이것이 view의 상태를 변경하게 된다면 Side effect를 만들게 된다.
* 스크린 상의 `label`의 `text`를 수정 -> 디스크에 저장된 데이터를 수정 -> Side effect 발생
* Side effect 자체는 나쁘지 않다. 문제는 컨트롤이 가능하냐는 것
* 각각의 코드에 대해서, 해당 코드가 어떤 Side effect를 발생시킬 수 있는 코드인지, 단순 과정을 나열한 것인지, 명확한 결과값만을 발생시키는 것인지 정확히 인지하고 있는 것이 중요하다.

> **RxSwift**는 이러한 이슈를 추적할 수 있게 해준다.

###### 4. Declarative code
* 명령형 프로그래밍에서 상태 변화는 자유자재로 가능하다.
* 함수형 프로그래밍에서는 Side effect를 최소화하는 것을 목표로 한다.
* **RxSwift**는 이 두 가지를 결합하여 동작하게 한다.
	* 명령형 프로그래밍 + 함수형 프로그래밍
	* **자유로운 상태 변화 + 추적/예측 가능한 결과값**
* 선언형 코드(<-> 명령형 코드)를 통해 동작을 정의할 수 있으며, RxSwift는 관련 이벤트가 있을 때마다 이러한 동작을 실행하고 작업할 수 있는 불변의 고유한 데이터 입력을 제공한다.
* 이렇게 하면 변경 불가능한 데이터로 작업하고, 순차적이고 결과론적인 방식으로 코드를 실행할 수 있다.

###### Reactive systems
`반응형 시스템`이란 의미는 상당히 추상적이며 다음과 같은 특성의 대부분 또는 전부를 나타내는 iOS 앱을 다루게 된다.

* 반응(Responsive): 항상 UI를 최신 상태로 유지하며, 가장 최근의 앱 상태를 표시함
* 복원력(Resilient): 각각의 행동들은 독립적으로 정의되며, 에러 복구를 위해 유연하게 제공됨.
* 탄력성(Elastic): 코드는 다양한 작업 부하를 처리하며, 종종 lazy full 기반 데이터 수집, 이벤트 제한 및 리소스 공유와 같은 기능을 구현
* 메세지 전달(Message driven): 구성요소는 메세지 기반 통신을 사용하여 재사용 및 고유한 기능을 개선하고, 생명 주기와 클래스 구현을 분리함


#### Ⅳ. Foundation of RxSwift
###### 1. Observables

* `Observable<T>` 클래스는 Rx 코드의 기초
* `T` 형태의 데이터 스냅샷을 **전달**할 수 있는 일련의 이벤트를 비동기로 생성하는 기능
* 간단한 말로 다른 객체나 소비자가 시간이 지남에 따라 다른 개체에서 내보내는 이벤트나 값을 구독할 수 있도록 한다.
* `Observable<T>` 클래스를 사용하면 하나 이상의 `Observer`가 실시간으로 모든 이벤트에 반응하고 앱의 UI를 업데이트하거나 새로운 데이터 및 수신 데이터를 처리 및 활용할 수 있다.
* `Observable<T>` 클래스가 준수하는 `ObservableType` 프로토콜은 매우 간단하다. `Observable`은 세 가지 유형의 이벤트만 방출할 수 있다.(그리고 `Observer`는 수신할 수 있다.)
	* `next`: 최신 / 다음 데이터를 **전달**하는 이벤트
	* `completed`: 성공적으로 일련의 이벤트들을 종료시키는 이벤트. 즉, `Observable`이 성공적으로 자신의 생명 주기를 완료했으며, 추가적으로 이벤트가 생성하지 않을 것을 의미
	* `error`: `Observable`이 에러를 발생하였으며, 추가적으로 이벤트를 생성하지 않음을 의미(에러와 함게 완전 종료)
* 아래 그림과 같이 시간에 걸쳐서 발생하는 비동기 이벤트를 생각해보자.
![Observable](https://github.com/g4eng/RxSwiftStudy/blob/main/RxSwift_book/Assets/Observable.png?raw=true)

	* 위의 세 가지 유형의 `Observable` 이벤트는, `Observable` 또는 `Observer`의 본질에 대한 어떤 가정도 하지 않는다.
	* 따라서 델리게이트 프로토콜을 사용하거나, 클래스 통신을 위해 클로저를 삽입할 필요가 없다.

* 실상황에서 아이디어를 얻으려면 다음과 같은 두 가지의 Observable sequence(유한/무한)를 이해해야 한다.

###### Finite observable sequence

* 어떤 Observable seqeunce는 0, 1 또는 다른 값을 방출한 뒤, `completed` 또는 `error`를 통해 종료된다.
* iOS 앱에서, 네트워크를 통해 파일을 다운로드하는 코드를 생각해보자.

	* i) 다운로드를 시작하고, 들어오는 데이터를 관찰한다.
	* ii) 계속해서 파일 데이터를 받는다.
	* iii) 네트워크 연결이 끊어진다면, 다운로드는 멈출 것이고 연결은 에러와 함께 일시정지될 것이다.
	* iv) 또는, 성공적으로 모든 파일 데이터를 다운로드할 수 있을 것이다.
	* 이러한 흐름은 앞에서 서술한 `Observable`의 생명 주기와 정확히 일치한다. RxSwift 코드로 표현하면 다음과 같다.
	
	```swift
	 API.download(file: "http://www...")
	 	.subscribe(onNext: { data in
	 		// append data to temporary file
 		},
	 	onError: { error in 
 			// display error to user
	 	},
 		onCompleted: {
 			// use downloaded file
 		})
	```

	* `Api.download(file:)`은 네트워크를 통해 들어오는 `Data`값을 방출하는 `Observable<Data>` 인스턴스를 리턴할 것이다.
	* `onNext` 클로저를 통해 `next` 이벤트를 받을 수 있다. 예제에서는 받은 데이터를 디스크의 `temporary file`에 저장하게 될 것이다.
	* `onError` 클로저를 통해 `error` 이벤트를 받을 수 있다. alert 메시지같은 action을 취할 수 있을 것이다.
	* 최종적으로 `onCompleted` 클로저를 통해 `completed` 이벤트를 받을 수 있으며, 이를 통해 새로운 viewController를 push하여 다운로드 받은 파일을 표시하는 등의 액션을 취할 수 있을 것이다.

###### Infinite observable sequences

* 자연적으로 또는 강제적으로 종료되어야 하는 파일 다운로드같은 활동과 달리, 단순히 무한한 sequence가 있다. 보통 UI 이벤트는 무한하게 관찰 가능한 sequence이다.

* 예를 들어, 기기의 가로 / 세로 모드에 따라 반응해야하는 코드를 생각해보자.

	* i) `UIDeviceOrientationDidChange` observer를 추가한다.
	* ii) 방향 전환을 관리할 수 있는 callback 메소드를 제공해야 한다. `UIDevice`의 현재 방향값을 확인한 뒤, 이 값에 따라 화면이 표시될 수 있도록 해야한다.
	* 방향 전환이 가능한 디바이스가 존재하는 한, 이러한 연속적인 방향 전환은 자연스럽게 끝날 수 없다.
	* 결국 이러한 시퀀스가 사실 상 무한하기 때문에, 항상 최초값을 가지고 있어야 한다.
	* 사용자가 디바이스를 절대 회전하지 않는다고 해서 이벤트가 종료되는 것도 아니다. 단지 이벤트가 발생한 적이 없을 뿐.
	* RxSwift 코드로 표현하면 다음과 같다.
	
	```swift
	UIDevice.rx.orientation
		.subscribe(onNext: { current in
				switch current {
					case .landscape:
						// re-arrange UI for landscape
					case .portrait:
						// re-arrange UI for portrait
				}
		})
	```
	
	* `UIDevice.rx.orientation`은 `Observable<Orientation>`을 통해 만든 가상의 코드이다.
		* 아주 쉬운 코드로, 어떻게 만들 수 있는지는 다음 Chapter에서 배움...	* 이를 통해 현재 `Orientation`을 받을 수 있고, 받은 값을 통해 앱의 UI를 업데이트할 수 있다.
	* 해당 Observable에서는 절대 발생하지 않을 이벤트이기 때문에 `onError`나 `onCompleted` 파라미터는 건너뛸 수 있다.

##### 2. Operators

* `observableType`과 `Observable` 클래스의 구현에는 비동기 작업의 개별 부분을 추상화하는 많은 메소드가 포함되어 있으며, 함께 구성하여 보다 복잡한 논리를 구현할 수 있다. 이러한 메소드는 매우 독립적이고 구성 가능하기 때문에 이러한 방법을 보편적으로 `Operators(연산자)`라고 부른다.
* 이러한 Operator들은 주로 비동기 입력을 받아 Side effect 없이 출력만 생성하므로 퍼즐 조각과 같이 쉽게 결합할 수 있다.
* 예를 들어 `(5 + 6) * 10 - 2`라는 수식을 생각해보자
	* `*`, `()`, `+`, `-` 같은 연산자를 통해 데이터에 적용하고 결과를 가져와서 해결될 때까지 표현식을 계속 처리하게 된다.
	* 비슷한 방식으로 표현식이 최종값으로 도출될 때까지, `Observable`이 방출한 값에 Rx 연산자를 적용하여 Side effect를 만들 수 있다.
* 다음은 앞서 방향전환에 대한 예제에 Rx 연산자를 적용시킨 코드이다.

	```swift
	UIDevice.rx.orientation
		.filter { value in
			return value != .landscape
		}
		.map { _ in
			return "Portrait is the best!"
		}
		.subscribe(onNext: { string in
			showAlert(text: string)
		})
	```
	
	* `UIDevice.rx.orientation`이 `.landscape` 또는 `.portrait` 값을 생성할 때마다, Rx는 각각의 연산자를 데이터의 형태로 방출한다.
		* 먼저 `filter`는 `.landscape`가 아닌 값만을 내놓는다. 만약 디바이스가 landscape 모드라면 나머지 코드는 진행되지 않을 것이다. 왜냐하면 `filter`가 해당 이벤트의 실행을 막을 것이기 때문!
		* 만약 `.portrait` 값이 들어온다면, `map` 연산자는 해당 방향값을 택할 것이며 이것을 `String` 출력으로 변환할 것이다. ("`Portrait is the best!`")
		* 마지막으로, `subscribe`를 통해 결과로 `next` 이벤트를 구현하게 된다. 이번에는 `String` 값을 전달하고, 해당 텍스트로 alert을 화면에 표시하는 메소드를 호출한다.

* 연산자들은 언제나 입력된 데이터를 통해 결과값을 출력하므로, 단일 연산자가 독자적으로 할 수 있는 것보다 쉽게 연결 가능하며 훨신 많은 것을 달성할 수 있다.

##### 3. Schedulers

* 스케줄러는 Rx에서 dispatch queue와 동일한 것. 다만 훨씬 강력하고 쓰기 쉽다.
* RxSwift에는 여러가지의 스케줄러가 이미 정의되어 있으며, 99%의 상황에서 사용가능하므로 아마 개발자가 자신만의 스케줄러를 생성할 일은 없을 것이다.
* 이 책의 초기 반 정도에서 다룰 대부분의 예제는 아주 간단하고 일반적인 상황으로, 보통 데이터를 관찰하고 UI를 업데이트하는 것이 대부분이라 기초를 완전히 닦기 전까지 스케줄러를 공부할 필요는 없다.
* (찍먹으로...) 기존까지 GCD를 통해서 일련의 코드를 작성했다면 스케줄러를 통한 RxSwift는 다음과 같이 돌아간다.
![Scheduler](https://github.com/g4eng/RxSwiftStudy/blob/main/RxSwift_book/Assets/Scheduler.png?raw=true)

	* 각 색깔로 표시된 일들은 다음과 같이 각각 스케줄(1, 2, 3...) 된다.
		* `network subscription`(파랑)은 (1)로 표시된 `Custom NSOperation Scheduler`에서 구동된다.
		* 여기서 출력된 데이터는 다음 블록인 `Background Concrruent Scheduler`의 (2)로 가게 된다.
		* 최종적으로, 네트워크 코드의 마지막 (3)은 `Main Thread Serail Scheduler`로 가서 UI를 새로운 데이터로 업데이트하게 된다.

#### Ⅴ. App Architecture
* RxSwift는 기존의 앱 아키텍처에 영향을 주지 않는다. 주로 이벤트나 비동기 데이터 시퀀스 등을 처리하기 때문이다.
* 따라서 Apple 문서에서 언급된 MVC 아키텍처에도 Rx를 도입할 수 있다. 물론 MVP, MVVM 같은 아키텍처를 선호한다면 역시 가능하다.
* Reactive 앱을 만들기 위해 처음부터 프로젝트를 시작할 필요도 없다. 기존 프로젝트를 부분적으로 리팩토링하거나 단순히 앱에 새로운 기능을 추가할 때도 사용가능하다.
* Microsoft의 MVVM 아키텍처는 데이터 바인딩을 제공하는 플랫폼에서 이벤트 기반 소프트웨어용으로 개발되었기 때문에, 당연히 RxSwift와 MVVM 아키텍처를 같이 쓸 때 아주 멋지게 작동한다.
	* ViewModel을 사용한다면 `Observable<T>` 속성을 노출할 수 있으며 ViewController의 UIKit에 직접 바인딩이 가능하다.
	* 이렇게 하면 모델 데이터를 UI에 바인딩하고 표현하고 코드를 작성하는 것이 매우 간단해진다.

#### Ⅵ. RxCocoa
* RxCocoa는 RxSwift의 동반 라이브러리로, UIKit과 Cocoa 프레임워크 기반 개발을 지원하는 모든 클래스를 보유하고 있다.
	* RxSwift는 일반적인 Rx API라서, Cocoa나 특정 UIKit 클래스에 대한 아무런 정보가 없다.
* 예를 들어, RxCocoa를 이용하여 `UISwitch`의 상태를 확인하는 것은 다음과 같이 매우 쉽다.

	```swift
	toggleSwitch.rx.isOn
		.subscribe(onNext: { enabled in
				print( enabled ? "It''s ON" : "It's OFF")
		})
	```
	
	* RxCocoa는 `rx.isOn`과 같은 프로퍼티를 `UISwiich`클래스에 추가해주며, 이를 통해 이벤트 시퀀스를 확인할 수 있다.
