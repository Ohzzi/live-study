### 목표
자바의 열거형에 대해 학습하세요.

### 학습할 것 (필수)
- enum 정의하는 방법
- enum이 제공하는 메소드 (values()와 valueOf())
- java.lang.Enum
- EnumSet

Enum(Enumeration), 우리말로 번역하면 "열거형"이라 불리는 개념은 자바 1.5버전부터 도입되었으며 서로 연관된 상수들을 하나로 모아 대표 이름을 정해 정의한 상수 집합이다. 기존에 상수를 정의하는 방법은 static final을 붙여서 만드는 방법이 있었는데, 모든 상수들을 static final로 정의하자니 이름의 충돌이 발생할 수도 있고, 코드도 복잡해진다. 인터페이스나 클래스로 따로 정의하여 사용하는 방법도 있지만 이 경우 상속, 인터페이스 구현 등으로 인해 상수값의 타입 안정성이 떨어진다. (컴파일시 타입 검사를 하지 않으므로)

enum의 사용은 이러한 단점들을 해결하고 다음과 같은 장점을 가져온다.
- 코드의 간략화(가독성의 증가)
- 인스턴스 생성과 상속의 방지
- 컴파일시 타입 에러를 체크함
- enum 키워드를 이용하였기 때문에 열거형의 의도를 분명히 선언

### enum 정의하는 방법

enum은 클래스처럼 선언하고 사용할 수 있으며, 다른 클래스처럼 별도의 java파일, 클래스 안, 클래스 밖에서 선언이 가능하다.

```java
// Color.java
public enum Color {
    RED, GREEN, BLUE
}

// 클래스 안에서 선언
public class Car {
    public enum Color {
        RED, GREEN, BLUE
    }
    
    ...
}

// 클래스 밖에서 선언
public class Car {
    public Color color;
    
    ...
}

enum Color {
    RED, GREEN, BLUE
}
```

enum안에 객체들을 상수로 사용할 수도 있다.

```java
public enum Car {
    SONATA("Sonata", "white"), MORNING("Morning", "red");

    private final String name;
    private final String color;

    Car(String name, String color) {
        this.name = name;
        this.color = color;
    }
}
```

enum 클래스의 디폴트 생성자는 private으로, public으로 변경하면 컴파일 에러가 발생한다. enum은 클래스가 로드되는 시점에 하나의 인스턴스로 생성되며, 싱글톤(singleton) 형태로 어플리케이션 전체에서 사용된다.

```java
// enum의 사용
public class Car {
    public Color color; 

    public static void main(String[] args) {
        Car myCar = new Car();
        myCar.color = BLACK;
        System.out.println("내 자동차의 색깔: " + myCar.color);
    }

enum Color {
    RED, WHITE, BLACK, GRAY
}

// 출력 결과
> 내 자동차의 색깔: BLACK
```

### enum이 제공하는 메소드

enum을 만들면 기본적으로 후술할 java.lang.Enum 클래스를 상속받기 때문에 java.lang.Enum의 메소드들을 사용할 수 있다.

