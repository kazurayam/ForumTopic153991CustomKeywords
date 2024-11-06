# ForumTopic153991CustomKeywords

@authro kazurayam
@date 7th Nov  2024

## What is this?

This is a small Katalon Studio project for a demonstration purpose. You can download a zip of this project from the [Releases](https://github.com/kazurayam/ForumTopic153991CustomKeywords/releases) page, unzip it, run it on your local Katalon Studio. I used Katalon Studio Free v10.0.0, which let us use the "Custom Keywords" feature in the Free version.

Why I created this?

In Katalon User forum, @ESTEBAN raised a post at
https://forum.katalon.com/t/why-cannot-i-call-a-customkeyword-from-scripts-groovy/153991
. He asked a question:

>Do you know why we can’t use CustomKeywords within Include > Scripts

I would explain my view as follows.

## Explanation

### First response

I don't know. It is by design. Ask Katalon for sure.

Let me tell you my guess.

I think that the `CustomKeywords` class was originally designed to be used by the Test Case Editor in Manual mode. It is not meant to be used by human.

You know, the Manual mode lets you manipulate a Test Case in a tabular format in GUI. When you finished manipulating it and saved the change, the Editor generates a source code of Groovy language. While generating a groovy source, the Editor have to translate an entry that calls a custom class in the GUI table into a line of Groovy statement. At that instant, the Editor generates a line in the format of

`CustomKeywords."fully.qualified.class.name.methodName"(args...)`

When the Editor in Manual mode re-opens a test case script and when it finds `CustomKeywords.XXXX(yyy)` notation, it will restore the view in tabular format with a label that indicates that the row calls a Custom keyword. As such, `CustomKeywords.XXXX(yyy)` notation plays an important role for the Editor in Manual mode; but not for human.

On the other hand, we do not have a dedicated Editor with "Manual" mode for "Include > Scripts" which generates Groovy source. Therefore we do not need `CustomKeywords` class for "Include > Scripts". Don't worry. A script under the "Include/Scripts" folder can call your Groovy classes under the "Keywords" folder directly.

### Second response

@ESTEBAN

Let me tell you one point to make your understanding clearer.

In a Test Case script in the Script mode, you can call your Groovy class as a custom keyword. You can write a `Test Cases/TC1`, which goes as follows

```
import pages.Homepage
...
Homepage.clickHrefSignup()
```

This TC1 will run fine.

Please note that this code does not bother calling the `CustomKeywords` class to invoke `pages.Homepage.clickHrefSignup` method.

I mean, if you write a Test Case in the Script mode with your hands, you do not need the `CustomKeywords` class at all.

I would rather recommend you NOT to use the `CustomKeywords."fully.qualified.class.name.methodName"(args...)` notation in the Script mode of Test Case Editor **as long as you never use the Manual mode**.

### Third response

By the way, in the official document https://docs.katalon.com/katalon-studio/keywords/custom-keywords/introduction-to-custom-keywords-in-katalon-studio , it writes

> # Use custom keywords in the script view
>To use your defined custom keywords in the script view of a test case, do as follows:
>1. The Class `CustomKeywords` of Katalon Studio allows you to access all custom keywords. Enter the following syntax into the script editor:

```
CustomKeywords.
```

??? This description would make you confused as it looks against my explanation above.

Well, in my humble opinion, they wrote right; but partially.

I suppose that they cut describing the alternative way (call your custom keywords directly without `CustomKeywords`). Why? --- to make the doc readable for IT beginners, possibly.

The alternative way requires you to have the basic Java/Groovy programming knowledge. What is `import` statement? What is static method and instance method? What is `new` keyword? What is `class`? What is `instance`? ... These questions are too easy for you @ESTEBAN but not for everyone. I guess, Katalon thought it is better not to mention the alternative in the doc for conciseness.



