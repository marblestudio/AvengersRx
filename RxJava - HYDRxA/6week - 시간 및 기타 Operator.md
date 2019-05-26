# 기타 변환 연산자 및 시간 기반 연산자

자주 쓰이게 되는 변환 연산자 및 시간 연산자들을 살펴보자.


## list

- reduce
- scan
- groupBy
- window
- buffer
- delaySubscription
- interval
- timer



## reduce() 함수

> 발행한 데이터를 모두 사용하여 최종 결과 데이터를 합성할 때 활용합니다.
> 
함수형 프로그래밍의 가장 기본 연산자인 map, filter, reduce 중에 하나.



<img src="https://user-images.githubusercontent.com/7722921/57961782-2a8efc80-794c-11e9-995b-6ab68f90ea60.png">

Observable이 onComplete 되는 시점에 결과값을 방출하게 된다. 주로 계산이나 수치를 다룰 때 사용할 수 있다.



```
public final Observable<T> reduce(Func2<T, T, T> accumulator) {
	return scan(accumulator).last();
}
```

reduce 함수 원형을 살펴보니, scan의 가장 마지막 값을 가져오고 있었다.



## scan() 함수
> Observable에서 데이터가 방출될 때 마다 데이터들을 조합한 중간 결과 데이터를 발행합니다.
> 
reduce가 onComplete 시점에 최종 결과값만을 방출한다면, scan은 데이터가 방출될 때마다 중간 결과값을 하나씩 다 보여준다는 차이점이 있다.



<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/scan.png" alt="">



## groupBy() 함수
> 하나의 Observable을 어떤 기준을 통해 나누어 여러 개의 Observable그룹을 생성합니다.
> 


<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/groupBy.png" alt="">

예제 같이 짜보자...


Observable 내에 GroupedObservable이라는 형태로 들어가있음.
```
public final <K> Observable<GroupedObservable<K, T>> groupBy(final Func1<? super T, ? extends K> keySelector) {
	return lift(new OperatorGroupBy<T, K, T>(keySelector));
}
```



## window() 함수
> groupBy과 비슷한 개념으로, 특정 조건 값을 기준으로 별도의 Observable로 분리시킵니다.


<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/window3.png" alt="">

병렬적으로 처리되는 groupBy()와는 다른 형태라는 것을 이해할 수 있다.


함수 원형을 살펴보면, Observable 내에 Observable을 들고있는 형태
```
public final Observable<Observable<T>> window(int count) {
	return window(count, count);
}
```

```
Observable.just(1,2,3,4,5,6,7,8,9)
            .window(3)
            .subscribe { s ->
                println("New Observable")
                s.subscribe { p -> println("onNext $p") } }
```

```
New Observable
onNext 1
onNext 2
onNext 3
New Observable
onNext 4
onNext 5
onNext 6
New Observable
onNext 7
onNext 8
onNext 9
```


## buffer() 함수
> 일정 시간동안 데이터를 모아두었다가 한꺼번에 발행합니다.
 
명시간 조건만큼 데이터를 모았다가 List의 형태로 방출한다.



```
public final Observable<List<T>> buffer(int count) {
	return buffer(count, count);
}
```

<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/buffer4.png" alt="">
skip값 > count값 => '3개 값 중에 2개만 취하고 1개는 스킵한다'



예제를 보면, 
```
Observable.just(1,2,3,4,5,6,7,8,9)
	.buffer(2, 3)
	.subscribe { s -> println("onNext : $s") }
```
count = 2, skip = 3 ==> '3개 중에 2개만 취하고 1개는 스킵한다'
위의 값을 출력해보면

```
onNext : [1, 2]
onNext : [4, 5]
onNext : [7, 8]
```
결과값 납득 ㅇㅋ



그렇다면,
count = 3, skip = 2 일 경우
```
Observable.just(1,2,3,4,5,6,7,8,9)
            .buffer(3, 2)
            .subscribe { s -> println("onNext : $s") }
```
결과값이 어떻게 출력될까?
???



## delaySubscription() 함수



일정 시간을 지연시켜 데이터를 방출하는 delay 함수는 다들 알고 있을 것이다.
<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/delay.png" alt="">


<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/delaySubscription.s.png" alt="">
 
값의 방출시점 뿐만 아니라, 구독의 시점까지도 지연시킬 수 있는 함수가 delaySubscription.
출력되는 결과값만으로는 차이가 드러나진 않는다. 구독 시점까지도 제어하고 싶을 때 사용.



## interval() 함수
> 일정 시간 간격으로 데이터를 방출하여 Observable을 생성합니다.

롤링배너를 구현할 때 사용하면 유용하다.


## timer() 함수
> 일정 시간이 지난 후에 한 개의 데이터를 발행하고 onComplete()를 발생합니다.
 
interval 함수와의 차이점은 '한번만' 실행한다는 점이다.


<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/timer.png" alt="">
interval 함수의 첫번째 값인 '0' 만을 발행하고 끝난다.



## timeInterval
> 어떤 값을 발행했을 때, 이전 값을 발행한 이후 얼마나 시간이 흘렀는지 알려줍니다.



<img src="https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/timeInterval.png" alt="">



간단한 예제 살펴보면,
```
Observable.interval(333, TimeUnit.MILLISECONDS)
            .timeInterval()
            .subscribe { s -> println("onNext : $s") }
```
```
onNext : TimeInterval [intervalInMilliseconds=341, value=0]
onNext : TimeInterval [intervalInMilliseconds=330, value=1]
onNext : TimeInterval [intervalInMilliseconds=331, value=2]
onNext : TimeInterval [intervalInMilliseconds=334, value=3]
onNext : TimeInterval [intervalInMilliseconds=333, value=4]
onNext : TimeInterval [intervalInMilliseconds=332, value=5]
.
.
.
```
이런 함수도 있구나~ 정도로 알아두면 될 것 같음.


