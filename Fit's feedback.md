#android #android/kotlin

# Things to learn and improve 📚

## <u>General Stuff</u>

### Code review
1. **Formatting/Styling** - Always follow definitive formatting tools and standards.
2. **Readability** - Ideally speaking, a pull request should be like a narrative. Naming conventions and code structure.
3. **Logical correctness** - Basic level of functionality for which the change is intended.
4. **Reusability** - Planning for lesser code is also good code.
5. **Design** - Code components should be extensible for the foreseeable future
6. **Tests/Logging/metrics** - Applicable for backend systems and applications.
7. **Backward compatibility** - You might need a lot of context to evaluate this.
8. **Sugar syntax** - Suggest a better syntax and better usage of language constructs (This might look like showing off, but see how an alternative syntax could add value and optimize at a low level)
9. **Approve.**

### ProGuard/R8
#### What classes should be avoided [(-keep)](https://blog.mindorks.com/things-to-care-while-using-proguard-in-android-application/)
- Exclude ProGuard from <span style="background:#d3f8b6">Data Classes</span>
	- For example, if you are having a class named **User** and it contains variable like **firstName** and **lastName** , then the <span style="background:#d3f8b6">Proguard will change your <b>User</b> class name as <b>A</b> or something like that. At the same time, your variable names may be changed to some <b>x</b> , <b>y</b> , <b>z</b> . So, if you want to store the data of the same variable stored, in the Database you will get a run time exception as no such field will be found in the database.</span>
- Don’t use Fragment TAGs while using Proguard
	- So, using **FragmentName.class.getSimpleName()** should be avoided.
- Handle Reflection
	- So, always keep the class using reflection in your Proguard rules.
- Handle View
	- Whenever you create your own view in Android application make sure the view class that you are making is kept in the Proguard rule otherwise you will get class not found exception.
- Adding Proguard rules for libraries
	- In our Android Application, we use a number of open source libraries. But at the same time if you are using Progurad then you must include the Proguard rules of the libraries you are using if any.
- Keep Native codes
	- While using native code in your Android application i.e. while using c++ code in your Android application, you must be aware of the consequences of the obfuscation by Proguard.
- Resource opening from JAR/APK
	- So, you should keep the names of classes that load resources from the APK in your Proguard rules.
