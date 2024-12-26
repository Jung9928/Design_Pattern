# Factory 패턴

### 1. 개념
- 객체 성생 로직을 별도의 팩토리 클래스로 캡슐화하여, 클라이언트 코드가 구체적인 클래스 인스턴스를 생성하는 책임을 팩토리 클래스에서 맡기도록 하는 디자인 패턴.
- 객체 생성 방식에 대한 구체적인 구현을 숨기고, 객체의 생성에 필요한 복잡한 과정을 분리하여 클라이언트 코드가 객체를 생성하는 과정을 단순화함

<br>

### 2. 등장 배경
- 객체 생성 로직이 복잡하거나, 객체 생성 시점에서 클래스의 구체적인 종류를 모르거나 객체 생성 방식이 변경될 가능성이 있을 때를 유연하게 처리하기 위해 등장.
- 객체 생성 로직을 클라이언트 코드에서 분리함으로써 코드 유연성과 재사용성 높이고, 객체 생성 방식을 유연하게 변경 가능

<br>

### 3. 언제 사용하면 좋을까?
- 객체 생성하는 과정이 복잡할 때
- 생성해야 하는 객체 종류가 많고 그에 따라 생성 방법이 달라질 때
- 클라이언트 코드에서 객체 생성 로직을 숨기고 싶을 때
- 객체 생성 방식을 변경해야 할 경우 (유연성이 필요한 경우)
  
<br>

### 4. 활용 예시
- `GUI 프레임워크` : 여러 종류의 버튼이나 텍스트 필드를 생성해야 할 때, 각기 다른 OS에 맞는 버튼을 생성하는 팩토리 패턴을 사용 가능
- `게임 개발` : 다양한 종류의 캐릭터나 아이템을 생성해야 할 때, 팩토리 패턴을 사용하여 각기 다른 캐릭터 객체나 아이템 객체를 생성
- `데이터베이스 커넥터` : 다양한 종류의 DB에 맞는 커넥션 객체를 생성할 시, 유용하게 사용


<br><br>


### 5. 장점
- `유연성`
  - 객체 생성 방식을 바꾸더라도 클라이언트 코드에서 객체 생성 방법을 수정할 필요가 없음


- `객체 생성 캡슐화`
  - 객체 생성 로직을 클라이언트 코드에서 분리하여, 코드가 단순하고 유지보수가 용이


- `확장성`
  - 새로운 객체 타입을 추가하는 데 있어서 기존 코드를 변경할 필요가 적음
  
<br><br>

### 6. 단점
- `복잡성 증가`
  - 팩토리 클래스를 추가해야 하므로 시스템 구조가 복잡해질 수 있음


- `직접적인 객체 생성 코드가 없어짐`
  - 팩토리 클래스에 의존하게 되어, 객체 생성에 대한 직접적인 제어가 어려워질 수 있음




# Factory 패턴 예시
```Java
// 제품 인터페이스
interface Product {
    void create();
}

// 구체적인 제품 클래스들
class ConcreteProductA implements Product {
    
    public void create() {
        System.out.println("ConcreteProductA created");
    }
}

class ConcreteProductB implements Product {
    public void create() {
        System.out.println("ConcreteProductB created");
    }
}

// 팩토리 클래스
class ProductFactory {
    public static Product getProduct(String type) {
        if(type.equalsIgnoreCase("A")) {
            return new ConcreteProductA();
        } 
        else if(type.equalsIgnoreCase("B")) {
            return new ConcreteProductB();
        }
        throw new IllegalArgumentException("Unknown product type");
    }
}

// 클라이언트 코드
public class Main {
    public static void main(String[] args) {
        // 팩토리 메소드를 사용하여 객체 생성
        Product productA = ProductFactory.getProduct("A");
        productA.create();
        
        Product productB = ProductFactory.getProduct("B");
        productB.create();
    }
}
```
- `Product 인터페이스`
    - `Product` 인터페이스는 제품이 가지는 공통적인 메소드를 정의.
    - 구체적인 제품 클래스는 이 인터페이스를 구현함


- `구체적인 제품 클래스들`
    - `ConcreteProductA`와 `ConcreteProductB`는 각각 `Product` 인터페이스를 구현하는 구체적인 클래스임.


- `ProductFactory`
    - `ProductFactory` 클래스는 `getProduct()` 메소드를 통해 제품의 종류에 따라 적절한 인스턴스를 반환하는 팩토리 클래스임.
    - 이 클래스에서 객체 생성 로직을 캡슐화하여 클라이언트는 팩토리 메소드를 통해 객체 생성 가능
    

- `클라이언트 코드`
    - `Main` 클래스는 `ProductFactory`를 통해 객체를 생성하고, 생성된 객체의 `create()` 메소드를 호출함.


- 위 코드에서 클라이언트 코드는 객체 생성 방법에 대해 알지 못하고, 단지 `ProductFactory`를 통해 객체를 얻음. <br>
  객체 생성 방식이 변경되더라도 클라이언트 코드는 수정할 필요 X