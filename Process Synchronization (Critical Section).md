# Process Synchronization (Critical Section) Concurrency Control

**Process Synchronization: 프로세스 동기화**

- 프로세스가 동시에 실행되면서 생기는 문제를 해결하는 것
- **= Concurrency Control: 병행 제어** (와 같은 의미이다.)

[세마포어](https://www.notion.so/Process-Synchronization-Critical-Section-Concurrency-Control-07784fa100ba4775a266b48323517efd)

### 데이터의 접근

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled.png)

### Race Condition (경쟁 상태)

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%201.png)

### OS에서의 race condition

**언제 발생하는가?**

1. kernel 수행 중 인터럽트 발생 시
2. Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernel data

1. **interrupt handler v.s. kernel**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%202.png)

2. **Preempt a process running in kernel?**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%203.png)

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%204.png)

u: user, k: kernel

1. **multiprocessor**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%205.png)

### Process Synchronization 문제

- 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process)간의 실행 순서(orderly execution)를 정해주는 매커니즘 필요

- **`Race Condition`**
    - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- race condition을 막기 위해서는 concurrent process는 동기화(synchronize)되어야 한다.

### The Critical-Section Problem

- `**Critical Section(임계 영역)`** : ****공유자원에 접근하는 경우
- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 **critical section**이 존재
- Problem
    - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다
        
        ![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%206.png)
        
        두 프로세스가 모두 공유자원에 접근하려는 상황
        
        ![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%207.png)
        
    
    ![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%208.png)
    
    코드 분류 → 크리티컬 섹션이거나 아니거나. 공유자원을 접근하는 코드거나 아니거나로 구분
    

---

### 임계구역 문제에 대한 프로그램적 해결법의 충족 조건

**알고리즘이 만족해야 할 조건들**

- Mutual Exclusion (상호 배제)
    
    프로세스 Pi가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안된다.
    
- Progress (진행)
    
    아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 한다.
    
- Bounded Waiting (유한 대기)
    
    프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.
    

- 가정
    - 모든 프로세스의 수행 속도는 0보다 크다
    - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다

### **Algorithm 1**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%209.png)

- turn을 교대로 해준다

### Algorithm 2

- flag라는 변수를 프로세스가 각각 가진다

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2010.png)

- flag[i]가 True인 상태에서 while문에 들어갔는데 flag[j]도 True상태일 때 문제 발생
    - 둘 다 눈치 보느라 크리티컬섹션에 둘 다 들어가지 못하고 기다리는 상황 발생.
- if 깃발을 들고있는 상태에서 cpu에게 뺏긴다면?
    
    

### Algorithm 3

- turn, flag 둘 다 사용

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2011.png)

