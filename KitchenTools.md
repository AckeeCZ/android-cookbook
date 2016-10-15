# Third party libraries and frameworks

### Retrofit

[Retrofit][1] is a modern, composeable and type-safe REST client for Android. It is developed by guys from [Square][2] (Dagger, Picasso, SQLBrite etc.). Basically, there are no serious alternatives to Retrofit for now. So, if you're planning to communicate via REST API, this is your obvious choice.

Retrofit v2 connection layer is based on [OkHttp][3], another excellent library from Square, and it allows you to use all the power of this Http client like interceptors to handle network requests and responses in any phase of communication. Retrofit itself nandles the REST part with the help of annotation processing and API description adapter. Moreover, you may provide any of JSON/XML converters you like to handle serialization/deserialization and customize them the way you want. Retrofit may handle the requests/responses synchronously or asynchronously and provides easy thread control. 

Anothr one great feature of Retrofit is RxJava support. Now you may easily integrate your API requests to your chains in one row without live out all this callback hell.

All this Retrofit blocks (factories) may be set to the client via the builder pattern on instance creation. This library works really well alongside Dagger. The interface for API calls may be then easily provided to any component in your app. 

Retrofit is really simple to use. If you don't want to use all thes additional and custom features, you don't even bother to know if they exist. ust create a client instance, map your adapter methods with REST endpoints and you're good. :)

There is a geat [resource][4] about Retrofit where you will find anything about the library abilities.


[1]:	https://square.github.io/retrofit/
[2]:  https://square.github.io/
[3]:  https://github.com/square/okhttp
[4]:  https://futurestud.io/tutorials/retrofit-getting-started-and-android-client
