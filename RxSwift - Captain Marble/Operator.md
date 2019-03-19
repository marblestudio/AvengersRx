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
 -- 인자로 넣은 데이터를 차례로 발행하는 Observable 생성.
--  **최대 10개**까지 값을 넣을 수 있음(같은 타입에 한하여)
- RxSwift
-- 단 하나의 데이터만 넣을 수 있음.

### of 
- RxJava 
-- 없음

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

~~~swift 
Observable<Int>.interval(1.0, scheduler: MainScheduler.instance).subscribe(onNext: { num in
		print(num)
})
~~~
