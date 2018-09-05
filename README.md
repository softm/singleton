# 원본 : https://examples.javacodegeeks.com/core-java/java-singleton-design-pattern-best-practices-examples/
# Singletone

## Singletone Design Pattern?
  - Singleton 패턴은 Creational 디자인 패턴의 일부.
  - Java에서 new키워드는 필요할 때마다 클래스의 인스턴스를 작성합니다.
      그러나 개발자가 다음과 같은 클래스의 독립 인스턴스를 가져야하는 경우도 있습니다.
      - 단일 데이터베이스 연결 개체
      - 단일 공유 리소스
    ![텍스트](http://examples.javacodegeeks.com/wp-content/uploads/2018/07/jcg-singleton_design_pattern_architecture_diag._1.jpeg)

## 전제 조건
  - 정적 멤버 : static클래스 레벨 변수 와 마찬가지로 JVM 메모리에 단일 인스턴스를 생성 합니다.
  - Private Constructor : 외부 세계로부터 Singleton 클래스의 인스턴스 생성을 제한합니다 (즉, new키워드를 사용하여이 클래스의 초기화 가 방지됩니다)
  - 정적 팩토리 메소드 : 이것은 Singleton 객체에 대한 전역 액세스 지점을 제공하고 인스턴스를 호출자에게 반환합니다.

## 구조
  - 단일 인스턴스의 소유권은 변경할 수 없습니다.
  - 게으른 초기화가 권장됩니다.
  - 싱글 톤 인스턴스에 대한 전역 액세스는 별도로 제공되지 않습니다.

## SingletonClassDemo.java
```java
package com.java.design.pattern.singleton;

// Lazy Initialization
public class SingletonClassDemo {

	// Static attribute.
	private static SingletonClassDemo instance = null;

	// Private constructor.
	private SingletonClassDemo() {

	}

	// Static function.
	public static SingletonClassDemo getInstance() {
		// If there is no instance available, create new one (i.e. lazy initialization).
		if (instance == null) {
			instance = new SingletonClassDemo();
		}
		return instance;
	}
}
```

## Eager Initialization
```java
package com.java.design.pattern.singleton;

// Eager Initialization
public class SingletonClassDemo2 {

	// Static attribute.
	// Making the "instance" attribute as "final" ensures that only one instance of the class exists.
	private static final SingletonClassDemo2 instance = new SingletonClassDemo2();

	// Private constructor.
	private SingletonClassDemo2() {

	}

	// Static function.
	public static SingletonClassDemo2 getInstance() {
		return instance;
	}
}
```

  - Static Initialization
```java
// Static block initialization for exception handling.
static {
	try {
		instance = new StaticSingletonClassDemo();
	} catch(Exception ex) {
		throw new RuntimeException("Exception occurred in creating the singleton instance ...!");
	}
}
```

## Bill Pugh Singleton
  - SingletonBillPughDemo.java
```java
package com.java.design.pattern.singleton;

public class SingletonBillPughDemo {

	// Private constructor.
	private SingletonBillPughDemo() {

	}

	// Static inner class.
	private static class Lazyholder {
		// Static attribute.
		private static final SingletonBillPughDemo INSTANCE = new SingletonBillPughDemo();
	}

	// Static function.
	public static SingletonBillPughDemo getInstance() {
		return Lazyholder.INSTANCE;
	}
}
```

## Using Enum
package com.java.design.pattern.singleton;
```java
public enum SingletonEnum {
	INSTANCE;
}
```
// The singleton instance can be accessed via "SingletonEnum.INSTANCE".
   - 이 방법은 쉽지만 두 가지 단점이 있습니다. 즉
        열거 형은 지연 초기화를 지원하지 않습니다.
        열거 형에서 Singleton 클래스를 Multi-ton으로 변경하는 것은 불가능합니다.

## Thread-Safe Singleton
  - SingletonClassDemo3.java
```java
package com.java.design.pattern.singleton;

// Thread-Safe Implementation
public class SingletonClassDemo3 {

	// Static attribute.
	private static SingletonClassDemo3 instance = null;

	// Private constructor.
	private SingletonClassDemo3() {

	}

	// Static function.
	// Only one thread can execute this at a time.
	public static synchronized SingletonClassDemo3 getInstance() {
		// If there is no instance available, create new one (i.e. lazy initialization).
		if (instance == null) {
			instance = new SingletonClassDemo3();
		}
		return instance;
	}
}
```

## Double Check Locking Principle
```java
package com.java.design.pattern.singleton;

// Double Check Locking Principle
public class SingletonClassDemo4 {

	// Static attribute.
	private static SingletonClassDemo4 instance = null;

	// Private constructor.
	private SingletonClassDemo4() {

	}

	// Static function.
	public static SingletonClassDemo4 getInstance() {
		// Double check locking principle.
		// If there is no instance available, create new one (i.e. lazy initialization).
		if (instance == null) {

			// To provide thread-safe implementation.
			synchronized(SingletonClassDemo4.class) {

				// Check again as multiple threads can reach above step.
				if (instance == null) {
					instance = new SingletonClassDemo4();
				}
			}
		}
		return instance;
	}
}
```

## Using Volatile Keyword
```java
package com.java.design.pattern.singleton;

// Double Check Locking Principle
public class SingletonClassDemo5 {

	// Static and Volatile attribute.
	private static volatile SingletonClassDemo5 instance = null;

	// Private constructor.
	private SingletonClassDemo5() {

	}

	// Static function.
	public static SingletonClassDemo5 getInstance() {
		// Double check locking principle.
		// If there is no instance available, create new one (i.e. lazy initialization).
		if (instance == null) {

			// To provide thread-safe implementation.
			synchronized(SingletonClassDemo5.class) {

				// Check again as multiple threads can reach above step.
				if (instance == null) {
					instance = new SingletonClassDemo5();
				}
			}
		}
		return instance;
	}
}
```

##  Ways to Kill Singleton - Reflection
```java
try {
	Constructor[] constructors = Singleton.class.getDeclaredConstructors();
	for (Constructor constructor : constructors) {

		// Below Code Will Destroy the Singleton Pattern
		constructor.setAccessible(true);
		instanceTwo = (Singleton) constructor.newInstance();
		break;
	}
} catch (Exception ex) {
	ex.printStackTrace();
}
```
  - Serialization
```java
// Implementing the 'readResolve()' method.
protected Object readResolve() {
	return getInstance();
}
```
  - Clone
    Clone은 인스턴스 복사본을 생성하여 클래스의 Singleton 디자인을 파괴 할 수있는 개념입니다.
    이 문제를 극복하려면 개발자가 clone()메서드 를 재정의하고 해당 메서드에서 CloneNotSupportedException예외를 throw 해야합니다.
```java
@Override
protected Object clone() throws CloneNotSupportedException {
	throw new CloneNotSupportedException();
}
```  
# 엄지 손가락 규칙
  - 응용 프로그램에서 스레드 안전성을 보장하기 위해 싱글 톤 디자인 패턴에서 이중 잠금 원칙을 구현합니다.
  - 당신의 싱글 톤을 깨뜨릴 수 있으므로 오브젝트 복제를 조심하십시오. 개체의 clone()메서드를 제한하는 것을 기억하십시오.
  - 자바 리플렉션 API에주의하십시오. 다시 싱글 톤을 깨뜨릴 수 있습니다. (instance != null)검사가 true이면 생성자에 런타임 예외를 던집니다.
  - 직렬화에서 싱글 톤 클래스를 안전하게합니다.
  - java.lang.Runtime및 java.awt.DesktopJVM이 설치된 2 개 개의 싱글 클래스들이다
