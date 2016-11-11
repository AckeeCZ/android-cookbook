# Dependency injection

Dependency injection (DI) is a mechanism of providing the object with the dependencies (references to other objects the original object is dependent on). If you're not familiar with this pattern, this vague definition wont tell you much. But really, this concept is a lot simplier than you might think, and I'm sure you've done a million times before. As James Shore writes in [this useful article about DI][1]: "Dependency injection means giving an object its instance variables.". That's it... And nothing more.

Suppose you have some component `ProdRestInteractor` that communicates with your production REST API. And at some point in your app you need to access and get data from it:

```java
public class DataPresenter {

    // ...

    public void showData() {
        // ...
        data = new ProdRestInteractor().getData();
        // ...
    }
}
```

You may create a new object or access singleton, but it doesn't matter as long as you're coupling the method with one particular instance. What if sometimes, instead of `ProdRestInteractor`, you want to use `DevRestInteractor` in the same method? Or what if you want to mock this object and test it?

Right, you have to extract instance creation/choosing from `DataPresenter` class and delegate it to another component. For example by passing this instance to the constructor or using the setter:

```java
public class DataPresenter {

    IRestInteractor restInteractor;

    public DataPresenter(IRestInteractor restInteractor) {
        this.restInteractor = restInteractor;
    }

    // ...

    public void setRestInteractor(IRestInteractor restInteractor) {
        this.restInteractor = restInteractor;
    }

    public void showData() {
        // ...
        data = restInteractor.getData();
        // ...
    }
}
```

So, this was the dependency injection you've just did. :) Now, `DataPresenter` is decoupled from particular `IRestInteractor` instance and knows nothing about the data source (it may be production server, development server, mock or whatever else). The next object in the dependencies tree may choose to lift the dependency further up or decide what interactor to inject depending on build type, some config or even choose it in runtime, it's up to you.

Sounds pretty good, eh? But what if there are a lot of dependencies and a lot of components that require injection. There will be quite a mess with all those constructors and setters. And if you want to change some of the dependencies, remove or add one, you may need to rewrite it in multiple places. Moreover, the code becomes clumsy and less prone to errors. That's when the DI frameworks are coming to our help. 

In Ackee, we are using [Dagger 2][2]. You may read more about it vs [Dagger 1][3] [here][4]. But in short, some guys from [Square][5] who were working on first Dagger moved to Google and created the second one, which has its own advantages and disadvantages. We're using the second one because we're thinking it is more modern and easier to understand and use, thought misses some useful features. Anyway, you may choose your own.

Both Daggers are very well documented and there is a lot of blog posts and discussions on this topic, so we think that another tutorial here is redundant, you can easily google it by yourself. We'll just leave here a couple of examples how we use DI.

### Example 1: Dagger 2 and build variants

In Android Studio with the help of Gradle, we may easily customize our builds depending on build types and flavors ([variants][6] altogether). We may make conditional calls in the code or even have different files with the same name that provide different data or functionality depending on build variant. In the combination with Dagger 2 it gives us a powerful tool to manage all the dependencies for all build variants in one place without ever worrying about it again.

Here is a small code snippet showing different ways of combining DI and build variants in practice:

