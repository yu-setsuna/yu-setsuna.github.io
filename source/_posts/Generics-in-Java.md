---
title: Generics in Java
date: 2020-05-16 17:39:04
tags:
    - java
    - generic
categories: java
---

*以下注释内容无法通过编译*
## 关于协变 （Covariant）
- Java 中数组协变的，泛型不是。
- Java 1.5 开始子类覆盖父类的方法时可以返回具体的类型。

<!-- more -->

``` java
class Food {}
class Fruit extends Food {}
class Apple extends Fruit {}

@Test
public void covarianceTest() {
    // 数组支持协变
    Fruit[] fruits = new Apple[]{ new Apple() };

    // 编译错误，泛型不支持协变
    // ArrayList<Fruit> array = new ArrayList<Apple>();

    // 可以使用上界限定通配符
    List<? extends Fruit> list = new ArrayList<Apple>();
}
```

## 上界限定通配符（Upper Bounds Wildcards）
只能传入该类型及其子类的对象
``` java
private static void extendTest(List<? extends Fruit> fruits) {
    // 此时容器不知道 List 的具体类型，（不能确定传入的是Fruit或Apple），因此为避免类型转换出错，也无法再向容器中添加任何类型
    // List<?> 同理

    // fruits.add(new Fruit());
    // fruits.add(new Apple());
    // fruits.add(new Food());
    fruits.add(null);
}
```

## 下界限定通配符（Lower Bounds Wildcards）
只能传入该类型及其父类的对象
``` java
private static void superTest(List<? super Fruit> fruits) {
    // 此时容器已经可以确实传入的类型一定是Fruit或它的父类
    // 因此可以向容器中添加Fruit和它的子类对象，不会发生类型转换错误
    fruits.add(new Apple());
    fruits.add(new Fruit());
    // fruits.add(new Food());
}
```

## 限定符测试
``` java
@Test
public void genericTest() {
    // 只能传入Fruit和它的父类
    superTest(new ArrayList<Food>());
    // superTest(new ArrayList<Apple>());

    // 只能传入Fruit和它的子类
    extendTest(new ArrayList<Apple>());
    // extendTest(new ArrayList<Food>());
}
```

## 递归类型参数（Recursive Type Parameter）

简化的栗子：
``` java
abstract class ClassA<T extends ClassA<T>> {
    abstract T m();
}

class ClassB extends ClassA<ClassB> {
    @Override
    public ClassB m() {
        return new ClassB();
    }
}
```

*Effective Java*中的建造者模式：
``` java
public abstract class Pizza {
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        // Subclasses must override this method to return "this"
        protected abstract T self();
    }
    
    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone(); // See Item 50
    }
}

// Subclass with hierarchical builder (Page 15)
public class NyPizza extends Pizza {
    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        @Override protected Builder self() { return this; }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}
```

使用递归的类型参数和抽象的self方法一样，允许在子类中适当地进行方法链接，不需要转换类型。  

`<T extend builder<T>>`和`<T extend builder>`这两者的区别：
> 递归的 T extends Builder<T>是指，T 只能是 Builder 的子类型。因为 Builder是个泛型类，在描述「某个具体的 Builder 类型」 时，就必须带上类型参数。对于这里来说，就是 T。T extends Builder 严格来说是错误的。
当然，对于 Java 来讲，你不带 T 也是完全没问题的，编译器也不会给你警告，最多让 IDEA 提示一句「Raw use of parameterized class」，毕竟 Java 的泛型编译后都会被擦除，运行是时候都是一样的。不过，带上泛型更有利于编译器检查，也更有利于写出类型安全的代码。

Pizza的构造器参数为`Builder<?> builder`，这里泛型定义为<? extend Builder>不应该更好吗？
> 因为 Builder 声明为 T extends Builder<T> 就保证了 T 一定是 Builder 的子类型， 这里就没有必要再重复限制一次了。

父类的Builder使用递归泛型及使用`self()`是为了 父类方法返回子类 这种功能吗？
> Self 类型是在接口中用描述实现接口的具体类型的特殊类型标识，用来安全地从父类型向子类型转换对象。一般来说，把父类型的对象转换为子类型的对象，都要用强制类型转换。这样做很不优雅，也不「type safe」。不过利用递归的类型参数，可以避免强制类型转换，这也是递归类型参数的重要用法。


## 参考链接
[知乎：Java泛型递归模式的意义](https://www.zhihu.com/question/390135686)