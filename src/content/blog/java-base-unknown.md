---
author: Maloong🐎🐲
pubDatetime: 2023-03-23T19:56:00Z
title: 卷起来🐎🐲💪 -- Java基础
postSlug: java-base-unknown
featured: false
draft: false
tags:
  - java
ogImage: ""
description: 工作十年也许都不知道的Java基础。
---

工作十年也许都不知道的 Java 基础。

## Table of contents

## 访问修饰符 public,private,protected,以及不写（默认）时的 区别

Java 中，可以使用访问修饰符来保护对类、变量、方法和构造方法的访 问。Java 支持 4 种不同的访问权限。

- private : 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部 类）
- default (即缺省，什么也不写，不使用任何关键字）: 在同一包内可见，不使用 任何修饰符。使用对象：类、接口、变量、方法。
- protected : 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意： 不能修饰类（外部类）。
- public : 对所有类可见。使用对象：类、接口、变量、方法

访问修饰符：

| 修饰符    | 当前类 | 同包 | 之类 | 其他包 |
| --------- | ------ | ---- | ---- | ------ |
| private   | ✅     | ❌   | ❌   | ❌     |
| default   | ✅     | ✅   | ❌   | ❌     |
| protected | ✅     | ✅   | ✅   | ❌     |
| public    | ✅     | ✅   | ✅   | ✅     |

## final finally finalize 区别

- final 可以修饰类、变量、方法，修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修
  饰变量表示该变量是一个常量不能被重新赋值。
- finally 一般作用在 try-catch 代码块中，在处理异常的时候，通常我们将一定要执行的代码方法
  finally 代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代
  码。
- finalize 是一个方法，属于 Object 类的一个方法，而 Object 类是所有类的父类，该方法一般由垃圾
  回收器来调用，当我们调用 System.gc() 方法的时候，由垃圾回收器调用 finalize()，回收垃圾，一
  个对象是否可回收的最后判断。

## static 存在的主要意义

- static 的主要意义是在于创建独立于具体对象的域变量或者方法。以致于即使没有创建对象，也能使用属
  性和调用方法！
- static 关键字还有一个比较关键的作用就是用来形成静态代码块以优化程序性能。static 块可以置于类中
  的任何地方，类中可以有多个 static 块。在类初次被加载的时候，会按照 static 块的顺序来执行每个 static
  块，并且只会执行一次。为什么说 static 块可以用来优化程序性能，是因为它的特性:只会在类加载的时
  候执行一次。因此，很多时候会将一些只需要进行一次的初始化操作都放在 static 代码块中进行。

### static 应用场景

因为 static 是被类的实例对象所共享，因此如果某个成员变量是被所有对象所共享的，那么这个成员变量
就应该定义为静态变量。
因此比较常见的 static 应用场景有：

1. 修饰成员变量
2. 修饰成员方法
3. 静态代码块
4. 修饰类【只能修饰内部类也就是静态内部类】
5. 静态导包

### static 注意事项

1. 静态只能访问静态。
2. 非静态既可以访问非静态的，也可以访问静态的。

## 多态的实现

Java 实现多态有三个必要条件：继承、重写、向上转型。

- 继承：在多态中必须存在有继承关系的子类和父类。
- 重写：子类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的 方法。
- 向上转型：在多态中需要将子类的引用赋给父类对象，只有这样该引用才能够具 备技能调用父类的方法
  和子类的方法。

只有满足了上述三个条件，我们才能够在同一个继承结构中使用统一的逻辑实现 代码处理不同的对象，
从而达到执行不同的行为。

## 抽象类和接口的对比

抽象类是用来捕捉子类的通用特性的。接口是抽象方法的集合。
从设计层面来说，抽象类是对类的抽象，是一种模板设计，接口是行为的抽象，是一种行为的规范。

接口的字段默认都是 static 和 final 的。

抽象类能使用 final 修饰吗？

不能，定义抽象类就是让其他类继承的，如果定义为 final 该类就不能被继承， 这样彼此就会产生矛
盾，所以 final 不能修饰抽象类

Java8 中接口中引入默认方法和静态方法，以此来减少抽象类和接口之间 的差异。
现在，我们可以为接口提供默认实现的方法了，并且不用强制子类来实现它。 接口和抽象类各有优缺
点，在接口和抽象类的选择上，必须遵守这样一个原则：

- 行为模型应该总是通过接口而不是抽象类定义，所以通常是优先选用接口，尽量 少用抽象类。
- 选择抽象类的时候通常是如下情况：需要定义子类的行为，又要为子类提供通用 的功能。

## 成员变量与局部变量的区别有哪些

- 成员变量：有默认初始值。
- 局部变量：没有默认初始值，使用前必须赋值。

## 在 Java 中定义一个不做事且没有参数的构造方法的作用

Java 程序在执行子类的构造方法之前，如果没有用 super()来调用父类特定的构 造方法，则会调用父类中
“没有参数的构造方法”。因此，如果父类中只定义了 有参数的构造方法，而在子类的构造方法中又没有
用 super()来调用父类中特定 的构造方法，则编译时将发生错误，因为 Java 程序在父类中找不到没有参数
的构 造方法可供执行。解决办法是在父类里加上一个不做事且没有参数的构造方法。

## 在调用子类构造方法之前会先调用父类没有参数的构造方法，其目的是？

帮助子类做初始化工作。

## 内部类

内部类可以分为四种：成员内部类、局部内部类、匿名内部类和静态内部类。

### 静态内部类

定义在类内部的静态类，就是静态内部类。

```java
public class Outer{
        private static int radius = 1;
        static class StaticInner {
                public void visit(){
                        System.out.println('visit outer static variable:' + radius);
                }
        }
}
```

静态内部类可以访问外部类所有的静态变量，而不可访问外部类的非静态变量； 静态内部类的创建方
式，new 外部类.静态内部类()，如下：

```java
Outer.StaticInner inner = new Outer.StaticInner();
inner.visit();
```

### 成员内部类

定义在类内部，成员位置上的非静态类，就是成员内部类。

```java
public class Outer {
    private static int radius = 1;
    private int count = 2;

    class Inner {
        public void visit(){
            System.out.println("visit outer static variable:" + radius);
            System.out.println("visit outer variable:" + count);
        }
    }
}
```

成员内部类可以访问外部类所有的变量和方法，包括静态和非静态，私有和公 有。成员内部类依赖于外
部类的实例，它的创建方式外部类实例.new 内部类()，如 下：

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.visit();
```

### 局部内部类

定义在方法中的内部类，就是局部内部类

```java

public class Outer {
    private static int static_a = 1;
    private int out_a = 2;

    public void testFunctionClass() {
        int inner_a = 3;
        class Inner {
            private void fun() {
                System.out.println(out_a);
                System.out.println(static_a);
                System.out.println(inner_a);
            }
        }
        Inner inner = new Inner();
        inner.fun();
    }

    public static void testStaticFunctionClass() {
        int inner_b = 4;
        class Inner {
            private void fun() {
                //System.out.println(out_a); 编译错误，定义在静态方法中的局部类不可以访问外部类的实例变量
                System.out.println(static_a);
                System.out.println(inner_b);
            }
        }
        Inner inner = new Inner();
        inner.fun();
    }

}

```

定义在实例方法中的局部类可以访问外部类的所有变量和方法，定义在静态方法 中的局部类只能访问外
部类的静态变量和方法。局部内部类的创建方式，在对应 方法内，new 内部类()，如下：

```java
public void testFunctionClass() {
    int inner_a = 3;
    class Inner {
        private void fun() {
            System.out.println(out_a);
            System.out.println(static_a);
            System.out.println(inner_a);
        }
    }
    Inner inner = new Inner();
    inner.fun();
}
```

### 匿名内部类

匿名内部类就是没有名字的内部类，日常开发中使用的比较多。

```java

public class Outer {

    public void test(final int i) {
        new Service() {
            public void method() {
                for (int j = 0; j < i; j++) {
                    System.out.println("匿名内部类");
                }
            }
        }.method();
    }

    // 匿名内部类必须继承或实现一个已有的接口
    interface Service {
        void method();
    }
}
```

除了没有名字，匿名内部类还有以下特点：

- 匿名内部类必须继承一个抽象类或者实现一个接口。
- 匿名内部类不能定义任何静态成员和静态方法。
- 当所在的方法的形参需要被匿名内部类使用时，必须声明为 final。
- 匿名内部类不能是抽象的，它必须要实现继承的类或者实现的接口的所有抽象方 法。

### 内部类的优点

我们为什么要使用内部类呢？因为它有以下优点：

- 内部类有效实现了“多重继承”，优化 java 单继承的缺陷。
- 匿名内部类可以很方便的定义回调。

### 局部内部类和匿名内部类访问局部变量的时候，为什么变量必须要加上 final？

是因为生命周期不一致， 局部变量直接存储在栈中，当方法执行结束后，非 final 的局部变量就被销毁。而局部内部类对局部变量的引用依然存在，如果局部内部类要调用局部变量时，就会出错。加了 final， 可以确保局部内部类使用的变量与外层的局部变量区分开，解决了这个问题。
反编译后可以看到，外部类以及被访问的局部变量会通过构造函数传进去，对于局部变量，内部类使用的引用和外部类使用的并不是同一个，而如果局部变量不是 final 的话，当其中一方对其重新赋值就会导致内部类和外部类的数据不同步。

```java
public class Outer {
    private Integer b = 3;
    void outMethod(int h) {
        int a = 10;

        class Inner {
            void innerMethod() {
                System.out.println(h);
                System.out.println(b);
                System.out.println(a);
            }
        }
        //a = 100; 不修改编译运行都正常
        //h = 100; 不修改编译运行都正常
        Inner i = new Inner();
        i.innerMethod();
    }
}
```
