# Functional Programming

## Functional Interface

- Contains only one method. This removes the ambiguity for the lambda.
- Example: `Runnable` is a functional interface and has only one method (`run`).
- Uses the annotation: `@FunctionalInterface`

## Invoke Dynamic

1. `invokevirtual`: Normal Classes
2. `invokespecial`: Constructors and Private Methods
3. `invokestatic`: Static Methods
4. `invokeinterface`: Interface Methods

5. `invokedynamic`: This is used for invoking lambda expressions (since Java 8). Binds things at runtime.

## Predefined Functional Interfaces

1. Predicate: Checks condition
   `T --> Boolean`

   ```java
   private static <T> List<T> filterList(List<T> list, Predicate<T> predicate) {
       List<T> newList = new ArrayList<>();
   	    for (T t : list) {
               if (predicate.test(t)) {
                   newList.add(t);
               }
           }
       return newList;
   }
   ...
   List<String> list = new ArrayList<>();
   list.add("Hello");
   list.add("Bye");
   list.add("");
   list.add("Karan");
   Predicate<String> containsNonEmpty = s -> s.length() > 0;
   List<String> filteredList1 = filterList(list, containsNonEmpty);
   System.out.println(filteredList1);
   
   Predicate<String> containsLetterE = s -> s.contains("e");
   List<String> filteredList2 = filterList(list, containsLetterE);
   System.out.println(filteredList2);
   ```

2. Consumer
   `T --> void`

   ```java
   	private static <T> List<T> printList(List<T> list, Consumer<T> consumer) {
   		List<T> newList = new ArrayList<>();
   		for (T t : list) {
   	        consumer.accept(t);
   	    }
   	    return newList;
   	}
   ```

3. Function
   `T --> R`
   Method: `apply()`

4. Supplier
   `() --> T`
   Method: `get()`

5. Unary     Operator
   `T --> T`
   Used when we want a function that is restricted to return the same     type as the input parameter.         

6. Binary Operator
   `(T, T) -> T`

7. BiPredicate
   `(T, R) -> boolean`

8. BiConsumer
   `(T, U) -> void`

9. BiFunction
   `(T, U) -> R`

## Generic Functional Interfaces

```java
@FunctionalInterface
interface FunctionalGenerics<T, R> {
	R execute(T t);
}
...
FunctionalGenerics<String, String> function1 = s -> s.substring(1, 5);
String result1 = function1.execute("Hello World");

FunctionalGenerics<String, int> function2 = s -> s.length();
int result2 = function2.execute("Hello World");
```

## Method Reference

Used to use an already implemented method for a lambda expression.
Method reference is used in four cases:

1. `object :: instanceMethod`

   ```java
   System.out :: println
   ```

2. `Class :: staticMethod`

   ```java
   Math :: random
   ```

3. `Class :: instanceMethod`

   ```java
   Function<String, Integer> length = String :: length;
   int a = length.apply("Hello");
   System.out.println(a)
   ```

4. `Class :: new` (Constructor Reference)

   ```java
   Function<Runnable, Thread> threadGenerator = r --> Thread::new;
   ```

> Note: There can be cases that cannot be converted to Method References.

## Optional

`null` value always gives `NullPointerException` in Java which can lead to crashes. Using checks to check null values degrades the business logic and readability of the code.

Solution is to use `java.util.Optional<T>`

```java
private SoundCard soundcard;
```

becomes

```java
private Optional<Soundcard> soundcard;
```

Optional tells the consumer that the object can be null.

- 16 Bytes
- Immutable
- Separate Object

Therefore, `Optional` can affect performance and cannot be used all the time.

```java
String value = "Hello";
Optional<String> optional = Optional.of(value);

Optional<String> empty = Optional.empty();

Optional<String> nullable = Optional.ofNullable(value);
Optional<String> emptyOptional = Optional.ofNullable(null); // Gives empty optional
```

### Getting value from Optional using `orElse()`

```java
String value = optionalObject.orElse(0); 
is equivalent to 
String value = optionalObject.isPresent() ? optionalObject.get() : 0;
```

