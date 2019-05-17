# Time Based Operators

### Buffering Operators

##### 1. Replaying past elements 

~~~swift
let timer = Observable<Int>.interval(1,scheduler:MainScheduler.instance)
let replay = timer.replay(2)
_ = replay.connect()

replay.subscribe(onNext:{
	print("first replay: \($0)")
}).disposed(by: self.disposeBag)
        
replay.delaySubscription(3, scheduler: MainScheduler.instance).subscribe(onNext:{
	print("second replay: \($0)")
}).disposed(by: self.disposeBag)
~~~

delaySubscription은 디프리케이트 된 오퍼레이터지만, 교재에 나와있는 관계로 사용하였다. 

위의 코드에서는 버퍼 2만큼 저장해놓고, 3초후에 다른 옵저버가 구독하는 코드이다. 출력은 아래와 같이 진행된다.

first replay: 0
first replay: 1
first replay: 2
second replay: 1
second replay: 2
first replay: 3
second replay: 3
...

- replayAll()은 모든 이벤트를 저장해둔다. 하지만 메모리 성능 이슈가 있을 수 있으니 주의하도록 하자.

### Time Operator

##### 1.interval

~~~swift
let interval = Observable<Int>.interval(1,scheduler:MainScheduler.instance)
        interval.subscribe(onNext:{
            print("timer : \($0)")
        }).disposed(by: self.disposeBag
~~~

예제에서 주로 쓰이는 interval이다. 넘겨준 인자수만큼 이벤트를 방출해주는 오퍼레이터다.



> 이번 스터디에서 진행하기로 했던 오퍼레이터(replay, replayAll, buffer, window, delaySubscription, interval, timer, timeout) 중에서 replay, replayAll, interval을 제외하고는 모두.. 디프리게이티드 되었다..
>
> 오늘은 여기까지..