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
Java 中数组可以声明为父类型，但列表不可以。  
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
``` java
private static void extendTest(List<? extends Fruit> fruits) {
    // 此时容器不知道 List 的具体类型，无法添加任何类型
    // List<?> 同理

    // fruits.add(new Fruit());
    // fruits.add(new Apple());
    // fruits.add(new Food());
    fruits.add(null);
}
```

## 下界限定通配符（Lower Bounds Wildcards）
``` java
private static void superTest(List<? super Fruit> fruits) {
    fruits.add(new Apple());
    fruits.add(new Fruit());
    // fruits.add(new Food());
}
```

## 限定符测试
``` java
@Test
public void genericTest() {
    superTest(new ArrayList<Food>());
    // superTest(new ArrayList<Apple>());

    extendTest(new ArrayList<Apple>());
    // extendTest(new ArrayList<Food>());
}
```

## 递归类型参数（Recursive Type Parameter）
在父类中并不知道子类的类型，使用递归的类型参数可以在子类动态返回自己的类型而不需要进行类型转换。
``` java
abstract class ClassA<T extends ClassA<T>> {
    abstract T add();
}

class ClassB extends ClassA<ClassB> {
    @Override
    public ClassB add() {
        return new ClassB();
    }
}
```

## 源码
[Github](https://github.com/yu-setsuna/effective-java-3e-source-code/blob/master/src/test/java/effectivejava/chapter2/item2/hierarchicalbuilder/PizzaTest.java)