# Functional Programming

## First Class Citizens

Functions are like any other variables. Can be passed like variable, returned from methods and can be assigned to variables.

## Pure Functions

1. Output of function depends on internal algorithm and input value.
   Same Input --> Same Function --> Same Output
2. No Side Effects
   Pure functions do not read/write to/from external sources.
   (Writing to console using `System.out` is also a side effect. Making changes to input parameters of the function is also a side effect.)

At any point, one should be able to replace the function call with a return value and result of the program should remain same after replacing function.

```java
int x = add(multiply(1, 3), multiply(3, 4));
// equivalent to
int x = add(3, 12);
```

## Higher Order Functions

Takes one or more functions as arguments.
AND/OR
Returns a function.

## Referential Transparency

means that the reference should not have any effect.

> 2-3, 3-4, and -1 are treated as same thing under referential transparency as any of the values can be used.

## Function Chaining

Each method returns an object which is fed to another method.

```java
// Internal Working of Consumer
@FunctionalInterface
interface Absorper<T> {
    public void accept(T t);
    
    default Absorper<T> thenAccept(Absorper<T> absorper2) {
        Objects.requireNonNull(absorper2);
        return (T t) --> {
            this.accept(t);
            absorper.accept(t);
        };
    }
}
...
Absorper<String> absorper1 = System.out::println;
Absorper<String> absorper2 = System.out::println;
absorper1.thenAccept(absorper2.thenAccept(absorper2)).accept("Hello");

// Using Built-in Consumer Class
Consumer<String> sout = System.out::println;
sout.andThen(sout.andThen(sout)).accept("Hello");
```

## Function Composition

`func1.compose(func2);`
Results of function 2 are given to function 1 as arguments.

```java
@FunctionalInterface
interface Function<T, R> {
    R apply(T t);
    default <V> Function<V, R> compose(Function<V, T> stringLength) {
        return (V v) -> apply(stringLength.apply(v));
    }
}
...
Function<String, Integer> stringLength = String::length;
Function<Integer, Integer> incrementInteger = (x) -> x + 1;
Function<String, Integer> incrementLength = incrementInteger.compose(stringLength);
System.out.println(incrementLength.apply("Karandeep"));
```

## Closure

Variables used inside a lambda expression should be final or effectively final.

```java
int x = 100;
Task task = () -> System.out.println(x);
```

Here `x` is referenced inside the lambda and is not final. 

The `x` variable inside the lambda expression is a free variable referenced from the lexical context. If this Task `task` is passed to another function and executed in that function, the passed function is the closure in that case.

```java
private static void execute(Task t) {
    t.doTask();
}
...
task.execute();
```

The result will be still the same because the value of `x` is passed to `execute(...)`.

> Closure is a function that refers free variables in its lexical context.

## Currying

```java
Function<Integer, Function<Integer, Function<Integer, Integer>>> addThree = x -> y -> z = x + y + z;
int x = addThree.apply(1).apply(2).apply(10);
```

### Lazy Initialization

Evaluation is not done until the result is required.

---

# Design Patterns

## Iterator Pattern

```
class CustomList<T> {
    private List<T> list;

    CustomList() {
        this.list = new ArrayList<>();
    }

    public void add(T t) {
        list.add(t);
    }

    public void forEach(Consumer<T> consumer) {
        for (T t : list) {
            consumer.accept(t);
        }
    }
}
...
CustomList<String> stringCustomList = new CustomList<>();
        stringCustomList.add("Hello");
        stringCustomList.add("Bye");
        Consumer<String> printer = System.out::println;
        stringCustomList.forEach(printer);
```

## Strategy Pattern

Used to switch between different algorithms/strategies/logics on the run time.

Different lambda functions can be passed to switch between the strategies.

```java
StockListFilters.filter(stock, stock -> stock.getSymbol().equals("AZ")).forEach(System.out::println);
```

# Streams

- Declarative
- Parallel Processing 
- Flexible

