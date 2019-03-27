# Subject

앞서, ```observable```을 배웠다.
오늘은 ```subject```를 배워보겠다. [ReactiveX](http://reactivex.io/documentation/subject.html)의 subject설명을 보면, 복잡한데, 한마디로
**subscriber의 속성과 Observable의 속성을 둘 다 가지고 있다는 것이다**

Observable처럼 데이터를 발행할 수도 있고(onNext), subscriber처럼 발행된 데이터를 바로 처리할수도 있다. 

cold observable(구독할때까지 발행을 기다리는 경우)을 hot observable(구독의 여부와 상관없이 데이터를 발행한다)로 바꿀 수 있는것이 ```Subject```다.
> cold observable은 구독자가 구독하는 순간, 분비된 데이터를 처음부터 발행한다. 그에 반해 hot observable은 구독한 시점부터 observable에서 발행한 데이터를 받는다.

cold > hot의 개념이 잘 이해가 안갈 수 있다. 

그냥 내가 원하는 데이터를 구독자의 여부와 상관없이 발행하고 싶다! 라고 하면 ```Subject```를 사용하면 된다.

Subject에는 4가지 종류가 있다.
```AsyncSubject```,  ```BehaviorSubject```,  ```PublishSubject```,  ```ReplaySubject```가 있다.

하나씩 차근차근히 보도록 하자.

## AsyncSubject
`AsyncSubject`는 소스 Observable로부터 배출된 마지막 값(만)을 배출하고 소스 Observable의 동작이 완료된 후에야 동작한다.
// AsyncSubject 마블 다이어 그램

`AsyncSubject`에 보면 되는건 그냥 **마지막 데이터**다. 
내가 마지막 데이터에만 관심이 있다면 AsyncSubject를 사용하면 된다.

위 마블 다이어그램을 확인해보면, 처음 subscribe했을 때, 아무 데이터를 받지 않은 것을 볼 수 있다. 왜냐? AsyncSubject가 발행한 **마지막 데이터**가 없기때문이다. 
AsyncSubject가 그 뒤로 데이터를 발행했다고 해서 subscriber에게 데이터를 주는게 아니다. 시간이 지나고 subscribe하면, 왠지 **지금까지** AsyncSubject에 발행된 마지막 데이터가 초록색 원이니까 초록색 원을 줄 것 같지만, 받는건 파란색 원이다. 
여기서 알 수 있는 사실은 AsyncSubject가 complete되고 난 후!!!에. 그러니까 AsyncSubject가 complete된 시점에서의 마지막 데이터를 발행하는 것이다. 
동시에 처음에 subscribe했던 구독자에게도 ㄷㅔ이터가 발행된다.
한마디로, AsyncSubject가 onComplete되는 순간 구독자들에게 데이터를 주는 것이다. 

~~~swift 
let asyncSubject = AsyncSubject<String>()

asyncSubject.subscribe(onNext: { (string) in
            print("subscriber 1 : \(string)")
        }, onCompleted: {
            print("subscriber 1 on completed")
})
asyncSubject.subscribe(onNext: { (string) in
            print("subscriber 2 : \(string)")
        }, onCompleted: {
            print("subscriber 2 on completed")
})
~~~
자 위와같은 코드가 있다고 생각해보자.
asyncSubject라는 ```AsyncSubject```인스턴스를 만들고 걔를 subscribe를 2번했다. 2명의 구독자가 생긴것이다.

하지만 콘솔에는 아무것도 출력이 되지 않는다. 
지금까지는 당연하다. asyncSubject가 아무 데이터를 발행하지 않았기 때문이다.
발행해볼까?

~~~swift 
asyncSubject.onNext("zedd")
asyncSubject.onNext("marshmello")
~~~

자 데이터를 발행했지만, 여전히 콘솔에는 아무것도 찍히지 않는다.
왜냐? 위에서 AsyncSubject의 정의를 봤듯이, AsyncSubject가 onComplete되지 않는 이상 데이터는 subscriber들에게 오지 않는다.
asyncSubject를 onComplete시켜보자.

~~~swift 
asyncSubject.onCompleted()
~~~

자, 이제 콘솔에 뭔가가 찍힐것이다. 

**
subscriber 1 : marshmello
subscriber 2 : marshmello
subscriber 1 on completed
subscriber 2 on completed
**



















