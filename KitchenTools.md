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

## Stetho

This is one of the libraries that isn't used in the production phase, moreover, it should be disabled for all but debug builds. [Stetho][17] is an ultimate debugging tool from Facebook that allows to debug... practically everything that Android Studio debug doesn't allow. Stetho is relying on ADB ([Android Debug Bridge][18]) and allows to access its features via Chrome Developer Tools or command-line interface. 

Here are the main features of Stetho that we're using in Ackee:

- Access database in real-time: search and read values, change/delete values, make SQL queries etc. If you're worrying about security, Stetho uses ADB, so nobody except the developer can't access this data. Just turn off Stetho for production.
- Access, create, update and delete Shared Preferences and other local storages.
- With the help of StethoInterceptor for [OkHttp][3] analyze Http requests (read headers and bodies, visualize timing, filter errors and more other googies)
- Analyze layouts in real-time: Stetho shows the current layout structure and highlights the current view in your device as you click on it. Moreover it shows all the information about the view (visibility, margins, padding, size etc.)

There are even more features that we don't know yet. But even now, we can't imagine our development without this handy tool.

## Dagger

[Dagger][19] and [Dagger 2][20] are fast and easy to use libraries for dependency injection. You can read more about dependency injection and Dagger libraries in our [article][21].

## SQLBrite

[SQLBrite][22] is a reactive SQLite wrapper that provides access to database with the help of observables that may listen to table updates. It allows to acquire and handle database updates "real-time". But if you don't want to be reactive all the time, SQLBrite provides a classical imperative interface to access to the DB.

SQLBrite isn't in full swing yet, so there aren't much tutorials and examples on the internet, but the documentation is quite good and the interface is easily learnable given you are familiar with [RxJava][11]. 

In Ackee we are using another wrapper on SQLBrite :). [SQLBrite Dao][23] makes the DB access easier with the help of reactive Data Access Objects (DAOs). It allows to map POJOs with DAOs with the help of annotation processor and provides convenient interface for content values creation and getting data from cursor. You may reed more about it [here][24].

The reactive approach to DB is really great. It eliminates a lot of boilerplate code and makes you stop worrying if you always have the actual data in your views. But you should be more carful. At first, we were really enthusiastic about this approach, but on our first big project when we were using SQLBrite, we noticed some drop in performance. After a bit of debugging we've found out that it is due to several big join queries, which observables are constantly listening to the changes in each table from these joins. Fortunately, the listening is customisable, and when we started to listen only to required updates, the times impoved drastically.

SQLBrite requires RxJava knowledge and some caution, but when used wisely, it is really a great tool that simplifies work with database. Now as we have some experience with it, we will possibly write an article about it in the near future.

## Anko

Instead of traditional XML layouts we use [Anko][31] for definition of layouts. At one of MDevTalks David had a [presentation][30] about our approach to Anko. Here are some general rules that we use when building this layouts:
* standard Kotlin Style [guides][32] applies as for any other Kotlin code
* every layout is in separate class, we don't define layouts directly in Activities or Fragments. There is one exception with custom Views, layout is defined in constructor of View
* properties are defined at the top of the View before any child Views
* id and layoutParams are first properties defined for View. 
* between properties and first child View is newline
* between two consecutive child Views in parent ViewGroup is newline
* lparams are defined after the View definition. If it's not possible (top level ViewGroup) its defined inside the View traditionally
* we try to make custom extensions on ViewManager that contains common View components, like `titleTextView` `defaultInputLayout` 
* empty block {} is forbidden. If View has no additional attributes, just don't add this block :)
* if View has its constructor with most common property eg. `textView(text = R.string.my_text)` it's always preferred to use this constructor. 

Here is an example of simple layout with there applied rules

```kotlin

/**
 * Layout of login screen
 */
class LoginLayout(parent: ViewGroup) : ViewLayout(parent) {

    lateinit var inputMail: TextInputLayout
    lateinit var inputPassword: TextInputLayout
    lateinit var btnLogin: Button
    lateinit var progress: View

    override fun createViewInternal(ankoContext: AnkoContext<ViewGroup>): View {
        return with(ankoContext) {
            frameLayout {
                nestedScrollView {
                    isFillViewport = true

                    verticalLayout {
                        padding = dip(16)

                        inputMail = defaultTextInputLayout {
                            hint = string(R.string.login_email_hint)
                            with(editText!!) {
                                inputType = InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_VARIATION_EMAIL_ADDRESS
                            }
                        }

                        inputPassword = defaultTextInputLayout {
                            isPasswordVisibilityToggleEnabled = true
                            hint = string(R.string.login_password_hint)
                            with(editText!!) {
                                inputType = InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_VARIATION_PASSWORD
                                onEditorAction { _, actionId, _ ->
                                    if (actionId == EditorInfo.IME_ACTION_DONE) {
                                        btnLogin.performClick()
                                        hideIme()
                                    }
                                }
                            }
                        }

                        space().lparams(height = 0, weight = 1f)

                        btnLogin = primaryButton(text = R.string.login_btn_login)
                                .lparams(width = matchParent)
                    }
                }.lparams(width = matchParent, height = matchParent)

                progress = screenProgress()
            }
        }
    }
}
```

### Some other tools that we are using

- [Nucleus MVP][25] - framework that supports reactive approach to [MVP pattern][26]
- [Butterknife][27] (read about it in our [article][21] about dependency injection)
- [RxBinding][29] - Rx support for view events
- [ParcelablePlease][28] - annotation processing-based generator for parcelable boilerplate code

[1]:  https://square.github.io/retrofit/
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
[17]: http://facebook.github.io/stetho/
[18]: https://developer.android.com/studio/command-line/adb.html
[19]: http://square.github.io/dagger/
[20]: https://google.github.io/dagger/
[21]: https://github.com/AckeeCZ/android-cookbook/blob/master/DependencyInjection.md
[22]: https://github.com/square/sqlbrite
[23]: https://github.com/sockeqwe/sqlbrite-dao
[24]: http://hannesdorfmann.com/android/sqlbrite-dao
[25]: https://github.com/konmik/nucleus
[26]: TODO
[27]: http://jakewharton.github.io/butterknife/
[28]: https://github.com/sockeqwe/ParcelablePlease
[29]: https://github.com/JakeWharton/RxBinding
[30]: https://t.co/EMuCLjh3n6
[31]: https://github.com/Kotlin/anko
[32]: https://android.github.io/kotlin-guides/style.htm
