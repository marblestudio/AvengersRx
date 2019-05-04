# Transforming & Combining Operator

변환과 결합 연산자를 알아보자

#####  변환 연산자

- toList
- map
- flatMap
- concatMap
- switchMap
- materialize
- dematerialize

##### 결합 연산자

- startWith
- concat
- merge
- combineLatest

## toList

Observable을 하나의 List로 만들어서 처리하게하는 오퍼레이터다.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/toList.2.png)

```java
  static final class ToListObserver<T, U extends Collection<? super T>> implements Observer<T>, Disposable {
        final SingleObserver<? super U> downstream;

        U collection;

        Disposable upstream;

        ToListObserver(SingleObserver<? super U> actual, U collection) {
            this.downstream = actual;
            this.collection = collection;
        }

        @Override
        public void onSubscribe(Disposable d) {
            if (DisposableHelper.validate(this.upstream, d)) {
                this.upstream = d;
                downstream.onSubscribe(this);
            }
        }

        @Override
        public void dispose() {
            upstream.dispose();
        }

        @Override
        public boolean isDisposed() {
            return upstream.isDisposed();
        }

        @Override
        public void onNext(T t) {
            collection.add(t);
        }

        @Override
        public void onError(Throwable t) {
            collection = null;
            downstream.onError(t);
        }

        @Override
        public void onComplete() {
            U c = collection;
            collection = null;
            downstream.onSuccess(c);
        }
    }
```

위 ToListObserver에서 볼 수 있듯이 반환형은 Single이며 Observable에서 emit된 값들을  하나로 묶어서 onComplete가 호출되는 시점에 onSuccess를 호출한다.



## map

대표적인 변환 오퍼레이터 설명은 딱히 필요없을듯.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/map.png)

```java
static final class MapObserver<T, U> extends BasicFuseableObserver<T, U> {
        final Function<? super T, ? extends U> mapper;

        MapObserver(Observer<? super U> actual, Function<? super T, ? extends U> mapper){
            super(actual);
            this.mapper = mapper;
        }

        @Override
        public void onNext(T t) {
            if (done) {
                return;
            }

            if (sourceMode != NONE) {
                downstream.onNext(null);
                return;
            }

            U v;

            try {
                v = ObjectHelper.requireNonNull(
                    mapper.apply(t), "The mapper function returned a null value."
                );
            } catch (Throwable ex) {
                fail(ex);
                return;
            }
            downstream.onNext(v);
        }

        @Override
        public int requestFusion(int mode) {
            return transitiveBoundaryFusion(mode);
        }

        @Nullable
        @Override
        public U poll() throws Exception {
            T t = qd.poll();
            return t != null ? ObjectHelper.<U>requireNonNull(
                mapper.apply(t),
                "The mapper function returned a null value."
            ) : null;
        }
    }
```

위 구조로 Observer가 구성되어있고 Function이라는 함수형 interface를 이용하여 map을 수행한다.

Android는 java8을 지원하지 않으니까 당연한 부분

```java
/**
 * A functional interface that takes a value and returns another value, possibly with a
 * different type and allows throwing a checked exception.
 *
 * @param <T> the input value type
 * @param <R> the output value type
 */
public interface Function<T, R> {
    /**
     * Apply some calculation to the input value and return some other value.
     * @param t the input value
     * @return the output value
     * @throws Exception on error
     */
    R apply(@NonNull T t) throws Exception;
}
```



## flatMap, concatMap, switchMap

emit된 값을 파라미터로 다른 Observable을 생성할때 쓰는 오퍼레이터들이다.

서로 약간의 차이점이 있는데 하나씩 봐보자.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/flatMap.png)

flatMap은 보다시피 순서보장이 안된다.

## ![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/concatMap.png)

concatMap은 순서보장이 된다.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/switchMap.png)

switchMap은 보다시피 이전 타임라인에서 발행되지 못한 값이 있으면 무시되고 가장 최근에 값이 emit된다. 

## materialize

materialize는 Notification이라는 기존 값과, onNext, onError, onCompelte 상태를 알 수 있는 객체로 변환시켜주는 오퍼레이터다.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/materialize.png)

```java
static final class MaterializeObserver<T> implements Observer<T>, Disposable {
        final Observer<? super Notification<T>> downstream;

        Disposable upstream;

        MaterializeObserver(Observer<? super Notification<T>> downstream) {
            this.downstream = downstream;
        }

        @Override
        public void onSubscribe(Disposable d) {
            if (DisposableHelper.validate(this.upstream, d)) {
                this.upstream = d;
                downstream.onSubscribe(this);
            }
        }

        @Override
        public void dispose() {
            upstream.dispose();
        }

        @Override
        public boolean isDisposed() {
            return upstream.isDisposed();
        }

        @Override
        public void onNext(T t) {
            downstream.onNext(Notification.createOnNext(t));
        }

        @Override
        public void onError(Throwable t) {
            Notification<T> v = Notification.createOnError(t);
            downstream.onNext(v);
            downstream.onComplete();
        }

        @Override
        public void onComplete() {
            Notification<T> v = Notification.createOnComplete();

            downstream.onNext(v);
            downstream.onComplete();
        }
    }
```

