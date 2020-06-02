---
layout: post
title: 非信任代码交互的同步方法
date: 2020-06-02 20:30
author: admin
comments: true
categories: [java]
tags: [synchronized, java]
---

如果一个类可能会和非信任代码交互，使用 private final lock object  

共享可变变量的同步访问方法有2种：方法同步和代码块同步。方法被申明为synchronized，或者代码块使用this，他们都使用这个Object的内置锁作为监视器。攻击者能够非法长期获得并持有锁，导致Dos.  

解决办法是使用 private final lock object. 它使用了对象this内部申明的field而不是this对象的内置锁。实现方法是 synchronized(obj)而不是 synchronized 方法，攻击者由于访问不到this内部private field，获取不到obj，所以没法造成锁竞争。  

对于静态方法，static synchronized方法也容易被攻击。当静态方法被申明为synchronized，执行时会获得该class对象内置锁。非信任代码可以通过object.getClass()方法获得class对象的内置锁。  
所以对于static field, 需要用private static final Object上锁来维护它  


举几个例子加深理解  
- - -  
1. 方法同步, 非信任代码获取object后无限期锁住它导致changeValue()获取不到锁

```java
public class SomeObject {

    // Locks on the object's monitor
    public synchronized void changeValue() {
        // ...
    }
    
    public static SomeObject lookup(String name) {
        // ...
    }
}

// Untrusted code
String name = // ...
SomeObject someObject = SomeObject.lookup(name);
if (someObject == null) {
    // ... handle error
}
synchronized (someObject) {
    while (true) {
        // Indefinitely lock someObject
        Thread.sleep(0x7FFFFFFF);
    }
}
```

2. Public Non-final Lock Object, 非信任代码可以修改lock指向，比如Integer.valueOf(1),利用IntegerCache搞成全局锁

```java
public class SomeObject {
  public Object lock = new Object();
 
  public void changeValue() {
    synchronized (lock) {
      // ...
    }
  }
}
```

3. Private Final Lock Object, **正确合规**, lock私有且不可改变，多线程调用this.changeValue方法井然有序

```java
public class SomeObject {
  private final Object lock = new Object(); // private final lock object
 
  public void changeValue() {
    synchronized (lock) { // Locks on the private Object
      // ...
    }
  }
}
```

4. 静态不合规同步方法，非信任代码可以获取类型并无限期加锁，导致changeValue()获取不到锁

```java
public class SomeObject {
  //changeValue locks on the class object's monitor
  public static synchronized void changeValue() {
    // ...
  }
}
 
// Untrusted code
synchronized (SomeObject.class) {
  while (true) {
    Thread.sleep(Integer.MAX_VALUE); // Indefinitely delay someObject
  }
}
```

5. private static final Object， **正确合规** , SomeObject.lock静态私有且不可改变，多线程调用 SomeObject.changeValue方法井然有序

```java
public class SomeObject {
  private static final Object lock = new Object();
 
  public static void changeValue() {
    synchronized (lock) { // Locks on the private Object
      // ...
    }
  }
}
```
