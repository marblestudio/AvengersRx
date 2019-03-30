# Relay

Relay는 이제껏 배운 Subject와 달리 RxSwift에 있는 것이 아니라, RxCocoa에 있다.

![rxcocoa](./images/RxCocoa.png)

즉 뭔가 UI와 관련있을 것 같지 않은가?
맞ㄷㅏ

일단 Relay의 종류에는 2가지가 있는데, ```PublishRelay```와 ```BehaviorRelay```가 있다.


## PublishRelay / BehaviorRelay

PublishRelay는 우리가 Subject에서 배웠던 ```PublishSubject```와 이름이 비슷하다.
맞다! PublishRelay는 PublishSubject와 굉장히 유사하다.
소스코드를 보자.

![publish](./images/PublishRelay.png)

위 코드를 보면 알 수 있듯이, 

**PublishRelay is a wrapper for `PublishSubject`.**
PublishRelay는 PublishSubject의 래퍼클래스라고 한다.

**Unlike `PublishSubject` it can't terminate with error or completed.**
PublishSubject와 다르게 error 또는 completed로 인해 종료되지 않는다.


`BehaviorRelay`역시 PublishRelay처럼 RxCocoa에 구현되어있으며, `BehaviorSuject`의 래퍼클래스이며 역시나 **error 또는 completed로 인해 종료되지 않는다.**  BehaviorRelay와 PublishRelay 한가지 차이점이 있는데 `accept`를 보고 말하도록 하겠다.  

먼저 이 사실은 나중에 보도록 하고, Subject와 다른점은 `onNext`가 없다는 사실이다. (물론 onError와 onCompleted도 없다.)

relay에 값을 주는 방법은 accept를 사용하는 방법이다. 

~~~swift
let publishRelay = PublishRelay<String>()
publishRelay.accept("Zedd")
publishRelay.subscribe { (string) in
    print(string)
}
publishRelay.accept("Marshmello")
~~~

결과는 뭐가 나올까?

**next("Marshmello")**\
가 나온다.
Zedd는 구독전에 발생한 이벤트이므로 Publish특징을 가진 PublishRelay에서는 받지 못한다.

~~~swift 
let behaviorRelay = BehaviorRelay(value: "Zedd")
behaviorRelay.accept("Alan")
behaviorRelay.subscribe { (string) in
    print(string)
}
behaviorRelay.accept("Marshmello")
~~~

결과는 뭐가 나올까? 퀴즈~

자, 아무튼 이렇게 accept로 값을 relay에 보낼 수 있다는 사실을 알았다.

위에서 언급한 BehaviorRelay와 PublishRelay 한가지 차이점은 `value`이다. 
`value`는  BehaviorRelay에있는 프로퍼티로, 정의는 **Current value of behavior subject**이다. 예제를 보도록 하자. (왜 subject지?)

~~~swift 
let behaviorRelay = BehaviorRelay(value: "Zedd")
behaviorRelay.accept("Alan")
behaviorRelay.subscribe { (string) in
    print(string)
}
behaviorRelay.accept("Marshmello")
print(behaviorRelay.value)
~~~

맨 아래에 BehaviorRelay의 value를 출력하도록 했다. 뭐가 나올까? 

**Marshmello**가 나온다.


자 그럼 우리의  남은 궁금증은 단 하나다.
**error 또는 completed로 인해 종료되지 않는다는게 무엇을 의미하는가?**

여러 stream으로 부터 PublishSubject 로 바인딩해줄 때, stream들 중에서 하나라도 complete 를 흘려보내게 되면, PublishSubject는 중단되고 말것이다.

