# Third party libraries and frameworks

## Retrofit

[Retrofit][1] is a modern, composeable and type-safe REST client for Android. It is developed by guys from [Square][2] (Dagger, Picasso, SQLBrite etc.). Basically, there are no serious alternatives to Retrofit for now. So, if you're planning to communicate via REST API, this is your obvious choice.

Retrofit v2 connection layer is based on [OkHttp][3], another excellent library from Square, and it allows you to use all the power of this Http client like interceptors to handle network requests and responses in any phase of communication. Retrofit itself nandles the REST part with the help of annotation processing and API description adapter. Moreover, you may provide any of JSON/XML converters you like to handle serialization/deserialization and customize them the way you want. Retrofit may handle the requests/responses synchronously or asynchronously and provides easy thread control. 

Another great feature of Retrofit is RxJava support. Now you may easily integrate your API requests to your chains in one row without live out all this callback hell.

All this Retrofit blocks (factories) may be set to the client via the builder pattern on instance creation. This library works really well alongside Dagger. The interface for API calls may be then easily provided to any component in your app. 

Retrofit is really simple to use. If you don't want to use all thes additional and custom features, you don't even bother to know if they exist. Just create a client instance, map your adapter methods with REST endpoints and you're good. :)

There is a geat [resource][4] about Retrofit where you will find anything about the library abilities.

## Picasso

Retrofit and OkHttp are great, but when it goes about image downloading, they aren't so suitable. Fortunately there exist several libraries that may handle this work really good. We are using [Picasso][5], the one from [Square][2].

If [Retrofit][1] is an obvious choice for REST API client, it isn't so clear with image downloading libraries. There is quite a competition between Picasso, [Glide][6], [Fresco][7] from Facebook and a little more old [UIL][8]. It seems like Picasso and Glide are quite similar +/- some features when Fresco uses a little bit different aproach. You may read more about the comparison [here][9].

We were using Glide and Fresco for a little, but now we are with Picasso as suits all our needs: it allows to load an image to `ImageView` using one row, it allows different scaling, resizing and custom transformations, it allows to use callbacks and load images as bitmaps to custom targets, it uses robust OkHttp downloader that handles caching and provides neat logging. But it's up to you to decide what image downloading library is the best for you, all seem quite solid.

Again, this [resource][10] gives a very detailed overview on Picasso (and Glide as well), don't hesitate to use it.

## RxJava/RxAndroid

[RxJava][11] is the Java implementation of [ReactiveX][12] framework. You may read more about this approach [here][13]. [RxAndroid][14] are just Android-specific extensions for RxJava.

So, what is RxJava and why it is awesome? Unfortunately, it's impossible to answer these questions in such short format. Maybe we'll post a big article about that in the future. But shortly, with the reactive approach (combination of functional programming and "observer" pattern) you may now create complex asynchronous applications without the pain you're used to with classical Android approach. Callback hell, threading and Android components lifecycle handling problems are all eliminated with elegant, combinebale and easily readable RxJava/RxAndroid chains (or streams) with the help of some other libraries. For example, getting the data from REST API, save it to the database and show to the user if (when) the view is available may look like this:

```java
    apiInteractor.getBooks()  // Retrofit request
        .subscribeOn(Schedulers.io()) // do the request in I/O thread
        .doOnSubscribe(() -> showProgress(true))
        .flatMap(bookDao::storeBooks)  // save to DB with the help of SQLBrite
        .observeOn(AndroidSchedulers.mainThread()) // all the code below will be executed on the main thread
        .compose(deliverFirst()) // deliver the result to the first attached view (Nucleus MVP library)
        .subscribe(split(this::onBooksLoaded, this::onBooksLoadFailed)) // handle success and fail: show books/error to the user, hide progress etc.
```

Looks nice, but RxJava requires Java 8 support that Android doesn't have. At the moment if you want to use RxJava on Android. you should use [this plugin][15] plugin. But with the popularity of this approach the situation may change.

The big drawback of RxJava that it isn't quite intuitive for the programmers who are used to classical imperative OOP approach, because it works with other concepts - streams and time. The learning curve may be quite long but steep. At first it doesn't make any sense, but when this "heureka" moment comes, you'll love RxJava.

If you ar new to this approach and want to know more, read [this blog][16].

[1]:	https://square.github.io/retrofit/
[2]:  https://square.github.io/
[3]:  https://github.com/square/okhttp
[4]:  https://futurestud.io/tutorials/retrofit-getting-started-and-android-client
[5]:  http://square.github.io/picasso/
[6]:  https://github.com/bumptech/glide
[7]:  https://github.com/facebook/fresco
[8]:  https://github.com/nostra13/Android-Universal-Image-Loader
[9]:  http://stackoverflow.com/questions/29363321/picasso-v-s-imageloader-v-s-fresco-vs-glide
[10]: https://futurestud.io/tutorials/picasso-getting-started-simple-loading
[11]: https://github.com/ReactiveX/RxJava
[12]: http://reactivex.io/
[13]: TODO
[14]: https://github.com/ReactiveX/RxAndroid
[15]: https://github.com/evant/gradle-retrolambda
[16]: http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/
