---
title: Some tags in maven
date: 2019-11-19 19:15:30
tags: 
    - java
    - maven
categories: java
---


## Maven标签`<scope/>`的作用 

### compile  
默认值，即项目的整个编译、测试、运行阶段

### provide  
只作用于编译阶段、测试阶段，运行时使用容器或JDK中的jar包，例如，当使用Java EE构建一个web应用时，你会设置对Servlet API和相关的Java EE APIs的依赖范围为provided，因为web容器提供了运行时的依赖。 

### runtime  
不作用在编译时，但会作用在运行和测试时 

### test  
表明使用此依赖范围的依赖，只在编译测试代码和运行测试的时候需要，应用的正常运行不需要此类依赖。 

### import  
只可在`<dependcyManagent/>`中使用，表示从其它的pom中导入dependency的配置  
> This scope is only supported on a dependency of type pom in the `<dependencyManagement/>` section. It indicates the dependency to be replaced with the effective list of dependencies in the specified POM's `<dependencyManagement/>` section. Since they are replaced, dependencies with a scope of import do not actually participate in limiting the transitivity of a dependency. 
> [https://maven.apache.org](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)  

<!-- more -->

## Maven标签`<dependencies/>`和`<dependencyManagement/>`的区别 

Maven使用`<dependencyManagement/>`提供管理依赖版本的方式，通常用于pom顶层。 
使用`<dependencyManagement/>` 元素能让子项目中引用一个依赖而不用显式的列出版本号。 

Maven 会向上找到一个拥有`<dependencyManagement/>` 元素的项目配置版本;
`<dependencyManagement/>`里只声明依赖，但需要在子项目中进行引入。


## 阿里巴巴 Java 开发手册中对 `<dependencies/>` 的规定 
> 【推荐】所有 pom 文件中的依赖声明放在语句块中，所有版本仲裁放在 语句块中。 说明：里只是声明版本，并不实现引入，因此子项目需要显式的声 明依赖，version 和 scope 都读取自父 pom。而所有声明在主 pom 的 里的依赖都会自动引入，并默认被所有的子项目继承。  