# Builder 패턴

### 1. 개념
- 복잡한 객체를 생성하는 과정을 단계적으로 객체를 구성할 수 있도록 도와주는 디자인 패턴
- 객체 생성의 세부적인 과정과 객체 자체를 분리하여 동일한 생성 절차에서 서로 다른 타입의 객체를 생성할 수 있음
- 객체를 생성하는 복잡한 과정을 하나의 객체로 캡슐화하여 사용자가 객체를 쉽게 만들 수 있도록 하는 패턴

<br>

### 2. 등장배경
- 객체가 복잡해지면서 생성자나 setter 메소드를 통해 객체를 생성하는 것이 점점 더 어려워지고 <br> 
  가독성이 떨어졌기 때문에 등장함. <br>
  여러 인자들 중 일부만 설정하거나 설정의 순서가 중요할 경우, Builder 패턴을 사용하면 코드가 더 깔끔하고 유연하게 됨

<br>

### 3. 언제 사용하면 좋을까?
- 객체가 여러 속성을 가지고 있고, 그 속성들이 많아서 생성자가 너무 복잡하거나 인자들이 많아져 가독성이 떨어질 때
- 객체를 생성할 때 선택적인 인자가 많고, 객체의 생성 순서가 중요한 경우
- 동일한 객체의 여러 변형을 생성해야 할 때
- 불변 객체를 생성할 때 유용

<br>

### 4. 활용 예시
- GUI 컴포넌트 생성
    - 다양한 속성을 가진 GUI 요소 (ex : 버튼, 텍스트 박스)를 생성할 때, 선택적인 속성만을 설정할 수 있음 <br><br>

- 문서 작성 시스템
    - 다양한 형태의 문서를 생성할 때, 문서의 각 부분(머리말, 본문, 꼬리말 등)을 쉽게 조합할 수 있게 해줌 <br><br>

- 네트워크 요청 설정
    - HTTP 요청을 만들 때, 요청의 메소드, 헤더, 본문 등 다양한 속성을 조합하여 요청을 생성할 수 있음 <br><br>
 


### 5. 장점
- 가독성 향상
    - 생성자의 인자가 많거나 순서가 중요한 경우, Builder 패턴을 사용하면 코드의 가독성이 훨씬 좋아짐 <br><br>

- 유연성
    - 객체의 다양한 속성을 선택적으로 설정할 수 있어 많은 속성을 가진 객체를 만들 때, 유용함 <br><br>

- 불변 객체 생성
    - Builder패턴을 사용하면 불변 객체를 쉽게 생성 가능 <br><br>

- 객체 생성 과정의 캡슐화
  - 객체의 생성 과정을 캡슐화하여 외부에서 객체 생성 방식을 신경쓰지 않도록 할 수 있음 <br><br>


### 6. 단점
- 코드가 길어질 수 있음
    - 복잡한 객체를 만들기 위해 여러 빌더 메소드가 필요하기 때문에 코드가 길어질 수 있음 <br><br>
  
- 초기 구현 복잡도 
    - 처음에는 Builder 클래스와 메소드를 정의해야 하므로 초기 구현이 복잡할 수 있음 <br><br>
  
- 객체 생성에 추가적인 시간 소요
  - 매번 객체를 새로 생성할 때마다 빌더를 사용해야 하므로, 객체 생성에 시간이 추가로 소요될 수 있음 <br><br>
  

### 7. Builder 패턴 예시
```Java
import java.util.*;

// 제품 클래스 (복잡한 객체)
public class Car {
    private String model;
    private String engine;
    private int doors;
    private boolean sunroof;
    
    // 생성자는 private로 선언하여 외부에서 직접 생성하지 못하게 한다.
    private Car(Builder builder) {
        this.model = builder.model;
        this.engine = builder.engine;
        this.doors = builder.doors;
        this.sunroof = builder.sunroof;
    }
    
    public String getModel() {
        return model;
    }
    
    public String getEngine() {
        return engine;
    }
    
    public int getDoors() {
        return doors;
    }
    
    // 빌더 클래스 (Builder 패턴을 적용)
    public static class Builder {
        private String model;
        private String engine;
        private int doors;
        private boolean sunroof;
        
        // 필수 값에 대한 메소드 (예시 : 모델, 엔진)
        public Builder model(String model) {
            this.model = model;
            return this;
        }
        
        public Builder engine(String engine) {
            this.engine = engine;
            return this;
        }
        
        // 선택적 값에 대한 메소드 (ex : 문 개수, 선루프)
        public Builder doors(int doors) {
            this.doors = doors;
            return this;
        }
        
        public Builder sunroof(boolean sunroof) {
            this.sunroof = sunroof;
            return this;
        }
        
        // 객체 생성 메소드
        public Car build() {
            return new Car(this);
        }
    }
}

// 클라이언트 코드
public class Main {
    public static void main(String[] args) {
        // Builder를 사용하여 Car 객체 생성
        Car car1 = new Car.Builder()
                .model("Sedan")
                .engine("V6")
                .doors(4)
                .sunroof(true)
                .build();
        
        
        Car car2 = new Car.Builder()
                .model("Coupe")
                .engine("V8")
                .doors(2)
                .sunroof(false)
                .build();
        
        // 객체 출력
        System.out.println("Car 1 : " + car1.getModel() + ", Engine : " + car1.getEngine() + ", Doors: " + car1.getDoors() + ", Sunroof: " + car1.hasSunroof());
        System.out.println("Car 2: " + car2.getModel() + ", Engine: " + car2.getEngine() + ", Doors: " + car2.getDoors() + ", Sunroof: " + car2.hasSunroof());
    }
}

```
```text
실행 결과

Car 1: Sedan, Engine: V6, Doors: 4, Sunroof: true
Car 2: Coupe, Engine: V8, Doors: 2, Sunroof: false
```
