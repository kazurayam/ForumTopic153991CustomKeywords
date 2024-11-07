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

### Last response


Let me tell you my guess.

I think that the `CustomKeywords` class was originally designed for the sake of Test Case Editor in Manual mode. It is not meant for human.

Let me show you an example.

I made a custom Groovy class `Keywords/pages/Homepage.groovy`:

```
package pages

import com.kms.katalon.core.annotation.Keyword

class Homepage {

    @Keyword
    static void clickHrefSignup() {
        println "clickHrefSignup was invoked"
    }
}
```

I made 2 Test Case scripts:

1. `Test Cases/TC1`

```
import pages.Homepage

Homepage.clickHrefSignup()
```

When I ran the TC1, it ran fine. The output was like this this:

```
11月 07, 2024 9:22:58 午前 com.kms.katalon.core.logging.KeywordLogger startTest
情報: --------------------
11月 07, 2024 9:22:58 午前 com.kms.katalon.core.logging.KeywordLogger startTest
情報: START Test Cases/TC1
clickHrefSignup was invoked
11月 07, 2024 9:22:59 午前 com.kms.katalon.core.logging.KeywordLogger endTest
情報: END Test Cases/TC1
```

2. `Test Cases/TC2`

```
CustomKeywords.'pages.Homepage.clickHrefSignup'()
```

When I ran the TC2, it ran fine. The output was like this this:
```
11月 07, 2024 9:03:41 午前 com.kms.katalon.core.logging.KeywordLogger startTest
情報: --------------------
11月 07, 2024 9:03:41 午前 com.kms.katalon.core.logging.KeywordLogger startTest
情報: START Test Cases/TC2
clickHrefSignup was invoked
11月 07, 2024 9:03:42 午前 com.kms.katalon.core.logging.KeywordLogger log
情報: pages.Homepage.clickHrefSignup is PASSED
11月 07, 2024 9:03:42 午前 com.kms.katalon.core.logging.KeywordLogger endTest
情報: END Test Cases/TC2
```

As you see, both of TC1 and TC2 ran fine. The outputs are identical.

Now, let's look at the TC1 and TC2 in the "Manual" view of the Test Case Editor.

1. TC1 ![TC1](https://kazurayam.github.io/ForumTopic153991CustomKeywords/images/TC1.png)

2. TC2 ![TC1](https://kazurayam.github.io/ForumTopic153991CustomKeywords/images/TC2.png)

Please compare these 2 screenshots. You would surely notice how the Test Case Editor in Manual view renders them differently. How can the Editor differentiate them? The key is the code format as:

`CustomKeywords."fully.qualified.class.name.methodName"(args...)`

When the Editor in Manual view re-opens a test case script and when it finds `CustomKeywords.XXXX(yyy)` notation in the source, it will renders the row that calls a Custom keyword, as it did for TC2. Otherwise, it will restore the row with a meaningless label `Method Call Statement` as it did for TC1. As such, the `CustomKeywords.XXXX(yyy)` notation is significant for the Editor software. It gives the Editor in Manual view a signal how it should render the source code line in the tabular form.

Now, let me go back to @ESTEBAN's original question:

>"Why we do not have CustomKeywords for "Include > Scripts?"

Katalon Studio does not provide a dedicated Editor with "Manual" view for "Include > Scripts". Therefore we do not need `CustomKeywords` class for "Include > Scripts" at all. Don't worry. You can write a script under the "Include/Scripts" folder using text editor, which calls your Groovy class under the "Keywords" folder directly.