[Rules to implement :FarHandPointUp:](https://engineering.teknasyon.com/code-optimization-with-proguard-and-r8-in-android-4d92e15a398b)
[Practical Examples](https://medium.com/androiddevelopers/practical-proguard-rules-examples-5640a3907dc9)

#### Minified debug build type
The default build types are configured such that _debug_ doesn’t run ProGuard. That makes sense, because we want to iterate and compile fast when developing, but still want the release build to use ProGuard to be as small and optimized as possible.

But in order to fully test and debug any ProGuard problems, it’s good to set up a separate, minified debug build like this:

``` gradle
buildTypes {  
  debugMini {  
    initWith debug  
    minifyEnabled true  
    shrinkResources true  
    proguardFiles getDefaultProguardFile('proguard-android.txt'),   
                  'proguard-rules.pro'
    matchingFallbacks = ['debug']  
  }  
}
```


With this build type, you’ll be able to [connect the debugger](https://developer.android.com/studio/debug/index.html), [run UI tests](https://developer.android.com/training/testing/ui-testing/espresso-testing.html) (also on a CI server) or [monkey test](https://developer.android.com/studio/test/monkey.html) your app for possible problems on a build that’s as close to your release build as possible.

#### Dependency Injection
 There are libraries that solve this problem by automating the process of creating and providing dependencies. They fit into two categories:
- <span style="background:#d3f8b6">Reflection-based</span> solutions that connect dependencies at runtime.
	- Koin??
- <span style="background:#d3f8b6">Static</span> solutions that generate the code to connect dependencies at compile time.
	- Dagger Hilt
## <u>Kotlin</u>

### Data Class
- Data classes in Kotlin are primarily <span style="background:#d3f8b6">used to hold data.</span> For each data class, the compiler automatically generates additional member functions that <span style="background:#d3f8b6">allow you to print an instance to readable output, compare instances, copy instances,</span> and more.
### Sealed Class
- Provide a robust way to <span style="background:#d3f8b6">define restricted class hierarchies</span><span style="background:#d3f8b6">, improving type safety and maintainability.</span> By ensuring all subclasses are known at compile-time, sealed classes facilitate exhaustive `when` expressions and controlled inheritance
### Extension functions
- Kotlin provides <span style="background:#d3f8b6">the ability to extend a class or an interface with new functionality without having to inherit from the class or use design patterns</span> such as ***Decorator***. This is done via special declarations called ***extensions***.
- By defining an extension, <span style="background:#d3f8b6">you are not inserting new members into a class, only making new functions callable with the dot-notation on variables of this type.</span>
### lateinit vs lazy
- **lateinit**
	- The `lateinit` keyword in Kotlin enables the declaration of non-nullable properties without immediate initialization. This is beneficial when dealing with properties that necessitate a context or extensive initialization that cannot be done during object creation. However, it is crucial to initialize `lateinit` properties before accessing them.
	- Is useful when <span style="background:#d3f8b6">working with properties that require a context or heavy initialization that cannot be performed during object creation.</span> It provides flexibility by deferring the initialization to a more suitable time, such as within a specific method or initialization block. This can enhance performance by avoiding unnecessary computations or initializations until they are needed.
- **lazy**
	- The property will <span style="background:#d3f8b6">only be computed and initialized when it is accessed for the first time.</span> Subsequent accesses to the property will return the cached value.
	- Initialization <span style="background:#d3f8b6">is beneficial when dealing with properties that are computationally expensive to initialize but may not be needed throughout the object's entire lifespan.</span> By lazily initializing such properties, the overhead of unnecessary computations can be avoided, and <span style="background:#d3f8b6">the initialization can be postponed until the property is accessed.</span> This approach can optimize resource usage and improve overall application efficiency.
- **Differences**
	- **Initialization time**
		- The main difference between `lateinit` and `lazy` in **Kotlin** is <span style="background:#d3f8b6">the timing of initialization.</span> 
		- `lateinit` allows for <span style="background:#d3f8b6">delaying the initialization of a property until a later point</span>
		- `lazy` <span style="background:#d3f8b6">initialize the property only when it is first accessed.</span>
	- **Nullable vs Non-Nullable**
		- **lateinit** properties must be declared as non-nullable, meaning they cannot hold null values.
		- **lateinit** properties will be initialized before accessing them, and accessing an uninitialized `lateinit` property results in a `UninitializedPropertyAccessException`.
		- **lazy** properties can be either nullable or non-nullable.
		- **lazy** properties are only initialized when they are first accessed, and if they are never accessed, they remain uninitialized.
		- if a **lazy** property is not accessed in certain scenarios, it can remain uninitialized or be assigned a null value to indicate its optional nature.
	- **Thread Safety**
		- **lazy** properties in **Kotlin** provide an inherent `synchronization` mechanism to guarantee that initialization occurs only once, even in multi-threaded scenarios.
		- The **lazy** delegate is thread-safe by default, ensuring that multiple threads attempting to access the property simultaneously will wait for initialization to complete and share the same initialized value.
		- **lateinit** properties do not offer built-in thread-safety mechanisms. <span style="background:#d3f8b6">Developers must take responsibility for ensuring proper initialization of <b>lateinit</b> </span>properties before accessing them in a concurrent environment.
		- Failure to do so can result in race conditions and unpredictable behavior.
	- **Compile Time vs Runtime Errors**
		- If a **lateinit** property is accessed before being initialized, a `UninitializedPropertyAccessException` is thrown at runtime. This can lead to <span style="background:#d3f8b6">bugs that are only detected during runtime</span> and may require careful debugging to identify the cause.
		- **lazy** properties are checked for initialization at compile time. If is not properly initialized or if the initialization logic throws an exception, <span style="background:#d3f8b6">the error is caught during compilation</span>. This early error detection improves code quality and reduces the likelihood of runtime crashes.
### val vs var
- **val**:
	- Is used for declaring a reference which will not be able to repoint to another object.
	- <span style="background:#d3f8b6">You can not change the reference but you can always change the state of pointed object.</span>
	- <span style="background:#d3f8b6">An object has state and behaviour. Its behaviour is exposed through its functions.</span> 
- ***var:***
	- A variable <span style="background:#d3f8b6">holds a reference to an object.</span>
	- var means the variable can point to a different array.
- Code example
		- ![[Pasted image 20240823162617.png]]
### Scope functions
- It's sole purpose is to <span style="background:#d3f8b6">execute a block of code within the context of an object.</span>
- When call a lambda expression, it <span style="background:#d3f8b6">forms a temporary scope</span> and you can access the object without its name.
- <span style="background:#d3f8b6">There are two main differences between each function: the way they refer to context object and their return value</span>
- **Context**
	- `this`: `run`, `with`, `apply`
	- `it`: `let`, `also`
- **Return value**
	- *return the context object itself:* `apply`, `also`
	- *return the lambda result*: `let`, `run`, `with`
- **let**
	- executing a lambda on non-nullable objects
- **run**
	- Object configuration and computing the result
- **with**
	- Grouping function calls on an object
- **apply**
	- Object configuration
- **also**
	- Additional effects
### High order functions
- get more details
## <u>Kotlin Multiplatform - KMM</u>
### Ktor
### Koin
- Single: creates a single instance for that object (Singleton)

## Testing
### Unit Testing
### UI Testing

## <u>Jetpack Compose 🤖</u>
### Recomposition - more about [recomposition](https://developer.android.com/develop/ui/compose/mental-model)
- In an **imperative UI model**, <span style="background:#d3f8b6">to change a widget, you call a setter on the widget to change its internal state.</span> 
- **In Compose**, you call the composable function again with new data. Doing so causes the function to be ***recomposed*** <span style="background:#d3f8b6">the widgets emitted by the function are redrawn, if necessary,</span> with new data. The Compose framework can intelligently recompose only the components that changed.
### States - more about states info [here](https://developer.android.com/develop/ui/compose/state#stateful-vs-stateless) and [here](https://decode.agency/article/jetpack-compose-state/)
- Compose is declarative and as such the only way <span style="background:#d3f8b6">to update it is by calling the same composable with new arguments.</span> These arguments are representations of the UI state. <span style="background:#d3f8b6">Any time a state is updated a <b>recomposition</b> takes place.</span>
- There are three ways to declare a `MutableState` object in a composable
	- `val mutableState = remember { mutableStateOf(default) }`
	- `var value by remember { mutableStateOf(default) }`
		- The `by` ***delegate*** syntax requires the following imports:
			- `import androidx.compose.runtime.getValue`
			- `import androidx.compose.runtime.setValue`
				- ***Delegate:*** The expression after `by` is a _delegate_, because the `get()` (and `set()`) that correspond to the property will be delegated to its `getValue()` and `setValue()` methods. Property delegates don't have to implement an interface, but they have to provide a `getValue()` function (and `setValue()` for `var`s).
	- `val (value, setValue) = remember { mutableStateOf(default) }`
- **remember:** <span style="background:#d3f8b6">stores a single object in memory during the recompositions</span> of a composable function. Retain state between recompositions while the composable remains in memory
- **rememberSavable:** <span style="background:#d3f8b6">it adds automatic state saving and restoration, this is particularly helpful for preserving state across screen rotations</span>
- **mutableStateOf:** <span style="background:#d3f8b6">allows the state to be mutable and observable triggering UI updated</span> whenever it value changes
- [more about remember/savable/mutableStateOf](https://blog.zachklipp.com/remember-mutablestateof-a-cheat-sheet/)
### Side-effects
- Any operation that <span style="background:#d3f8b6">affects the state of the system outside the function being executed.</span> Should be kept separated from the UI rendering logic
#### LaunchedEffect
- run suspend functions (Coroutines) in the scope of a composable
- It takes multiple keys as params and i<span style="background:#d3f8b6">f any of the keys changes, it cancels the existing coroutine and launch again.</span>
- Useful for network calls, updating db without blocking the UI thread
- <span style="background:#d3f8b6">It launches on first composition</span>
- **Use Case**
	- Fetching data from network/database when a particular state changes
#### rememberCoroutineScope
- obtain a composition-aware scope to launch a coroutine outside a composable
- <span style="background:#d3f8b6">In order to launch a coroutine outside of a composable, but scoped so that it will be automatically canceled once it leaves the composition, use `rememberCoroutineScope`</span>
- Also use `rememberCoroutineScope` <span style="background:#d3f8b6">whenever you need to control the lifecycle of one or more coroutines manually, for example, cancelling an animation when a user event happens.</span>
![[Pasted image 20250210084155.png|519x356]]
#### rememberUpdatedState
- <span style="background:#d3f8b6">reference a value in an effect that shouldn't restart if the value changes</span>
- Use `rememberUpdatedState` to create a reference to this value which can be captured and updated. This approach is helpful for effects that contain long-lived operations that may be expensive or prohibitive to recreate and restart.
#### DisposableEffect
- The `DisposableEffect` composable is utilized to execute an effect when a Composable function is initially created. <span style="background:#d3f8b6">It then clears the effect when the Composable is removed from the screen.</span>
- It's similar to LaunchedEffect, but it provides a Disposable function for cleanup when the effect should be disposed of.
- **Use Case**
	- Starting and stopping a listener, such location listener or a sensor
	- Cleaning up resources, like closing a database connections or canceling a network request
#### SideEffect
-  <span style="background:#d3f8b6">publish Compose state to non-Compose code</span>
- It ensures that code inside it will always run **after** the `Composable` function has finished rendering.
- **Use Case**
	- Logging or reporting analytics
	- <span style="background:#d3f8b6">You want to perform some actions that are related to the UI but don’t directly affect its state</span>
#### produceState
- convert non-Compose state into Compose state
- [`produceState`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#produceState\(kotlin.Any,kotlin.coroutines.SuspendFunction1\)) launches a coroutine scoped to the Composition that can push values into a returned [`State`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/State). Use it to convert non-Compose state into Compose state, for example bringing external subscription-driven state such as `Flow`, `LiveData`, or `RxJava` into the Composition.
- The producer is launched when `produceState` enters the Composition, and will be cancelled when it leaves the Composition. The returned `State` conflates; setting the same value won't trigger a recomposition.
- Even though `produceState` creates a coroutine, it can also be used to observe non-suspending sources of data. To remove the subscription to that source, use the [`awaitDispose`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/ProduceStateScope#awaitDispose\(kotlin.Function0\)) function.
- The following example shows how to use `produceState` to load an image from the network. The `loadNetworkImage` composable function returns a `State` that can be used in other composables.
#### derivedStateOf
- Is a composable that can be used to derive new state based on the values of other state variables. <span style="background:#d3f8b6">It is useful when you need to compute a value that depends on other values, and you want to avoid recomputing the value unnecessarily.</span>
- You should use the [`derivedStateOf`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#derivedStateOf(kotlin.Function0)) function <span style="background:#d3f8b6">when your inputs to a composable are changing more often than you need to recompose.</span> This often occurs when something is frequently changing, <span style="background:#d3f8b6">such as a scroll position, but the composable only needs to react to it once it crosses a certain threshold. `derivedStateOf` creates a new Compose state object you can observe that only updates as much as you need.</span> In this way, it acts similarly to the Kotlin Flows [`distinctUntilChanged()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/distinct-until-changed.html#:%7E:text=Returns%20flow%20where%20all%20subsequent,a%20StateFlow%20has%20no%20effect.) operator.
#### snapshotFlow
- convert Compose's State into Flows
- allows you to create a flow that emits the current value of a state object, and then emits any subsequent changes to that object. This can be useful for creating reactive UIs that respond to changes in state, without having to manually manage callbacks or listeners.
### Nested collections
- to be created
### Accessibility - here's a [codelab](https://developer.android.com/codelabs/jetpack-compose-accessibility#0)
- To be created
## <u>Async stuff 📣</u>
### Coroutines
Coroutines is a recommended solution for asynchronous programming on Android. Noteworthy features include the following:
- **Lightweight**: You can run many coroutines on a single thread due to support for [_suspension_](https://kotlinlang.org/docs/reference/coroutines/basics.html), which doesn't block the thread where the coroutine is running. Suspending saves memory over blocking while supporting many concurrent operations.
- **Fewer memory leaks**: Use [_structured concurrency_](https://kotlinlang.org/docs/reference/coroutines/basics.html#structured-concurrency) to run operations within a scope.
- **Built-in cancellation support**: [Cancellation](https://kotlinlang.org/docs/reference/coroutines/cancellation-and-timeouts.html) is propagated automatically through the running coroutine hierarchy.
- **Jetpack integration**: Many Jetpack libraries include [extensions](https://developer.android.com/kotlin/ktx) that provide full coroutines support. Some libraries also provide their own [coroutine scope](https://developer.android.com/topic/libraries/architecture/coroutines) that you can use for structured concurrency.
#### Coroutine context
- is a set of rules and configurations that define how the coroutine will be executed (it's like a map with possible keys and values)
#### Job
- It's a reference of the coroutine itself
#### SupervisorJob
- This job can continue the children coroutine if fails and don't cancel the parent Job
#### Dispatchers
- Are coroutine contexts that specify the thread or threads that can be used by the coroutine to run the code
- system won't create 1 thread per coroutine, but will try to used the ones that are already alive
##### Default
- Will be used when no dispatcher is defined
- <span style="background:#d3f8b6">Used to run tasks that make intensive use of CPU</span>
- It can use as many threads as CPU cores
##### IO
- All tasks that <span style="background:#d3f8b6">will block the thread while waiting for an answer from another system</span>, server requests, db, files, sensors, etc
- The size of this thread pool is 64 (can be configured to use more)
##### Unconfined
- it's difficult to predict what thread will be used for this
- don't use it unless you're very sure of what are you doing
##### Main
- this will use the UI
- defined as the dispatcher for the viewModelScope
- if we want to call an API call on Main thread, it will throw an exception by default
#### Coroutine Builders
##### withContext
- switches the context of the current coroutine
- It's used for tasks where you need to get a result or when you need to switch to a different dispatcher temporarily.
- is a suspending function by itself, so we don't need to use it inside another suspending functions
- return the result block of the code
##### runBlocking
- <span style="background:#d3f8b6">This builder blocks the current thread until all the tasks inside that coroutine are finished. </span>
- is very helpful to test suspending tasks. <span style="background:#d3f8b6">In your tests</span>, wrap the suspending task you want to test with a `runBlocking` call, and you will be able to assert the result and prevent that the test finishes before the background task ends.
##### launch
-  It’s **the simplest way to create coroutines**. As opposed to `runBlocking`, <span style="background:#d3f8b6">it won’t block the current thread</span> (if we use the proper dispatchers, of course).
- <span style="background:#d3f8b6">This builder always needs a scope.</span>
- Returns a `Job`
- `job.join()`
	- you can **block the coroutine associated with that job until all the child jobs are finished**. All the suspending functions that are called inside that coroutine are tied to this job, so when the job can find out when all those child jobs finish and then continue the execution.
	- it's a suspension function itself, so it needs to be called inside another coroutine
- `job.cancel`
	- This function will cancel all its associated child jobs.
	- is a regular function, so it doesn’t require a coroutine to be called.
##### async
-  Allows **running several background tasks in parallel**.
- It’s not a suspending function itself, so when we run `async`, the background process starts, but it immediately continues running the next line. 
- Always needs to be called inside another coroutine, and it returns a specialized job that is called `Deferred`.
- This object has a new function called `await()`, which is the blocking one. 
- We’ll call `await()` only when we need the result. If the result is not ready yet, the coroutine is suspended at that point. If we had the result already, it’ll just return it and continue. This way, you can run as many background tasks as you need.
#### Coroutine Scopes
##### Global Scope
- When Coroutines are launched within the global scope, they live long as the application does.
- If the coroutines finish it’s a job, it will be destroyed and will not keep alive until the application dies
##### LifeCycle Scope
- The only difference is that when we use the lifecycle scope, <span style="background:#d3f8b6">all the coroutines launched within the activity also dies when the activity dies.</span>
- It is beneficial as our coroutines will not keep running even after our activity dies.
##### ViewModel Scope
- <span style="background:#d3f8b6">The only difference is that the coroutine in this scope will live as long the view model is alive.</span>
- This scope survives orientation changes
### Flow
#### Cold Flows
- <span style="background:#d3f8b6">Are created on-demand and emit data when they’re being observed.</span>
- Each observer gets it's own sequence of values
- They are not shared
#### Hot Flows
- <span style="background:#d3f8b6">Are always active and can emit data regardless of whether or not they’re being observed.</span>
- **StateFlow**
	- It's a specialized and optimized subclass of **SharedFlow** which stores and replays the latest value only
	- <span style="background:#d3f8b6">Is a hot flow</span>, it remains in memory as long as the flow is collected or while any other references to it exist from a garbage collection root. You can turn cold flows hot by using the [`shareIn`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/share-in.html) operator.![[Pasted image 20250114120057.png|475x223]]
- **SharedFlow**
	- Publish values that are broadcasted to all observers
	- It can manage optional replay cache/buffer
	- Replaces `BroadcastChannel` API
- **Operators**
	- **ShareIn**
		- Returns a SharedFlow
	- **StateIn**
		- Returns a StateFlow
### Differences between Flow and Coroutines
- In coroutines, a _flow_ is a type that can emit multiple values sequentially, as opposed to _suspend functions_ that return only a single value. For example, you can use a flow to receive live updates from a database.
- Flows are built on top of coroutines and can provide multiple values. A flow is conceptually a _stream of data_ that can be computed asynchronously. The emitted values must be of the same type. For example, a `Flow<Int>` is a flow that emits integer values.
### MutableState and MutableStateFlow
#### MutableState
- **Scope:** <span style="background:#d3f8b6">Designed to manage UI State within compose</span>
- **UseCase:** Is best suited <span style="background:#d3f8b6">for a simple state that is local to individual composables</span> or when the state <span style="background:#d3f8b6">does not need to be shared across different layers of the app</span>
- **ThreadSafe and Concurrency:** Updates must be on the MainThread, requiring careful threading. Concurrency can be more complicated in more complex or multi-threaded environments
- **Lifecycle and Stream capabilities:** Tightly coupled with the Compose runtime which automatically handles lifecycle events within the UI. Does not support streaming multiple values over time unless explicitly managed within composables
- **Read-Only Exposure:** Does not support transforming into a read-only state directly. If exposed can be casted back to MutableState, leading to unintended mutations
#### MutableStateFlow
- **Scope:** <span style="background:#d3f8b6">A general purpose state holder that is part of Kotlin's coroutines</span>, it's useful across all application layers
- **UseCase:** <span style="background:#d3f8b6">This is ideal for states that are observed or shared across multiple components</span>
- **ThreadSafe and Concurrency:** Inherently thread safe and can safely updated from any thread. Supports concurrent operations and can be combined with other flows.
- **Lifecycle and Stream capabilities:** Lifecycle aware when used with scopes like "viewmodelScope". Naturally supports emitting multiple values over time, providing a robust solutions for dynamic data streams
- **Read-Only Exposure:** It can be exposed as StateFlow, a read-only interface that prevents external mutations and adheres to best practices in encapsulation.
## <u>Design Patterns ⚒️</u>
get more [info](https://refactoring.guru/design-patterns/what-is-pattern)
### Creational
### Structural
### Behavioral
## <u>SOLID 🔬</u>
### S: Single Responsibility Principle
- The single responsibility principle states that a class, module, or function <span style="background:#d3f8b6">should have only one reason to change</span>, meaning it <span style="background:#d3f8b6">should do one thing.</span>
### O: Open-Closed Principle
- The open-closed principle states that classes, modules, and functions <span style="background:#d3f8b6">should be open for extension but closed for modification.</span>
- This principle might seem to contradict itself, but you can still make sense of it in code. It means you <span style="background:#d3f8b6">should be able to extend the functionality</span> of a class, module, or function by adding more code without modifying the existing code.
### L: Liskov Substitution Principle - <font color="#c00000">(Learn more about this*)</font>
- The principle states that <span style="background:#d3f8b6">child classes or subclasses must be substitutable for their parent classes or super classes.</span> In other words, the <span style="background:#d3f8b6">child class must be able to replace the parent class.</span> This has the advantage of letting you know what to expect from your code.
### I: Interface Segregation Principle
- The principle states that clients should <span style="background:#d3f8b6">not be forced to implement interfaces or methods they do not use.</span>
- More specifically, the ISP suggests that software developers should <span style="background:#d3f8b6">break down large interfaces into smaller, more specific ones,</span> so that clients only need to depend on the interfaces that are relevant to them. This can make the codebase easier to maintain.
### D: Dependency Inversion Principle - <font color="#c00000">(Learn more about this*)</font>
- The dependency inversion principle is about decoupling software modules. That is, making them as separate from one another as possible.
- The principle states that high-level modules should not depend on low-level modules. Instead, they should both depend on abstractions. Additionally, abstractions should not depend on details, but details should depend on abstractions.
- In simpler terms, this means instead of writing code that relies on specific details of how lower-level code works, <span style="background:#d3f8b6">you should write code that depends on more general abstractions that can be implemented in different ways.</span>
## <u>Clean Architecture 🧼</u>
### Presentation/UI
- This layer deal with handling the UI part.
- It contains Activity, Fragment, ViewModel, and imports with the Android package.
- This is a pure Android library or application module in the multi-module application.
- <span style="background:#d3f8b6">This purely deals with UI and views the system of Android and its state handling.</span>
### Domain
- This layer deal with the Business logic part.
- <span style="background:#d3f8b6">It contains UseCase, a Repository interface.</span>
- This is a pure Java/Kotlin module in the multi-module application.
- <span style="background:#d3f8b6">It calls a method of the repository to fetch data without caring how and from where it comes.</span>
### Data
- <span style="background:#d3f8b6">This layer deal with all Data related part like fetch, store, etc.</span>
- It Contains Repository Implementation from the case.
- This can be a pure Java/ Kotlin module based on the library you used in the multi-module application.
![[Pasted image 20240823152511.png]]