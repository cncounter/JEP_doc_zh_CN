# JEP 361: Switch Expressions

| Author      | Gavin Bierman                                                |
| ----------- | ------------------------------------------------------------ |
| Owner       | Jan Lahoda                                                   |
| Type        | Feature                                                      |
| Scope       | SE                                                           |
| Status      | Closed / Delivered                                           |
| Release     | 14                                                           |
| Component   | specification / language                                     |
| Discussion  | amber dash dev at openjdk dot java dot net                   |
| Effort      | S                                                            |
| Duration    | M                                                            |
| Relates to  | [JEP 354: Switch Expressions (Second Preview)](https://openjdk.org/jeps/354) |
|             | [JEP 325: Switch Expressions (Preview)](https://openjdk.org/jeps/325) |
| Reviewed by | Alex Buckley, Brian Goetz                                    |
| Endorsed by | Brian Goetz                                                  |
| Created     | 2019/09/04 06:35                                             |
| Updated     | 2022/03/11 20:22                                             |
| Issue       | [8230539](https://bugs.openjdk.java.net/browse/JDK-8230539)  |

## Summary

Extend `switch` so it can be used as either a statement or an expression, and so that both forms can use either traditional `case ... :` labels (with fall through) or new `case ... ->` labels (with no fall through), with a further new statement for yielding a value from a `switch` expression. These changes will simplify everyday coding, and prepare the way for the use of pattern matching in `switch`. This was a [preview language feature](http://openjdk.java.net/jeps/12) in [JDK 12](https://openjdk.java.net/jeps/325) and [JDK 13](https://openjdk.java.net/jeps/354).

## History

Switch expressions were [proposed in December 2017](https://mail.openjdk.java.net/pipermail/amber-dev/2017-December/002412.html) by [JEP 325](https://openjdk.java.net/jeps/325). JEP 325 was [targeted to JDK 12 in August 2018](https://mail.openjdk.java.net/pipermail/jdk-dev/2018-August/001827.html) as a [preview feature](https://openjdk.java.net/jeps/12). One aspect of JEP 325 was the overloading of the `break` statement to return a result value from a switch expression. Feedback on JDK 12 suggested that this use of `break` was confusing. In response to the feedback, [JEP 354](https://openjdk.java.net/jeps/354) was created as an evolution of JEP 325. JEP 354 proposed a new statement, `yield`, and restored the original meaning of `break`. JEP 354 was [targeted to JDK 13 in June 2019](https://mail.openjdk.java.net/pipermail/jdk-dev/2019-June/003052.html) as a preview feature. Feedback on JDK 13 suggested that switch expressions were ready to become final and permanent in JDK 14 with no further changes.

## Motivation

As we prepare to enhance the Java programming language to support [pattern matching (JEP 305)](https://openjdk.java.net/jeps/305), several irregularities of the existing `switch` statement -- which have long been an irritation to users -- become impediments. These include the default control flow behavior between switch labels (fall through), the default scoping in switch blocks (the whole block is treated as one scope), and the fact that `switch` works only as a statement, even though it is often more natural to express multi-way conditionals as expressions.

The current design of Java's `switch` statement follows closely languages such as C and C++, and supports fall through semantics by default. Whilst this traditional control flow is often useful for writing low-level code (such as parsers for binary encodings), as `switch` is used in higher-level contexts, its error-prone nature starts to outweigh its flexibility. For example, in the following code, the many `break` statements make it unnecessarily verbose, and this visual noise often masks hard to debug errors, where missing `break` statements would mean accidental fall through.

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

We propose to introduce a new form of switch label, "`case L ->`", to signify that only the code to the right of the label is to be executed if the label is matched. We also propose to allow multiple constants per case, separated by commas. The previous code can now be written:

```java
switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
    case TUESDAY                -> System.out.println(7);
    case THURSDAY, SATURDAY     -> System.out.println(8);
    case WEDNESDAY              -> System.out.println(9);
}
```

The code to the right of a "`case L ->`" switch label is restricted to be an expression, a block, or (for convenience) a `throw` statement. This has the pleasing consequence that should an arm introduce a local variable, it must be contained in a block and is thus not in scope for any of the other arms in the switch block. This eliminates another annoyance with traditional switch blocks where the scope of a local variable is the entire block:

```java
switch (day) {
    case MONDAY:
    case TUESDAY:
        int temp = ...     // The scope of 'temp' continues to the }
        break;
    case WEDNESDAY:
    case THURSDAY:
        int temp2 = ...    // Can't call this variable 'temp'
        break;
    default:
        int temp3 = ...    // Can't call this variable 'temp'
}
```

Many existing `switch` statements are essentially simulations of `switch` expressions, where each arm either assigns to a common target variable or returns a value:

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

Expressing this as a statement is roundabout, repetitive, and error-prone. The author meant to express that we should compute a value of `numLetters` for each day. It should be possible to say that directly, using a `switch` *expression*, which is both clearer and safer:

```java
int numLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    case THURSDAY, SATURDAY     -> 8;
    case WEDNESDAY              -> 9;
};
```

In turn, extending `switch` to support expressions raises some additional needs, such as extending flow analysis (an expression must always compute a value or complete abruptly), and allowing some case arms of a `switch` expression to throw an exception rather than yield a value.

## Description

### Arrow labels

In addition to traditional "`case L :`" labels in a switch block, we define a new simplified form, with "`case L ->`" labels. If a label is matched, then only the expression or statement to the right of the arrow is executed; there is no fall through. For example, given the following `switch` statement that uses the new form of labels:

```java
static void howMany(int k) {
    switch (k) {
        case 1  -> System.out.println("one");
        case 2  -> System.out.println("two");
        default -> System.out.println("many");
    }
}
```

The following code:

```java
howMany(1);
howMany(2);
howMany(3);
```

results in the following output:

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