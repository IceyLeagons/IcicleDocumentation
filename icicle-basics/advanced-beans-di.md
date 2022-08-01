# 🎓 Advanced Beans/DI

### @PostConstruct

The PostConstruct annotation can be used on methods, to turn them into, well a secondary constructor.

These PostConstruct methods will only be called after the @Bean methods have been handled, and the setters have been auto-wired, but before the bean is registered.

{% hint style="warning" %}
PostConstruct methods should not have parameters!
{% endhint %}

### Setter Auto-Wiring

There are some cases when you don't want to auto-wire only via constructors. For this exact purpose, we have introduced setter autowiring. Just create a setter and put @Autowired on top of it.

{% hint style="info" %}
Hint: You can have multiple beans auto-wired in the same setter as well.
{% endhint %}

```java
@Service
@Qualifier("secondImpl")
public class DemoServiceImpl implements DemoService {
    
    @Autowired
    public void setterAutowiring(OtherService service, DemoService defaultImplementation) {
        [...]
    }
}
```

### Qualifiers

Remember, when we told you, that services could only have one implementation? Well, that was kind of a lie, we're sorry.

In order to achieve multiple implementations of the same service, you need to meet the `@Qualifier` annotation. Using it is pretty straightforward:&#x20;

1. Apply it to the implementation
2. Give it a qualifier (aka. name)
3. When you auto-wire, apply the same annotation, with the same qualifier

{% hint style="warning" %}
There can be one implementation, which does not use a qualifier. This is basically the default implementation. When you auto-wire and don't specify a qualifier, this is what will be injected.&#x20;
{% endhint %}

Let us show you an example. Imagine that we have a service called DemoService, and we have 2 implementations for it:

```java
public class ServiceUser {
    public ServiceUser(@Qualifier("secondImpl") DemoService second,
                        DemoService default) {
        [...]
    }
}
```

```java
@Service
public class DefaultDemoService implements DemoService {
    [...]
}

@Service
@Qualifier("secondImpl")
public class OtherDemoService implements DemoService {
    [...]
}
```

### Value Modifiers

Modifiers can be used to modify a value passed to a method at runtime using different logic.\
These modifiers are enabled via our proxy.\
But let an example speak for itself:

{% hint style="info" %}
Modifiers can only be used inside Icicle managed beans. Modifiers also require the method to have `@ModifiersActive on it (this is to save resources).`
{% endhint %}

```java
@ModifiersActive
public void test(@DefaultValue("N/A") String input) {
    // If the supplied input is null, the N/A will be printed, otherwise the value passed.
    System.out.println(input);
}
```

The implementation of this modifier is:

```java
@MethodValueModifier(DefaultValue.class)
public class DefaultValueHandler implements ValueModifier {

    @Override
    public Object modify(Object input, Parameter parameter) {
        return input == null ? parameter.getAnnotation(DefaultValue.class).value() : input;
    }
}

```

### @Primary marked beans

Oh. This section is still waiting to be written...
