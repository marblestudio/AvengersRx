# Transforming Observables

## map

~~~swift 
Observable.just(4).map { $0 * 2 }.subscribe(onNext: { (num) in
            print(num)
        }, onCompleted: {
            print("on complete")
        })
~~~
결과: 8 "on complete"
가장 간단한 변환 오퍼레이터.
Observable이 배출한 항목에 함수를 적용한다.

## flatMap
flatMap도 하나의 map임. 데이터를 조작할 수 있다. 
하나의 Observable이 발행하는 항목들을 **여러개의 Observable로 변환**하고, 항목들의 배출을 차례차례 줄 세워 하나의 Observable로 전달한다.
```map()```은 원하는 입력값을 어떤 함수에 넣어서 변환 할 수 있는 1:1 함수이지만,
```flatMap()```은 똑같이 함수에 넣더라도 **결과가 Observable**로 나온다.


~~~swift 
Observable.just(4).flatMap { Observable.just($0 * 2) }.subscribe(onNext: { (num) in
            print(num)
        }, onCompleted: {
            print("on complete")
        })
~~~
결과: 8 "on complete"
flatMap에서 Observable로 만들어줘서 리턴해줘야함. 

## concatMap
flatMap과 매우 비슷.
flatMap 마블 다이어그램을 보면, flatMap()은 먼저 들어온 데이터를 처리하는 도중에 새로운 데이터가 들어오면 니중에 들어온 데이터ㄹ의 처리 결과가 **먼저** 출력될 수 있음.
> 이를 인터리빙(interleaving)이라고 함.

하지만 ```concatMap```은 먼저 들어온 순서대로 데이터를 처리함. 즉, 순서를 보장함.
다만, flatMap과 비교하여 시간차이가 많이 날 수 있음. (concatMap이 더 오래걸림)

### switchMap(RxJava)

switchMap은 또 concatMap이랑은 다름.
concatMap()이 인터리빙이 발생 할 수 있는 상황에서 동작의 순서를 보장해준다면, switchMap()은 순서를 보장하기 위해 기존에 진행중이던 작업을 바로 중단합니다. 
// switchMap 마블 다이어그램

즉 마지막에 들어온 데이터의 처리는 보장한다!
switchMap은 센서의 값을 얻어와서 처리하는 경우 유용하다. 센서값은 중간값보다는 최종적인 값으로 결과를 처리하는 경우가 많기 때문.

> RxSwift에서는 **flatMapLatest**라는 이름으로 있다.

### groupBy
원본 Observable이 배출하는 항목들을 키(Key) 별로 묶은 후 Observable에 담는다. 
이렇게 키 별로 만들어진 Observable들은 자기가 담고 있는 묶음의 항목들을 배출한다
~~~swift
 Observable.from([1,2,3,4,5,6,7,8,9,10])
            .groupBy(keySelector: { (value) -> Bool in
                return value % 2 == 0
            })
            .flatMap({ (grouped) -> Observable<[Int]> in
                return grouped.toArray()
            })
            .subscribe(onNext: { (result) in
                print(result)
            })
~~~

결과: 
[1,3,5,7,9]
[2,4,6,8,10]

### scan
Observable이 배출한 항목에 **연속적으로** 함수를 적용하고 실행한 후 성공적으로 실행된 함수의 리턴 값을 발행한다.
```reduce()```와 비슷. reduce는 Observable에서 모든데이터가 입력된 후, 그것을 종합하여 마지막 1개의 데이터만을 subscriber에게 발행한다.
반면 scan은 실행할 때 마다 **중간 결과 및 최종결과**를 subscriber에게 발행.

~~~swift
  Observable.of(1,2,3,4,5,6,7,8,9,10)
            .reduce(0, accumulator: +)
            .subscribe(onNext: { (num) in
                        print(num)
            }, onCompleted: {
                        print("on complete")
            })
~~~

결과: 55 "on complete"


~~~swift
 Observable.of(1,2,3,4,5,6,7,8,9,10)
           .scan(0, accumulator: +)
           .subscribe(onNext: { (num) in
                  print(num)
             }, onCompleted: {
                  print("on complete")
             })
~~~
결과:

1
3
6
10
15
21
28
36
45
55
"on complete"
