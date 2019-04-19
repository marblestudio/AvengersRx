# Operator - Filtering

이번 시간에는 Operator에 대해서 알아볼건데,
그 중에서도 **필터링 연산자**의 종류와 사용 예제를 알아보자.


## list

- Debounce
- Distinct
- ElementAt
- Filter
- IgnoreElements
- Sample
- Skip - SkipLast
- Take - TakeLast

참고 : http://reactivex.io/documentation/operators.html#filtering


## 1. debounce

>특정 시간대가 다른 항목을 방출하지 않고, 통과한 경우에만 Observable에서 항목을 방출합니다. 

-> 뭔 소린지...?


여기 그림을 보자

![debounce](https://user-images.githubusercontent.com/7722921/56428070-0d3d2300-62f9-11e9-8a48-38ca8c6f56c3.png)

일정 시간 간격을 설정한 후, 그 시간 간격 내로 들어오는 이벤트는 '무시'하고, 시간 간격이 지난 이후에는 가장 마지막 이벤트를 방출한다.

![debounce2](https://user-images.githubusercontent.com/7722921/56428408-37dbab80-62fa-11e9-9daf-b1085cb302e6.png)

시간 간격을 **3초**로 설정했다고 가정.
노란 공 - [1초] - 초록 공 - [4초] 시간 차가 있을 때, 노란 공 이벤트는 무시된다.
*왜냐? 설정한 시간 간격이 다 지나기도 전에 다음 이벤트가 들어왔기 때문에* 
노란 공은 '무시'되고 시간 간격 내 가장 마지막 이벤트인 초록 공만이 방출된다.


## debounce 실 활용 예
>TextView에서 실시간으로 입력받는 즉시 api를 call 하여 결과를 보여주는 경우, 시간 간격을 설정하여 완성도 있는 키워드로 검색결과를 불러올 수 있다. 
>=> api 호출 비용 절약.


## debounce 실 활용 예
<img src="https://user-images.githubusercontent.com/7722921/56428875-e3d1c680-62fb-11e9-88ae-81d0feccddc1.png" width="300">

식후감에서 가게명을 입력하면 실시간으로 주소를 검색하는 코드를 확인해보자.



<img width="400" alt="스크린샷 2019-04-19 오후 11 42 09" src="https://user-images.githubusercontent.com/7722921/56429141-cea96780-62fc-11e9-88c5-0cd2080e7409.png"> 
지난 시간에 배운 PublishRelay
<img width="486" alt="스크린샷 2019-04-19 오후 11 43 11" src="https://user-images.githubusercontent.com/7722921/56429167-ebde3600-62fc-11e9-945d-678618fa1954.png">
accept()를 통해 검색어를 넘겨줌.
<img width="573" alt="스크린샷 2019-04-19 오후 11 44 02" src="https://user-images.githubusercontent.com/7722921/56429195-07494100-62fd-11e9-903a-ad160c0e7a17.png">
0.3초로 시간 간격을 설정.



## 2. Distinct
>이 연산자는 Observable에 의해 생성된 중복 항목을 억제합니다.
>
>한 마디로, 중복 항목은 무시하는 연산자.


## distinct 예제 - mini Quiz!
![distinct](https://user-images.githubusercontent.com/7722921/56430228-b0456b00-6300-11e9-9934-6e68a52d624d.png)
결과값은? 
넘나쉽다진짜루
ㅋㅋ






## 파생 연산자 - distinctUntilChanged
>이전 이벤트와 비교해서 다른 값일 경우에 이벤트를 방출, 같으면 무시합니다.
>
>말 그대로, 값에 변화가 생길 때만 이벤트를 방출.



## 3. ElementAt
>이 연산자는 Observable에서 방출한 하나의 항목 ' n' 만 내보냅니다.
>


인덱스 n 에 있는 값만을 방출한다.

<img width="714" alt="스크린샷 2019-04-20 오전 12 47 50" src="https://user-images.githubusercontent.com/7722921/56431769-f6e99400-6305-11e9-8e3d-f4265eb0c739.png">

설명이 필요가 없음. ㅇㅇ.



## 4. Filter
>이 연산자는 술어 테스트를 통과한 Observable의 항목만 내보냅니다.



Filter 연산자는 Observable을 필터링하여 조건 테스트를 통과하는 이벤트만을 방출한다.

<img src="https://user-images.githubusercontent.com/7722921/56431983-c1917600-6306-11e9-84d4-01cfce0b3edc.png">



앞에서도 한번 나왔던 코드

<img width="573" alt="스크린샷 2019-04-19 오후 11 44 02" src="https://user-images.githubusercontent.com/7722921/56429195-07494100-62fd-11e9-903a-ad160c0e7a17.png">

중간 부분에 *it.isNotBlank()* 로 빈 값이 아니면 다음 연산을 시행하는 방식으로 사용되었다.



## 5. IgnoreElements
>이 연산자는 Observable에서 항목을 방출하지 않지만 종료 알림 (onComplete 또는 onError)을 반영합니다. 



Observable의 내용은 관심없고, complete 또는 error 가 어떻게 떨어지는지만 알고 싶을 경우에 사용한다. 



<img width="778" alt="스크린샷 2019-04-20 오전 1 23 40" src="https://user-images.githubusercontent.com/7722921/56433192-03bcb680-630b-11e9-833e-3ab19ad6b52b.png">

결과값
<img width="237" alt="스크린샷 2019-04-20 오전 1 25 08" src="https://user-images.githubusercontent.com/7722921/56433232-277ffc80-630b-11e9-872d-80cf8de27823.png">

onNext가 호출되지 않는다.



## 6. Sample
>이 연산자는 Observable이 방출한 가장 최근의 항목을 주기적인 시간 간격으로 방출합니다.




<img src="https://user-images.githubusercontent.com/7722921/56433925-9199a100-630d-11e9-86da-df0c5c287420.png">

Observable을 일정한 시간 간격으로 쪼갠 후, 각 마디에서 가장 마지막 값을 취한다고 생각하면 쉽다.

샘플 채취하듯이 각 시간마디에서 하나씩 뽑아내는 느낌으로 이해하자.


## Sample예제 - mini Quiz
<img src="https://user-images.githubusercontent.com/7722921/56434211-a32f7880-630e-11e9-8e87-b9a04752dddd.png">

결과값은 어떻게 출력될까?

단, take(5) -> 5개 이벤트만 방출한다.






## 7. Skip
>Observable에 의해 방출된 첫번째 n개 항목을 표시하지 않습니다.



설명이 필요없음...ㅇㅇ.

<img src="https://user-images.githubusercontent.com/7722921/56434554-040b8080-6310-11e9-9d4a-cb6f42641354.png">



<img src="https://user-images.githubusercontent.com/7722921/56434600-35844c00-6310-11e9-9076-c4c2834c3e81.png">

```{.java}
skip(long time, TimeUnit unit, Scheduler scheduler)
```

위와 같이 시간 구간을 지정할 수도 있다.



## 파생 연산자 - SkipLast
뒤에서 부터 n개 항목을 무시.

<img width="713" alt="스크린샷 2019-04-20 오전 2 08 06" src="https://user-images.githubusercontent.com/7722921/56434863-6618b580-6311-11e9-86f0-66469b2656db.png">



## 8. Take
>Observable에 의해 방출된 첫번째 n 개의 항목만을 취합니다.
>Skip과는 반대 개념.



어후.. 이미지 셔틀

<img src="https://user-images.githubusercontent.com/7722921/56434984-ca3b7980-6311-11e9-8671-fd2fadc6c760.png">



사실 아까 예제에서도 봤었다.

<img src="https://user-images.githubusercontent.com/7722921/56434211-a32f7880-630e-11e9-8e87-b9a04752dddd.png">


얘도 역시 정수값 대신 시간 구간을 지정할 수 있다.
```{.java}
take(long time, TimeUnit unit, Scheduler scheduler)
```


## 파생 연산자 - TakeLast
설명 안해도..ㅇㅋ?



이번 시간에는 **필터링 연산자**에 대해서 알아보았다.
사실 생각보다 실제 업무에 적용해볼 만한 적당한 예제나 사례가 안떠올라서
이미지 셔틀이 되어버렸는데, 
다음에는 더 예제에 대한 고민을 많이 해봐야겠다...
