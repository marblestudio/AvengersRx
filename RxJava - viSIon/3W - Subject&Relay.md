# Subject

**Subject**클래스는 **Observable**과 **Observer**의 속성을 모두 가지고 있는 클래스이다.

실제로  RxJava에서 Subject는 Observable과 Observer 모두를 구현하고 있다.

```java
public abstract class Subject<T> extends Observable<T> implements Observer<T> {
    ...
}
```

Observable 처럼 데이터를 **subscribe** 할 수 있으며

Observer처럼 데이터를 onNext같은 메서드를 통해 **emit** 할 수도 있다는 의미다.

또한, **Cold Observable을 Hot Observable로 바꿔주는 역할**도 하고있다.

Subject는 4가지의 종류가 있다.

- PublishSubject
- BehaviorSubject
- AsyncSubject
- ReplaySubject

하나하나 알아보자



### PublushSubject

가장 일반적인 Subject클래스로 구독자가 subscribe() 를 호출한 이후 시점부터의 데이터를 전달받을 수 있다.

![img](http://reactivex.io/documentation/operators/images/S.PublishSubject.png)

위 마블에서와 같이 첫 번째 타임라인에서는 모든 아이템을 전달받고, 두 번째 타임라인에서는 subscribe 이후인 파란공만 전달 받는다.



### BehaviorSubject

BehaviorSubject클래스는 subscribe시 가장 최근 값 혹은 기본값을 넘겨주는 클래스이다. 여러개의 값들이 emit되었어도 오직 가장 마지막에 emit된 값을 넘겨준다. 기본값을 가지려면 createDefault 함수로 생성하면 된다.

![img](http://reactivex.io/documentation/operators/images/S.BehaviorSubject.png)

위 마블에서 첫 번째 타임라인에서 subcirbe시 초기값인 보라공을 전달 받고, 두 번째 타임라인에선 가장 최근에 emit된 값인 초록공을 전달받는다.



### AsyncSubject

AsyncSubject클래스는 Observable에서 발행한 마지막 데이터를 얻어올 수 있는 Subject 클래스이다.

마지막으로 방출된 데이터를 기억하고 있다가 onComplete()를 호출하면 그 값을 구독자에게 보낸다.

마지막 데이터가 아닌 데이터들은 무시당한다.

![img](http://reactivex.io/documentation/operators/images/S.AsyncSubject.png)

위 마블과 같이 완료되기 전까지는 구독자에게 데이터를 전달하지 않다가 완료됨과 동시에 모든 구독자에게 마지막 데이터를 발행하고 종료된다.



### ReplaySubject

ReplaySubject클래스는 subscirbe시 subscribe를 시작한 시점과 관계 없이 subject가 emit했던 모든 값들을 전달받습니다. 이러한 특성 때문에 메모리 누수를 염두해두고 사용해야합니다.

![img](http://reactivex.io/documentation/operators/images/S.ReplaySubject.png)

위 마블에서 두 번째 타임라인과 같이 subscribe한 시점에서 그간 emit되었던 모든 데이터를 전달받습니다.