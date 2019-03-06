# ReactiveX

## Rx를 시작하기 전에 알아야 할것 - 왜 Reactive Programming?
> Reactive Programming : 데이터 흐름과 전달에 관한 프로그래밍 패러다임.
> 객체지향 프로그래밍이 패러다임이듯이, 리액티브 프로그래밍도 하나의 패러다임이다.

OOP(객체지향 프로그래밍)에는 다형성, 상속 등 여러가지 특징이 있다. 
Reactive Programming도 특징이 있다.

**데이터 흐름을 먼저 정의하고, 데이터가 변경되었을 때, 연관되는 함수나 수식이 업데이트 된다**

사실, 리액티브 프로그래밍을 검색해보면 나오는 말이 다 비슷비슷 할 것이다.
Reactive Programming. 중점적으로 봐야 할 것은 **반응형(Reactive)** 이다.

내가 데이터를 보고있다가, 즉 관찰(Observe)하고 있다가 데이터가 변하면 그 변한 이벤트를 가지고 관련된 업데이트 로직이 수행된다.

SW를 만들면서, 대부분의 경우 비동기 처리를 해야한다. 
그 중 한가지 예는, UI에 뿌려줄 **데이터**를 서버에 요청하고 그것을 받은 뒤, 해당 데이터를 가지고 UI를 업데이트 해야한다.

그럼 우리는 데이터가 정상적으로 도착했다! 라는 사실을 알아야만 한다.
요청은 하고 데이터가 아직 안왔을 수도 있지 않은가
데이터를 다 받고, 그 다음에 UI를 업데이트 해야한다. 

// 코드

다행히 Swift에는 이러한 비동기 처리를 지원하는 API들이 많이 존재한다.
Callback(Closure), Delegate, Notification

위 코드는 Callback으로 구현한 건데, 데이터를 다 받고 나서, 그제서야 UI를 업데이트 해준다.

만약에 저 데이터에 변경사항이 생긴다고 생각해보자,
그럼 해당 변경사항을 명시해준 뒤, 다시 UI를 업데이트 해야할 것이다.
즉, 내가 **명시적으로** UI를 한번 더 업데이트 해줘야 한다는 것이다.

우리가 아까 말한 Reactive Programming이란, 데이터가 변경되면, 저 UI업데이트 로직을 알아서 한다는 것이다. 
**왜냐? 데이터가 변경되는 것을 관찰(Observe)하고 있기 때문이다.**

그럼 조금 더 Reactive하게 저 코드를 바꿔보자.

// 코드

자. 이제는 데이터의 변경사항을 감지하여 알아서 UI가 업데이트 되게 된다.
우리는 지금 Reactive Programming을 한 것이다.
짝짝

## Why use Rx?
자. 아무런 라이브러리가 없이, Swift만으로 Reactive Programming을 할 수 있다.
그럼 Rx는 뭘까?
Rx는 Reactive Extension의 약어다. 
Reactive Programming을 손쉽게(?) 해주는, 가능하게 해주는 구현체라고 생각하면 된다.

그러니까, **Rx != Reactive Programming** 인거다.

자, 대분의 iOS프로젝트에서 Alamofire(네트워킹 라이브러리)를 쓰지않는가?
그럼 왜 쓰지?
지금 자기가 Alamofire를 쓰는 이유를 잠깐 생각해보자.


사실 Alamofire가 없어도 네트워킹은 가능하다.
URLSession이라는 API가 iOS에 있기 때문이다.
Alamofire도 내부구현은 URLSession으로 되어있다.

그럼 왜 쓰지?
URLSession을 쓰면 되는데 Alamofire 왜쓰지?
다들 써서?

정답은 간단하다.
URLSession으로 짜면 장황하고 긴 코드를, Alamofire를 사용하면 가독성도 좋아지고, 코드의 길이도 짧아진다.

// urlsession코드

Request를 만들고, dataTask를 만들고, resume을 명시적으로 해야하고....
굉장히 코드가 긴 것을 볼 수 있다. 

// alamofire코드

하지만 Alamofire를 사용함으로써 코드도 줄어들고 훨씬 가독성이 좋아진것을 볼 수 있다.

이것이..!! 이것이 Alamofire를 사용하는 이유이자 모든 라이브러리를 사용하는 이유 일것이다.
내가 길고 장황한 코드를 짜지 않고, 간단한 메소드 호출 하나로 내가 원하는 걸 가능하게 해주는 것. 
그것이 우리가 라이브러리를 사용하는 이유이다.

우리가 글을 적을 때를 프로그래밍 한다고 하지 않는다.
어떠한 프로그래밍 언어를 사용하여 그것을 가지고 코드를 짤 때, 비로소 프로그래밍 한다고 할 수 있다.

Rx가 Reactive Programming의 구현체라고 위에서 언급했다. 
구현체? 그냥 쉽게 **라이브러리**라고 생각하면 된다.

Reactive Programming을 할 수 있는 프로그래밍 언어는 굉장히 많다.
나는 그 중에서 Swift라는 언어를 쓴거고.
- RxJava는 java에서 reactive programming을 쉽고 간결하게 해주는 라이브러리
- RxSwift도 마찬가지로 Swift에서 Reactvie programming을 쉽게 도와주는 라이브러리다.

> Rx를 모를 때, 나는 RxSwift가 Swift와 비슷한 **언어**라고 생각한적이 있다. 
> RxSwift는 라이브러리다. Alamofire와 같은 녀석이다.
> 내가 길고 장황하게 써야 해결하는 것을 간단한 코드 몇줄만으로도 똑같은 동작을 내게 해주는 **라이브러리**다.