```java
List<Book> filteredBooks = books.parallelStream()
    .filter(book -> book.getRating() > 3)
    .filter(book -> book.getGenre().equalsIgnoreCase("Horror"))
    .collect(Collectors.toList());
```

- Streams are immutable and can be used only once.
- Intermediate operations (filter, map, reduce) on the streams are lazy and only executed when the terminal operation (collect) is executed.

## Numeric Streams

- Provides methods like:
  - `mapToDouble(book -> book.getRatings)`: converts to primitive double stream.
  - `average()`
  - `max()`
  - `sum()`
  - ...

## Infinite Streams

Use methods like `Stream.iterate(start, function)` or `Stream.generate(supplier)`.

## Flatten Streams

```java
Stream<String> a = Stream.of("Hello", "World");
Stream<String> b = Stream.of("How", "are you");
Stream<String> c = Stream.of(a, b).flatMap(e -> e);
System.out.println(c.collect(Collectors.toList()));
```

`Stream.of(a, b)` is a `Stream<Stream<String>>` which is flattened using `.flatmap(e -> e)`

Here, `e` is the stream being flattened and not the elements of the stream.

## Parallel Streams

`.parallelStream()`

Streams should be:

- Stateless: Result should not depend on any state during the pipeline execution.
- Non-interfering: Data source of the stream should not change during pipeline execution.
- Associative: Order of data should not affect the result.

> Stateless: does not depend on external information.
> Stateful: Depends on external information.

## Spliterator

Used to connect streams with custom sources
It can have some characteristics and are defined using constant variables defined as:

- `ORDERED`
- `DISTINCT`: Each element is different. (Set)
- `SORTED`
- `SIZED`: Known size
- `NONNULL`: No element is null.
- `IMMUTABLE`: Element source cannot be structurally modified.
- `CONCURRENT`: Elements can be safely modified without any synchronization.
- `SUBSIZED`: All the spliterators resulting from `trySplit()` method are also sized.

### Optimizing with Spliterator

Example: We have a sorted list and we mention that as spliterator's properties.
If any operation on the stream has `.sorted()` operation, it will automatically avoid sorting it again. So the `.sorted()` operation won't run twice.

### Custom Spliterator

```c
LINE X+0: BOOK NAME A
LINE X+1: BOOK AUTHOR A
LINE X+2: PRICE A
LINE X+3: BOOK NAME B
...
```

We read this data as a stream using regular spliterator which gives us a stream of all lines. Next, we can feed this stream to a custom spliterator which uses information in 3 subsequent lines to create a book object and add to the list of book objects.

```java
class Book {
    private String name, author;
    private double price;
    
    public Book(String name, String author, double price) {
        this.name = name;
        this.author = author;
        this.price = price;
    }
}
...
    class CustomSpliterator implements Spliterator<Book> {
        private String name, author;
        private double price;
        private Spliterator<String> baseSpliterator;
        
        public CustomSpliterator(Spliterator<String> baseSpliterator) 
            
            this.baseSpliterator = baseSpliterator;
    }

    @Override
    public boolean tryAdvance(Consumer<? super Book> action) {
        if (this.baseSpliterator.tryAdvance(name -> this.name = name)
            && this.baseSpliterator.tryAdvance(author -> this.author = author)
            && this.baseSpliterator.tryAdvance(price -> this.price = double.parseDouble(price))) {
            action.accept(new Book(this.name, this.author, this.price));
            return true;
        }
        return false;
    }

    @Override
    public Spliterator<Book> trySplit() {
        return null;
    }

    @Override
    public long estimateSize() {
        return baseSpliterator.estimateSize() / 3;
    }

    @Override
    public int characteristics() {
        return baseSpliterator.characteristics();
    }
}
...
Spliterator<String> baseSpliterator = ...;
Spliterator<Book> bookSpliterator = new CustomSpliterator(baseSpliterator);
Stream<Book> stream = StreamSupport.stream(bookSpliterator, false);
```

---

# Collectors

- Converting to lists, sets, maps, and other collections.

- Grouping.

  ```java
  System.out.println(Stream.of(1, 2, 3, 4, 5)
                    .collect(Collectors.groupingBy(e -> e>3 )));
  // {false = [1, 2, 3], true = [4, 5]}
  ```