```java
@Module(
        includes = {
                // typically the DI is divided to several modules depending on functionality
        }
)
public class AppModule {
    private final App app;

    public AppModule(App app) {
        this.app = app;
    }

    @Provides
    @Singleton
    public Application provideApplication() {
        return app;
    }

    @Provides
    @Singleton
    public Logger provideLogger() {
        /*
          Suppose we want to log different info levels for debug and release builds. We just need to set it once in our DI module and then 
          inject it anywhere we want. If we want to add more levels in the future, we don't need to alter the code of injected classes,
          just change this one row.
        */
        return new Logger(BuildConfig.DEBUG); 
    }
    
    @Provides
    @Singleton
    public RestAdapter provideRestAdapter() {
        // Here we're providing REST API desctiption for Retrofit library
        return new Retrofit.Builder()
                .baseUrl(ApiConfig.BASE_URL) // ApiConfig (as well as base URL) differs for different API flavors (for example we have devApi and prodApi)
                .addConverterFactory(GsonConverterFactory.create())
                .client(generateClient())
                .build()
                .create(RestAdapter.class);
    }

    /*
      This class is generating a OkHttp client for our Retrofit. Suppose in production version of API, we need to add some
      extra headers to our requests. We are using ProdHeaderInterceptor for this. With our conditional call here, we're adding
      these headers only if it is "prodApi" build. Otherwise, this row will be ignored. Again, if we'll need to make some changes,
      we don't need to rewrite the classes that are using this dependency
    */
    private OkHttpClient generateClient(OAuthStore store) {
        OkHttpClient.Builder builder = new OkHttpClient.Builder();
        if (BuildConfig.FLAVOR.equals("prodApi")) {
            builder..addNetworkInterceptor(new ProdHeaderInterceptor())
        }
        return builder;
    }
    
    // ... 
}
```


### Example 2: using Dagger 2 for tests

Suppose we have a class (e.g. presenter) with injected dependencies:

```java
public class SomePresenter {

    @Inject
    IDatabase database;
    
    @Inject
    SharedPreferences sharedPreferences;
    
    @Inject
    IRestApi restApi;
    
    // ...

}
```

And a test for it:

```java
public class SomePresenterTest {

    @Test
    public void testFunction() {
        // ...
    }
}
```

And here is our Dagger module;

```java
@Module(
        includes = {
                // typically the DI is divided to several modules depending on functionality
                // suppose IRestApi is provided by another module (e.g. ServiceModule)
        }
)
public class AppModule {
    private final App app;

    public AppModule(App app) {
        this.app = app;
    }

    @Provides
    @Singleton
    public Application provideApplication() {
        return app;
    }

    @Provides
    @Singleton
    IDatabase provideDatabase() {
        return new ProdDatabase(app);
    }

    @Provides
    @Singleton
    SharedPreferences provideSharedPreferences() {
        return app.getSharedPreferences(Constants.SP_NAME_PROD, Context.MODE_PRIVATE);
    }
    
    // ... 
}
```

And the main component:

```java
@Singleton
@Component(modules = {AppModule.class})
public interface AppComponent {
    void inject(SomePresenter somePresenter);
}
```

Suppose, while testing, you need to change some of presenter dependencies with others (REST API mocks, testing database etc.). Of course you may use [Mockito][7] for these purposes, but sometimes, for example in UI tests, you may not access and replace these components from the test. Thats when injecting proper components from the beginning is handy.

Let's create another module (note: in Dagger 1 you may just extend the AppModule and override only the methods you need):

```java
@Module(
        includes = {
                // Here we can include existing modules, replace them by mocks or use anything else
                // Suppose we include TestServiceModule thad provides REST API mock 
        }
)
public class TestAppModule {
    private final App app;

    public AppModule(App app) {
        this.app = app;
    }

    @Provides
    @Singleton
    public Application provideApplication() {
        return app;
    }

    @Provides
    @Singleton
    IDatabase provideDatabase() {
        return new TestDatabase(app); // using the test database 
    }

    @Provides
    @Singleton
    SharedPreferences provideSharedPreferences() {
        return app.getSharedPreferences(Constants.SP_NAME_TEST, Context.MODE_PRIVATE); // using another fil for shared preferences
    }
    
    // ... 
}
```

If you want to inject some of these dependencies straight into the test, just extend your component like this:

```java
@Singleton
@Component(modules = {TestAppModule.class})
public interface TestAppComponent extends AppComponent {
    void inject(SomePresenterTest somePresenterTest);
}
```

Now we just need to replace the component in our injector class with the newly created TestAppComponent. In Ackee, we store component singleton in custom Application (App) class, so the setup method for the test will look like this:

