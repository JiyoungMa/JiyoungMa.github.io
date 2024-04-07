---
layout: post
title:  "1. 싱글톤(Singleton) 패턴"
date:   2024-04-07 14:30:00 +0900
categories: Design_Pattern
---

# 1. 싱글톤(Singleton) 패턴

> **😉 :** '코딩으로 학습하는 GoF의 디자인 패턴' 공부 기록입니다.

<h3> 싱글톤 패턴이란? </h3>
: 인스턴스를 오직 한개만 제공하는 클래스 <br>
- 왜 필요할까? : 인스턴스가 여러개일 때 문제가 생길 수 있는 경우가 있다 <br>
ex) 애플리케이션에서 전역적으로 상태를 관리할 때, 데이터베이스 연결 등 자원을 공유해야 하는 경우 <br>

<h5> 1. 가장 간단한 싱글톤 패턴 </h5>

```java
public class Status{
    private static Status singleton;

    private Status() {}

    public static Status getInstance(){
        if (singleton == null){
            singleton = new Status();
        }

        return singleton;
    }
}
```

생성자를 private으로 설정해서 외부에서 생성자를 사용하지 못하도록 한다.  
싱글톤 객체는 내부에서 private으로 설정하여 외부에서 변경 및 직접 접근하지 못하도록 한다.  
외부에서 해당 클래스의 인스턴스는 오직 getInstance()를 통해서만 받도록 한다.<br>
하지만 해당 방식은 멀티 쓰레드 환경에서 문제가 발생할 수 있다.  
: 두 쓰레드가 간발의 차이로 if (singleton == null) 임을 확인하고 if문 안으로 들어왔을 때, 둘 다 singleton 객체를 설정하려고 할 수 있다.
<br>

<h5>2. Thread-safe 한 싱글톤 패턴 </h5>

<h6>Synchronized 사용하기</h6>

```java
public class Status{
    private static Status singleton;

    private Status() {}

    public static synchronized Status getInstance(){
        if (singleton == null){
            singleton = new Status();
        }

        return singleton;
    }
}
```
synchronized 키워드를 통해 thread-safe하게 만들 수 있다.  
다만 성능 저하가 발생할 수 있다.
<br>


<h6>이른 초기화 (eager initialization)</h6>

```java
public class Status{
    private static Status singleton = new Status();

    private Status() {}

    public static Status getInstance(){
        return singleton;
    }
}
```
synchronized를 사용하는 것 보다 성능을 신경쓰고 싶고, 객체 만드는 비용이 크지 않다면 아예 생성하고 시작하자.
<br>

<h6>Double checked locking</h6>

```java
public class Status{
    private static volatile Status singleton;

    private Status() {}

    public static Status getInstance(){
        if (singleton == null){
            synchronized (Status.class){
                if(status == null){
                    singleton = new Status();
                }
            }
        }

        return singleton;
    }
}
```
매번 synchronized가 걸리지 않아 성능이 향상되고 필요한 시점에 인스턴스 생성이 가능하다.  
주의할 점은 인스턴스에 volatile이 필요하다. 
<br>

<h6>Static Inner 클래스 사용하기</h6>

```java
public class Status{
    private Status() {}

    private static class StatusHolder{
        private static final Status INSTANCE = new Status();
    }

    public static Status getInstance(){
        return StatusHolder.INSTANCE;
    }
}
```
권장하는 방법 중 하나로, getInstance가 처음 호출 될 때 INSTANCE도 처음 생성되어 lazy loading도 가능하다.
<br>

> 하지만 지금까지 살펴본 모든 방법들은 리플렉션, 직렬화 역직렬화에 의해 깨질 수 있다.  
즉 사용하는 쪽에 의해서 싱글톤이 깨질 수 있다.

<h6>Enum 사용하기</h6>

```java
public enum Status {
    INSTANCE;
}
```
리플렉션, 직렬화 역직렬화에 모두 안정적이다.  
단점은 클래스를 로딩하는 순간 이미 만들어진다는 점과 상속이 불가능하다는 점이 있다.