# Operator - Transforming Observables(avengers ver.)

## toArray
이름만 보면 알 수 있듯이, Array로 바꿔주는 operator같은 느낌이다.
~~~swift
     Observable.of("Iron Man", "Captain America", "Captain Marvel")
            .toArray()
            .subscribe(onNext: { (avengers) in
                print(avengers)
            })
~~~
결과는? ["Iron Man", "Captain America", "Captain Marvel"]

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

## enumerated
enumerated뜻은 뭐 일일이 헤아리다..정도로 보면 된다. 이것도 마찬가지로 굳이 Rx가 아니더라도..많이 본거라..
RxSwift에서 뜻은, An observable sequence that contains tuples of source sequence elements and their indexs. 
인덱스와 그 element을 포함한 tuple 옵저버블을 리턴한다. 

~~~swift 
Observable.of("Iron Man", "Captain America", "Captain Marvel")
            .enumerated()
            .subscribe(onNext: { (avengers) in
                print(avengers)
            })
~~~

이렇게 하면,

**(index: 0, element: "Iron Man")\ 
(index: 1, element: "Captain America")\ 
(index: 2, element: "Captain Marvel")**






