- Busy Waiting (=spin lock) 문제 발생
    
    **바쁜대기란 아무것도 하지 않는 빈 반복문을 계속 돌다가 [임계 구역](https://namu.wiki/w/%EB%AE%A4%ED%85%8D%EC%8A%A4#s-2.1)에 진입할 수 있을 때 진입하는 방식으로, 빈 반복문을 반복하기 때문에 계속적으로 컨텍스트 교환이 발생하며 이로 인하여 처리 효율이 떨어지는 단점이 있다. 또한, 어떠한 프로세스가 먼저 임계 구역에 진입을 할 수 있을지에 대한 처리를 할 수 없다는 단점 또한 존재한다.**
    
    - while문에서 lock을 걸어 상대방이 못들어가고 wait하게 하는 것
    - 스핀락은 Critical Section에 진입이 불가능할 때 진입이 가능할 때 까지 루프를 돌면서 재시도 하는 방식으로 구현된 락을 가리킨다. 스핀락은 바쁜 대기의 한 종류이다.
    - 스핀락은 운영 체제의 스케줄링 지원을 받지 않기 때문에, 해당 스레드에 대한 Cnotext Switch가 일어나지 않는다. 따라서 스핀락은 임계 구역에 짧은 시간안에 진입할 수 있는 경우에 Context Switch을 제거할 수 있어서 효율적이다. 하지만 만약 스핀락이 오랜 시간을 소요한다면 다른 스레드를 실행하지 못하고 대기하게 되며, 이 경우 비효율적인 결과를 가져온다
        
        ![[https://akdl911215.tistory.com/220](https://akdl911215.tistory.com/220)](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2012.png)
        
        [https://akdl911215.tistory.com/220](https://akdl911215.tistory.com/220)
        
    

---

⬆️ 소프트웨어적으로 해결

### Synchronization Hardware

**Test and set (검사와 지정)**

- 하드웨어의 지원을 받아 명령어를 실행하는 도중에 타임아웃이 걸리지 않도록 하는 방식
- 하드웨어적인 Instruction(Test_and_set(data))이 지원돼 데이터를 읽고 쓰는 작업을 동시에 할 수 있다면, lock을 금방 해결할 수 있다. [참고](https://jhnyang.tistory.com/41)

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2013.png)

- Test_and_set(a)
    - a라는 데이터의 현재 값을 읽어내고, a라는 데이터의 값을 1(True)로 바꿔준다 → lock을 건다
    - **Lock = 1(True)**
    - **Unlock = 0(False)**

### Semaphores

**세마포어 S는 정수값을 가지는 변수**이며, 다음과 같이 P와 V라는 명령에 의해서만 접근할 수 있다. (P와 V는 각각 try와 increment(증가)를 뜻하는 [네덜란드어](https://ko.wikipedia.org/wiki/%EB%84%A4%EB%8D%9C%EB%9E%80%EB%93%9C%EC%96%B4) Proberen과 Verhogen의 머릿글자를 딴 것이다.)

- **P연산과 V연산으로 구성된 일종의 추상 자료형.**
    - **P연산은 자원을 획득하는 과정, V연산은 자원을 반납하는 과정.**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2014.png)

- S: 자원의 갯수를 센다. 1: 한 개, 0: 자원이 남아있지 않음
    - 1 이상의 숫자일 때는 남아있는 자원의 수를 세는 counting 역할
- 상대방이 V연산을 하기 전에 자신은 P연산만 하기 때문에 Busy-wait이 발생할 수 있다.

**세마포어는 동시에 여러 개의 프로세스/스레드가 임계 구역에 접근할 수 있도록 카운트를 가지고 있는데 카운트가 1인 특별한 세마포어가 바로 뮤텍스이다.**

### Critical Section of n Processes

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2015.png)

문제점: 비지 웨이팅(스핀락). 계속 while문을 걸면서 상대방이 못들어가게 하는것. 내가 cpu를 잡아서 while문에서 돌아도 상대 프로세스에서 변수를 셋팅해줘야 빠져나올수 있다

Spin Lock 은 이름이 뜻하는대로, 만약 다른 스레드가 lock을 소유하고 있다면 그 lock이 반환될 때까지 계속 확인하며 기다리는 것이다. "조금만 기다리면 바로 쓸 수 있는데 굳이 컨텍스트 스위칭으로 부하를 줄 필요가 있나?" 라는 컨셉으로 개발된 것으로 크리티컬 섹션에 진입이 불가능할때 컨텍스트 스위칭을 하지 않고 잠시 루프를 돌면서 재시도 하는 것을 말합니다. Lock-Unlcok 과정이 아주 짧아서 락하는 경우가 드문 경우(즉; 적절하게 크리티컬 섹션을 사용한 경우) 유용하다. Spin Lock 은 다음과 같은 특성을 갖는다.

1. Lock을 얻을 수 없다면, 계속해서 Lock을 확인하며 얻을 때까지 기다린다. 이른바 바쁘게 기다리는 busy wating이다.
2. 바쁘게 기다린다는 것은 무한 루프를 돌면서 최대한 다른 스레드에게 CPU를 양보하지 않는 것이다.
3. Lock이 곧 사용가능해질 경우 컨택스트 스위치를 줄여 CPU의 부담을 덜어준다. 하지만, 만약 어떤 스레드가 Lock을 오랫동안 유지한다면 오히려 CPU 시간을 많이 소모할 가능성이 있다.
4. 하나의 CPU나 하나의 코어만 있는 경우에는 유용하지 않다. 그 이유는 만약 다른 스레드가 Lock을 가지고 있고 그 스레드가 Lock을 풀어 주려면 싱글 CPU 시스템에서는 어차피 컨택스트 스위치가 일어나야 하기 때문이다. 주의할 점 스핀락을 잘못 사용하면 CPU 사용률 100%를 만드는 상황이 발생하므로 주의 해야 한다. 스핀락은 기본적으로 무한 for 루프를 돌면서 lock을 기다리므로 하나의 쓰레드가 lock을 오랫동안 가지고 있다면, 다른 blocking된 쓰레드는 busy waiting을 하므로 CPU를 쓸데없이 낭비하게 된다.

장점은 스핀락을 잘 사용하면 context switch를 줄여 효율을 높일 수 있습니다. 무한 루프를 돌기 보다는 일정 시간 lock을 얻을 수 없다면 잠시 sleep하는 back off 알고리즘을 사용하는 것이 훨씬 좋습니다.

### Block / Wakeup Implementation

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2016.png)

- Ready→ Block → Ready 상태로 바꿔가며 작업하기 때문에 오버헤드가 발생할 수 있다.

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2017.png)

- 자원을 반납만 하고 끝나는게 아니라, 혹시 이 자원을 기다리다가 잠들어있는 프로세스가 있다면 깨워주는 연산이 V연산에 같이 들어간다.
- S가 음수면 누군가 자원을 기다리고 있다는 뜻이 되고, 
양수이면 자원에 여분이 있기 때문에 기다리지않고 쓰고 있는 상황이다 라는 의미.

### Which is better?

- Busy-wait v.s. Block/wakeup
- Block/wakeup overhead v.s. Critical section 길이
    - Critical section의 길이가 긴 경우 Block/Wakeup이 적당
    - Critical section의 길이가 매우 짧은 경우 Block/Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음 (B는 Ready→ Block → Ready 상태로 바꿔가며 작업하기 때문에 오버헤드가 발생한다)
    - 일반적으로는 Block/Wakeup 방식이 더 좋음
    

### Two Types of Semaphores

- **Counting semaphore**
    - 도메인이 0이상인 임의의 정수값
    - 주소 resource counting에 사용
- **Binary semaphore (=mutex)**
    - 0또는 1값만 가질 수 있는 semaphore
    - 주로 mutual exclusion (lock/unlock)에 사용

### Deadlock and Starvation

- Deadlock
    
    **둘 이상의 프로세스가 서로** 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
    
- S와 Q가 1로 초기화된 semaphore라 하자.
    
    ![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2018.png)
    
- Starvation
    
    indefinite blocking. 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
    

---

세마포어로 해결하는 3가지 문제

### Bounded-Buffer Problem 한정-버퍼 문제

**(Producer-Consumer Problem)** 여러 개의 프로세스를 어떻게 동기화할 것인가에 관한 고전적인 문제

이 문제를 해결하는 것을 **생산자-소비자 협동**이라 하며, [버퍼](https://ko.wikipedia.org/wiki/%EB%B2%84%ED%8D%BC_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99))가 [동기화](https://ko.wikipedia.org/wiki/%EB%8F%99%EA%B8%B0%ED%99%94)되어 정상적으로 동작하는 상태를 뜻한다. 문제를 해결하기 위해 [세마포어](https://ko.wikipedia.org/wiki/%EC%84%B8%EB%A7%88%ED%8F%AC%EC%96%B4)를 활용할 수 있다.

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2019.png)

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2020.png)

### Readers-Writers Problem 독자-저자 문제

여러 명의 독자와 저자들이 하나의 저장 공간(버퍼)을 공유하며 이를 접근할 때 발생하는 문제

- 독자는 공유 공간에서 데이터를 읽어온다. 여러 명의 독자가 동시에 데이터를 읽어오는 것이 가능하다.
- 저자는 공유 공간에 데이터를 쓴다. 한 저자가 공유 공간에 데이터를 쓰고 있는 동안에는 그 저자만 접근이 가능하며, 다른 독자들과 저자들은 접근할 수 없다.
- 한 Process가 DB에 write 중일 때 다른 process가 접근하면 안됨
- solution
    - Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB에 접근하게 해준다
    - Writer는 대기 중인 Reader가 하나도 없을 때 DB접근이 허용된다
    - 일단 Writer가 DB에 접근 중이면 Reader들은 접근이 금지된다
    - Writer가 DB에서 빠져나가야만 Reader의 접근이 허용된다

**Shared data**

- DB 자체
- readcount: /* 현재 DB에 접근 중인 Reader의 수 *

**Synchronization variables**

- mutex
    
    /* 공유 변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용 *
    
- db
    
    /* Reader와 Writer가 공유 DB 자체를 올바르게 접근하게 하는 역할 */
    

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2021.png)

