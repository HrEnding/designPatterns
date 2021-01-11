# 设计模式

[https://www.processon.com/view/link/5ffc59397d9c080e58bb2912]: 



## 一、创建型模式

### I、单例模式

单例模式，顾名思义就是只有一个实例，并且自己负责创建自己的对象，这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。下面我们来看下有哪几种实现方式吧。

1、懒汉式

```java
//懒汉式单例类.在第一次调用的时候实例化自己   
public class Singleton {  
	//私有构造
    private Singleton() {}  
    private static Singleton single=null;  
    //静态工厂方法   
    public static Singleton getInstance() {  
         if (single == null) {    
             single = new Singleton();  
         }    
        return single;  
    }  
} 
```

懒汉式，顾名思义就是实例在用到的时候才去创建，“比较懒”，用的时候才去检查有没有实例，如果有则返回，没有则新建。有线程安全和线程不安全两种写法，区别就是synchronized关键字。

2、饿汉式

```java
//饿汉式单例类.在类初始化时，已经自行实例化   
public class Singleton {  
    private Singleton() {}  
    private static final Singleton single = new Singleton();  
    //静态工厂方法   
    public static Singleton getInstance() {  
        return single;  
    }  
}
```

饿汉式，从名字上也很好理解，就是“比较勤”，实例在初始化的时候就已经建好了，不管你有没有用到，都先建好了再说。好处是没有线程安全的问题，坏处是浪费内存空间。

3、双检锁

```java
public static Singleton getInstance() {  
    if (singleton == null) { 
        //实例化时，使用synchronized锁
        synchronized (Singleton.class) {    
            if (singleton == null) {    
                singleton = new Singleton();   
            }    
        }    
    }    
    return singleton;   
} 
```

双检锁，又叫双重校验锁，综合了懒汉式和饿汉式两者的优缺点整合而成。看上面代码实现中，特点是在synchronized关键字内外都加了一层 if 条件判断，这样既保证了线程安全，又比直接上锁提高了执行效率，还节省了内存空间。

4、静态内部类

```java
public class Singleton { 
    //静态内部类
    private static class LazyHolder {    
       private static final Singleton INSTANCE = new Singleton();    
    }    
    private Singleton (){}    
    public static final Singleton getInstance() {    
       return LazyHolder.INSTANCE;    
    }    
} 
```

静态内部类的方式效果类似双检锁，但实现更简单。但这种方式只适用于静态域的情况，双检锁方式可在实例域需要延迟初始化时使用。

### II、原型模式

当系统中需要大量创建相同或者相似的对象时，就可以通过“原型设计模式”来实现。原型模式是“创建型设计模式”中的一种。

原型模式的核心思想是，通过拷贝指定的“原型实例（对象）”，创建跟该对象一样的新对象。简单理解就是“克隆指定对象”。

这里提到的“原型实例（对象）”，就是被克隆的对象，它的作用就是指定要创建的对象种类。

需要拷贝的原型类必须实现"java.lang.Cloneable"接口，然后重写Object类中的clone方法，从而才可以实现类的拷贝。

Cloneable是一个“标记接口”，所谓的标记接口就是该接口中没有任何内容。标记接口的作用就是为了给所有实现了该接口的类赋予一种特殊的标志。

```java
//Cloneable接口源码
public interface Cloneable{
}
```

只有当一个类实现了Cloneable接口后，该类才会被赋予调用重写自Object类的clone方法得权利。否则会抛出“CloneNotSupportedException”异常。

```java
//clone方法的源码
protected native Object clone() throws CloneNotSupportedException;
```

原型模式中的拷贝对象可以分为：“浅拷贝”和“深拷贝”。

1、浅拷贝

当类的成员变量是基本数据类型时，浅拷贝会复制该属性的值赋值给新对象。当成员变量是引用数据类型时，浅拷贝复制的是引用数据类型的地址值。这种情况下，当拷贝出的某一个类修改了引用数据类型的成员变量后，会导致所有拷贝出的类都发生改变。

【代码案例】

eg：一个人，名叫“小菜鸟”，职业是“快递员”。现在因快递数量剧增，导致一个人无法按时完成任务，从而需要克隆出来属性与其一模一样的优秀快递员。

```java
/**
 * Person类
 */
public class Person implements Cloneable{
    //姓名
    private String name;
    //职业
    private String occupation;
    //get()和set()方法
    ...
    //重写Object类的clone方法
    @Override
    protected Object clone(){
        Person person = null;
        try{
            //调用父类方法，完成浅拷贝
            person = (Person)super.clone();
        } catch(CloneNotSupportedException e){
            e.printStackTrace();
        }
        return person;
    }
}
```

```java
public class PersonTest(){
    public static void main(String[] args){
        Person prototypePerson = new Person("小菜鸟","快递员");
        Person clonePerson = (Person)prototypePerson.clone();
        System.out.println("prototypePerson:"+prototypePerson);
        System.out.println("clonePerson:"+clonePerson);
    }
}
```