```java
public class SomePresenterTest {

    @Before
    public void setUp() {
        context = InstrumentationRegistry.getInstrumentation().getTargetContext(); // get the comtext from Instrumentation
        testComponent = DaggerTestAppComponent.builder() // DaggerTestAppComponent is generated class
                .testAppModule(new TestAppModule((App) context.getApplicationContext())) // creating the module with the app context
//                .serviceModule(new TestServiceModule()) we may add other modules if we want
                .build();
        App.setAppComponent(testComponent); // now we set/replace the component singleton in the App class, so the other classes will be injected with the test component instead of the main one 
        testComponent.inject(this); // finally, we're injecting the test itself
    }

    @Test
    public void testFunction() {
        // ...
    }
}
```

And don't forget to clean after the test (delete testing DB, SP etc.) :).

Now, all the classes that are requesting injection from the App will be injected with the dependencies from TestAppModule. As you may see, these modules may be customized according to your desires. And all this can be done in DI modules/components only, without any intervention to the original classes. 

## Views injection with Butterknife

[Butterknife][8] from Jake Wharton is another very useful library that eliminates a lot of boilerplate code and makes the views representation in the classes more readable. 

In Butterknife, you may annotate views (eliminating `findViewById`) or methods (eliminating click, checked and other listeners creation).

Butterknife is quite easy and well documented. You just need to inject the views from your Activity/Fragment/View/Dialog into you target class with the `Butterknife.bind(target, source)` method and then you may use all the annotated views and methods.

Typically, the `bind()` method is called after view creation. If the view with the ID from annotation doesn't exist, the app will crash on injection. Alternatively, you may annotate the view with `@Nullable` annotation, and the injection will be successful even if the view doesn't exist. But if the view still doesn't exist when accessed, the app will crash with null pointer exception. `@Nullable` annotation is useful when you have multiple injections or you have some view/fragment that may interchange layouts or other situations when the view isn't guaranteed to exist. 

With the `@OnClick`, `@OnCheckedChanged` and other method annotations Butterknife will generate listeners for you. You may pass the list of multiple view IDs to the single method and then work with the view, received as the parameter. Or you don't pass anything if this method is in the `View` itself. But in most cases, you are working with the single view ID.

Here is a little example of using Butterknife with the fragment:

```java
public class SomeFragment extends Fragment {

    @BindView(R.id.text)
    TextView text;
    @BindView(R.id.image1)
    ImageView image1;
    @BindView(R.id.image2)
    ImageView image2;
    
    private Unbinder unbinder;
    
    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_some, container, false);
        unbinder = ButterKnife.bind(this, view);
        return view;
    }
    
    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        text.setText("Hello butter!"); // now you can access your views, as they're already injected
    }
    
    @Override 
    public void onDestroyView() {
        super.onDestroyView();
        unbinder.unbind(); // don't forget to unbind Butterknife to proper release of resources 
    }
    
    @OnClick(R.id.button)
    public void onButtonClick() {
        text.setText("On butter click");
    }
    
    @Optional // this is a @Nullable analog for method
    @OnCheckedChanged({R.id.checkbox1, R.id.checkbox1})
    public void onCheckboxCheck(View view, boolean checked) {
        switch (view.getId()) {
            case R.id.checkbox1:
                image1.setVisibility(checked ? View.VISIBLE : View.GONE);
            case R.id.checkbox2:
                image2.setVisibility(checked ? View.VISIBLE : View.GONE);
        }
    }
}
```

Butterknife has other useful features, just take a look at the docs. If you're interested in how Butterknife works, read [this][9].

[1]:	http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html
[2]:	https://google.github.io/dagger/
[3]:	http://square.github.io/dagger/
[4]:	http://stackoverflow.com/questions/31354975/java-dependency-injection-dagger-1-vs-dagger-2-which-is-better
[5]:    http://square.github.io/
[6]:    https://developer.android.com/studio/build/build-variants.html
[7]:    http://mockito.org/
[8]:    http://jakewharton.github.io/butterknife/
[9]:    https://medium.com/@lgvalle/how-butterknife-actually-works-85be0afbc5ab
