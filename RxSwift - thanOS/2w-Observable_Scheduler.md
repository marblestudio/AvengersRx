Observable
=======
### Cocoa and UIKit Asynchronous APIs

--------------

#####  1. Observable<T>

immutable한 T타입의 비동기 이벤트 시퀀스를 발생시킨다. 
Observable<T>는 한개 이상의 옵저버들이 실시간으로 어떤 이벤트에 반응하여 앱 UI를 업데이트하거나 들어오는 데이터를 처리할 수 있다. 

Observable은 다음 3가지의 이벤트만 방출한다.

- A next event : 최신/다음 데이터를 '전달'하는 이벤트. (옵저버가 값을 "받는" 방법이다.)
- A complete event : Observable 생명주기를 성공적으로 완료하며 시퀀스를 종료하는 이벤트다.
- An error event : Error를 방출하며 시퀀스를 종료한다.

아래의 그림처럼 시간 순으로 비동기 이벤트를 방출하는 Observable을 볼 수 있다. 

![ObservableSequence](./images/2w/ObservableSequence.png)

다음과 같이 하나의 Observable에 여러 Observer들이 구독할 수도 있다.

![ObservableSequence](./images/2w/MultipleObservers.png)

Observable은 Observable, Observer 자체와 연결되는 것이 아니다. **이벤트 시퀀스**를 이용하기 때문에, delegate 프로토콜을 이용할 필요도 없고, class간의 통신을 위해 클로져를 삽입 할 필요가 없다. 