## Custom Collector

- `Supplier<A> supplier()`
  Object to which collected elements will be added.
- `BiConsumer<A, T> accumulator()`
  How elements will be added to the supplier's object.
- `Function<A, R> finisher()`
  Operation on list once everything is collected. Example: Sorting.
- `BinaryOperator<A> combiner()`
  Takes two lists and combine them. Used in parallel stream collection's merging.
- `Set<Characteristics> characteristics`
  Characteristics of the collector.

```java
Collector.of(ArrayList::new,
             ArrayList::add,
             (list1, list2) -> {
                 list1.add(list2);
                 return list1;
             },
             (list) -> {
                 Collection.sort(list);
                 return list;
             },
             Collector.Characteristics.IDENTITY_FINISH
        );
```

---

# Reactive Programming

- Responsive
  System responds in timely manner.
- Resilient
  System should stay responsive in case of failures. System should deal with the errors/crashes gracely.
- Elastic
  System increases/decreases the resources based on the requirements.
- Message Driven
  Communication between reactive systems should be message driven and non blocking.

## Async and Callback Hell

Concurrency in Java is managed using threads and callbacks. However, multiple callbacks are difficult to handle.

## RxJava

1. Push based and not pull based. It is like observer pattern.
2. Errors are treated like data on separate channels.
3. Uses operators to compose events.
4. Uses different kinds of schedulers.
5. BackPressure using Flowable. Used to slow down the flow of data in consumers.

## `Observable.create`

`Observable<T>` is an interface with one method: `subscribe()`.

Observer can pass different types of events to the Observable.

1. `onSubscribe()`
   When observer subscribe to an observable.
2. `onNext()` - Data Channel
   Passes the next element to the observer.
3. `onComplete()` - Completion Channel
   Tells that the emission of elements is complete.
4. `onError()` - Error Channel
   Errors are also first class citizens in RxJava. Error to observer using on `onError()` event.

```java
Observable<Integer> observable = new ObservableCreate<>(new observableOnSubscribe<Integer>() {
    @Override
    public void subscribe(@NonNull ObservableEmitter<Integer> emitter) throws Throwable {
        try {
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onNext(3);
            emitter.onComplete();
        } catch (Throwable error) {
            emitter.onError(error);
        }
    }
});
Observer<Integer> observer = new Observer<Integer>() {
    @Override
    public void onSubscribe(@NonNull Disposable d) {
        System.out.println("Subscribed");
    }

	@Override
	public void onNext(@NonNull Integer integer) {
		System.out.println("Next: "+integer);
	}

	@Override
	public void onError(@NonNull Throwable e) {
		System.out.println("Error: "+e);
	}

	@Override
	public void onComplete() {
		System.out.println("Complete");
	}
};

observable.subscribe(observer);

/* 
	Result
		Subscribed
		Next: 1
		Next: 2
		Next: 3
		Complete
*/
```

### Ways of creating observable

1. Using `.just`

   ```java
   Observable<Integer> observable = Observable.just(1, 2, 3);
   ```

2. Using Iterable

   ```java
   Observable<Integer> observable = Observable.fromIterable(namesList);
   ```

3. Using lambdas

   ```java
   // observable.subscribe(lambdaOnNext, lambdaOnError, lambdaOnComplete);
   observable.subscribe(
       i -> System.out.println(i);
       Throwable::printStackTrace,
       System.out.println("Completed"));
   ```

   ```java
   // observable.subscribe(lambdaOnNext, lambdaOnError);
   observable.subscribe(
       i -> System.out.println(i),
       Throwable::printStackTrace);
   ```

   ```java
   // observable.subscribe(lambdaOnNext);
   observable.subscribe(i -> System.out.println(i));
   ```

## Cold Observable

It connects to the data producer when the observer subscribes to it. Two observers can get different values from a cold observable. 

### Unicast

It can emit different elements to different observers.
Eg: Observable emitting elements generated by a list. The list may change between two subscriptions.
Eg: Observable emitting elements from web socket. The socket is connected only when subscription happens.

