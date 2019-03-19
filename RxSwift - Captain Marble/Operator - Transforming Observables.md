# Transforming Observables

## map

~~~swift 
Observable.just(4).map { $0 * 2 }.subscribe(onNext: { (num) in
            print(num)
        }, onCompleted: {
            print("on complete")
        })
~~~
