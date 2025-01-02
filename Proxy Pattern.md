# Proxy 패턴

### 1. 개념
- 다른 객체에 대한 접근을 제어하는 객체를 제공하는 디자인 패턴
- 즉, 실제 객체에 대한 `대리인`을 두어, 클라이언트가 실제 객체에 접근하기 전에 대리 객체가 그 요청을 대신 처리하도록 함.
- 대리 객체는 실제 객체에 대한 접근을 제어하거나, 필요한 경우 실제 객체에 대한 호출을 지연시키는 등의 역할을 수행

<br>

### 2. 등장배경
- 클라이언트와 실제 객체 간의 연결을 지연시키거나 실제 객체에 대한 접근을 제어할 필요성에 의해 등장
- 객체의 생성 비용이 크거나 접근이 제한된 경우에 대리 객체를 사용하여 리소스를 효율적으로 관리하거나 보안상 이유로 접근을 제한할 수 있음

<br>

### 3. 언제 사용하면 좋을까?
- `지연 로딩` 
  - 객체의 생성이나 초기화 비용이 많이 드는 경우, 실제 객체의 초기화를 지연시킬 때 유용 <br><br>
  
- `보안`
  - 실제 객체에 대한 접근을 제어하여 보안을 강화할 때 사용. <br>
    예를 들어, 권한이 있는 사용자만 실제 객체에 접근하도록 할 수 있음 <br><br>
    
- `접근 제어`
  - 객체의 메소드 호출을 제어하거나 기능 제한 시, 사용 <br><br>

- `리소스 관리`
  - 객체 생성 비용이 큰 경우, 자원의 낭비를 줄이기 위해 객체를 한 번만 생성하고 이후에는 Proxy를 사용하여 관리할 수 있음 <br><br>

<br>

### 4. 활용 예시
- DB 연결
    - DB 연결 객체에 대한 Proxy 를 사용하여 실제 DB에 접근하기 전에 연결을 관리하거나 지연시킬 수 있음

- 이미지 렌더링
    - 고해상도 이미지를 지연 로딩하는 경우, 실제 이미지 로딩은 Proxy 객체가 대신 처리하고, 초기에는 작은 썸네일만 로딩 가능

- 보안 및 인증
    - 특정 기능이나 객체에 대한 접근을 제한하여 인증된 사용자만 접근할 수 있도록 함


<br><br>

### 5. 장점
- 리소스 효율성
    - 실제 객체의 생성이나 작업을 지연시키거나, 한 번만 생성하여 여러 번 사용할 수 있게 함 <br><br>

- 보안
    - Proxy를 통해 실제 객체에 대한 접근 제어 가능. -> 객체 보안성 향상

- 유연성
    - Proxy 객체는 실제 객체에 대한 인터페이스를 구현하여, 클라이언트 코드와 실제 객체 간의 의존성을 줄이고 유연한 설계 가능


### 6. 단점
- 성능 오버헤드
    - Proxy 객체가 중간에서 처리를 하므로, 실제 객체와의 통신에 있어 성능 오버헤드가 발생 가능 <br><br>

- 복잡성 증가
    - Proxy 객체가 추가됨에 따라 시스템의 구조가 복잡해질 수 있음. 특히, 대리 객체가 많으면 코드가 복잡해질 수 있음 <br><br>

- 디버깅 어려움
  - Proxy 객체가 중간에서 개입하므로, 디버깅 시 실제 객체와 Proxy 객체의 상호작용을 파악하기 어려울 수 있음 <br><br>


### 7. Proxy 패턴 예시
```Java
// 실제 객체 (RealSubject)
public class RealSubject {
    public void request() {
        System.out.println("Request from RealSubject");
    }
}

// 대리 객체 (Proxy)
public class Proxy extends RealSubject {
    private RealSubject realSubject;
    
    @Override
    public void request() {
        // 실제 객체의 생성 및 호출을 지연시킬 수 있음
        if(realSubject == null) {
            realSubject = new RealSubject();            // 실제 객체 필요 시 생성
        }
        
        // 추가적인 작업을 수행한 후 실제 객체의 메소드를 호출
        System.out.println("Proxy : Before calling realSubject.request()");
        realSubject.request();
        System.out.println("Proxy : Before calling realSubject.request()");
    }
}

// 클라이언트 코드
public class Main {
    public static void main(String[] args) {
        // proxy 객체를 통해 RealSubject에 접근
      
        RealSubject proxy = new Proxy();
        proxy.request();
    }
}
```
```text
실행 결과

Proxy: Before calling realSubject.request()
Request from RealSubject
Proxy: After calling realSubject.request()
```
- `RealSubject`는 실제 객체로, 클라이언트가 요청을 전달하고 처리하는 객체. `request()`메소드를 통해 요청을 처리
- `Proxy`는 실제 객체에 대한 접근을 제어하는 대리 객체. `request()` 메소드를 호출할 때, 실제 객체를 지연시켜 생성하거나 요청 전에 추가작업 가능
- `Main`은 클라이언트 코드로, `Proxy`객체를 통해 실제 객체에 접근. 클라이언트는 실제 객체가 `Proxy`를 통해 제공되는 인터페이스만 사용하고, 그 뒤에서 실제 객체가 호출됨