![](https://images.velog.io/images/ohzzi/post/e8a58c0f-9bb2-4828-a156-7820776d42a3/20210211_205807.png)

이중에서 주로 사용하는 메소드에 대해 알아보자.

#### values()

values() 메소드는 오라클 공식문서의 java.lang.Enum에서 찾아볼 수 없는데, 이 메소드는 컴파일러가 컴파일 시에 모든 enum에 추가해주는 메소드다.

values() 메소드는 해당 enum의 모든 상수를 저장한 배열을 생성하여 반환한다.

```java
enum Color {
    RED, WHITE, BLACK, GRAY
}

public class Test {
    public static void main(String[] args) {
    	Color[] color = Color.values();
        for (int i = 0; i < color.size(); i++) {
            System.out.println(color[i]);
        }
    }

// 출력 결과
> RED
> WHITE
> BLACK
> GRAY
```

#### valueOf()

valueOf() 메소드는 String을 매개변수로 받아 해당 문자열과 일치하는 enum의 상수를 반환한다. 매개변수로 받는 String과 완벽히 일치하는 enum 상수 값이 있어야 값을 반환하며, 그 외의 경우에는 예외를 던진다.

또한 valueOf는 클래스와 String을 모두 매개변수로 받아 사용할 수도 있는데, 매개변수로 받은 클래스에서 arg를 찾아서 enum에서 가져온다. 클래스를 붙이지 않을 경우 enum에서 자체적으로 반환하는 것이다.

```java
enum Color {
    RED, WHITE, BLACK, GRAY
}

public class Test {
    public static void main(String[] args) {
    	Color color = Color.valueOf("GRAY");
        System.out.println(color);
    }

// 출력 결과
> GRAY
```

#### ordinal()

ordinal() 메소드는 해당 enum 상수가 정의된 순서(0부터 시작)를 반환한다. 즉, enum을 enum 상수들의 하나의 배열이라고 생각하면 해당 enum 상수의 인덱스가 되는 것이다. 주의할 점은, 이때 반환되는 값은 enum 상수가 정의된 순서이지, 값 자체가 아니라는 것이다.

그런데, 자바 공식 문서를 보면 다음과 같이 적혀있다.
> Most programmers will have no use for this method. It is designed for use by sophisticated enum-based data structures, such as EnumSet and EnumMap.  
**대부분의 프로그래머들은 이 메소드를 사용하지 않습니다. 이 메소드는 EnumSet이나 EnumMap 같이 enum을 바탕으로 한 자료 구조를 사용할 목적으로 설계되었습니다.**

따라서 EnumSet이나 EnumMap을 사용하지 않는데 이 메소드를 사용하는 것은 올바르지 못한 사용이라고 볼 수 있다.

#### name()

name() 메소드는 enum 상수의 이름을 String으로 반환한다. 자바 공식 문서에 보면, name() 메소드에 대해서도 사용에 대한 조언이 쓰여져 있다.

> Most programmers should use the toString() method in preference to this one, as the toString method may return a more user-friendly name. This method is designed primarily for use in specialized situations where correctness depends on getting the exact name, which will not vary from release to release.  
**대부분의 프로그래머는 toString 메서드가 더 사용자에게 친숙한 반환 할 수 있으므로 우선적으로 toString () 메서드를 사용해야합니다. 이 방법은 주로 정확한 이름을 얻는 데 의존하는 특수한 상황에서 사용하도록 설계되었으며 릴리스 버전마다 다르지 않습니다.**

java.lang.Object 클래스에서 상속받는 toString() 메소드를 우선적으로 사용하는 것이 좋다고 한다.

#### getDeclaringClass

getDeclaringClass() 메소드는 enum 상수의 객체 타입을 반환한다.

### java.lang.Enum

전술했듯이, 모든 enum은 내부적으로 java.lang.enum 클래스를 상속받는다. 때문에 다른 클래스를 상속받을 수 없다. (자바는 다중 상속이 금지되어 있다.) toString()을 제외한 모든 메소드는 final로 선언되어 있어 enum 클래스에서 오버라이딩 할 수 없다.

### EnumSet

EnumSet은 그 이름에서 유추할 수 있듯이, enum 타입으로 구성된 집합이다. EnumSet은 java.util 패키지에 정의되어 있으며, 추상 클래스다. 이름에 Set이 붙는 것에서 알 수 있듯, EnumSet은 Set 클래스의 일반적인 메소드들을 가지고 있다.

EnumSet 클래스는 다음과 같이 사용한다.

```java
public class EnumSetTest {
    enum Day {
        SUN, MON, TUE, WED, THU, FRI, SAT
    }
    
    public static void main(String[] args) {
        EnumSet es = EnumSet.allOf(Day.class);
        EnumSet esCopy = EnumSet.copyOf(es);
        EnumSet es2 = EnumSet.of(Day.SAT, Day.SUN);
        EnumSet es3 = EnumSet.noneOf(Day.class);
        EnumSet es4 = EnumSet.range(Day.MON, Day.FRI);
        
        System.out.println(es);
        System.out.println(esCopy);
        System.out.println(es2);
        System.out.println(es3);
        System.out.println(es4);
    }
}
// 출력 결과
> [SUN, MON, TUE, WED, THU, FRI, SAT] // es
> [SUN, MON, TUE, WED, THU, FRI, SAT] // esCopy
> [SAT, SUN] // es2
> [] // es3
> [MON, TUE, WED, THU, FRI] // es4
```

EnumSet의 메소드로는 allOf(), of(), copyOf(), noneOf(), range() 등이 있다.

allOf() 메소드와 noneOf() 메소드는 enum의 클래스를 인자로 받는데, 해당 enum 타입으로 구성된 EnumSet을 만든다. 이때 allOf()는 enum의 모든 원소를 가진 EnumSet을, noneOf()는 빈 EnumSet을 만든다. of() 메소드는 enum의 요소들을 직접 찾아서 EnumSet에 넣는다. range() 메소드는 시작점과 끝점을 지정하는데, 시작점과 끝점 사이의 모든 enum 원소들을 포함하는 EnumSet을 만든다. (이때, 시작점과 끝점 또한 포함한다.) copyOf() 메소드는 enum이 아닌 EnumSet을 인자로 받는데, 해당 EnumSet을 복사한 EnumSet을 반환한다.

코드를 보면 EnumSet 타입의 인스턴스들을 만들 때, EnumSet의 메소드를 인스턴스 없이 사용하고 있다. EnumSet 클래스는 모든 메소드에 static 키워드가 붙어있다. 따라서 EnumSet의 인스턴스를 생성하지 않고 메소드를 사용할 수 있다.

이펙티브 자바를 보면 EnumSet을 비트 필드(정수 자료형의 각 한 비트씩에 값을 할당해서 0과 1로 포함관계를 Set처럼 나타내는 형태) 대신 사용하라고 한다. 비트 필드는 컴파일 이후에야 값이 할당되며, 그대로 출력되었을 때 무슨 값인지 모른다는 단점이 있다. EnumSet은 이런 단점들을 보완하는 한편, 다른 Set 구현체와도 함께 사용할 수 있으며, 기본적으로 구성 요소가 64개 이하일 경우, 자동적으로 long 비트 필드 하나로 표현하여 비트 필드에 버금가는 성능을 보여준다.

EnumSet과 비트 필드에 대한 자세한 내용은 다음 링크로 대신한다.
> [item36, 비트 필드 대신 EnumSet을 사용하라](https://medium.com/lucky-sonnie/item-36-%EB%B9%84%ED%8A%B8-%ED%95%84%EB%93%9C-%EB%8C%80%EC%8B%A0-enumset%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC-4facbb3f0d57)
