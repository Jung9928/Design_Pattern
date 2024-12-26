# Singleton 패턴

###  :question: 개념
- 클래스의 인스턴스를 오직 하나만 생성하고, 그 인스턴스에 접근할 수 있는 전역적인 접근점을 제공하는 디자인 패턴.
- 객체의 상태가 전역적으로 공유되어야 할 때 유용.
- 싱글톤 패턴을 사용하면 어플리케이션 전반에 걸쳐 하나의 인스턴스를 공유하며, 메모리 leak 방지 & 여러 객체가 같은 자원에 접근할 시, 일관성 유지 가능.

<br>

### :star2: 장점
- 매번 인스턴스를 생성할 때마다 heap공간을 할당받을 필요가 없이 기존에 생성된 인스턴스를 사용하므로 메모리 낭비 방지
- 최초 생성 이후, new 키워드를 사용하지 않으니 JVM에 의해 수행되는 JIT 컴파일 과정에서 Native 아키텍처(인텔이면 x86 or x64)의 메모리 할당 인스트럭션이 생성되지 않으므로 성능 향상.
- 전역공간에 생성하기때문에 다른 클래스의 인스턴스들이 데이터 공유가 가능.
- DBCP처럼 공통된 객체를 생성해서 사용해야할 경우 많이 사용(쓰레드 풀, 캐시, 레지스트리 설정, 로그 기록 객체 등)
  <br/><br/>

<br>

### :collision: 단점
- 싱글톤 인스턴스가 너무 많은 일을 하거나 많이 참조(공유)될 경우 다른 클래스와의 coupling(결합도)가 높아져 "개방-폐쇄 원칙"을 위배하게 된다. <br> 
  --> 테스트하기 어려워지고 유지보수하기 까다로워진다. <br><br>

- 멀티쓰레드 환경에서 동기화처리를 안하면 인스턴스가 여러 개 생성되는 경우가 발생할 수 있다.
  
<br><br>

# Singleton 코드 예시
```Java
// 아래 코드는 멀티스레드 환경에서도 안전하게 싱글톤 패턴을 적용할 수 있도록 synchronized 블록을 사용하여 동기화 처리하였음

public class Singleton {
    
    // 1. private static 변수로 싱글톤 인스턴스를 하나만 생성
    private static Singleton instance;
    
    // 2. 생성자를 private로 설정하여 외부에서 인스턴스를 생성하지 못하도록 제한
    private Singleton() {
        // 생성자 로직 (필요한 초기화 작업)
    }
    
    // 3. public static 메소드를 통해 유일한 인스턴스에 접근
    public static Singleton getInstance() {
        
        if(instance == null) {
          // 멀티스레드 환경에서 동기화 처리가 필요할 수 있음
          synchronized (Singleton.class) {
            if (instance == null) {
              instance = new Singleton();         // 첫 번째 인스턴스 생성
            }
          }
        }
        return instance;
    }
    
    // 4. 테스트용 메소드
    public void showMessage() {
        System.out.println("Singleton Pattern in Java!");
    }
}

class Main {
    public static void main(String[] args) {
        // 싱글톤 인스턴스를 가져와서 사용
        Singleton singleton = Singleton.getInstance();
        singleton.showMessage();
        
        // 같은 인스턴스를 참조하는지 확인
        Singleton anotherSingleton = Singleton.getInstance();
        System.out.println(singleton == anotherSingleton);              // true 출력 (같은 인스턴스)
    }
}

```
- `private static 변수`
  - `instance`변수는 유일한 인스턴스를 저장. <br>
  

- `private 생성자` 
  - `Singleton` 클래스의 생성자는 외부에서 호출할 수 없도록 `private`으로 선언되어, 외부에서 인스턴스를 직접 생성할 수 없음. <br><br>
  
- `getInstance() 메소드`
  - 이 메소드는 인스턴스가 아직 생성되지 않은 경우에만 새로 생성하고, 이미 생성된 인스턴스를 반환. <br>
    이를 통해 어플리케이션에서 인스턴스가 하나만 존재하도록 보장. <br><br>
  
- 싱글톤 인스턴스 접근 
  - `getInstance()`를 통해 싱글톤 객체에 접근가능. <br>


<br><br>


```Java
import java.util.Calender;

public class CompanyTest {
    
    public static void main(String[] args) {
        
        Calender cal = Calender.getInstance();
    }
}
```
- 자바에서 쓰이는 Calender도 Singleton 패턴이 적용되어있어 new 키워드를 사용하여 생성자를 호출하는 것이 아니라 클래스명으로 접근하여 사용한다.