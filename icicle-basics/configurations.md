---
description: This page contains a config file that's for demonstration purposes.
---

# 🔧 Configurations

Icicle provides an easy way for multi-file configurations. Our solution also allows you to explain the different configuration fields with comments.

{% hint style="info" %}
Currently, we only support YAML and Properties as our configuration file, but we're planning to introduce HOCON and TOML.
{% endhint %}

When you create a config or add a new field to an already existing config, Icicle will create the config file if necessary and updates the default values if needed.

Default values are taken from the field's default values set by you in the class. When the default value and the config set value are different, we'll inject the new value into the field. Basically from now on the contents of the config file controls the values of the fields inside the config bean.

### Let's create a config file!

Creating a simple config file is as easy as:

```java
@Config(value = "config.yml", headerLines = {"You can even add headers!"})
public class MyConfig implements Configuration {

    @ConfigField("settings.prefix")
    @ConfigComment("Change the prefix of the demo.")
    public String prefix = "DefaultPrefix";

    @ConfigField("settings.auto-update")
    public boolean autoUpdate = true;                
}
```

This code will result in the following file:

{% code title="config.yml" %}
```yaml
# You can even add headers!

settings:
  prefix: DefaultPrefix # Change the prefix of the demo.
  auto-update: true
```
{% endcode %}

{% hint style="warning" %}
**Implementing Configuration is required.** This is what gives you the methods necessary to reload, load, etc. your config.

Even though the interface uses defaults, which throw UnImplementedExceptions, the ConfigDelegator will proxy your config class, and route these methods to the appropriate driver.
{% endhint %}

{% hint style="info" %}
The driver is selected based on the extension of the file you provide!
{% endhint %}

### Accessing values of a config

Accessing configuration values can be done in **2 ways**.

* One is auto-wiring the whole config, and accessing it via the fields
* &#x20;You can auto wire specific values and not the whole config, however, with this, you lose the ability to change or reload those values.

Let's take a look at both of them

{% tabs %}
{% tab title="Whole Config method" %}
```java
@Service
public class UpdaterServiceImpl implements UpdaterService {

    private boolean autoUpdate;

    @Autowired
    public UpdaterServiceImpl(MyConfig config) {
        this.autoUpdate = (boolean) config.get("settings.prefix");     
    }
}
```
{% endtab %}

{% tab title="Specific value auto-wiring" %}
```java
@Service
public class UpdaterServiceImpl implements UpdaterService {

    private boolean autoUpdate;

    @Autowired
    public UpdaterServiceImpl(@Property("settings.prefix") boolean autoUpdate) {
        this.autoUpdate = autoUpdate;  
    }
}
```
{% endtab %}
{% endtabs %}

With that being said, you now know the configuration system of Icicle. It is easy, isn't it?\
Let's move forward, and see, how you can make your plugins accessible in many languages!