## Hot Observable

It connects to the data producer irrespective of the subscription. If two observers subscribe to a hot observable, will get the same values at any given point of time.

### Multicast

It emits the same elements to different observers.
Eg: Observable connects to the socket and start emitting irrespective of the subscription. Two subscribers will get same elements.

## Changing cold observable to hot observable

```java
source = Observable.interval(1, TimeUnit.SECONDS);
```

This is a cold observable.

```java
source = Observable.interval(1, TimeUnit.SECONDS).publish;
source.connect();
```

Here it becomes a hot observable by `publish` and `connect`.

### Types of Observable

#### Single Observable

Single element is emitted.

```java
Single.just("Alex")
    .subscribe(System.out.println);
```

#### Maybe Observable

Single element may or may not be emitted.

#### Completable

No element is emitted. Used to indicate the successful completion or error.

```java
Completable.fromRunnable(() -> System.out.println("Some Process Running"))
    .subscribe(() -> System.out.print("Process Completed Successfully"));
```

### Disposing Observer

```java
Disposable d = observerable.subscribe(System.out::println);
Thread.sleep(1000);
d.dispose();
```

Disposable `d` will subscribe to the observable for 1 second and then unsubscribe.

## Operators for Observable

```java
Observable.just(1, 2, 3, 4)
    .filter(e -> e>1)
    .sorted()
    .subscribe(System.out::println);
```

### Suppressing Operators

#### Filter

Same events without any modification but skips some elements that do not meet the conditions.

#### Take

Emits first N Elements and then stop the emission.

#### Skip

Emits first N elements and then start the emission.

#### Distinct

Skips the duplicates.

#### ElementAt

Gives the element at index.

### Transforming Operators

#### Map

Change input to some other form using a function.

#### Cast

Cast to a given class.

#### Delay

Delay emission for a given time. There is a delay in emission but no loss of elements.

#### DelaySubscription

Delay subscription for a given time. There is a delay in subscription which means we loose elements emitted in that time.

#### Scan

Takes accumulator function as a argument. Apply function to first item and feeds result to next.

```java
scan((x, y) -> x+y);
// converts 1, 2, 3, 4, 5 --> 1, 3, 7, 12
```

### Reducing Operators

Takes a series of emissions and converts to a single observable.
`Observable<T> --> Single<T>`

#### Count

Counts number of emissions.

#### Reduce

Identical to scan but emits the final accumulation only.

#### Contains

Checks whether a given element is emitted or not.

#### All

Verifies if all the emissions satisfies a given condition.

#### Any

Verifies if any emission satisfies a given condition.

### Error Recovery Operator

#### OnErrorReturnItem

Returns some default element in case of error.

#### OnErrorReturn

Takes lambda to return the default element.

#### OnErrorResumeNext

Emits multiple values instead of a single element in case of error.

#### Retry

Resubscribes to the same observable again.

### Action Operators

1. `doOnNext()`
2. `doOnError()`
3. `doOnSubscribe()`
4. `doOnComplete()`

## RxJava - `Merge`, `Concat`

### Merge

```java
merge(observable1, observable2);
```

Merges two observables in a parallel kind of order. Stops merging if errror occurs.

```java
// obs1 -> 1, 2, 3, 4
// obs2 -> 5, 6, 7, 8
merge(obs1, obs2) -> 1, 5, 2, 6, 3, 7, 4, 8
```

### Concat

Sames as merge but merges in sequencial way.

```java
concat(obs1, obs2) -> 1, 2, 3, 4, 5, 6, 7, 8
```

Not possible to give order if both observable have different emission interval.

## RxJava - `flatMap` and `concatMap`

### flatMap

Uses a mapper to create observable for each emitted element. When all observerables are ready, it merges them in a parallel way.

### concatMap

Works same as `flatMap` but merges observables in a sequential manner.

## `amb ` (Ambiguous)

Used to get data from multiple source but consider the data from the faster source.

Example:
`obs1` emitting at 1 element/second
`obs2` emitting at 10 elements/second