Observer는 이런식으로 기존 값을 Notification으로 래핑하여 넘겨주게 되어있다.

```java
/**
 * Represents the reactive signal types: onNext, onError and onComplete and
 * holds their parameter values (a value, a Throwable, nothing).
 * @param <T> the value type
 */
public final class Notification<T> {

    final Object value;

    /** Not meant to be implemented externally. */
    private Notification(Object value) {
        this.value = value;
    }

    /**
     * Returns true if this notification is an onComplete signal.
     * @return true if this notification is an onComplete signal
     */
    public boolean isOnComplete() {
        return value == null;
    }

    /**
     * Returns true if this notification is an onError signal and
     * {@link #getError()} returns the contained Throwable.
     * @return true if this notification is an onError signal
     * @see #getError()
     */
    public boolean isOnError() {
        return NotificationLite.isError(value);
    }

    /**
     * Returns true if this notification is an onNext signal and
     * {@link #getValue()} returns the contained value.
     * @return true if this notification is an onNext signal
     * @see #getValue()
     */
    public boolean isOnNext() {
        Object o = value;
        return o != null && !NotificationLite.isError(o);
    }

    /**
     * Returns the contained value if this notification is an onNext
     * signal, null otherwise.
     * @return the value contained or null
     * @see #isOnNext()
     */
    @SuppressWarnings("unchecked")
    @Nullable
    public T getValue() {
        Object o = value;
        if (o != null && !NotificationLite.isError(o)) {
            return (T)value;
        }
        return null;
    }

    /**
     * Returns the container Throwable error if this notification is an onError
     * signal, null otherwise.
     * @return the Throwable error contained or null
     * @see #isOnError()
     */
    @Nullable
    public Throwable getError() {
        Object o = value;
        if (NotificationLite.isError(o)) {
            return NotificationLite.getError(o);
        }
        return null;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Notification) {
            Notification<?> n = (Notification<?>) obj;
            return ObjectHelper.equals(value, n.value);
        }
        return false;
    }

    @Override
    public int hashCode() {
        Object o = value;
        return o != null ? o.hashCode() : 0;
    }

    @Override
    public String toString() {
        Object o = value;
        if (o == null) {
            return "OnCompleteNotification";
        }
        if (NotificationLite.isError(o)) {
            return "OnErrorNotification[" + NotificationLite.getError(o) + "]";
        }
        return "OnNextNotification[" + value + "]";
    }

    /**
     * Constructs an onNext notification containing the given value.
     * @param <T> the value type
     * @param value the value to carry around in the notification, not null
     * @return the new Notification instance
     * @throws NullPointerException if value is null
     */
    @NonNull
    public static <T> Notification<T> createOnNext(@NonNull T value) {
        ObjectHelper.requireNonNull(value, "value is null");
        return new Notification<T>(value);
    }

    /**
     * Constructs an onError notification containing the error.
     * @param <T> the value type
     * @param error the error Throwable to carry around in the notification, not null
     * @return the new Notification instance
     * @throws NullPointerException if error is null
     */
    @NonNull
    public static <T> Notification<T> createOnError(@NonNull Throwable error) {
        ObjectHelper.requireNonNull(error, "error is null");
        return new Notification<T>(NotificationLite.error(error));
    }

    /**
     * Returns the empty and stateless shared instance of a notification representing
     * an onComplete signal.
     * @param <T> the target value type
     * @return the shared Notification instance representing an onComplete signal
     */
    @SuppressWarnings("unchecked")
    @NonNull
    public static <T> Notification<T> createOnComplete() {
        return (Notification<T>)COMPLETE;
    }

    /** The singleton instance for createOnComplete. */
    static final Notification<Object> COMPLETE = new Notification<Object>(null);
}
```

노티피케이션은 이렇게 생겨먹음 더 자세한건 코드를 보십쇼..

## dematerialize

노티피케이션을 거꾸로 풀어줄때 사용한다.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/dematerialize.png)

기존에 dematerialize()라는 파라미터가 없는 오퍼레이터는 output generic tpye을 override해서 typesafe하지 않다는 이유로 2.2.4부터 deprecated되었다. 

```java
@CheckReturnValue
@SchedulerSupport(SchedulerSupport.NONE)
@Deprecated
@SuppressWarnings({ "unchecked", "rawtypes" })
public final <T2> Observable<T2> dematerialize() {
    return RxJavaPlugins.onAssembly(
        new ObservableDematerialize(this, Functions.identity())
    );
}
```

위는 deprecated된 오퍼레이터고

```java
@Experimental
@CheckReturnValue
@SchedulerSupport(SchedulerSupport.NONE)
public final <R> Observable<R> dematerialize(
    Function<? super T, Notification<R>> selector
) {    
    ObjectHelper.requireNonNull(selector, "selector is null");
    return RxJavaPlugins.onAssembly(new ObservableDematerialize<T, R>(this, selector));
}
```

