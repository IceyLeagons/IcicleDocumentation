---
description: A quick walkthrough thru the basics of localization.
---

# 🌐 Localization

Providing multi-language support is vital for a successful plugin. Icicle provides an easy-to-use system with a built-in script language similar to Excel's.

Localization is done through the `TranslationService`.

### Setting up StringProviders

`StringProviders` are the ones that provide the `TranslationService` with a string for a key from a file. With this abstraction, the developers can implement any kind of source they can think of, be it a simple file-based one, or maybe a remote solution.&#x20;

Built-in solutions are the following:

* FileStringProvider --> Used for switching between multiple LanguageFiles

If you want to set up your own, do it like this:

```java
public class MyStringProvider implements TranslationStringProvider {

    @Override
    public String get(String language, String key, String defaultValue) {
        // We recommend automatically setting the defaultValue at the key, if your
        // source does not contain a key. We also recommend returning the defaultValue here as well
        // although it should be handled in the TransltionService.
        return defaultValue;
    }
}

```

### Setting up LanguageProviders

`LanguageProviders` are the ones responsible for providing a language code for a `Player`. Sources can be databases, geolocation, or just a fallback fixed language.

If you want to use the built-in providers, they are the following:

* ConstantLanguageProvider --> Always returns a pre-set language (used as fallback)
* PlayerLanguageGeoProvider --> returning language-based of geolocation (experimental, relies on external API!!!)
* Planned: ~~DatabaseLanguageProvider~~

If you've decided you want a custom implementation, you can do so like this:

```java
public class MyLanguageProvider implements LanguageProvider {
    @Override
    public String getLanguage(Object key) {
        // key is an object due to multi-environment support.
        // In Bukkit environments it is Player, but we recommend
        // writing an instanceof check just in case
        return "returning language code, ex.: en, de, hu";
    }
}
```

### Using the TranslationService

The `TranslationService` must be auto-wired. By default - if no providers have been set up - it will use "en" as the language, and will only use the default values.

When first getting keys, the service will automatically append the default values to a translation file of choice (`StringProvider` must be set up for this).

{% hint style="warning" %}
By default, the **TranslationService does not parse color codes**, as it's in the core module, and the core does not depend on Bukkit.
{% endhint %}

### Advanced Usage - String Code

`StringCode` is a special Excel formula-like script language, that provides translators with the ability to write logic inside a string, therefore making language-specific tasks easier.

{% hint style="warning" %}
Code must be written between {}, escaping with \ is supported.
{% endhint %}

<details>

<summary>Example</summary>

```java
You have {IF(EQ(amount, 1), IF(SW(item, 'a', 'e', 'i', 'o', 'u'),'an','a'),amount)} {item}{IF(GT(amount, 1), 's', '')}.
```

`amount` and `item` are parameters passed from the plugin.

If the amount is 12, and the item is apple: `You have 12 apples.`\
If the amount is 1, and the item is apple: `You have an apple.`\
If the amount is 1, and the item is pear: `You have a pear.`

</details>

The following table shows all the functions you can use & their purpose.

{% hint style="info" %}
These are just the built-in functions. The system is completely expandable.
{% endhint %}

| Function                                   | Description                                                                                                    |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------- |
| **TRUE**()                                 | Constant true value.                                                                                           |
| **FALSE**()                                | Constant false value.                                                                                          |
| **IF**(statement, true, false)             | If the statement is true, then the value inside true will be returned, otherwise the value inside false.       |
| **OR**(statement1, \[statement2], ...)     | If any of the statements inside the function return true, then the function will return true, otherwise false. |
| **NOT**(statement)                         | Returns the logical opposite of the statement.                                                                 |
| **AND**(statement1, \[statement2], ...)    | This function will only return true, if all of the statements inside it return true.                           |
| **EW**(value, test, \[test2], ...)         | Tests whether the value ends with the supplied test values. Will return true/false accordingly.                |
| **SW**(value, test, \[test2], ...)         | Tests whether the value starts with the supplied test values. Will return true/false accordingly.              |
| **NE**(value1, value2)                     | This function will return true if value1 and value2 do not match.                                              |
| **EQ**(value1, value2)                     | This function will return true if value1 is equal to value2.                                                   |
| **LTEQ**(number1, number2)                 | Tests whether number1 is equal to or less than the second number2.                                             |
| **LT**(number1, number2)                   | Tests whether number1 is less than the second number.                                                          |
| **GTEQ**(number1, number2)                 | Tests whether number1 is equal to or greater than the second number2.                                          |
| **GT**(number1, number2)                   | Tests whether numbe1 is greater than the second number.                                                        |
| **CONCAT**(value, \[value2], ...)          | Concatonates the values inside the function, and returns them.                                                 |
| **JOIN**(separator, value, \[value2], ...) | Joines the values inside the function together with the supplied separator.                                    |
| **SUB**(number1, number2)                  | Subtracts number2 from number1.                                                                                |
| **ADD**(number1, number2)                  | Adds the two numbers together, and returns the result.                                                         |
| **MUL**(number1, number2)                  | Multiplies the two numbers together, and returns the result.                                                   |
| **MOD**(number1, number2)                  | Returns the remainder of number1 divided by number2.                                                           |
| **DIV**(number1, number2)                  | Divides the first number with the second.                                                                      |
