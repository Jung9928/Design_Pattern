# Producer-Consumer 패턴

### 1. 개념
- 2개의 스레드, 즉 `Producer`와 `Consumer`가 동일한 작업을 공유하는 문제를 해결하는 디자인 패턴
- `Producer`는 데이터를 생성하고 `Consumer`는 데이터를 소비하는 역할 수행
- 2개의 스레드가 데이터를 공유하는 동안 발생할 수 있는 문제를 해결하기 위해 `Queue`나 `Buffer`를 사용하여 데이터를 전달.
- 생산자와 소비자가 서로 독립적으로 작업할 수 있도록 하고, 큐에 데이터를 쌓거나 가져오는 작업을 조정하여 데이터를 효율적으로 처리

<br>

### 2. 등장배경
- 멀티스레딩 환경에서 `동기화` 문제해결과 여러 스레드가 데이터를 공유하거나 <br> 
  동시 접근 시, 발생할 수 있는 충돌 및 race condition을 방지하고 효율적인 작업 처리를 위해 등장 <br><br>

<br>

### 3. 언제 사용하면 좋을까?
- 데이터 생성과 소비가 비동기적으로 일어날 때
- 생성되는 데이터의 속도와 소비되는 데이터의 속도가 다를 때
- 데이터의 처리 과정이 큐를 사용하여 유연하게 관리되길 원할 때
- 멀티스레드 환경에서 데이터의 동기화를 안전하게 처리하고 싶을 때

<br>

### 4. 활용 예시
- 파일 처리 시스템
    - 하나의 스레드는 파일을 읽어들이고 다른 스레드는 파일에서 읽어들인 데이터를 처리 <br><br>

- 이벤트 처리 시스템
    - 이벤트를 발생시키는 `Producer`와 이벤트를 처리하는 `Consumer`가 비동기적으로 작업을 수행 <br><br>

- 네트워크 서버
    - 요청을 받는 `Producer`와 클라이언트에게 응답하는 `Consumer`가 각각 작업을 처리 <br><br>

- DB 시스템
  - 데이터를 생성하는 프로세스와 데이터를 처리하는 프로세스가 독립적으로 작업할 때 <br><br>

<br>

### 5. 장점
- 효율적인 작업 분리
    - `Producer`와 `Consumer`가 서로 독립적으로 작업을 수행할 수 있어 시스템의 효율성 증대 <br><br>
    
- 비동기 처리
    - 데이터를 생성하는 작업과 소비하는 작업이 독립적으로 수행하고 이 작업들이 비동기적으로 처리되어 성능 최적화 가능 <br><br>

- 부하 분산
    - 데이터 생산과 소비의 속도가 다를 때, 큐를 통해 부하 분산 가능 <br><br>
  
- 메모리 관리
  - 큐를 사용하여 메모리를 효율적으로 관리 가능, 데이터가 쌓이지 않도록 할 수 있음 <br><br>


### 6. 단점
- 동기화 문제
    - 멀티스레드 환경에서 `Producer`와 `Consumer` 간의 동기화 문제를 해결해야 함. <br>
      이를 위해, `wait()`와 `notify()` 같은 동기화 매커니즘을 사용할 필요가 있음. <br><br>

- 대기 시간
    - 만약 큐가 비어있거나 꽉 차 있다면, 대기하는 스레드가 발생하게 되며, 이로 인해 성능 저하가 있을 수 있음 <br><br>

- 복잡성
    - 큐와 동기화 매커니즘을 적절히 사용해야 하므로 코드가 복잡해질 수 있음 <br><br>


### 7. Producer-Consumer 패턴 예시
```Java
import java.util.*;

// 큐 클래스
class SharedQueue {
    
    private Queue<Integer> queue = new LinkedList<>();
    private final int MAX_SIZE = 10;
    
    // 생산자 - 큐에 데이터 추가
    public synchronized void produce(int data) throws InterruptedException {
        while (queue.size() == MAX_SIZE) {
            wait();     // 큐가 가득 찼으면 대기
        }
        queue.offer(data);  // 큐에 데이터 추가
        System.out.println("Produced: " + data);
        notifyAll();            // 데이터를 소비할 수 있도록 알림
    }
    
    // 소비자 - 큐에서 데이터 제거
    public synchronized int consume() throws InterruptedException {
        while (queue.isEmpty()) {
            wait();             // 큐가 비었으면 대기
        }
        int data = queue.poll();        // 큐에서 데이터 제거
        System.out.println("Consumed : " + data);
        notifyAll();            // 데이터를 생산할 수 있도록 알림
        return data;
    }
}

// 생산자 클래스
class Producer implements Runnable {
    private SharedQueue sharedQueue;
    
    public Producer(SharedQueue sharedQueue) {
        this.sharedQueue = sharedQueue;
    }
    
    @Override
    public void run() {
        try {
            for(int i=0; i<20; i++) {
                sharedQueue.produce(i);
                Thread.sleep(100);          // 생산 주기
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

// 소비자 클래스
class Consumer implements Runnable {
    private SharedQueue sharedQueue;
    
    public Consumer(SharedQueue sharedQueue) {
        this.sharedQueue = sharedQueue;
    }
    
    @Override
    public void run() {
        try {
            for(int i = 0; i<20; i++) {
                sharedQueue.consume();
                Thread.sleep(150);              // 소비 주기
            }
        } catch(InterruptedException e) {
            e.printStackTrace();
        }
    }
}

// 메인 클래스
public class Main {
    public static void main(String[] args) {
        SharedQueue sharedQueue = new SharedQueue();
        
        Thread producerThread = new Thread(new Producer(sharedQueue));
        Thread consumerThread = new Thread(new Consumer(sharedQueue));
        
        producerThread.start();
        consumerThread.start();
    }
}
```
```text
실행 결과

Produced: 0
Consumed: 0
Produced: 1
Consumed: 1
Produced: 2
Consumed: 2
...

```
- `SharedQueue`는 생산자와 소비자가 공유하는 큐. <br>
  큐가 가득 찼을 땐 생산자가 대기하고, 큐가 비었을 땐 소비자가 대기하도록 `wait()`와 `notifyAll()` 메소드를 사용하여 동기화 처리 <br><br>

- `Producer`는 데이터를 생성하는 역할을 수행하는 스레드. <br>
  큐에 데이터를 추가하고, 그 후 소비자에게 알림. <br><br>

- `Consumer`는 큐에서 데이터를 소비하는 역할을 수행하는 스레드. <br>
  큐에서 데이터를 꺼내고 그 후, 생산자에게 알림. <br><br>

