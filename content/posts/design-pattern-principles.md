---
title: "S.O.L.I.D Design Principles"
date: 2019-09-08T09:43:07+08:00
draft: true
---

# Single Responsibility Principle

SRP比较常见，也很好理解，可以用下面一句话概括。

> There should never be more than one reason for a class to change.

# Liskov Substitutation Principle

Barbara Liskov在她的论文里这样描述LSP

> Let ф(x) be a property provable about objects x of type T. Then ф(y) should be true for objects y of type S where S is a subtype of T.

在代码里可以解释为，任何使用父类的地方，都可以用子类替换并且程序的行为和结果没有发生变化。

LSP一种是对OO中继承的规范，要求在子类继承父类的时候不要通过重载父类方法，而改变类的行为。这看上去与多态是矛盾的，其实是分别针对两种不同的情况。

多态是面向行为编程，通常都是通过抽象类或者接口去定义一系列相关的方法，实现类或者子类各自实现然后表现出多态。实际情况中，不同的实现类会有相同实现或者为了某种目的，接口提供了一个默认的实现，那么在C++这样用抽象类的语言中，看上去确实是子类继承了父类，并且改变了父类的行为，违反了LSP。但实际上这只是代码的共用，不能算典型的继承。

示例如下

```TypeScript
// Drawable的接口就是用来实现多态的，它规定的只是行为，至于到底是什么东西
// 去行使这些行为它是无所谓的。
interface Drawable {
    draw(): void;
}

class Circle implements Drawable {
    public draw() {
        // draw a circle
    }
}

class Rectangle implements Drawable {

    protected width: number;
    protected height: number;

    public getArea() {
        return width * height;
    }

    public draw() {
        // draw a rectangle
    }
}

// Square是Rectangle的一种特例，有自己个性存在，
// 但它本质上还是Rectangle，所以类似getArea的共性
// 方法，其结果要与父类保持一致。这就是LSP的要求所在。
class Square extends Rectangle {
    public getArea() {
        return super.getArea();
    }
}

```

# Dependency Inversion Principle

from wikipedia
> A. High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g. interfaces).
> 
> B. Abstractions should not depend on details. Details (concrete implementations) should depend on abstractions.

DIP这个名字很不好理解，说是传统的设计中高层模块会依赖底层的模块，现在要让高层模块独立于底层模块，所以要inverse dependency。其实说白了就是基于接口、抽象的设计。