# Operator - Combining

이번 시간에는 Combining(결합) 연산자들에 대해서 알아보자.
결합 연산자들은 여러 개의 Observable을 조합하여 하나의 Observable로 만드는 경우에 유용한 녀석들이다.



# list

- zip
- withLatestFrom
- switchOnNext
- amb



## zip() 함수

> 여러 Observable에서 발행된 데이터를 결합하여 단일 데이터를 방출합니다.

결합 연산자의 가장 대표적인 함수라고 말할 수 있겠다. 여러 Observable의 데이터를 조합하여 하나의 Observable을 만들어낸다. 최대 9개의 Observable을 결합할 수 있다. 예시를 보자.



```
val observable = 
    Observable.zip(
        Observable.just("image1", "image2", "image3"),
        Observable.just("caption1", "caption2", "caption3", "caption4")
    ) { img, desc -> Pair(img, desc) }
        .subscribe { s -> println("onNext : $s") }
```

결과값은
```
onNext : (image1, caption1)
onNext : (image2, caption2)
onNext : (image3, caption3)
```
모든 Observable에서 데이터가 방출되면 그 시점에 데이터 결합이 이루어진다.
-> 그러니까 만약에 1번 Observable, 2번 Observable, 3번 Observable을 zip함수로 결합시켰는데, 2번 Observable이 아무런 이벤트도 발생시키지 않는 비어있는 녀석이라면, zip을 해도 결과값이 나오지 않을 것이다.



### (응용) 시간과 결합하기

zip함수를 시간과 결합하여 사용하면, 데이터를 발행하는 시간을 조절할 수 있다.

```
Observable.zip(
	Observable.just("image1", "image2", "image3"),
	Observable.interval(1000L, TimeUnit.MILLISECONDS)
) { img, time -> img }
	.subscribe { s -> println("onNext : $s") }
```

> 1초마다 한번씩 결과값이 출력된다.
> 



## withLatestFrom() 함수

> 여러 개의 Observable을 결합하는데, 하나의 Observable을 기준으로 값이 변경될 때마다, 다른 Observable의 최신 값을 받아와 결합한다.
> 



<img alt="스크린샷 2019-05-18 오전 12 20 08" src="https://user-images.githubusercontent.com/7722921/57938227-b7ad6380-7902-11e9-81ae-668c0a60aaf2.png">

첫번째 Observable이 변경될 때 마다 각 Observable의 최신 값들을 가져와 결합하여 데이터를 방출.
각각의 Observable이 변경될 때 마다 데이터를 방출하는 combineLatest와는 차이점이 있다.



<img height="600" src="https://user-images.githubusercontent.com/7722921/57939639-b3367a00-7905-11e9-9775-aa4e5a29ddb7.png"> 상단에 **다음** 버튼을 눌렀을 때의 코드를 보자.



<img alt="스크린샷 2019-05-18 오전 12 40 37" src="https://user-images.githubusercontent.com/7722921/57939585-9b5ef600-7905-11e9-9f40-1db923b43c2e.png">

**다음** 버튼을 누른 시점에 **제목, 내용, 태그**의 최신 값과 검색하여 선택한 **음식점 정보**의 최신 값이 존재하면 조합하여 새로운 데이터를 만들어 낸다.
만약, **다음** 버튼을 눌렀을 때 입력한 내용이 없다면 아무 동작도 하지 않을 것이다.



## switchOnNext() 함수
> 여러 Observable을 변경해가면서 원하는 값만을 수신한다.

이제까지는 방출된 데이터끼리 조합하여 새로운 데이터를 담은 Observable을 만들어냈다면, switchOnNext()는 여러 Observable의 일부 데이터를 그대로 가져와 하나의 Observable로 만들 수 있다.



<img src="https://user-images.githubusercontent.com/7722921/57941391-c77c7600-7909-11e9-8192-e5ec4e90cd46.png">

사실 어떻게 써야되는지 모르겠음...;;ㅠㅠ
추후 예제 추가 예정


## amb() 함수

> 여러 Observable 중 가장 먼저 데이터를 발행하는 Observable을 선택하여 그 해당 Observable 데이터만을 가져온다.



<img src="https://user-images.githubusercontent.com/7722921/57944429-ef6fd780-7911-11e9-8b42-74adb48d51b7.png">

최대 9개의 Observable까지 비교할 수 있다.
