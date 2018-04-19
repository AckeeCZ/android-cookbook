# Technologies and best practices

## Dependency injection

You may find detailed post about dependency injection (DI) [here][1].

## Reactive programming

ReactiveX is a relatively new "API for asynchronous programming with observable streams". But it isn't just API, it is a completely new paradigm. Maybe it is a little bit easier for people who are acquainted with functional programming, but took quite a time for me to switch my imperative thinking and begin to understand how Rx works.

The Rx approach already has implementations in almost all main programming languages. For us, Android developers, there exist [RxJava][4] and [RxAndroid][5] extensions. [We're using][7] them too.

Basically, the two main concepts of Rx approach are functional programming and Observer pattern. Just imagine the production line. In the beginning the first worker places a detail on it. In the end the second worker receives a complete product. He may or may not know what was in the beginning of the production line and what was in the middle. Maybe there were other workers that added some other details to the initial one, maybe the detail was somehow transformed, or maybe the detail was just changed for the final product. This is Rx.

In this example, the first worker is Producer (or Observable), the second worker is Subscriber (or Observer). The manipulations that are applied to the detail are Rx operators. The are a lot of predefined [operators][3] in each Rx implementation, and this doesn't count extensions! But we are using at most 20 of them on regular basis. And, of course, you may create your custom one.

Rx chains (or streams) are these production lines that we are talking about. When the first worker puts a detail on the line, he "emits an item". Then, if the second worker waits for his product on this line, hi "subscribes to the Observable". He may or may not receive the fial product after the detail is put on the conveyor, it depends on the operators inside.

The magic happens inside the chain. The variety of operators allows to manipulate with the emitted items the way you want: filter, delay, split, merge, reduce, copy, make them synchronous or asynchronous, retry, make them throw exception, switch threads and more and more.

We really want to explain it to you in detail, but it requires way more words and examples to fit in such a format. Now, as [RxJava 2.0][6] is released, we will take a deep look at it and post an article or presentation here.

But shortly, why Rx(Java) is cool:
- Asynchronous operations become way easier to code
- Threading becomes a piece of cake
- Passing a callback is history, now we're passing the Observables
- The code becomes more readable and intuitive
- New efficient way of error handling
- Now you won't have proble
- Modern libraries begin to support Rx (Retrofit, SQLBrite etc.)
- The Observer pattern is easy to implement as never before (allows real-time communication between components)
- ... more things

If you want to become reactive (if you aren't already), there is already a lot of good stuff about it on the internet. We recommend [this blog][8] and [this visualization][9] to understand better how the operators work.

## MVP (Model-view-presenter)
MVP is an architectural pattern used mostly for systems with UI. Now we're in the process of writing a serie of articles about our experience with this approach. The first part is [here][12], the second will be ready soon. You may also read about MVP for Android [here][10] and [here][11].

## MVVM (Model-View-ViewModel)
While MVP is of course a great way to compose your application's architecture, there is also another way which became currently preferred for our apps. This approach completely decouples business logic from view, because ViewModel has completely no information about the View. ViewModel provides all data as observable properties and also provides actions that can be triggered from View. This architecture improves testability and separation of concerns even more than MVP. You can read some high level information about MVVM for Android for example [here][13]. Since the first stable release of [Android Architecture Components][14] this architecture became simpler to implement than ever.

## Repository
Repository pattern is another good example of correct separation of concerns. Since we use MVVP we don't want each ViewModel to be aware of details about where all the data come from, where they are stored, whether they are cached etc. and we also want some of the data to outlive the lifecycle of single ViewModel, we delegate all these issues to the Repositories. Repository modules are responsible for handling data operations. They provide a clean API to the rest of the app. They know where to get the data from and what API calls to make when data is updated. You can consider them as mediators between different data sources (persistent model, web service, cache, etc.). Repository serves as our single source of truth and should be the only manager of all the data and its state (i.e. loading, refreshing only when necessary, providing loading state, etc.) and provides the data throughout the entire application. Using repositories is highly compatible with MVVM and is recommended way to structure your architecture in [Android Architecture Components][14] tutorial.

[1]:  https://github.com/AckeeCZ/android-cookbook/blob/master/DependencyInjection.md
[2]:  http://reactivex.io/
[3]:  http://reactivex.io/documentation/operators.html
[4]:  https://github.com/ReactiveX/RxJava
[5]:  https://github.com/ReactiveX/RxAndroid
[6]:  https://github.com/ReactiveX/RxJava/wiki/What's-different-in-2.0
[7]:  https://github.com/AckeeCZ/android-cookbook/blob/master/KitchenTools.md
[8]:  http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/
[9]:  http://rxmarbles.com/
[10]: http://antonioleiva.com/mvp-android/
[11]: https://github.com/konmik/konmik.github.io/wiki/Introduction-to-Model-View-Presenter-on-Android
[12]: https://medium.com/ackee/an-introduction-to-mvp-on-android-2fceaa4a11e#.b9fznedux
[13]: https://medium.com/upday-devs/android-architecture-patterns-part-3-model-view-viewmodel-e7eeee76b73b
[14]: https://developer.android.com/topic/libraries/architecture/guide.html