위가 그냥 experimental상태인 2.2.4부터 새로 추가된 오퍼레이터이다. 차이점은 ObservableDematerialize에 인자값으로 넘겨지는 값의 차이 Fuctions.identity()인지 사용자가 정의한 Function인지의 차이다. 한번 selector로 넘겨준 Functions.identity()가 뭔지 살펴보자.

```java
public final class Functions {  
    ...
	/** A singleton identity function. */
    static final Function<Object, Object> IDENTITY = new Identity();

    /**
     * Returns an identity function that simply returns its argument.
     * @param <T> the input and output value type
     * @return the identity function
     */
    @SuppressWarnings("unchecked")
    public static <T> Function<T, T> identity() {
        return (Function<T, T>)IDENTITY;
    }
    ...
}
```

위와 같이 Object 타입을 Object로 변경하는 function을 제너릭 타입으로 변경하여서 타입안전성을 무시하여 cast했기에 @SupressWarnings("unchecked")를 사용하고 있었기에 deprecated되었다. 이 부분에 대해 자세히 알고싶으면 이펙티브 자바를 보면된다. 

이번에는 이 Function들을 사용하는 DematerializeObserver를 살펴보자.

```java
static final class DematerializeObserver<T, R> implements Observer<T>, Disposable {
        final Observer<? super R> downstream;

        final Function<? super T, ? extends Notification<R>> selector;

        boolean done;

        Disposable upstream;

        DematerializeObserver(
            Observer<? super R> downstream,
            Function<? super T, ? extends Notification<R>> selector) {
            this.downstream = downstream;
            this.selector = selector;
        }

        @Override
        public void onSubscribe(Disposable d) {
            if (DisposableHelper.validate(this.upstream, d)) {
                this.upstream = d;

                downstream.onSubscribe(this);
            }
        }

        @Override
        public void dispose() {
            upstream.dispose();
        }

        @Override
        public boolean isDisposed() {
            return upstream.isDisposed();
        }

        @Override
        public void onNext(T item) {
            if (done) {
                if (item instanceof Notification) {
                    Notification<?> notification = (Notification<?>)item;
                    if (notification.isOnError()) {
                        RxJavaPlugins.onError(notification.getError());
                    }
                }
                return;
            }

            Notification<R> notification;

            try {
                notification = ObjectHelper.requireNonNull(
                    selector.apply(item), 
                    "The selector returned a null Notification"
                );
            } catch (Throwable ex) {
                Exceptions.throwIfFatal(ex);
                upstream.dispose();
                onError(ex);
                return;
            }
            if (notification.isOnError()) {
                upstream.dispose();
                onError(notification.getError());
            }
            else if (notification.isOnComplete()) {
                upstream.dispose();
                onComplete();
            } else {
                downstream.onNext(notification.getValue());
            }
        }

        @Override
        public void onError(Throwable t) {
            if (done) {
                RxJavaPlugins.onError(t);
                return;
            }
            done = true;

            downstream.onError(t);
        }

        @Override
        public void onComplete() {
            if (done) {
                return;
            }
            done = true;

            downstream.onComplete();
        }
    }
```

위 코드의 onNext의 부분처럼 selector를 이용하여 Notification의 타입을 변경한다.



## startWith

Observable을 emit하기 전에 전달받은 Observable을 먼저 emit한다.

![img](https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/startWith.png)

```kotlin
val foo = Observable.just(1, 2, 3)
val bar = Observable.just(4, 5, 6)

foo.startWith(bar)
	.subscribe { println(it) }
```

위에 대한 결과는

```
4
5
6
1
2
3
```



## concat

두 Observable을 합쳐주는 오퍼레이터

![img](https://raw.github.com/wiki/ReactiveX/RxJava/images/rx-operators/concat.png)

```kotlin
val foo = Observable.just(1, 2, 3)
val bar = Observable.just(4, 5, 6)

Observable.concat(foo, bar)
	.subscribe { println(it)  }
```

위에 대한 결과는

```
1
2
3
4
5
6
```



## merge

여러 Observable을 하나로 합쳐주는 오퍼레이터다. emit된 순서대로 값을 반환한다.

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/merge.png)

```kotlin
val foo = Observable.intervalRange(1, 3, 0, 100, TimeUnit.MILLISECONDS)
val bar = Observable.intervalRange(4, 3, 50, 100, TimeUnit.MILLISECONDS)

Observable.merge(foo, bar)
	.blockingSubscribe { println(it)  }
```

위에 대한 결과는

```
1
4
2
5
3
6
```



## combineLatest

여러 Observable의 최신 값들의 합을 제공하고 최신값으로 업데이트 될때마다 emit하는 오퍼레이터

![img](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/combineLatest.png)

```kotlin
val foo = Observable.intervalRange(1, 3, 0, 100, TimeUnit.MILLISECONDS)
val bar = Observable.intervalRange(4, 3, 50, 100, TimeUnit.MILLISECONDS)

Observables.combineLatest(foo, bar)
	.blockingSubscribe { println(it)  }
```

아까 merge에 대한 결과는

```
1
4
2
5
3
6
```

이었다 그러므로 위에 대한 결과는

```
(1, 4)
(2, 4)
(2, 5)
(3, 5)
(3, 6)
```
