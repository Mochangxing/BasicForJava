---
title: 几种实现Singleton的方式
tags: 单例设计模式,Java
---
单例设计模式应该是被谈论最多也是面试中被问得最多的设计模式了。单例设计模式的意义在于：在系统中一个类只有的实例，而且易于被外界访问，从而方便对实例个数进行控制并节约系统资源。
## 只适合单线程的教学版本
```
public class Singleton{
    private static Singleton instance = null;
    private Singleton(){}
    public static Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
} 
```
在以上实现方式中，给出了单例模式的几个基本特点：

 1. 私有构造函数，这样外部就不能实例化这个类了；
 2. 静态的getInstance()成员方法，这样能确保实例可以被创建出来，并且外部可以通过GetInstance()获得实例。
 3. getInstance()中需要判断已创建实例，如果是则直接返回，否则创建在返回。
 4. 保存实例为私有成员。
## 单例模式的实际应用版本
在上诉方式中，如果是在多线程的并发的条件下，将无法保证实例的唯一性。因为如果多个线程同时调用getInstance()的话，就有可能创建出多个实例。
### 实际应用版本之“Double-check”版
```
public class Singleton{
    private static Singleton instance = null;
    private Singleton(){}
    public static Singleton getInstance(){
        if(instance == null){
            sychronized(Singleton.class){
                if(instance == null){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
} 
```
这个版本为双重检查版，其说明如下：
 1. 首先判断实例是否已被创建，如果是则直接返回。
 2. 如果不是，则开始线程同步。
 3. 第二个判断条件是，如果在线程同步的过程中已经有另外的线程创建了实例，则不要再创建实例了，直接返回即可。

虽然说双重检查已经相当不错了，但还是存在隐患，因为`new Singleton();`并不是原子操作。JVM中new一个对象（非匿名对象）实际上分为下面3个步骤：
1. 分配内存
2. 调用构造函数初始化成员变量，形成实例
3. 将对象指向分配的内存空间，只有完成这一步对象才不为null。
但是JVM存在指令重排序的优化，所以以上的步骤的执行顺序有可能是1-2-3,也有可能是1-3-2。因此如果3执行完而2还没执行，这时另外一个线程二进入`if(instance == null)`,由于instance这时为非空但还没初始化，线程而使用instance自然就会报错。
于是双重检查版可以对singleton 声明为volatile，改进为：
```
public class Singleton{
    private volatile static Singleton instance = null;
    private Singleton(){}
    public static Singleton getInstance(){
        if(instance == null){
            sychronized(Singleton.class){
                if(instance == null){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
} 
```
不过遗憾的是，以上的版本只能在jdk1.5以后的版本中才能使用。因为老版本的内存模型是有缺陷的。

### 实际应用版之静态内部类版本
```
public class Singleton{
    private static class SingletonHolder{
        private static final Singleton INSTANCE;
    }
    private Singleton(){}
    public static Singleton getInstance(){
        return SingletonHolder.INSTANCE;
    }
    
}
```
上面这种方式，仍然使用JVM本身机制保证了线程安全问题；由于 SingletonHolder 是私有的，除了 getInstance() 之外没有办法访问它，因此它只有在getInstance()被调用时才会真正创建；同时读取实例的时候不会进行同步，没有性能缺陷；也不依赖 JDK 版本。



### 实际应用不得不用版之枚举实现
```
public enum Singleton{
    INSTANCE;
}
```
默认枚举实例的创建是线程安全的，所以不需要担心线程安全的问题。但是在枚举中的其他任何方法的线程安全由程序员自己负责。还有防止上面的通过反射机制调用私用构造器。






















 
