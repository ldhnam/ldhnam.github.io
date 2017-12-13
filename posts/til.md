## Android & Kotlin
[https://viblo.asia/p/kien-truc-mvvm-ket-hop-rxjava-2-voi-retrofit-2-oOVlYE9zl8W](https://viblo.asia/p/kien-truc-mvvm-ket-hop-rxjava-2-voi-retrofit-2-oOVlYE9zl8W)

[https://viblo.asia/p/rxjava-va-diffutil-WAyK8EwkZxX](https://viblo.asia/p/rxjava-va-diffutil-WAyK8EwkZxX)

Sử dụng `android:background="?android:attr/selectableItemBackground"` hay `foreground` cho Button, ViewGroup, View, item của RecyclerView để thấy được pressed.

When using merge as the root element of the layout of your Compound View, tools:parentTag can help you utilise all the features of the Design tool.

How can you use RxJava to track lifecycle events. Not sure what exactly is meant by this question, if you need to track any types of events singleton observables work great. You can create a singleton class that listens to activityLifecycleCallbacks or any other lifecycle you are concerned with and then propagates events through a PublishSubject or Relay. If you are concerned with a previous state/value you can use a BehaviorSubject/relay instead.

Quick, explain Rx Map vs Flatmap! Map operates on the value within the observable stream, for example if I have an observable that emits integers, I can map those to a String. Flatmap is for transforming an observable into another observable. Rather than having to return a scalar value from a flatmap you return another observable. Flatmaps are useful when you want to transform one observable into another observable in a manner where the later observable waits for emissions from the former. Both map and flatmap will be called for each emission of the source observable.

Explain why you would use Preferences vs SQLLite? Shared Preferences like any key/value store are useful for dumping simple values or serialized. SQL is better for relational data. For example if you want to save a postJson and will only need to retrieve it in its entirety then sharedpref or object storage will suffice. If instead you want to be able to query for a part of the postJSON, your best bet is to use a database.

Explain why you need an interface in Android Interfaces are contracts that you code against. One reason is to ease in testing, you can swap a different implementation during tests than during source execution.

Why do you need WeakReferences? In memory caches are a good use case, if nothing is referencing a cached value it may want to be cleaned up. You can set this relationship by making the cache/map reference your object with a weakreference. Weak references get cleaned up if it is the only reference.

Why is an inner class in an activity bad? Memory Leaks! any time you make an inner class you capture an instance of the containing class, you can use heap dumps or leak canary to find these leaks in a current app.

 Walk me thru a retrofit/Rx API call - Create a retrofit interface for your endpoint using Rxjava as a result type wrapping your actual response object, create an adapter for an implementation of that endpoint. Use Gson or moshi as a converter factory within the adapter builder. Use rxjava as a call adapter factory. Call your endpoint, wrap that observable in subscribeOn,observeOn and subscribe to result. Make sure to have an on error handler.

 Explain how retrofit could be required to be in a thread and how it can work in its own thread. I believe interviewer was asking for the difference betweene execute and enqueue. Currently execute will run on whatever the caller thread is while enqueue takes a callback which gets called on the main thread.

**Dagger 2**: 

`@Qualifier` annotation is provided by javax inject package and is used to qualify the dependency.

`@Scope` is used to specify the scope in which a dependency object persists. If a class getting dependencies, have members injected with classes annotated with a scope, then each instance of that class asking for dependencies will get its own set of member variables.

`@Singleton` ensure a single instance of a class globally. So, there will be only one DbHelper class instance for the app and whenever a class asks for DbHelper as a dependency, it will be provided with the same instance that is maintained in the Dagger’s dependency graph.

`@Inject` on the constructor instructs the Dagger to accumulate all the parameter dependencies when the class is being constructed.

`@PerActivity` is a scope and is used to tell the Dagger that the Context and Activity provided by the ActivityModule will be instantiated each time an Activity is created. So, these objects persist till that activity lives and each activity has its own set of these.

## Performance

 + Use Static Final For Constants
 ```
 static int intVal = 42;
static String strVal = "Hello, world!";
```
The compiler generates a class initializer method, called `<clinit>`, that is executed when the class is first used. The method stores the value 42 into `intVal`, and extracts a reference from the classfile string constant table for `strVal`. When these values are referenced later on, they are accessed with field lookups

We can improve matters with the `"final"` keyword:

```
static final int intVal = 42;
static final String strVal = "Hello, world!";
```
The class no longer requires a <clinit> method, because the constants go into static field initializers in the dex file. Code that refers to intVal will use the integer value 42 directly, and accesses to strVal will use a relatively inexpensive "string constant" instruction instead of a field lookup.

> **Note**: This optimization applies only to primitive types and `String` constants, not arbitrary reference types. Still, it's good practice to declare constants `static final` whenever possible.

+ Use Enhanced For Loop Syntax
+ Consider Package Instead of Private Access with Private Inner Classes
+ Avoid Using Floating-Point
+ Know and Use the Libraries


**When to use `“apply”`, when to use `“with”`?**

By definition, `apply` accepts a function, and sets its scope to that of the object on which apply has been invoked. This means that no explicit reference to the object is needed. Apply() can do much more than simply setting properties of course. It is a transformation function, capable of evaluating complex logic before returning. At the end, the function simply returns the same object (with the added changes), so one can keep using it on the same line of code.

**Lets see the differences between `“apply”` and `“with”`.**

There are mainly two differences:

`apply` accepts an instance as the receiver while `with` requires an instance to be passed as an argument. In both cases the instance will become this within a block.
`apply` returns the receiver and `with` returns a result of the last expression within its block.
Usually you use `apply` when you need to do something with an object and return it. And when you need to perform some operations on an object and return some other object you can use `with`.

**lateinit vs lazy**

There are many great features available in Kotlin, we can take advantage of all these features to write the better application in Kotlin. Among all those features, `lateinit` and `lazy` are important property initialization feature. We must know when to use which property initialization.

**lateinit**

`lateinit` is late initialization.

Normally, properties declared as having a non-null type must be initialized in the constructor. However, fairly often this is not convenient. For example, properties can be initialized through dependency injection, or in the setup method of a unit test. In this case, you cannot supply a non-null initializer in the constructor, but you still want to avoid null checks when referencing the property inside the body of a class.

To handle this case, you can mark the property with the `lateinit` modifier.

The modifier can only be used on var properties declared inside the body of a class (not in the primary constructor), and only when the property does not have a custom getter or setter. The type of the property must be non-null, and it must not be a primitive type.

**lazy**

`lazy` is lazy initialization.

lazy() is a function that takes a lambda and returns an instance of lazy which can serve as a delegate for implementing a lazy property: the first call to get() executes the lambda passed to lazy() and remembers the result, subsequent calls to get() simply return the remembered result.

`lazy` can only be used for val properties, whereas lateinit can only be applied to vars because it can’t be compiled to a final field, thus no immutability can be guaranteed.

`lateinit var` can be initialized from anywhere the object is seen from. If you want your property to be initialized from outside in a way probably unknown beforehand, use `lateinit`.

**What are Sealed Classes?**

> From the documentation: Sealed classes are used for representing restricted class hierarchies, when a value can have one of the types from a limited set, but cannot have any other type. They are, in a sense, an extension of enum classes: the set of values for an enum type is also restricted, but each enum constant exists only as a single instance, whereas a subclass of a sealed class can have multiple instances which can contain state.
