---
layout: post
title:  "3.Enforce the singleton property with a private constructor or an enum type"
date:   2024-09-18 01:09:00 +0900
categories: EffectiveJava
---

A Singleton is a class that is instantiated only once, such as a system component that should be unique. One downside of the Singleton is that it can be difficult to test, as creating mock instances of a Singleton class is challenging.

There are three ways to implement a Singleton, and I’d like to introduce two of them together, as they share many similarities.

<br>

Both ways are based on using a private constructor and a public static member.

- Private constructor and Static final field

```java
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
}
```

The private constructor is called only once when the INSTANCE is initialized, ensuring that no other instances can be created. Since there is no public or protected constructor, only a single instance of the class exists

<br>

- Private constructor and Public Static Factory Method

```java
public class Elvis {
	private static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
	public static Elvis getInstance() { return INSTANCE; }
}
```

Elvis.getInstance always returns same instance.

<br>

Each approach has its own advantages.

One benefit of the first method is that it clearly indicates the class is a Singleton, and it is simple.

The second method has the advantage of being flexible—it’s easy to modify the class later to be no longer Singleton. Additionally, a static factory can be modified as a generic Singleton factory. Another key advantage is that the static factory method can be used as a supplier. For example, `Elvis::getInstance` can be used as `Supplier<Elvis>`.

(Supplier is a functional interface in Java that takes no input parameters and returns a value.)

<br>

Both approaches share the same potential issues.

The first issue is reflection. Using `DeclaredConstructor`, even a private constructor can be accessed and invoked. To prevent this, you can modify the constructor to throw an exception if a second instance is attempted.

The second issue arises with serialization. Without a proper `readResolve` method, a new instance can be created during the serialization and deserialization process. To avoid this, you need to implement the `readResolve` method and ensure that all fields are declared `transient`.

```java
private Object readResolve(){
	return INSTNACE;
}
```

<br>

The final way to implement a Singleton is by using an `enum` class.

```java
public enum Elvis{
	INSTANCE;
}
```

This approach is simple and effectively avoids both the reflection and serialization problems. Except in cases where the Singleton class needs to extend another class, this is generally considered the best way to implement a Singleton.