# Operator

## 그 전에..Operator?

데이터를 **조작**한다고 생각하면 된다. 이러한 조작에는 생성, 변환, 결합 등 다양한 카테고리가 존재한다. 
언어별로 공통적으로 제공되는 오퍼레이터도 있지만, 반대로 특정 언어(구현체)에서만 제공하는 오퍼레이터들도 존재한다. 

거의 모든 오퍼레이터들은 Observable상에서 동작하고 Observable을 리턴한다.
때문에, 오퍼레이터를 연달아 호출 할 수 있는 오퍼레이터 체인을 제공한다.
> 오퍼레이연산자 체인은 **호출순서**에 따라 실행결과가 달라진다.

> 따라서, Observable의 오퍼레이터 체인은 원본 Observable과 독립적으로 실행될 수 없고 _순서대로_ 실행되어야 한다. 왜냐하면, 이미 이해하고 있듯이 오퍼레이터 체인에서 먼저 실행된 오퍼레이터가 리턴한 Observable을 기반으로 다음 오퍼레이터가 동작하기 때문이다.


## 생성연산자
앞에서 본거랑 같다. 말그대로 Observable을 생성해주는 Operator. (= 데이터의 흐름을 만든다.)

```just(), of(), create(), fromXXX()...등 ```

하나하나 보도록 하자.

### just
- RxJava
> 인자로 넣은 데이터를 차례로 발행하는 Observable 생성.
>  **최대 10개**까지 값을 넣을 수 있음(같은 타입에 한하여)
- RxSwift
> 단 하나의 데이터만 넣을 수 있음.

### of 
- RxJava 
>  없음

- RxSwift
~~~swift 
let observable2 : Observable<Int> = Observable.of(1, 2, 3)
~~~
주어진 값들의 타입추론을 통해 Observable 생성. 값들을 차례로 발행한다. 


~~~swift 
let observable2: Observable<[Int]> = Observable.of([1, 2, 3])
~~~
이렇게 배열도 잘 받을 수 있음. 
[1, 2, 3]이라는 단일 요소를 넣었기 때문에

~~~swift 
let observable1: Observable<[Int]> = Observable.just([1, 2, 3])
~~~

just를 한것과 결과 동일.

### interval
일정 시간 간격으로 데이터를 발행한다.
특이사항(?)은 스케쥴러를 지정해줘야 한다. 

~~~swift 
Observable<Int>.interval(1.0, scheduler: MainScheduler.instance).subscribe(onNext: { num in
print(num)
})
~~~

결과 : 0 1 2 3 .....

interval은 기본적으로 **영원히 지속**되기 때문에 폴링용도로 많이 사용.

### timer
timer는 interval과 유사하지만 **한 번만** 실행하는 실행하는 오퍼레이터다.
지정된 시간 이후에 한개의 데이터를 발행하고, onComplete()이벤트가 발생한다. 

~~~swift 
Observable<Int>.timer(2, scheduler: MainScheduler.instance).subscribe(onNext: { (num) in
            print(num)
        }, onCompleted: {
            print("on complete")
        })
~~~

결과: 0 "on complete"

보통 일정시간이 지난 후 어떤 동작을 하고 싶을 때 사용.

### range
range는 주어진 값(n) 부터 m개의 integer객체를 발행한다.

~~~swift 
Observable<Int>.range(start: 1, count: 100).subscribe(onNext: { (num) in
            print(num)
        }, onCompleted: {
            print("on completed")
        })
~~~

결과: 1 2 3 4 ... 99 100 "on completed"
스케쥴러를 따로 지정 x, 즉 현재 쓰레드에서 실행. 즉, range오퍼레이터는 for, while문을 대체 할 수 있음.

### intervalRange(RxSwift x)
그냥 interval + Range를 합친거
interval처럼 일정한 시간 간격으로 값을 발행하지만 range처럼 n부터 m개의 값만 발행하고 onComplete()발생.
즉, interval처럼 무한히 값을 발행하지 x


RxSwift에는 없는 오퍼레이터인데, 있는 오퍼레이터를 가지고 만들 수 있다. 

~~~swift 
let interval = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
let range = Observable.range(start: 1, count: 10)
 Observable.zip(interval, range)
           .subscribe(onNext : { (_, num) in
                print(num)
            }, onCompleted: {
                print("on complete")
            })
~~~

결과: 1 2 3....9 10 "on complete"

### defer 

> do not create the Observable until the observer subscribes, and create a fresh Observable for each observer

defer는 timer와 비슷하지만 데이터 흐름 생성을 구독자가 ```subscribe()```를 호출할 때 까지 미룰 수 있다.
즉, ```subscribe``` 하는 순간 새로운 ```Observable```생성


//defer 마블 다이어그램

defer의 핵심은 "and create a fresh Observable for each observer"
구독할 때 마다 새로운 Observable로 만들어지므로 최신의 데이터를 받을 수 있음.

~~~swift 
func permissionObservable() -> Observable<PHAuthorizationStatus> {
	return .just(PHPhotoLibrary.authorizationStatus())
}
~~~

사진 접근 권한 얻어오는 코드.
위 코드는 ```subscribe```할 때의 접근권한 상태를 가져오는게 아니라, Observable이 만들어질 그 때의 상태를 가져옴. 만들어질때와 subscribe할 때의 사진 권한은 다를 수 있음. 이때 defer를 쓰면 유용하다.

~~~swift 
func permissionObservable() -> Observable<PHAuthorizationStatus> {
	return Observable.deferred {
		return .just(PHPhotoLibrary.authorizationStatus())
	}
}
~~~
> RxSwift에서는 ```deferred```라는 이름으로 있다. 

### repeat 
말 그대로 반복하는 오퍼레이터. 네트워킹을 할 때, 해당 서버가 잘 살아있는지 확인할 때 쓴다고 함.

RxJava같은 경우에, repeat()에 인자를 넣지 않으면 무한히 반복하고 repeat(3)처럼 인자를 넣으면 3번 반복하고 종료함.
RxSwift경우(repeat이 아니라 repeatElement임), repeatElement임()의 인자는 반복할 element. 즉, 무한히 반복하는건 디폴트고 어떤걸 무한히 반복할건지 인자안에 넣어줌. 
> ex) repeatElement임(3) -> 3이 무한히 반복 

~~~swift 
Observable<Int>.repeatElement(1).subscribe(onNext: { (num) in
            print(num)
        }, onCompleted: {
            print("on complete")
        })
~~~
결과: 1 1 1 ... 무한

```take```를 이용해 데이터 발행을 제한 할 수 있음. 

~~~swift 
 Observable<Int>.repeatElement(1)
 		.take(3)
            	.subscribe(onNext: { (num) in
            		print(num)
        	}, onCompleted: {
          		print("on complete")
})
~~~

결과 : 1 1 1 "on complete"
