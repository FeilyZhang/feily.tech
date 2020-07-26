---
title: "Design of seven kinds of singleton patterns"
date: 2019-04-20T15:07:23+08:00
categories: ["Java"]
tags: ["Concurrent"]
draft: false
---

单例模式提供了一种在多线程情况下保证实例唯一性的解决方案，单例模式设计的标准是：懒加载、高性能、线程安全。

## 一、饿汉式

```java
// final modifier, no inheritance allowed.
public final class Singleton {

    // Some business code.
    
    private static Singleton instance = new Singleton();
    
    // Private constructor, no external `new` is allowed.
    private Singleton() {
        
    }
    
    public static Singleton getInstance() {
        return instance;
    }
    
}
```

测试如下，我们启动10个线程测试其获得的线程实例

```java
public class SingletonTest {

    public static void main(String[] args) {
        
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " "
                    + Singleton.getInstance().toString());
            }, "thread-" + i).start();
        }
        
    }

}
```

输出为

```java
thread-0 tech.feily.doc.thread.Singleton@df7eb94
thread-4 tech.feily.doc.thread.Singleton@df7eb94
thread-3 tech.feily.doc.thread.Singleton@df7eb94
thread-2 tech.feily.doc.thread.Singleton@df7eb94
thread-7 tech.feily.doc.thread.Singleton@df7eb94
thread-8 tech.feily.doc.thread.Singleton@df7eb94
thread-1 tech.feily.doc.thread.Singleton@df7eb94
thread-9 tech.feily.doc.thread.Singleton@df7eb94
thread-6 tech.feily.doc.thread.Singleton@df7eb94
thread-5 tech.feily.doc.thread.Singleton@df7eb94
```

可见，均为相同的实例。懒汉式的关键在于`instance`作为类变量并且直接得到了初始化，即如果主动使用(使用类变量属于主动使用，会导致类的加载并初始化)Singleton类，那么instance实例将会直接完成创建并初始化。具体而言，instance作为类变量在类初始化的过程中被收集进`<clinit>()`方法中，该方法能够百分百保证同步，即instance在多线程环境下不可能被实例化两次，但是instance被ClassLoader加载后可能很长一段时间才被使用，那么就意味着instance实例所开辟的堆内存会驻留更久的时间。

综上，这种办法的优点在于保证了多个线程下的唯一实例，在一个类中的成员属性比较少且占用的内存资源不多的情况下性能还可以，但是无法进行懒加载(即类实例在需要时再创建)。

## 二、懒汉式

懒汉式就是在使用类实例的时候再去创建，这样就避免了类在初始化时提前创建，代码如下

```java
// final modifier, no inheritance allowed.
public final class Singleton {

    // Some business code.
    
    private static Singleton instance = null;
    
    // Private constructor, no external `new` is allowed.
    private Singleton() {
        
    }
    
    public static Singleton getInstance() {
        if (null == instance) {
            instance = new Singleton();
        }
        return instance;
    }
    
}
```

这样，`instance`在初始化阶段由于为`null`，那么就不会占用内存空间，在使用时即调用`getInstance()`方法后才会被实例化。

我们继续启动10个线程测试是否可以保证多线程环境下的实例唯一性，测试代码同上，输出为

```java
thread-2 tech.feily.doc.thread.Singleton@59e410f2
thread-5 tech.feily.doc.thread.Singleton@38f983ba
thread-0 tech.feily.doc.thread.Singleton@38f983ba
thread-4 tech.feily.doc.thread.Singleton@38f983ba
thread-1 tech.feily.doc.thread.Singleton@38f983ba
thread-3 tech.feily.doc.thread.Singleton@59e410f2
thread-8 tech.feily.doc.thread.Singleton@38f983ba
thread-7 tech.feily.doc.thread.Singleton@38f983ba
thread-6 tech.feily.doc.thread.Singleton@38f983ba
thread-9 tech.feily.doc.thread.Singleton@38f983ba
```

可见，并没有保证实例唯一性，此处instance可以看作是多线程的共享变量，即多线程对instance进行操作，那么就有可能某线程在判断了`instance == null`之后CPU时间片到而进入就绪状态，待重新进入运行态时就执行了`new`操作，所以线程不安全，无法保证多线程环境下的实例唯一性。

