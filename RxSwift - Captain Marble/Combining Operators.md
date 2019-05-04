# Combining Operators


## startWith

뭔가 시작 할떄 뭔가 같이 해주는것 같은 느낌적인 느낌
맞음

~~~swift
var observable = PublishSubject<String>()
        
        observable
        .startWith("Zedd")
        .subscribe(onNext: { (str) in
            print(str)
        })
    
        observable.onNext("Iron Man")
        observable.onNext("Captain America")
        observable.onNext("Captain Marvel")
~~~

출력결과는???


## concat

// concat 마브 다이어그램

간단히 말해서 2개 이상의 옵저버블을 concat(연결, 이음) 하는 오퍼레이터이다. 
특징은 한 옵저버블이 끝나야만, complete되어야만 다음 옵저버블이 이어질 수 있다. 


~~~swift 

var observable = PublishSubject<String>()
        
        var galaxy = PublishSubject<String>()

        observable.concat(galaxy)
            .subscribe(onNext: { (str) in
                print(str)
            })
        observable.onNext("Iron Man")
        observable.onNext("Captain America")
        observable.onNext("Captain Marvel")
        observable.onCompleted()
        
        galaxy.onNext("Star-Lord")
        galaxy.onNext("Gamora")
        galaxy.onNext("Groot")
        galaxy.onCompleted()
~~~

자..이렇게 해주었다. 출력은 뭐가 될까? 

**Iron Man\
Captain America\
Captain Marvel\
Star-Lord\
Gamora\
Groot**

이렇게 된다! 옵저버블간의 순서를 강제해주고 싶을 때 쓰면 된다.

## concatMap

flatMap과 매우 비슷.
flatMap 마블 다이어그램을 보면, flatMap()은 먼저 들어온 데이터를 처리하는 도중에 새로운 데이터가 들어오면 니중에 들어온 데이터ㄹ의 처리 결과가 **먼저** 출력될 수 있음.
> 이를 인터리빙(interleaving)이라고 함.

하지만 ```concatMap```은 먼저 들어온 순서대로 데이터를 처리함. 즉, 순서를 보장함.
다만, flatMap과 비교하여 시간차이가 많이 날 수 있음. (concatMap이 더 오래걸림)

## merge

머지..가 머지?
:)


// 머지 마브 다이어그램

말 그대로 옵저버블을 머지하는 오퍼레이터다. 예제 보자. 


~~~swift

        var observable = PublishSubject<String>()
        var galaxy = PublishSubject<String>()
        
        let avengers = Observable.of(observable, galaxy)
        
        avengers.merge().subscribe(onNext: { (str) in
            print(str)
        })
        
        observable.onNext("Iron Man")
        observable.onNext("Captain America")
        observable.onNext("Captain Marvel")
        
        galaxy.onNext("Star-Lord")
        galaxy.onNext("Gamora")
        galaxy.onNext("Groot")
~~~

출력은..

**Iron Man\
Captain America\
Captain Marvel\
Star-Lord\
Gamora\
Groot**

이다. 

아니 concat이랑 다를게 머임
이런 동기적인 상황에서는 concat과 merge가 같다고 봐도 된다. 근데 concat은 무조건 순서르 보장해주지마 merge는 이론적으로 인터리빙이 가능함 ㅇㅇ


## merge(maxConcurrent)

머지랑 똑같은데 max concurrent가 있네?
함 보자
~~~swift 

  var observable = PublishSubject<String>()
        
        var galaxy = PublishSubject<String>()
        
        let avengers = Observable.of(observable, galaxy)
        
        avengers.merge(maxConcurrent: 1).subscribe(onNext: { (str) in
            print(str)
        })
        
        observable.onNext("Iron Man")
        observable.onNext("Captain America")
        observable.onNext("Captain Marvel")
        
        galaxy.onNext("Star-Lord")
        galaxy.onNext("Gamora")
        galaxy.onNext("Groot")
        
~~~

저기 merge부분에 maxConcurrent를 준 것을 볼 수 있다.
1로...

그럼 결과는??
퀴즈~~


## combineLatest

combineLatest 마블 ㄷㅏ이어그램

각 옵저버블에서 최신의 데이터가 emit될때마다 각 옵저버블의 최신 데이터들이 결합되어서 최종 옵저버블이 만들어진다. 

~~~swift
var observable = PublishSubject<String>()
        
        var galaxy = PublishSubject<String>()
        
        Observable.combineLatest(observable, galaxy, resultSelector: { (lastLeft, lastRight) in
                
                "\(lastLeft) \(lastRight)"
                
        }).subscribe(onNext: { (str) in
            print(str)
        })
~~~

자 이렇게 두개의 옵저버블을 combineLatest을 해주고 두 string을 결합시켜준걸 리턴한다.
이제 onNext로 값을 보내보자.


~~~swift
observable.onNext("Iron Man")
        galaxy.onNext("Star-Lord")
        galaxy.onNext("Gamora")
        
        observable.onNext("Captain America")
        observable.onNext("Captain Marvel")
        
        galaxy.onNext("Star-Lord")
        galaxy.onNext("Gamora")
        galaxy.onNext("Groot")
~~~

이렇게하면₩₩~~

**Iron Man Star-Lord\
Iron Man Gamora\
Captain America Gamora\
Captain Marvel Gamora\
Captain Marvel Star-Lord\
Captain Marvel Gamora\
Captain Marvel Groot**


이렇게 결합된당. 
하나씩 살펴보면, 처음 아이언맨이 들어갔을 때는 아무것도 안나오고 스타로드가 들어갔을 때, 결합되서 나온것을 볼 수 있다. 두번째로 가모라가 들어갔을 때 (아이언맨이랑 다른 옵저버블) 아이언맨이 있는 옵저버블에는 가장 최신의 데이터가 아이언맨이므로 가모라는 아이언맨과 combine되게 된다.



