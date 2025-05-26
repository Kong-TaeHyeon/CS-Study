## 디자인 패턴

### 디자인 패턴이란?

특정 상황에서 공통적으로 발생하는 문제에 대해 재사용 가능한 해결책이다. 소스나 기계 코드로 바로 전환될 수 있는 완성된 디자인은 아니며, 다른 상황에 맞게 사용될 수 있는 **문제들을 해결하는데에 쓰이는 템플릿**이다.(출처 : 위키백과)

### 싱글톤 패턴

```java
class Singleton {
    private static Singleton singletonObject;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (singletonObject == null) singletonObject = new Singleton();

        return singletonObject;
    }
}

public class Main {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();

        Singleton instance2 = Singleton.getInstance();

        System.out.println(instance1 == instance2); // true.
    }
}
```

특정 클래스의 **인스턴스를 하나만 생성하여 사용**하는 패턴입니다. 이는 Java/Spring 에서 수 없이 사용되는 패턴이기도 합니다.

#### 싱글톤을 사용하는 이유

앞서 말한것처럼 하나만 생성하여 재사용하는 패턴은 **'생성'의 비용이 비싸기 때문**입니다.

한 예로, DB 커넥션 풀(Connection Poll)이 있습니다. DB 커넥션 풀이란, 어플리케이션과 데이터베이스 사이의 **커넥션을 미리 여러 개 만들어 놓고**, 필요할 때 사용하기 다시 반납하는 시스템입니다.

즉, 이러한 커넥션을 관리하는 커넥션 풀이 싱글톤이 아니라면 매번 커넥션 풀을 생성함과 동시에 DB와 어플리케이션 사이의 커넥셔닝 여러개 만들어지고, **만들어진 커넥션을 재사용할 수 없게 될 것**입니다.

#### 싱글톤 패턴의 단점

1. 의존성

싱글톤 패턴의 경우, 클래스의 인스턴스를 미리 생성해두기 때문에, 해당 인스턴스가 변경되는 경우 이를 참조하고 있는 모든 클래스들에게 영향이 미칠 수 있게 됩니다.

- 이러한 문제를 스프링에서는 **의존성 주입(Dependency Injection)**을 통해 결합도를 낮추게 됩니다.
- 스프링은 객체를 직접 생성하지 않고, 이를 스프링 컨테이너가 생성한 빈을 주입해주어 의존성을 외부로 분리할 수 있습니다.

```java
public class Singleton implements AnotherInterface {
    // ...
}

public class User {
    private AnotherInterface a;
}
```

- 이처럼 스프링에서는 인터페이스에 의존하는 형태로 변경할 수 있게 됩니다.

2. 자원 공유의 문제

만약, 싱글톤 인스턴스의 자원이 특정 기능으로 인해 계속 변하는 문제가 생긴다면, 이는 해당 인스턴스를 사용하는 다른 클래스들에게도 영향이 갈 수 있게 됩니다. 이는 테스트 환경에서도 매번 초기화를 진행주어야 하는 문제로 이어질 수 있습니다.

### 팩토리 패턴

객체를 사용하는 코드에서 **객체 생성 부분을 떼어내 추상화한 패턴**입니다. 이를 통해 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정하는 패턴입니다.

#### 팩토리 패턴의 장점

객체 생성 코드를 직접 작성하여 사용하지 않고, 공통 인터페이스 또는 추상 클래스 타입을 반환하는 메서드에 위임하게 되어, **객체 종류가 변경되더라도 클라이언트 코드를 수정할 필요가 없게** 됩니다.

```java
public interface Button {
    void click();
}

public class OneButton implements Button {
    // 구현부
}

public class TwoButton implements Button {
    // 구현부
}

public class ButtonFactory {
    public static Button createButton(String button) {
        if (button.equals("OneButton")) {
            return new OneButton();
        }

        else if (button.equals("TwoButton")) {
            return new TwoButton();
        }
    }
}
```

- 스프링의 Bean 등록 방식은 내부적으로 객체 생성의 책임을 스프링 컨테이너에게 맡기는 구조와 동일합니다.

#### 팩토리 패턴의 단점

1. 클래스 수 증가.
   팩토리 패턴을 사용하면, 구현 클래스뿐만 아니라 팩토리 클래스도 추가로 작성하게 됩니다.

2. 코드 추적의 어려움
   객체 생성이 팩토리 내부에 숨겨져 있으므로, 어떤 클래스가 실제로 생성되고 있는지 직관적으로 파악하기 어렵습니다.

### 전략 패턴

객체의 행위를 바꾸고 싶은 경우 '직접' 수정하지 않고 '캡슐화한 알고리즘'을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴.

```java
public interface SortStrategy {
    void sort(List<Integer> list);
}

public class AscendingSort implements SortStrategy {
    // 구현..
}

public class DescendingSort implements SortStrategy {
    // 구현..
}

public class SortContext {
    private SortStrategy strategy;

    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }

    public void run(List<Integer> list) {
        strategy.sort(list);
    }
}
```

이러한 패턴을 통해 **동적으로** 오름차순/내림차순으로 정렬 기능을 제공할 수 있게 됩니다.

#### 전략 패턴의 장점

- OCP 원칙 준수 : 새로운 전략 추가 시, 기존 코드를 수정하지 않습니다.
- 유연성 향상 : 런타임에 사용하고자 하는 전략을 교체할 수 있습니다.

#### 전략 패턴의 단점

- 클래스 증가 : 전략 수만큼 클래스가 많아지게 됩니다.
- 클라이언트가 전략을 사전에 알고 있어야, 어떤 전략을 사용할지 알 지 선택할 수 있습니다.

### 프록시 패턴

실제 객체에 대한 접근을 제어하거나, 부가 기능을 삽입하기 위해 대리 객체를 두고 요청을 대신 처리하는 패턴.

```java
public interface Service {
    void request();
}

public class RealService implements Service {
    public void request() {
        // ...
    }
}

public class ProxyService implements Service {
    private RealService realService;

    public void request() {
        // 로깅 작업.

        // 권한 확인 작업.

        realService.request();

        // 로깅 작업..
    }
}
```

- 스프링은 프록시 객체를 만들어 핵심 로직 앞뒤에 부가 기능(로깅, 트랜잭션 등)을 삽입합니다.

#### 프록시 패턴의 장점

비즈니스 로직과 공통 처리 로직을 분리하여, 공통 처리 로직을 재사용할 수 있게 됩니다.

#### 프록시 패턴의 단점

- 프록시에서 부가 처리를 하다보면 실제 작업이 지연될 가능성이 존재합니다.
- 실제 객체 외의 프록시 객체가 추가되므로 구조가 복잡해질 수 있습니다.
