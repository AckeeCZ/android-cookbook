## Dependency injection

Dependency injection (DI) is a mechanism of providing the object with the dependencies (references to other objects the original object is dependent on). If you're not familiar with this pattern, this vague definition wont tell you much. But really, this concept is a lot simplier than you might think, and I'm sure you've done a million times before. As James Shore writes in [this useful article about DI][1]: "Dependency injection means giving an object its instance variables.". That's it... And nothing more.

Suppose you have some component `ProdRestInteractor` that communicates with your production REST API. And at some point in your app you need to access and get data from it.

```java
public class DataPresenter {

    // ...

    public void showData() {
        // ...
        data = new ProdRestInteractor.getData();
        // ...
    }
}
```

You may create a new object or access singleton, but it doesn't matter as long as you're coupling the method with one particular instance. What if sometimes, instead of `ProdRestInteractor`, you want to use `DevRestInteractor` in the same method? Or what if you want to mock this object and test it?

Right, you have to extract instance creation/choosing from `DataPresenter` class and delegate it to another component. For example by passing this instance to the constructor or using the setter.

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

So, this was the dependency injection you've just did. :) Now, `DataPresenter` is decoupled from particular `IRestInteractor` instance and knows nothing about the source of data (it may pe production server, development server, mock or whatever else). The next object in the dependencies tree may choose to lift the dependency further up or decide what interactor to inject depending on build type, some config or even choose it in runtime, it's up to you.

Sounds pretty good, eh? But what if there are a lot of dependencies and a lot of components that require injection. There will be quite a mess with all those constructors and setters. And if you want to change some of the dependencies, remove or add one, you may need to rewrite it in multiple places. Moreover, the code becomes clumsy and less prone to errors. That's when the DI frameworks are coming to our help. 

In Ackee, we are using [Dagger 2][2]. You may read more about it vs [Dagger 1][3] [here][4]. But in short, some guys from [Square][5] who were working on first Dagger moved to Google and created the second one, which has its own advantages and disadvantages. We're using the second one because we're thinking it is more modern and easier to understand and use, thought misses some useful features. Anyway, you may choose your own.

Both Daggers are very well documented and there is a lot of blog posts and discussions on this topic, so we think that another tutorial here is redundant. We'll just leave here a couple of examples how we use DI.

// TODO add examples

// TODO Butterknife injection

[1]:	http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html
[2]:	https://google.github.io/dagger/
[3]:	http://square.github.io/dagger/
[4]:	http://stackoverflow.com/questions/31354975/java-dependency-injection-dagger-1-vs-dagger-2-which-is-better
[5]:  http://square.github.io/
