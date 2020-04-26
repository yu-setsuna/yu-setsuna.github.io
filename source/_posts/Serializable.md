---
title: Serializable
date: 2019-12-02 14:04:24
tags:
    - java
categories: java
---

## 序列化

Java 中使用 Serializable 接口进行序列化。  
Serializable 只是语义接口，无实际方法 


## SerialVersionUID 

Java 反序列化时通过判断字节流和相应实体类的 sesrialVersionUID 验证版本是否一致，一致时才能进行反序列化，否则抛出 InvalidCastException 



### 显式定义 

SerialVersionUID 的两种显式生成方式： 

``` java
// 默认 
private static final long serialVersionUID = 1L;  

// 根据类名、接口名、成员方法、属性生成的 64 位哈希 
private static final  long   serialVersionUID = xxxxL; 
```


### 隐式定义 

当一个类实现 Serializable 接口但没有定义 serialVersionUID 时，Java 序列化机制会根据编译的 Class 自动生成一个 serialVersionUID，此时如文件中没有发生变化，则 serialVersionUID 也不会发生变化 


### 阿里巴巴 Java 开发手册中对 serialVersionUID 的规定 

> 【强制】序列化类新增属性时，请不要修改 serialVersionUID 字段，避免反序列失败；如 果完全不兼容升级，避免反序列化混乱，那么请修改 serialVersionUID 值。 说明：注意 serialVersionUID 不一致会抛出序列化运行时异常。  


## 静态变量 

Java 在进行序列化时只保存对象的状态，不会保存静态变量，但是 serialVersionUID 会被写入到序列化文件中 

``` java
// ObjectStreamClass.java 

private void writeClassDesc(ObjectStreamClass desc, boolean unshared) throws IOException { 

    if (desc == null) { 
        this.writeNull(); 
    } else { 
        int handle; 
        if (!unshared && (handle = this.handles.lookup(desc)) != -1) { 
            this.writeHandle(handle); 
        } else if (desc.isProxy()) { 
            this.writeProxyDesc(desc, unshared); 
        } else { 
            this.writeNonProxyDesc(desc, unshared); 
        } 
    } 
} 

void writeNonProxy(ObjectOutputStream out) throws IOException { 
    out.writeUTF(this.name); 
    out.writeLong(this.getSerialVersionUID()); 
    byte flags = 0; 
    int i; 
    // ... 
} 
```


## transient 关键字 

Java 对象中使用 Serializable 接口进行序列化，类中不需要进行序列化的字段可以使用 trainsient 修饰 