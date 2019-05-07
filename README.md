# AvengersRx

## What is Rx?
- [HYDRxA - ReactiveX](https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20HYDRxA/1week%20-%20ReactiveX.md)
- [viSIon - Rx. 그것은 무엇인가](https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20viSIon/1W-Rx%20그것은%20무엇인가.md)
- [Captain Marble - ReactiveX](https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20Captain%20Marble/ReactiveX.md)
- [thanOS - Why ReactiveX](https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20thanOS/1w-Why%20ReactiveX%3F.md)
## Observable & Scheduler

- [HYDRxA - Observable](https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20HYDRxA/2week%20-%20Observable.md)
- [thanOS - Observable_Scheduler](https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20thanOS/2w-Observable_Scheduler.md)

## Subject & Relay

- [viSIon - Subject&Relay](https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20viSIon/3W%20-%20Subject%26Relay.md)
- [Captain Marble - Subject](https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20Captain%20Marble/Subject.md)
- [Captain Marble - Relay](https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20Captain%20Marble/Relay.md)
- [thanOS - Subject_Relay](https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20thanOS/3w-Subject_Relay.md)


## Operator
> RxJava기준 Operator임. 
>
> ###  필터링 연산자
- ignoreElements, elementAt, filter, skip, skipWhile, skipUntil, take, takeWhile, enumerated, takeUntil, distinctUntilChanged
  - [thanOS - Filtering Operator](<https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20thanOS/4w-Filtering_Operator.md>)
  - [HYDRxA - Filtering Operator](<https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20HYDRxA/4week%20-%20filtering_operator.md>)

### 변환 연산자
- toArray, map, enumerated, flatMap, flapMapLatest, materialize, dematerialize, unwrap
  - [Captain Marble - Transforming Operator(Avengers ver.)](<https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20Captain%20Marble/Transforming%20Operator(Avengers%20ver.).md>)
  - [viSIon - Transforming&Combining.md](<https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20viSIon/5W%20-%20Transforming%26Combining.md>)
  - [thanOS - 5w-Transforing Operator](<https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20thanOS/5w-Transforming_Operator.md>)

### 결합 연산자 (1) 
- startWith, concat, concatMap, merge, merge(maxConcurrent), combineLatest
  - [Captain Marble - Combining Operators](<https://github.com/marblestudio/AvengersRx/blob/master/RxSwift%20-%20Captain%20Marble/Combining%20Operators.md>)
  - [viSIon - Transforming&Combining.md](<https://github.com/marblestudio/AvengersRx/blob/master/RxJava%20-%20viSIon/5W%20-%20Transforming%26Combining.md>)
  
### 결합 연산자 (2) 
- zip, withLatestFrom, sample, amb, switchLatest, reduce, scan

### 시간 기반 연산자
- replay, replayAll, buffer, window, delaySubscription, interval, timer, timeout