### Dining-Philosophers Problem

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2022.png)

- 앞의 solution의 문제점
    - Deadlock 가능성이 있다
    - 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어버린 경우
- 해결 방안
    - 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다
    - 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 한다
    - 비대칭
        - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2023.png)

- P: lock 검, V: lock 해제

### Monitor

공유자원을 사용할 때 모든 프로세스가 세마포어 알고리즘을 따른다면 굳이 P()와 V()를 사용할 필요 없이, 자동으로 처리하면 되는데 이를 실제로 구현한 것이 모니터이다.

모니터는 공유자원을 내부적으로 숨기고, 공유자원에 접근하기 위한 인터페이스만 제공함으로써 자원을 보호하고 프로세스간에 동기화를 시킨다.

- 시스템 콜과 같은 방법으로 모니터도 보호할 자원을 임계구역으로 숨기고 임계구역에서 작업할 수 있는 인터페이스만 제공하여 자원을 보호한다. (모니터 큐)
- 왜 모니터를 사용? Semaphore의 문제점!
    - 코딩하기 힘들다
    - 정확성(correctness)의 입증이 어렵다
    - 자발적 협력(voluntary cooperation)이 필요하다
    - 한 번의 실수가 모든 시스템에 치명적 영향.
    - 잘못된 사용으로 인해 임계구역이 보호받지 못할 수 있다.
    
    ![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2024.png)
    
    - 1번 상황: P()와 V()를 반대로 사용하여 상호 배제가 보장되지 않은 경우로, 임계구역을 보호할 수 없다.
    - 2번 상황: 둘 다 lock을 걸고 들어가면서 wakeup이 발생하지 않은 경우이다.
    프로세스 간의 동기화가 이루어지지 않아 세마포어 큐에서 대기하고 있는 프로세스들이 무한 대기에 빠진다. 어느 누구도 크리티컬 섹션에 접근하지 못하게 됨.

