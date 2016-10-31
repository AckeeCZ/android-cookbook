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

// TODO MVP,...

[1]:  https://github.com/AckeeCZ/android-cookbook/blob/master/DependencyInjection.md
[2]:  http://reactivex.io/
[3]:  http://reactivex.io/documentation/operators.html
[4]:  https://github.com/ReactiveX/RxJava
[5]:  https://github.com/ReactiveX/RxAndroid
[6]:  https://github.com/ReactiveX/RxJava/wiki/What's-different-in-2.0
[7]:  https://github.com/AckeeCZ/android-cookbook/blob/master/KitchenTools.md
[8]:  http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/
[9]:  http://rxmarbles.com/
