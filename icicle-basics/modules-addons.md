---
description: Module adding & reasons for the usage of them.
---

# 🗃 Modules (addons)

Icicle's functionalities are separated into their respective modules. We've done this, so you only get what you really need.

These addons can be accessed via a simple dependency notation in the dependency manager of your choice, but we highly recommend using our Gradle Plugin (you've learned about it [here](../get-started.md#setting-up-your-development-environment)).

### Adding modules to your project

{% tabs %}
{% tab title="With Gradle plugin" %}
### First party addons

In the case of first party addons, you can add them to your project by extending your `dependencies` block in your `build.gradle.kts`, below is an example where we add the `nms` module.

```kts
repositories {
    igloo() // This is required for ALL first party addons,
            // as they are hosted on our m2.
}

dependencies {
    icicle("nms:1.0-SNAPSHOT") // This will add the NMS addon with the
                               // version 1.0-SNAPSHOT.

    // icicle() can also be called without any parameters.
    // In that case, it'll add the icicle-core dependency into your project.
    // Example: icicle()

    // icicle() can be called without a version provided.
    // In this case, it should fetch the latest version and download that.
    // Example: icicle("nms")
}
```

And... bam! You're done. That's all it takes to add a first party addon.

### Third party modules

When you want to add a third party module/addon to your project, all you'll need to do is add the addon like you would any other`dependency`, except that instead of`implementation`, you'll be using `addon`. Below this, you can find an example for such an addon.



```kts
dependencies {
    addon("com.example:real-addon:1.0-SNAPSHOT") // This will add the
                                                 // real-addon from the
                                                 // creator example.com with
                                                 // the version 1.0-SNAPSHOT.
}
```
{% endtab %}

{% tab title="Manually" %}
Create an icicle.yml file in your resources folder if it does not already exist, and open it up.

If there wasn't already a dependencies in that file, then create a new list and add your addon in the following format:

```yaml
dependencies:
    # If your addon is third party, then proceed as normal.
    - "com.example:real-addon:1.0-SNAPSHOT" # Everything is necessary here.
    
    # If your addon is first party, then the syntax is
    # "net.iceyleagons:icicle-addon-<addon name>:<addon version>
    - "net.iceyleagons:icicle-addon-nms:1.0-SNAPSHOT" # Once again,
                                                      # everything is
                                                      # necessary.
```
{% endtab %}
{% endtabs %}

### Modules in the Documentation

Modules are their own story, and most of them are so complex, they deserve their own sections in this documentation:

1. [Commands System](broken-reference)

### Choose your modules

<details>

<summary>List of First Party Modules</summary>

* Icicle-Commands --> complete easy-to-use command system
* Icicle-NMS --> interface-based NMS mappings (deobfuscated via DeFrost)
* Icicle-Serialization --> stupidly fast lightweight serialization library
* Icicle-Protocol --> our own, easy-to-use protocol library
* Icicle-Kotlin --> juicy Kotlin utilities

</details>

<details>

<summary>List of Third Party Modules</summary>



</details>

