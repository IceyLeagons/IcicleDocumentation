---
description: >-
  This page introduces the concept of beans and teaches the reader how to use
  them effectively.
---

# 🥜 Beans & Dependency Injection

The bean system is the heart of this entire framework (just like in Spring). It is the one responsible for creating your classes automatically, auto-wiring them in the necessary places, and responsible for tying everything together into a cozy ecosystem.

Prepare yourself for a load of "bad words", that may not make sense. This is a very technical page, but you have to endure it because this is the base of the framework.

### But what is a bean?

According to the Java white paper

> It is a reusable software component. A bean encapsulates many objects into one object so that we can access this object from multiple places. Moreover, it provides easy maintenance.

In our framework, beans are the objects that form the backbone of your application and that are managed by Icicle's IoC (Inversion of Control) container/system. A bean is an object that is instantiated, assembled, and otherwise managed by Icicle behind the scenes.

In our implementation, the IoC is basically the `AbstractIcicleApplication`, but the actual logic is mostly handled by the `BeanRegistry` and the `BeanManager`.

{% hint style="info" %}
There's a downside to this system however, and that is, that you cannot easily access bean objects from non-bean objects. For this, you'll have to have static access to the application specifically the `BeanRegistry`. If you follow the idiom of the structure, you really won't create non-bean objects. (not including utility classes)
{% endhint %}

Great, now that you've got a somewhat good idea about beans, let's dive deeper.

### What is Dependency Injection?

Dependency injection is a pattern we can use to implement IoC, where the implementation is setting an object's dependencies behind the scenes, automatically. It also resolves many issues like unresolvable dependencies or circular dependencies.

Connecting objects with other objects, or “injecting” objects into other objects, is done by an "assembler" rather than by the objects themselves. You can think of the job of this system as the Linker in C++ for example, but this is a very cruel comparison.

{% hint style="warning" %}
**In our implementation,** **DI can only be done via constructors & setters**. We are not planning to implement field auto-wiring, as it's considered bad practice.
{% endhint %}

### DI in practice

Let's look at an easy example: you have a manager that requires `JavaPlugin` to run.

{% tabs %}
{% tab title="Dependency Injection (Icicle)" %}
Here the **PlayerManagerImpl is automatically created**, you don't have to do that.\
This is also a `@Service`, which means you can auto-wire the same object by only specifying the interfaces it implements.&#x20;

{% hint style="warning" %}
Since interfaces can be implemented by many classes, Icicle will throw an error if a class is marked with `@Service` and implements an interface, that already has an implementation.
{% endhint %}

We've introduced a new thing here called `@Service`, this is basically the same as in Spring, but we'll talk about it later, so don't worry about it for now.\


{% code title="PlayerManagerImpl.java" %}
```java
@Service
public class PlayerManagerImpl implements PlayerManager {

    private final JavaPlugin plugin;

    @Autowired
    public PlayerManagerImpl(JavaPlugin plugin) {
        this.plugin = plugin;        
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="Native Code (without Icicle)" %}
Since natively there's no automatic creation, we'll have multiple classes here.

{% code title="PlayerManagerImpl.class" %}
```java
public class PlayerManagerImpl implements PlayerManager {

    private final JavaPlugin plugin;

    public PlayerManagerImpl(JavaPlugin plugin) {
        this.plugin = plugin;        
    }
}
```
{% endcode %}

{% code title="Main.java" %}
```java
public class MyPlugin extends JavaPlugin {
    private PlayerManager playerManager;
    
    @Override
    public void onEnable() {
        this.playerManager = PlayerManagerImpl(this);
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Types of Beans

As in the example, we've introduced a thing called `@Service`, but what is it, you may ask?\
Well, in this section you will learn about all the types of beans, and how they're related to one another.

{% hint style="warning" %}
We will discuss some advanced bean types here as well, that you won't really need in basic development, but will come into play whilst [Extending Icicle](broken-reference). So, don't freak out!
{% endhint %}

Every bean is basically a subtype of `@AutoCreate` (for the Spring guys, it's basically an equivalent of `@Component`). Later on different auto-create handlers will add different functionality to them.

So, without further ado, let's look at all the bean types that are by default in the core module:

* **@AutoCreate** - The most basic bean type, it only provides auto-creation, management, and dependency injection.
* **@Service** - A slightly advanced bean. You will probably use this the most. Other than the basic functionalities @AutoCreate provides, this also allows you to auto-wire the bean with all of their interfaces. However, an interface (basically the service) can only have one implementation (the actual class you put the @Service annotation on). If multiple implementations exist for the same service, you will get an error. (If I had to compare it to something, it would be like the fact that you cannot have the same class names in a package).
* **@GlobalService** - Basically the same as `@Service`, but the implementations also get registered into the `GlobalServiceProvider` (in Bukkit environments to Bukkit's `ServicesManager`)
* **@Config** - Reserved for configurations, learn more about them [here](configurations.md).
* **@MethodAdviceHandler** - Beans that handle the registration of `Advice` to the [ByteBuddy](https://bytebuddy.net/#/tutorial) proxy used by the `BeanManager`.
* **@MethodInterceptorHandler** - Beans that handle the registration of `Interceptors` to the [ByteBuddy](https://bytebuddy.net/#/tutorial) proxy used by the `BeanManager`.
* **@AnnotationHandler** - Beans that specialize in the customization part of the framework. They are what you will use to create new annotations and bean types. (They have 2 sub-types, but we're going to meet them later on, so no need to rush their introduction)

Remember, these bean types are the default in the core, modules can register their own, and you'll meet these specific sub-types in their designated module guide section.

