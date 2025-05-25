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
