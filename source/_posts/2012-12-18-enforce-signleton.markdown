---
layout: post
title: "使用私有构造方法或枚举类型实现单例"
subtitle: "Effective Java: Enforce the signleton property with a private constructor or an enum type"
date: 2012-12-18 17:37
comments: true
categories: [EffectiveJava, Java] 
---

**单例(singleton)，通俗的讲就是指一个只能实例化一次的类。**单例表示了一个本质上唯一的系统组件，例如窗口管理器或文件系统。  
在java1.5版本之前，实现单例的方法有两种。这两种方法都是在基于私有构造方法和暴露一个公共的静态成员以提供访问该实例的基础上实现的。  
<!-- more -->
在第一种方法中，暴露的成员是一个**final(不能改变)**的成员变量：
{% codeblock Elvis.java lang:java %}
//Singleton with public final field
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public void leaveTheBuilding() { ... }
}
{% endcodeblock %}

私有构造方法只被调用了一次，就是用来初始化成员变量Elvis.INSTANCE的。除非利用反射机制来调用私有构造方法，否则外部客户端代码无法修改该实例。如果你想避免此种情况的发生，可以通过修改构造方法，使其在要求进行第二次实例化的时候抛出异常。  

在第二种方法中，暴露的公共成员是一个静态工厂方法：
{% codeblock Elvis.java lang:java %}
//Singleton with static factory
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }

    public void leaveTheBuilding() { ... }
}
{% endcodeblock %}
每次调用Elvis.getInstance()方法时，都会返回同一个对象的引用，不会创建别的Elvis实例（同样存在上述的通过反射机制修改实例的情况）。  

比较而言，第一种方法的好处在于它的的**声明方式(public static final)**很清晰的体现出了它是一个单例，其公共静态成员变量是不可修改的。而第二种方法的优点则是提供了很大的灵活性，你可以随意修改方法的内部而不会改变客户端的API。例如，调用getInstance()方法的每个线程对应一个单例。  

如果要将通过以上两种方式实现单例的类进行序列化，那么仅仅实现Serializable接口是不够的。为了保证是单例的，需要将所有实例的成员变量声明为transient，并且提供一个readResolve()方法，否则的话，进行反序列化时导致生成一个新的实例。为了防止这种情况发生，以上例子中需要加入以下方法：
{% codeblock Elvis.java lang:java %}
//readResolve method to preserve singleton property
private Object readResolve() {
    //Return the one true Elvis and let the garbage collector take care of the Elvis impersonator.
    return INSTANCE;
}
{% endcodeblock %}

在jdk1.5中提供了第三种方法来实现单例。简单来说，就是创建一个单元素的枚举类型：
{% codeblock Elvis.java lang:java %}
//Enum singleton - the preferred approach
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() { ... }
}
{% endcodeblock %}
这种方法在功能上来说和第一种方法是相同的，但是它很清晰的提供了序列化机制，与此同时，还严格的保证了不会出现多次实例化，甚至于避免了复杂的序列化实现和反射机制带来的潜在的不安全因素。尽管这种方法目前来说没有得到广泛的接受，但是单元素的枚举类型的确是实现单例的最好的方法。