- 세마포어와의 차이점: 모니터는 P()와 V()를 사용할 필요가 없다. 임계구역의 보호나 프로세스의 동기화가 모니터 내부에서 처리되므로 사용하는 모니터로 작성한 함수명(monitor-name)을 호출하기만 하면 된다. 
모니터 자체가 하나의 프로세스만 실행되도록 제어한다.

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2025.png)

- 공유자원 선언, 프로시저, 초기화 코드가 포함된 병행성 구조 (병렬식구조→lock 걸 필요X)

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2026.png)

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2027.png)

**Condition variable 조건 변수 - 큐를 끼워넣는다**

상태만 변하고 변수값은 변하지 않는다. (변수값이 없다)

- wait(): 모니터 큐에서 자신의 차례가 올 때까지 기다린다. 세마포어 P()에 해당
- signal(): 모니터 큐에서 기다리는 다음 프로세스에 순서를 넘겨준다. 세마포어 V()에 해당

### Monitor 버전 코드

void~ 로 procedure body 를 만들어 병행적 구조로 만듦으로써 lock, unlock 과정을 없앰

**Bounded-Buffer Problem code**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2028.png)

- empty.wait(): 빌 때 까지 기다린다
- full.wait(): 소비할 것이 있을 때 까지 기다린다

**Dining Philosophers code**

![Untitled](Process%20Synchronization%20(Critical%20Section)%20Concurr%2007784fa100ba4775a266b48323517efd/Untitled%2029.png)

- state[i]: 공유 자원