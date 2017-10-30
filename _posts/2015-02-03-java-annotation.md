---
layout: post
title: "Java Annotations"
description: ""
category: Java
tags: []
---

Annotation Type Inject

@Target(value={METHOD, CONSTRUCTOR, FIELD})
@Retention(value=RUNTIME)
@Documented
public @interface Inject

标识可注入的构建器，方法，和字段。可应用到静态的也可以是实例成员。

注入顺序：

* 构建器首先被注入，接下来是字段，然后是方法。
* 超类中的字段和方法被注入，然后子类的被注入
* 不同的字段和不同的方法的注入顺序是未被指定的。

可注入的构建器用@Inject注释，并且接受0个或多个依赖作为参数。每个类最多可应用一个构建器注入。

可注入的字段：

用@Inject注释
非final的

@Inject Modifiers(opt) Type VariableDeclarators;


可注入的方法：
用@Inject注释
非抽象的
不声明自己的类型参数
可以返回结果
可以由其他的有效的名字
接受零个和多个依赖作为参数

public class Car{
	
	// Injectable constructor
	@Inject public Car(Engine engine) { ... }

	// Injectable field
	@Inject private Provider<Seat> searProvider;

	// Injectable package-private method
	@Inject void install(Windshield winshield, Truck truck) { ... }
}

用@Inject注释的方法被重写后，并且有@Inject注释，这个方法只会注入一次per injection request per instance。用@Inject注释的方法，被重写后没有@Inject注释，那么这个方法将不会被注入。

Circular Dependencies (环路依赖)
class A {
	@Inject B b;
}

class B {
	@Inject A a;
}


JAVA ANNOTATIONS

注释有多种用法：
* Information for the compiler - 编译器可以通过annotations侦测错误和压制警告
* Compile-time and deployment-time processing - 工具可以通过annotations产生代码，XML文件等等。
* Runtime processing - 一些annotations可以在运行时被检测

the format of an Annotation (注释的格式)

@Entity
@Override
@Author(
	name = "Bill"
	data = "03/27/2003"
)
class MyClass() { ... }

@SuppressWarnings(value = "unchecked")
void myMethod() { ... }

如果只有一个对象名字叫做value, 那么对象名字可以被忽略：
@SuppressWarning("unchecked")
void myMethod() { ... }

在同一个声明上可以使用多个注释(annotation)
@Author(name = "Bill")
@EBook
class MyClass { ... }

如果多个注释有相同的类型，那么这叫做a repeating annotation:
@Author(name = "Bill")
@Author(name = "John Smith")
class MyClass { ... }

Repeating annotations are supported as of the Java SE 8 release. For more information, see Repeating Annotations.
`重复注释`从JAVA SE 8开始支持

annotation类型是定义在java.lang或java.lang.annotation包中的类型之一。
Override或者SuppressWarnings是预定义的Java注释。


在哪里用注释

1.	声明注释

2.	类型注释(type annotation)  从Java SE 8，注释可以用来注释types

类型实例创建
	new @Interned MyObject();

类型转换
	myString = (@NonNull String) str;

implements句法
	class UnmodifiableList<T> implements
		@Readonly List<@Readonly T> { ... }

抛出异常声明
	void monitorTemperature() throws
		@Critical TemperatureException { ... }


声明注释类型

