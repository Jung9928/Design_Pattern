# Observer 패턴

### 1. 개념
- 객체 간의 1:N 관계를 정의하여 한 객체의 상태 변화가 있을 때, 그 객체에 의존하는 다른 객체들에게 자동으로 알림을 보내는 디자인 패턴
- 하나의 객체가 상태를 변경할 때, 이를 관련된 다른 객체들이 알 수 있도록 함.
- 보통 하나의 `주체(Subject)` 객체와 이를 관찰하는 여러 개의 `옵저버(Observer)` 객체가 존재

<br>

### 2. 등장배경
- 주로 이벤트 처리나 상태 변경에 반응해야 하는 시스템에서 등장하였는데 <br>
  예를 들어, GUI 시스템에서는 사용자의 입력을 처리하는 컴포넌트와 그에 반응하는 여러 뷰나 컨트롤러가 있을 수 있음. <br>
  만약 상태 변경 시, 이에 대한 알림을 해당 상태를 필요로 하는 모든 객체에 전달해야할 때 `Observer`패턴을 사용하여 자동으로 상태 변경을 알리기 위해 등장.

<br>

### 3. 언제 사용하면 좋을까?
- 객체 간의 관계가 1:N일 때, 즉 한 객체의 상태 변경이 여러 객체에 영향을 미칠 때
- 주체 객체의 상태가 변경될 때 여러 객체가 그 변경을 알아야 할 때
- 객체의 상태 변경을 감지하고 자동으로 알림을 보내는 시스템을 만들고 싶을 때
- 주체 객체와 옵저버 객체 간의 결합도를 낮추고 싶을 때

<br>

### 4. 활용 예시
- GUI 프레임워크
    - 버튼 클릭, 텍스트 필드 내용 변경 등 이벤트 발생 시, 그에 따라 여러 다른 요소가 반응하도록 할 수 있음 <br><br>

- MVC 패턴
    - 모델의 상태가 변경되면 뷰가 자동으로 갱신되도록 할 수 있음 <br><br>

- 주식 거래 시스템
    - 주식 가격 변화가 있을 시, 이를 알리기 위해 여러 투자자(옵저버)가 관련된 정보를 받을 수 있도록 설정 가능 <br><br>

- 알림 시스템
  - 다양한 종류의 알림을 각기 다른 채널(이메일, 문자, 푸시 등)을 통해 제공하는 시스템에서 활용. <br><br>

    

### 5. 장점
- 객체 간의 느슨한 결합
    - 주체 객체는 옵저버 객체들이 무엇을 하는지 알지 못하고 옵저버 객체는 주체 객체의 세부 구현에 의존하지 않으므로 두 객체가 독립적으로 변경될 수 있음

- 자동적인 알림
    - 주체 객체의 상태가 변경될 때 옵저버 객체들이 자동으로 알림을 받아 처리할 수 있음. <br>
      이로 인해, 코드의 유지보수성이 향상

- 유연성
    - 주체 객체와 옵저버 객체가 느슨하게 결합되어 있기 때문에 새로운 옵저버 객체를 추가하거나 제거하는 것이 용이


### 6. 단점
- 성능 문제
    - 옵저버가 많아질 경우, 주체 객체의 상태 변경에 대해 알림을 보내는 과정에서 성능 문제 발생할 수 있음 <br><br>

- 상태 변경의 복잡성
    - 상태 변경을 통지하기 전에 여러 옵저버가 상태를 체크하거나 추가적인 처리를 해야 하므로 상태 관리가 복잡해질 수 있음 


### 7. Observer 패턴 예시
```Java
import java.util.*;

// 주체(Subject) 인터페이스
interface Subject {
    void addObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}

// 옵저버(Observer) 인터페이스
interface Observer {
    void update(String message);
}

// 구체적인 주체(Subject) 클래스
class StockMarket implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String stockPrice;
    
    public void setStockPrice(String stockPrice) {
        this.stockPrice = stockPrice;
        notifyObservers();
    }
    
    @Override
    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
      observers.remove(observer);
    }
  
    @Override
    public void notifyObserver() {
        for(Observer observer : observers) {
            observer.update(stockPrice);   
        }
    }
}


// 구체적인 옵저버(Observer) 클래스
class Investor implements Observer {
    private String name;
    
    public Investor(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String message) {
        System.out.println(name + " received stock price update : " + message);
    }
}

// 클라이언트 코드
public class Main {
    public static void main(String[] args) {
        StockMarket stockMarket = new StockMarket();
        
        // 옵저버 객체(투자자들) 생성
        Investor investor1 = new Investor("Investor1");
        Investor investor2 = new Investor("Investor2");
        
        // 옵저버 등록
        stockMarket.addObserver(investor1);
        stockMarket.addObserver(investor2);
        
        // 주체 객체의 상태 변경(주식 가격 변경)
        stockMarket.setStockPrice("100 USD");
        
        // 옵저버 객체가 변경된 상태를 자동으로 알림
        stockMarket.setStockPrice("105 USD");
        
        // 옵저버 제거
        stockMarket.removeObserver(investor2);
        
        // 주체 객체의 상태 변경 (주식 가격 변경)
        stockMarket.setStockPrice("110 USD");
    } 
}
```
```text
실행 결과

Investor1 received stock price update: 100 USD
Investor2 received stock price update: 100 USD
Investor1 received stock price update: 105 USD
Investor2 received stock price update: 105 USD
Investor1 received stock price update: 110 USD
```
- `StockMarket` 객체는 주식 가격이 변경될 때마다 등록된 모든 `Investor`객체에게 이를 알리며, 옵저버 객체들은 이를 받아 출력함. <br>
  `Inverstor2`는 나중에 옵저버 목록에서 제거되었기 때문에 그 이후의 변경 사항은 `Investor2`에게 전달되지 않음 <br>