```java
Observable.amb(Arrays.asList(obs1, obs2))
    .subscribe(System.out::println);
```

This will take all the data from `obs2`.

## RxJava - Zip, CombineLatest

### `Zip(Observable, Observable)`

Takes one element from each observable and combine them using a lambda.

```C
zip
Emission 1:     a -- b -- c -- d
Emission 2:       1 -- 2 -- 3 -- 4
Final Emission: a1 -- b2 -- c3 -- d4
```

### `CombineLatest(Observable, Observable)`

Takes the latest two emissions and combine them. It doesn't wait for emission from a particular observable.

```c
merge
Emission 1:     a -- b --   -- c
Emission 2:      1 -- 2 -- 3 -- 4
Final Emission: a1 -- 1b -- b2 -- 23 -- 3c -- c4
```

## Observable Contract

Emissions must be passed sequentially and one at a time.

## Running Observable Parallel

```java
e -> {
    newThread(() -> 
              {
                  e.onNext("Hello");
                  e.onNext("Bye");
              }).start()
}).subscribe(...);
```

## Schedulers

1. Computation
   `Schedulers.computation()`
   Number of threads <= Number of available cores
2. IO
   `Schedulers.io()`
   Number of threads > Number of available cores
3. NewThread Schedulers
   `Schedulers.newThread()`
   New Thread per observer and destroy when done
4. Single 
   `Schedulers.single()`
   Sequencial Computation
5. Trampoline Scheduler
   Not used very often. Used to avoid recursive thread scheduling
6. Schedular.from(executorService)

>  Computation and IO create thread pool and reuse them.

### SubscribeOn

The scheduler closest to the source is used.

```java
subscribeOn(scheduler)
```

### ObserveOn

Used to mix and match upstream and downstream with different schedulers.
Using observerOn can run into consumer-producer problem. Fixed by Flowable and BackPressure.

## Replay

```java
observable...
    .replay()
    ...
```

Used to replay all the previous values if an observer subscribes later in the emission process. There are many ways to reduce the overhead from `replay()` like timelimit.

## Subjects

Something that can act as both observable and observer.

```java
Subject<Object> subject = PublishSubject.create();
subject.subscribe(System.out::println);
observable1.subscribe(subject);
observable2.subscribe(subject);
```

observable1 and observable2 should be running using schedulers (different threads).
`subject` subscribes to both of them and itself act like an observable.

`Subject<T>` is used to merge two or more observables.
Subjects are hot observables. So they should be subscribed before starting the emission.

### Adding emissions to subject

```java
Subject<Object> subject = PublishSubject.create();
subject.subscribe(System.out::println);
subject.onNext("Hello");
subject.onNext("Bye");
subject.onComplete();
```

### Types of subjects

#### PublishSubject

Starts emitting the source observable items from the moment observer subscribes to it. Its like watching a movie on TV. You can't go back to the movie that is already broadcasted.

#### ReplaySubject

Its like watching a movie on YouTube. You can start the movie from start. ReplaySubject maintains a cache for previously emitted elements.

#### BehaviourSubject

Replays one previously emitted elements and then emitts the current emissions.
Its like movie starts from the most recent minute.

#### UnicastSubject

Replays all the previous elements and clears the cache when any observer subscribes to it.

### Buffer

```java
observable...
    .buffer(4).subscribe(...);
```

Changes the elements into a collection of 4 elements and then emits them.

```java
.buffer(4, CollectionConstructor)...;
.buffer(4, Hashset:new)...;

.buffer(timelimit)...;

// We can use timing of some observable for buffering by:
.buffer(observable)...;
```

## Throwable

Used to skip elements in case of rapid emissions.

`throttleFirst`: First element after a given time.
`throttleLast`: Last element in the given time.

`throttleWithTimeout`: Most recent element after inactivity of given time.

## Flowable

- Used when producer is producing large amount of data and we had slower consumers.
- Ideally flowable should be used for concurrent computations.
- Slower than Observable. Has some overhead.
- Used for getting data from IO Sources like JDBC.

