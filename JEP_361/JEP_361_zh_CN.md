# JEP 361: switch增强


| 文档信息     | 值 |
| :------:    | :------------- |
| Author      | Gavin Bierman                                                |
| 负责人员     | Jan Lahoda                                                   |
| 文档类型     | 功能特征类(Feature)                                            |
| Scope       | SE                                                           |
| 文档状态     | Closed / Delivered                                           |
| Release     | 14                                                           |
| Component   | specification / language                                     |
| Discussion  | amber dash dev at openjdk dot java dot net                   |
| Effort      | S                                                            |
| Duration    | M                                                            |
| 相关提案     | [JEP 354: Switch Expressions (Second Preview)](https://openjdk.org/jeps/354) |
|             | [JEP 325: Switch Expressions (Preview)](https://openjdk.org/jeps/325) |
| 校对人员 | Alex Buckley, Brian Goetz                                    |
| Endorsed by | Brian Goetz                                                  |
| 创建时间     | 2019/09/04 06:35                                             |
| 更新时间     | 2022/03/11 20:22                                             |
| Issue       | [8230539](https://bugs.openjdk.java.net/browse/JDK-8230539)  |

## 概述(Summary)

本提案的目的, 是为了扩展和增强 `switch`, 使其既可以用作语句(statement), 也可以用作表达式(expression)，而且可以使用两种形式:

- 传统的冒号标签:  `case ... :` ,带默认回退fall through; 
- 新的箭头标签: `case ... ->` , 没有回退, 附带一个新的语句, 用于从 switch 表达式中产生一个值。 

这些更改将简化日常编码， 并为在 `switch` 中使用模式匹配做准备。 

在 [JDK 12](https://openjdk.java.net/jeps/325) 和 [JDK 13](https://openjdk.java.net/jeps/354) 版本中属于 [预览功能(preview language feature)](http://openjdk.java.net/jeps/12)。


## 历史(History)


switch表达式, 最早是在 [2017年12月](https://mail.openjdk.java.net/pipermail/amber-dev/2017-December/002412.html) 的 [JEP 325](https://openjdk.java.net/jeps/325) 中提出的。 JEP 325 成为 [2018年8月份JDK 12](https://mail.openjdk.java.net/pipermail/jdk-dev/2018-August/001827.html)的目标 [预览功能](http://openjdk.java.net/jeps/12)。
JEP 325 中有一个特性是重载 `break` 语句, 以从 switch 表达式返回结果值。 对 JDK 12 的反馈表明，这种 `break` 的使用很令人困惑。  作为对反馈的回应，创建了 [JEP 354](https://openjdk.java.net/jeps/354) 提案, 作为JEP 325的迭代。
JEP 354提出了一个新的 `yield` 语句, 并恢复了 `break` 原本的语义。 
JEP 354 成为 [2019年6月JDK 13](https://mail.openjdk.java.net/pipermail/jdk-dev/2019-June/003052.html) 的目标预览功能。 
JDK 13 相关的反馈表明, `switch` 表达式已准备好在 JDK 14 中成为最终的和永久的功能特征, 不再需要更改。


## 目的(Motivation)

在进行Java编程语言增强以支持 [模式匹配 (JEP 305)](https://openjdk.java.net/jeps/305) 的准备时，我们发现之前的 `switch` 语句有些不太好的地方, 一直饱受诟病, 也成为了我们的阻碍。 
包括:

- 多个 `switch` 标签之间的默认控制流行为（fall through）
- `switch` 块中的默认作用域范围（整个块被视为一个作用域范围）, 
- `switch` 仅仅只是一个语句, 即使它经常被用来作为一种更自然的多路条件表达式。

Java语言中的 `switch` 语句, 目前的设计紧跟 C、C++ 等语言，默认支持 fall through 语义。  虽然这种传统的控制流, 通常对于编写底层代码很有用（例如二进制编码的解析器）， 但由于在高级别的上下文中使用了 `switch`，其容易出错的性质明显超过了其灵活性。 
例如，在下面的代码中，这么多的 `break` 语句显得很没有必要, 而且很冗长，并且这种排版效果也容易引起调试问题, 但如果少写了 `break` 语句又会导致某些意料之外的结果。

```java
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        System.out.println(6);
        break;
    case TUESDAY:
        System.out.println(7);
        break;
    case THURSDAY:
    case SATURDAY:
        System.out.println(8);
        break;
    case WEDNESDAY:
        System.out.println(9);
        break;
}
```

我们提议引入一种新的`switch`标签格式:  "`case L ->`", 用来代表: 只有匹配到标签，才执行标签右侧的代码。 
我们还提议每个 `case` 允许多个常量，用英文逗号分隔。 
前面的代码现在可以写成：

```java
switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
    case TUESDAY                -> System.out.println(7);
    case THURSDAY, SATURDAY     -> System.out.println(8);
    case WEDNESDAY              -> System.out.println(9);
}
```

一个 "`case L ->`" 格式的switch标签, 右侧的代码只能是:
- 表达式
- 块
- 或者 `throw` 语句（为方便起见）。 

这有一个令人愉快的结果，如果某个分支声明了局部变量，则必须包含在一个块中, 因此就不在 switch 块中其他任何分支的作用域范围内。 
这消除了传统 switch 语句块的另一个弊端，因为其中局部变量的范围是整个块:

```java
switch (day) {
    case MONDAY:
    case TUESDAY:
        int temp = ...     // 'temp' 变量的作用域范围一直到右花括号 }
        break;
    case WEDNESDAY:
    case THURSDAY:
        int temp2 = ...    // 但这里的变量名不能再叫做 'temp'
        break;
    default:
        int temp3 = ...    // 这里也不能使用变量名 'temp'
}
```


现有的许多 `switch` 语句, 本质上是对 `switch` 表达式的模拟，其中每个分支:
- 要么给一个公共变量赋值
- 要么返回一个值

```java
int numLetters;
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        numLetters = 6;
        break;
    case TUESDAY:
        numLetters = 7;
        break;
    case THURSDAY:
    case SATURDAY:
        numLetters = 8;
        break;
    case WEDNESDAY:
        numLetters = 9;
        break;
    default:
        throw new IllegalStateException("Wat: " + day);
}
```

用语句来表述这类表达式, 看起来比较绕、代码重复、而且还很容易出错。 
上面这段代码的含义是: 我们应该为每天计算一个 `numLetters`值。 
换成 `switch` 表达式，可以更直观，更清晰，而且更安全：

```java
int numLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    case THURSDAY, SATURDAY     -> 8;
    case WEDNESDAY              -> 9;
};
```

反过来, 扩展  `switch` 以支持表达式会引发一些额外的需求, 例如扩展流分析（表达式必须始终计算某个值或突然完成），并允许 `switch` 表达式的某些 case 分支抛出异常, 而不是生成一个值。


## 描述(Description)

### 箭头格式的标签(Arrow labels)


除了传统 `switch` 语句块中的  "`case L :`" 标签外，我们还定义了一种新的简化形式: "`case L ->`" 标签。 
如果匹配到某个标签，则只执行箭头右侧的表达式或语句; 没有fall through。 
例如，给定以下使用新标签形式的 `switch` 语句：

```java
static void howMany(int k) {
    switch (k) {
        case 1  -> System.out.println("one");
        case 2  -> System.out.println("two");
        default -> System.out.println("many");
    }
}
```

如果调用下面的代码:

```java
howMany(1);
howMany(2);
howMany(3);
```

输出的结果内容为:

```
one
two
many
```

### Switch expressions

We extend the `switch` statement so it can be used as an expression. For example, the previous `howMany` method can be rewritten to use a `switch` expression, so it uses only a single `println`.

```java
static void howMany(int k) {
    System.out.println(
        switch (k) {
            case  1 -> "one";
            case  2 -> "two";
            default -> "many";
        }
    );
}
```

In the common case, a `switch` expression will look like:

```java
T result = switch (arg) {
    case L1 -> e1;
    case L2 -> e2;
    default -> e3;
};
```

A `switch` expression is a poly expression; if the target type is known, this type is pushed down into each arm. The type of a `switch` expression is its target type, if known; if not, a standalone type is computed by combining the types of each case arm.

### Yielding a value

Most `switch` expressions will have a single expression to the right of the "`case L ->`" switch label. In the event that a full block is needed, we introduce a new `yield` statement to yield a value, which becomes the value of the enclosing `switch` expression.

```java
int j = switch (day) {
    case MONDAY  -> 0;
    case TUESDAY -> 1;
    default      -> {
        int k = day.toString().length();
        int result = f(k);
        yield result;
    }
};
```

A `switch` expression can, like a `switch` statement, also use a traditional switch block with "`case L:`" switch labels (implying fall through semantics). In this case, values are yielded using the new `yield` statement:

```java
int result = switch (s) {
    case "Foo": 
        yield 1;
    case "Bar":
        yield 2;
    default:
        System.out.println("Neither Foo nor Bar, hmmm...");
        yield 0;
};
```

The two statements, `break` (with or without a label) and `yield`, facilitate easy disambiguation between `switch` statements and `switch` expressions: a `switch` statement but not a `switch` expression can be the target of a `break` statement; and a `switch` expression but not a `switch` statement can be the target of a `yield` statement.

Rather than being a keyword, `yield` is a restricted identifier (like `var`), which means that classes named `yield` are illegal. If there is a unary method `yield` in scope, then the expression `yield(x)` would be ambiguous (could be either a method call, or a yield statement whose operand is a parenthesized expression), and this ambiguity is resolved in favor of the yield statement. If the method invocation is preferred then the method should be qualified, with `this` for an instance method or the class name for a static method.

### Exhaustiveness

The cases of a `switch` expression must be *exhaustive*; for all possible values there must be a matching switch label. (Obviously `switch` statements are not required to be exhaustive.)

In practice this normally means that a `default` clause is required; however, in the case of an `enum` `switch` expression that covers all known constants, a `default` clause is inserted by the compiler to indicate that the `enum` definition has changed between compile-time and runtime. Relying on this implicit `default` clause insertion makes for more robust code; now when code is recompiled, the compiler checks that all cases are explicitly handled. Had the developer inserted an explicit `default` clause (as is the case today) a possible error will have been hidden.

Furthermore, a `switch` expression must either complete normally with a value, or complete abruptly by throwing an exception. This has a number of consequences. First, the compiler checks that for every switch label, if it is matched then a value can be yielded.

```java
int i = switch (day) {
    case MONDAY -> {
        System.out.println("Monday"); 
        // ERROR! Block doesn't contain a yield statement
    }
    default -> 1;
};
i = switch (day) {
    case MONDAY, TUESDAY, WEDNESDAY: 
        yield 0;
    default: 
        System.out.println("Second half of the week");
        // ERROR! Group doesn't contain a yield statement
};
```

A further consequence is that the control statements, `break`, `yield`, `return` and `continue`, cannot jump through a `switch` expression, such as in the following:

```java
z: 
    for (int i = 0; i < MAX_VALUE; ++i) {
        int k = switch (e) { 
            case 0:  
                yield 1;
            case 1:
                yield 2;
            default: 
                continue z; 
                // ERROR! Illegal jump through a switch expression 
        };
    ...
    }
```

## Dependencies

This JEP evolved from [JEP 325](https://openjdk.java.net/jeps/325) and [JEP 354](https://openjdk.java.net/jeps/354). However, this JEP is standalone, and does not depend on those two JEPs.

Future support for pattern matching, beginning with [JEP 305](https://openjdk.java.net/jeps/305), will build on this JEP.

## Risks and Assumptions

The need for a `switch` statement with `case L ->` labels is sometimes unclear. The following considerations supported its inclusion:

- There are `switch` statements that operate by side-effects, but which are generally still "one action per label". Bringing these into the fold with new-style labels makes the statements more straightforward and less error-prone.
- That the default control flow in a `switch` statement's block is to fall through, rather than to break out, was an unfortunate choice early in Java's history, and continues to be a matter of significant angst for developers. By addressing this for the `switch` construct in general, not just for `switch` expressions, the impact of this choice is reduced.
- By teasing the desired benefits (expression-ness, better control flow, saner scoping) into orthogonal features, `switch` expressions and `switch` statements could have more in common. The greater the divergence between `switch` expressions and `switch` statements, the more complex the language is to learn, and the more sharp edges there are for developers to cut themselves on.