解决的办法就是加锁，即给共享变量加锁。

## 三、懒汉式 + 同步方法

通过给懒汉式`getInstance()`方法进行同步，那么就会实现线程安全即多线程环境下实例的唯一性，如下

```java
// final modifier, no inheritance allowed.
public final class Singleton {

    // Some business code.
    
    private static Singleton instance = null;
    
    // Private constructor, no external `new` is allowed.
    private Singleton() {
        
    }
    
    public static synchronized Singleton getInstance() {
        if (null == instance) {
            instance = new Singleton();
        }
        return instance;
    }
    
}
```

同样的测试代码，输出为

```java
thread-3 tech.feily.doc.thread.Singleton@5dd54814
thread-2 tech.feily.doc.thread.Singleton@5dd54814
thread-0 tech.feily.doc.thread.Singleton@5dd54814
thread-6 tech.feily.doc.thread.Singleton@5dd54814
thread-5 tech.feily.doc.thread.Singleton@5dd54814
thread-9 tech.feily.doc.thread.Singleton@5dd54814
thread-1 tech.feily.doc.thread.Singleton@5dd54814
thread-4 tech.feily.doc.thread.Singleton@5dd54814
thread-7 tech.feily.doc.thread.Singleton@5dd54814
thread-8 tech.feily.doc.thread.Singleton@5dd54814
```

没毛病了。但是`synchronized`关键字又导致了`getInstance()`方法只能在同一时刻被一个线程访问，性能低下。

## 四、Double-Check

顾名思义，这种办法就是**检查两次**，作为解决**懒汉式 + 同步方法**方法的性能低下的缺陷，如下

```java
// final modifier, no inheritance allowed.
public final class Singleton {

    // Some business code.
    
    private static Singleton instance = null;
    
	Connection conn;
	Socket socket;
	
    // Private constructor, no external `new` is allowed.
    private Singleton() {
	    this.conn;
		this.socket;
    }
    
    public static Singleton getInstance() {
        if (null == instance) {
            synchronized (Singleton.class) {
                if (null == instance) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
    
}
```

这种办法很巧妙的解决了**懒汉式 + 同步方法**方法的性能低下的缺陷，不仅有懒汉式的懒加载，还有了同步方法带来的实例的唯一性，而且通过缩小锁的粒度提供了高效的数据同步策略，可以允许多个线程同时对getInstance()访问，但是现在的问题是，这种方式在多线程情况下可能会引起空指针异常。

在Singleton的构造函数中，需要分别实例化conn和socket两个资源，还有Singtelon自身，根据JVM运行时指令重排序和Happens-Before规则，这三者之间的实例化顺序并无前后约束关系，那么极有可能是instance最先被实例化，而conn和socket并未完成实例化。以两个线程为例，第一个线程进入了同步代码块，先创建了instance实例，但是还未来得及创建conn和socket实例便进入就绪状态，线程二判断到instance已经创建便去使用conn和socket，但是实际上并未创建，这样就造成了NPE。

## 五、Volatile + Double-Check

作为对Double-Check的NPE缺陷的改进，那么使用Volatile关键字修饰instance就可以避免这种重排序的发声，即

```java
private volatile static Singleton instance = null;
```

这样就满足了多线程下的单例、懒加载和获取实例的高效性需求。

## 六、Holder方式

Holder方式完全借助了类加载的特点，如下

```java
// final modifier, no inheritance allowed.
public final class Singleton {

    // Some business code.
    
    // Private constructor, no external `new` is allowed.
    private Singleton() {
    }
    
    private static class Holder {
        private static Singleton instance = new Singleton();
    }
    
    public static Singleton getInstance() {
        return Holder.instance;
    }
    
}
```

在Singleton类中并没有instance静态成员，而是将其放到了静态内部类Holder中，因此在Singleton类的初始化过程中并不会创建Singleton的实例，Holder类中定义了Singleton的静态变量，并且直接进行了实例化，当Holder被主动引用时会创建Singleton实例，Singleton实例的创建过程在Java程序编译时期收集至`<clinit>()`方法中，该方法又是同步方法，同步方法可以保证内存的可见性、JVM指令的顺序性和原子性，所以该模式应用较广。