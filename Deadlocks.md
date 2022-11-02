## Deadlocks (교착상태)
교착상태(Dead Lock)은 상호 배제에 의해 나타나는 문제점으로, 

둘 이상의 프로세스들이 자원을 점유한 상태에서 **서로 다른 프로세스가 점유하고 있는 자원을 요구하며 무한정 기다리는 현상**을 의미


![](https://velog.velcdn.com/images/zioo/post/9c4d537c-8164-4375-869d-38adf4343479/image.png)
## Deadlock Problem

#### Deadlock
- 일련의 프로세스들이 서로가 가진 자원을 기다리면서 block된 상태

#### Resource(자원)
- 하트웨어 소프트웨어 등을 포함하는 개념
  - I/O device, CPU cycle, memory space, semaphore
- 프로세스가 자원을 사용하는 사용절차
  - Request(요청)-> Allocate(자원획득) -> Use(사용) -> Relase (자원 반납)
#### Example
![](https://velog.velcdn.com/images/zioo/post/4d9f3e4f-702b-468f-a1c7-eef936d23718/image.png)

 
## Deadlock 발생의 4가지 조건
- **Mutual exclusion **
  - 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
- ** No preemption**
  - 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
- **Hold and wait (보유대기)**
  - 내가 가진 자원을 놓지 않으면서 다른 자원을 기다림 
- ** Circular wait (순환 대기)**
  - 자원을 기다리는 프로세스간에 사이클이 형성되어 있어야 함 
  
## Resource - Allocationn Graph 자원 할당 그래프 
![](https://velog.velcdn.com/images/zioo/post/1c1c648e-59d2-4779-b2f4-37b8a320f7c5/image.png)

- 작은 점 -> 자원의 instance
- cycle 이 없음  


![](https://velog.velcdn.com/images/zioo/post/aba7dca8-7ba2-48f6-a422-a8db93f7e382/image.png)
- cycle 이 있음 
- 자원의 instance가 여러 개 있으면 deadlock 일 수도 아닐 수도 있다 
- 1 graph :  deadlock O
- 2 graph :  deadlock X
  - 4번 프로세스가 다 사용하고 나서 반납하면 dead lock 사라지기 때문 

## Deadlock의 처리 방법
![](https://velog.velcdn.com/images/zioo/post/6dd52c4c-df8b-4a26-96ed-c6e33d312949/image.png)
Deadlock Ignorance
- deadlock이 자주 발생하지 않기 때문에 현대 컴퓨터 에서는 미연에 방지하지 않음 

### 1. Deadlock Prevention
![](https://velog.velcdn.com/images/zioo/post/c937e952-6035-4e8c-8286-0a36e3a46340/image.png)
- Mutual Exclusion (필수) -> 배제해서는 안되는 조건임
- Hold and Wait
  - 자진 반납
- No Preemption
  - 자원을 빼앗을 수 있게 한다. (CPU,memory) (자원 상태 저장)
- 생기지도 않을 deadlock 문제를 미리 방지하고 있어서 비효율적

### 2. Deadlock Avoidance
Deadlock 발생 가능성 계속 검사. 발생 가능성이 있다면 회피.

![](https://velog.velcdn.com/images/zioo/post/35173573-ac7d-4795-8cc9-624039372059/image.png)

![](https://velog.velcdn.com/images/zioo/post/67566633-4774-4763-b6ff-de24abcf435b/image.png)

- Safe state : Deadlock이 발생하지 않는 상태 
- Unsafe state : Deadlock 발생 가능성 있는 상태 (100%는 아님)

Safe state에 있던 프로세스는 언제든지 Unnsafe state로 넘어갈 수 있으며, 이것을 막아야 한다.

#### Deadlock을 회피하기 위한 알고리즘 2가지 
1) 자원이 하나 종류일 때 -> 자원 할당 그래프 
2) 자원이 여러 종류일 때 -> Banker's Algorithm 사용

일단 할당했다고 가정하고 Deadlock이 발생하는지 확인해 실제로 Deadlock이 발생하는 걸 막는다는 공통점이 있음 

#### Resource Allocation Graph algorithm
자원이 한 종류일 때 

![](https://velog.velcdn.com/images/zioo/post/ebc536d2-7267-467d-814d-3c62451b4626/image.png)

- 점선화살표
  - 프로세스가 새로 자원을 요구하는 것 
  - 프로세스가 평생에 적어도 한 번은 이 자원을 요청
- 3 graph 
  - deadlock 이 아님 
  - 점선이 실선이 되면(자원 요청하면) deadlock 이 생김 
  
- **deadlock 위험성이 있으면 애초에 자원을 주지 않음** 

#### Banker's Algorithm
자원이 여러 종류일 때 
![](https://velog.velcdn.com/images/zioo/post/8aefcd90-a42e-480f-8fb6-66460b8f22a2/image.png)

![](https://velog.velcdn.com/images/zioo/post/9fd181ae-02d2-4a94-9cdb-db6368d3c86e/image.png)
- 자원당 instance 여러 개인 경우 이 알고리즘 통해 dead lock을 막는다 
- Allocation : 현재 할당 중인 자원
- Max : 프로세스가 작업을 끝내기 위해 필요한 총자원
- Available : 현재 빌려줄 수 있는 자원의 양
- Need : 추가로 요청할 수 있는 양 

- 안전상태를 유지할 수 있는 요구만을 수락하고 불안전 상태를 초래할 사용자의 요구는 나중에 만족될 수 있을 때까지 계속 거절한다.
![](https://velog.velcdn.com/images/zioo/post/70435882-0001-43fd-ba01-fc0d40089587/image.png)

- p0이 b 2개 요청 -> 남아있는 양 있지만 추가요청 (7,4,3) 임 -> 남아있는 것 가지고 충족 x / 혹시 최대 요청을 할까봐 
- **최악의 경우를 고려** (7,4,3) 
- 보수적 알고리즘
- 가용자원 (0,0,0) 되면 무조건 deadlock은 아님 -> 다른 애가 반납할 수 있음 

### 3. Deadlock Detection and recovery
- Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover 


![](https://velog.velcdn.com/images/zioo/post/8e7f2d66-86cd-42c3-9ddb-37566c0291d5/image.png)

![](https://velog.velcdn.com/images/zioo/post/8be04ca1-4bdf-4eb4-a928-448d6ccf182f/image.png)

싸이클을 찾는 오버헤드는 얼마인가? 

O(n**2) -> n(n-1)

![](https://velog.velcdn.com/images/zioo/post/a93a7dd3-4a61-4bd8-b51a-21d43015c886/image.png)
- Resource 당 multiple instance 인 경우 
- 낙관적 관점으로 본다


![](https://velog.velcdn.com/images/zioo/post/2cb90e58-20a6-4ba0-a956-7ae41047f5c2/image.png)

p2 가 자원 c 하나를 요청

**deadlock 이 있는지 찾아볼 때 **
- 가용자원으로 처리할 수 있는지 보기
- 요청하지 않은 프로세스 (Request) 가용자원으로 합치기 -> P0 같은 경우
- 거기서 처리할 수 있는지 체크 


![](https://velog.velcdn.com/images/zioo/post/4f0d57ed-46e5-4b75-b944-6824b85aefc6/image.png)

Deadlock이 발견이 되면 Recovery 해야함

1. Process termination
    - 데드락에 연루된 프로세스 종료
  2. Resource Preemption
    - 데드락에 연루된 프로세스로 부터 자원 뺏기  


### 4. Deadlock Ignorance 
![](https://velog.velcdn.com/images/zioo/post/f4d74c51-020a-4086-ba87-a6c17d75f6c8/image.png)

- 사용자가 deadlock을 알아서 처리
- OS는 deadlock에 대해 대처하지 않는다. (현대의 대부분 운영체제)







