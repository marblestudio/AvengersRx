# Rx 그것은 무엇인가
Rx(Reactive Extensions)는 Observer 패턴, Iterator 패턴, 함수형 프로그래밍의 조합으로 이루어진 라이브러리입니다.

*[공식 홈페이지](http://reactivex.io/)*의 설명에서도 **An API for asynchronous programming with observable streams'** 라고 나와 있을 만큼 **비동기에서의 데이터 흐름과 전달에 관한 새로운 패러다임** 을 제시하고, 그로 인해서 다양한 입출력 **이벤트 스트림의 핸들링**을 가능하게 해주는 장점이 있습니다. 특히 콜백지옥을 벗어날 수 있는 좋은 수단이 생긴 것이죠.  

물론 비동기 처리를 위한 방법만으로 접근한다면 Rx만이 해답은 아닙니다. Java8 에서는 기존의 Future를 비동기로써 처리하게 해주는 *[CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)*라는 좋은 API가 생기긴 했지만, *[안드로이드에서는 Java8의 API들을 제대로 활용할 수 없으니](https://developer.android.com/studio/write/java8-support.html)* 사용할 수 없습니다. Kotlin의 *[Coroutine](https://kotlinlang.org/docs/reference/coroutines.html)*도 비동기적인 프로그래밍이 가능하지만 Experimental 상태에 있었다가 Kotlin 1.3버전에서 정식 지원되기 시작했고 Best Practice 또한 찾기 힘들기 때문에 사용이 꺼려지는 상태입니다. 

하지만 이러한 장점에도 불구하고 진입장벽이 어느 정도 있다는 단점도 같이 가지고 있습니다.



# Rx 어떻게 쓰나요?

일단 안드로이드에서 많이 사용하는 RxJava 기준으로 쓰겠습니다.

최신 버전의 Gradle Dependancy 추가는 *[RxJava Github](https://github.com/ReactiveX/RxJava)*를 참고하시면 됩니다.

```groovy
implementation "io.reactivex.rxjava2:rxjava:2.x.y" // gradle 추가의 예시
```



우선 Rx는 크게 세 부분으로 나눠집니다.

* 이벤트 혹은 데이터 스트림을 만들어내는 **Create**
* Rx의 다양한 Operator들과 Compose, Transform을 이용한 **Combine**
* 만들어진 Observable을 Subscribe 하여 작업을 수행하는 **Listen**




위에서 나타난 세 가지 작업 즉, **데이터 스트림을 만들어내고, 그것들을 조합, 가공하여 구독하는 것**이 Rx를 쓰는 가장 기본적인 방법이 되겠습니다.



##### 가장 기본적인 예시를 들어보자면

```java
Observable
    // create 부분
    .create(new ObservableOnSubscribe<String>() {
        @Override
        public void subscribe(ObservableEmitter<String> emitter) {
            final List<String> values = Arrays.asList("hello", "rx", "world");

            for(String value: values) {
                emitter.onNext(value);
            }

            emitter.onComplete();
        }
    })
    // combine 부분
    .map(new Function<String, String>() {
        @Override
        public String apply(String s) {
            return s + "!";
        }
    })
    // listen 부분
    .subscribe(
            new Consumer<String>() {
                @Override
                public void accept(String s) {
                    System.out.println(s);
                }
            },
            new Consumer<Throwable>() {
                @Override
                public void accept(Throwable throwable) {
                    System.out.println(throwable.toString());
                }
            },
            new Action() {
                @Override
                public void run() {
                    System.out.println("complete");
                }
            }
    );
```

##### 결과

```
hello!
rx!
world!
complete
```

이런 방식이 되겠습니다. 보기에는 매우 많은 코드들이 들어가 있는데, 사실 lambda를 통하여 코드의 양을 줄일 수는 있습니다.



##### lambda를 적용한 코드

```java
Observable
    // create 부분
    .create(emitter -> {
        final List<String> values = Arrays.asList("hello", "rx", "world");

        for(String value: values) {
            emitter.onNext(value);
        }

        emitter.onComplete();
    })
    // combine 부분
    .map(s -> s + "!")
    // listen 부분
    .subscribe(
            System.out::println,
            throwable -> System.out.println(throwable.toString()),
            () -> System.out.println("complete")
    );}
```

하지만 Rx를 공부하는 입장에서 무작정 코드 라인을 줄이는 것보다는 Rx에 익숙해지기 전까지는 어떠한 클래스와 메서드가 있는지를 확인하고 공부하는 것이 중요하다고 생각하기에 이번 포스팅에서는 lambda를 쓰지 않고 진행하겠습니다.

그렇다면 Create, Combine, Listen 각각에 대하여 자세히 알아봅시다.



# Create

Create 부분은 데이터나 이벤트 스트림을 만들어내는 부분입니다. 예시 코드에서 **Observable.create(...)**에 해당합니다. 



Rx에서 이런 Create 부분을 수행할 수 있는 클래스로는 5가지가 있습니다.

- **Observable**
- Flowable
- Single
- Maybe
- Completable





이 각각의 클래스 안에 데이터 스트림을 생성하는 많은 메서드들이 있습니다.

- **Create**
- Defer
- Empty
- Never
- Throw
- From
- Interval
- Just
- Range
- Repeat
- Start
- Timer



자세한 메서드들의 대한 설명은 *[Rx documentation](http://reactivex.io/documentation/operators.html#creating)*을 참고하시고, 우선은 가장 기본이 되는 **create**에 대하여 설명하겠습니다.

```java
Observable
    // create 부분
    .create(new ObservableOnSubscribe<String>() {
        @Override
        public void subscribe(ObservableEmitter<String> emitter) {
            final List<String> values = Arrays.asList("hello", "rx", "world");

            for(String value: values) {
                emitter.onNext(value);
            }

            emitter.onComplete();
        }
    })
```

예시에서 create 쪽 코드만 때놓고 보면 두 가지의 클래스가 보입니다.

- ObservableOnSubscribe<T>
- **ObservableEmitter<T>**



여기서 ObservableOnSubsribe는 

```java
public interface ObservableOnSubscribe<T> {
    void subscribe(@NonNull ObservableEmitter<T> emitter) throws Exception;
}
```

로 이루어진 functional interface로서 subscribe라는 메서드를 가지고 있고 ObservableEmitter 클래스를 메서드의 파라미터로 가지고 있습니다.



여기서 중요한 부분은 **ObservableEmitter<T>** 클래스입니다.



Rx에서는 **Emitter**라는 클래스가 있습니다. 이 **Emitter란 값들을 emit(방출하다) 하는 작업**을 하며 기존의 Iterator와 비슷한 동작을 수행합니다. Iterable과 Observable의 차이를 표를 통해서 표현해보겠습니다.

| **event**      | Iterable (pull)  | Observable (push)  |
| :------------- | ---------------- | ------------------ |
| retrieve data  | T next()         | onNext(T)          |
| discover error | throws Exception | onError(Exception) |
| complete       | !hasNext()       | onComplete()       |

여기서 이제 기존의 **Iterable의 pull** 방식과 Rx의 **Observable의 push** 방식의 차이가 나타납니다. 각각의 예시를 통해 설명하겠습니다.



##### Iterator 방식

```java
final List<String> values = Arrays.asList("hello", "rx", "world");

final Iterator<String> iterator = values.iterator();

while(iterator.hasNext()) {
    final String value = iterator.next();

    if(value.equals("world")) {
        throw new RuntimeException("throw error");
    }

    System.out.println(value);
}
```

##### 결과

```
hello
rx

java.lang.RuntimeException: throw error
```

Iterator 방식은 **hasNext()로 반복을 계속할지를 결정**한 후, **next()로 값을 받아옵니다**, **에러가 있을 시 throw**를 통하여 에러를 던져내는 방식입니다. 예제를 보면 "hello", "rx", "world" 3개의 값을 가진 리스트를 itrerator를 통해 반복시키는 과정에서 "world" 부분에서 고의로 에러를 발생시켰습니다. 그 결과 콘솔에 hellow, rx까지만 나타난 후 exception이 발생합니다.



이러한 Iterator 방식에서 우리는 값을 받을 때 **next()를 통해서 값을 pull** 받아서 사용한다고 표현합니다.



##### Observable 방식

```java
Observable
    .create(new ObservableOnSubscribe<String>() {
        @Override
        public void subscribe(ObservableEmitter<String> emitter) {
            final List<String> values = Arrays.asList("hello", "rx", "world");

            for(String value: values) {
                emitter.onNext(value);

                if(value.equals("world")) {
                    emitter.onError(new RuntimeException("throw error"));
                }
            }

            emitter.onComplete();
        }
    })
    .subscribe(
            new Consumer<String>() {
                @Override
                public void accept(String s) {
                    System.out.println(s);
                }
            },
            new Consumer<Throwable>() {
                @Override
                public void accept(Throwable throwable) {
                    System.out.println(throwable.toString());
                }
            },
            new Action() {
                @Override
                public void run() {
                    System.out.println("complete");
                }
            }
    );
```

##### 결과

```
hello
rx
java.lang.RuntimeException: throw error
```

Observable 방식은 Iterator 방식과 언뜻 보면 비슷할 수 있지만 큰 차이점이 있습니다. 바로 **Emitter에 값을 넣어주는(push) 차이입니다.**



Iterator방식에서는 값을 바로 받아서(pull) 써왔다면, **Observable 방식에서는 값을 하나씩 onNext()**에 넣어주며, **에러가 발생할 시 onError()**를 통해 에러를 던지며, **작업이 정상적으로 끝나면 onComplete()를 호출**하여 작업이 끝난 것을 알려줍니다.



이상으로 Rx의 Create 부분에 대하여 간략하게나마 알아봤습니다. 다음으로 Combine 부분에 대하여 알아보겠습니다.



# Combine

Combine 부분은 Rx의 다양한 Operator들과 Transform을 이용해 데이터 혹은 이벤트 스트림을 조합하는 부분입니다.



예시에서는 Operator 중에서 자주 사용하고 java8의 *[Stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)*에도 존재하는 map이라는 Operator를 사용했습니다.

```java
Observable
    .create(...)   
    // combine 부분
	.map(new Function<String, String>() {
        @Override
        public String apply(String s) {
            return s + "!";
        }
    })
```

예시에서 map operator는 Function이라는 클래스를 파라미터로 가지고 있는데, 이 Function이라는 클래스는

```java
public interface Function<T, R> {
    R apply(@NonNull T t) throws Exception;
}
```

이런 구조로 이루어져 있습니다. 여기서 T는 인풋 타입이고 R은 아웃풋 타입이라고 보시면 됩니다. 즉 **T타입의 객체를 가공하여 R타입으로 리턴**시키는 일을 하는 것이죠. **apply 부분에서는 데이터를 가공하는 일**을 하게 되는데 위의 예제에서는 인풋 String에 느낌표를 붙여주는 작업을 합니다. 위 예제의 apply 결과 T에 해당하는 부분은 String이고, R의 해당하는 부분도 String이므로 리턴값은 String인 느낌표가 붙은 String이 만들어지게 됩니다.



이상 map이라는 Operator에 대하여 설명했는데 Rx의 Operator는 정말 많고 다양하기 때문에 하나하나 설명하기는 힘들기 때문에 *[Rx documentation](http://reactivex.io/documentation/operators.html#transforming)*을 참조해주시면 되겠습니다. 



Operator 이외에도 Rx에서 데이터를 조합하는 일을 하는 클래스가 있는데 바로 **Transformer**라는 클래스입니다. Transformer 중에서 ObservableTransFormer를 예를 들어 설명하겠습니다.

```java
public interface ObservableTransformer<Upstream, Downstream> {
    @NonNull
    ObservableSource<Downstream> apply(@NonNull Observable<Upstream> upstream);
}

```

여기서 **ObservableSource**는 Observable이 implementation하고 있는 부모 인터페이스입니다. 그러므로 ObservableTransfomer는 **Observable<Upstream>을 Observable<DownStream>타입으로 변환 및 가공**하는 역할을 하게 됩니다. 여기서 얼핏 보면 map과 비슷하다고 생각하게 되는데 **map operator의 경우에는 값을 가공하지만 ObservableTrasnformer는 Observable 자체를 가공**한다는 점에서 차이가 있습니다.



Transformer의 사용법을 예시를 통해서 보여드리겠습니다.



##### Transfer 클래스

```java
class MapTransfer implements ObservableTransformer<String, String> {

    @Override
    public ObservableSource<String> apply(Observable<String> upstream) {
        return upstream
                .map(new Function<String, String>() {
                    @Override
                    public String apply(String s) {
                        return s + "!";
                    }
                });
    }
}
```

#####사용예시

```java
Observable
        // create 부분
        .create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) {
                final List<String> values = Arrays.asList("hello", "rx", "world");
                
                for(String value: values) {
                    emitter.onNext(value);
                }
                
                emitter.onComplete();
            }
        })
        // conbime 부분
        .compose(new MapTransfer())
        // listen 부분
        .subscribe(
                new Consumer<String>() {
                    @Override
                    public void accept(String s) {
                        System.out.println(s);
                    }
                },
                new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) {
                        System.out.println(throwable.toString());
                    }
                },
                new Action() {
                    @Override
                    public void run() {
                        System.out.println("complete");
                    }
                }
        );
```

##### 결과

```
hello!
rx!
world!
complete
```

여기서 중요한 부분은 **Transformer는 compose 메서드와 같이 사용**합니다. 또한, 기존의 map을 쓰는 예시와 결과는 같습니다만, ObservableTransformer같은 경우에는 Observalble 자체를 가공할 수 있다는 큰 차이가 있다는 점을 다시 한번 상기시키고 가겠습니다. 



정리하자면 적절한 Operator와 Transformer의 사용으로 원하는 데이터 혹은 이벤트 스트림을 가공하는 것이 Rx의 Combine 과정입니다. 다음으로는 Listen 과정에 대하여 알아보겠습니다.



# Listen

Listen은 우리가 Create에서 만들고 Combine에서 가공한 Observable 데이터 스트림을 Subscribe 하는 부분입니다. 즉 우리가 만든 데이터 혹은 이벤트 스트림을 사용하게 되는 부분입니다.



Observable의 subscribe 예시를 보면 이런 식으로 구성되어있습니다.

```java
Observable
        .create(...)
        .map(...)
        .subscribe(
                new Consumer<String>() {
                    @Override
                    public void accept(String s) {
                        System.out.println(s);
                    }
                },
                new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) {
                        System.out.println(throwable.toString());
                    }
                },
                new Action() {
                    @Override
                    public void run() {
                        System.out.println("complete");
                    }
                }
        );
```

예제에서의 subscribe는 **onNext에서 emit한 값을 받는 Consumer<T>** 부분, **onError에서 emit한 Throwable을 핸들링하는 Consumer<Throwable> **부분, **onComplete를 통해 완료를 전달받는 Action** 부분으로 이루어져 있습니다.



즉 subscribe메서드를 통해서 Observable을 구독하게 되면 Observable의 create에서 onNext()를 통해 emit한 데이터들이 subscribe로 전달되어 Consumer를 통하여 값을 받아서 사용하는 형태입니다.



이외에도 *[Do](http://reactivex.io/documentation/operators/do.html)*라는 Operator를 통해서 subscribe에서 원하는 타이밍에서의 핸들링이 가능합니다.



# 마무리

Rx는 정말 방대한 주제이기 때문에, 이렇게나마 간략하게 설명할 수밖에 없었습니다. 또한, 이번 포스팅에서 다뤄보지 못한 주제들도 정말 많으니 따로 꼭 공부하는 것이 Rx를 이해하는 데 도움이될 거라고 생각합니다. 다음부터는 Rx에 대해 하나하나 자세하게 파고드는 포스팅을 작성해 보겠습니다. 감사합니다.



## 참고문서

- http://reactivex.io
- https://docs.oracle.com/javase/8/docs/api
- https://developer.android.com/studio/write/java8-support.html
- https://kotlinlang.org/docs/reference/coroutines.html
- https://github.com/ReactiveX/RxJava