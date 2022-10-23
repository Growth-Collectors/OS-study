## CH3 Process

## 프로세스의 개념

Process is a **program in execution** 

### 프로세스의 문맥 (Context)

- **CPU** 수행 상태를 나타내는 하드웨어 문맥
    - Program Counter (PC)
        
    - 어디를 가리키고 있는지 
        
    - 각종 register
        
    - 어떤 값 가지는지
        
- 프로세스의 주소 공간 (메모리)
    - code, data, stack
    
- 프로세스 관련 **커널 자료구조**
    - PCB(Process Control Block) 프로세스가 하나 실행될 때마다 PCB가 관리 
        
    - Kernel Stack
        
        

### 프로세스의 상태 (Process State)

![](https://velog.velcdn.com/images/zioo/post/ebfeda15-be40-4ab8-97b3-5a13ae29d0df/image.png)

- Running
    - CPU 잡고 instruction 수행
- Ready
    - CPU를 기다리는 상태 (메모리 등 다른 조건 모두 만족)
- Blocked (wait, sleep)
    - CPU 를 얻어도 instruction 수행할 수 없는 상태
    - I/O 처럼 오래 걸리는 작업 중
        
        ex) Disk에서 file을 읽어와야 하는 경우 
        

추가적인 상태 

- New :  프로세스 생성중인 상태
- Terminated : 수행이 끝난 상태


![](https://velog.velcdn.com/images/zioo/post/a001a938-3d9b-4c70-9f9e-8c170093c56a/image.png)

모든 자원이 놀지 않고 일을 잘 할 수 있게 해주는 매커니즘이 필요 

### Process Control Block (PCB)

운영체제가 각 프로세스를 관리하기 위해 **프로세스당 유지하는 정보** 

![](https://velog.velcdn.com/images/zioo/post/83bdcf53-9fe0-4e3e-b8d4-f1e860eeacf8/image.png)

구조체 

1. OS 가 관리상 사용하는 정보 
2. CPU 수행 관련 하드웨어 값 
3. 메모리 관련
4. 파일 관련

### 문맥 교환 (Context Switch)

CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정 

- CPU 에서 실행중이던 프로세스의 상태를 PCB에 저장
- 새롭게 얻는 프로세스 상태를 PCB에서 읽어옴

- System call 이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님

### 프로세스를 스케줄링하기 위한 큐

- Job queue
    
    
   - 모든 시스템 내의 집합 
    

- Ready queue
    
    - 메모리 내에서 CPU를 잡아서 실행되기를 기다리는 프로세스 집합
    
- Device queues
    
    - I/O 처리를 기다리는 프로세스 집합
    

프로세스들은 각 큐를 오가며 실행 

### 스케줄러 (Scheduler)

순서를 정해준다 

- Long-term scheduler (job scheduler)
    
    - 프로세스에 memory(자원)를 주는 문제 
    
    - 요즘에는 사용하지 않음 time sharing system 에서는 무조건 ready
    
    
- Short-term scheduler (CPU scheduler)
    
    - 어떤 프로세스를 다음에 running 시킬지 정함 
    
    
- Medium-term scheduler (Swapper)
    
    - 메모리에 많은 프로그램이 올라와있으면 
    
    - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
    

### 프로세스의 상태 (Process State)

- Running
- Ready
- Blocked (wait, sleep)
    
    - I/O 등의 event를 스스로 기다리는 상태
    
- Suspended (stopped)
    
    - 외부적인 이유로 수행이 정지된 상태
    
    - 프로세스는 통째로 디스크에 swap out 된다

차이점 

Blocked : 자신이 요청한 event가 만족되면 Ready 

Suspended : 외부에서 resume 해주어야지 Active 가능

![](https://velog.velcdn.com/images/zioo/post/819a154f-e1ae-4a76-9267-8c1abe7c0f83/image.png)


요약 

- 동기식 입출력과 비동기식 입출력

- 프로세스 스케줄링 큐의 모습

- Thread

## 동기식 입출력과 비동기식 입출력

### 동기식 입출력

- I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
- 구현 1
    - I/O 가 끝날 때 까지 CPU를 낭비하면서 기다림
- 구현2
    - I/O가 끝날 때 까지 해당 프로그램에서 CPU를 빼앗음

## Thread
프로세스 내부에 CPU 수행 단위가 여러 개 있는 경우, 프로세스를 별도로 두는 것보다 프로세스 안에 thread를 여러개 두는 것이 더 가벼움

![](https://velog.velcdn.com/images/zioo/post/7689d3d1-24c0-4dba-a136-a438d269c215/image.png)

![](https://velog.velcdn.com/images/zioo/post/3150772b-d7a5-4c83-9dbe-ab3dd03980a0/image.png)

#### CPU 실행 단위 
= lightweight process ↔ heavyweight process 

-  Thread 구성 (공유하지 않는 것)
    - program counter
    - register set
    - stack space
- Thread 가 동료 thread와 공유하는 부분 (task)
    - code section
    - data section
    - OS resources
- 장점
    - 쓰레드 하나가 blocked 상태일 때 다른 쓰레드 실행 가능해서 **응답 시간 빠르다**
    - **자원 절약 :** 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상
    - 병렬성을 높일 수 있음

**PCB**

- 프로세스의 상태를 나타냄


※ CPU 수행 단위가 여러개 있게 되면, Stack도 별도로 가짐!
-----
 ![](https://velog.velcdn.com/images/zioo/post/84905da0-2003-4992-ad9f-a75520e228a7/image.png)
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 올 수 있음
- 위의 그림처럼, 프로세스는 하나만, 프로그램 카운터만 여러개 띄워놓음
- 프로세스 하나에 CPU 수행단위만 여러개 두고 있는 것이 쓰레드
- 다중 쓰레드로 구성된 태스크 구조에서는 하나의 서버 쓰레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있음
- 동일한 일을 수행하는 **다중 쓰레드가 협력하여 높은 처리율(throughput)**과 **성능 향상**을 얻을 수 있음
- **스레드를 사용하면 병렬성을 높일 수 있음**
    - 쓰레드를 이용하면 빠른 응답정 제공과 자원을 절약할 수 있는 장점이 있음
    - 같은 일을 하는 프로세스를 여러개 띄워놓게 되면, 각각의 메모리에 올라가야하므로 메모리 낭비가 심함
    - 하지만, 하나의 프로세스에 하나의 쓰레드만 놓게 되면, 성능 향상이나 자원 절약이 가능함
- Process는 하나기 때문에 PCB도 하나로 생성
- Process 안에 쓰레드가 여러개면, CPU 수행과 관련된 내용(program counter, registers)만 copy


-------
## Benefits of Threads

- Responsiveness (응답성)
    - 홈페이지 접속할 때 이미지 넣어서 사용자에게 보여줌 → 속도 빨라짐
- Resource Sharing
- Economy
    - 프로세스 하나 내에 쓰레드 추가 하는 거 오버해드가 적다
    - 프로세스 내의 thread간의 cpu switch는 간단
    - 프로세스 스위치는 오버해드 큼
- Utilization of MP Architectures
    - 멀티 프로세스 내에서 병렬 실행 가능

## Implementation of Threads

- Kernel Threads

- User Threads
    - library 지원을 받음
