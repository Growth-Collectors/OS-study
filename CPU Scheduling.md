## CPU and I/O Bursts in Program Execution

-   CPU만 연속적으로 쓰는 단계 : CPU burst
-   I/O를 실행하는 단계 : I/O burst

## CPU-burst Time의 분포

![](https://k.kakaocdn.net/dn/bjviWn/btrBQEuENR3/PalKQs05yguYS3dxk849vK/img.png)

-   **여러 종류의 job**(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요하다
    -  특히 io bound job은  Interactive job이라 응답시간이 길면 대기가 너무 길어짐
    -   CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용하기 위해 
   - 누구에게 줄 것인가?
   - 얼마나 오래 줄 것인가?

## 프로세스의 특성 분류

-   프로세스는 그 특성에 따라 다음 두 가지로 나눔
    -   **I/O-bound process**
        -   CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
        -   (many short CPU bursts)
    -   **CPU-bound process**
        -   계산 위주의 job
        -   (few very long CPU bursts.)

## CPU Scheduler & Dispatcher

-   **CPU Scheduler**
    -   Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다
-   **Dispatcher**
    -   CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
    -   이 과성을 context switch(문맥 교환)라고 한다
-   CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
    1.  Running -> Blocked (예 : **I/O** 요청하는 시스템 콜)
    2.  Running -> Ready (예 : 할당 시간 만료로 **timer interrupt**)
    3.  Blocked -> Ready (예 : I/O 완료 후 인터럽트)
    4.  Terminate
-   1, 4에서의 스케줄링은  **nonpreemptive(=강제로 빼앗지 않고 자진 반납)**
-   다른 스케줄링은  **preemptive(=강제로 빼앗음)**

## Scheduling Criteria(성능 척도)



 **1.  시스템 입장에서 성능**
	    

 -  효율과 양이 좋으면 좋음!
 -   **CPU utilization**(이용률)
	 - CPU가 일한 시간 / 전체 시간
      - CPU를 **가능한 바쁘게** 일을 시켜라
    -   **Throughput**(처리량)
	    - 주어진 시간 동안 작업을 완료한 양


**2. 프로세스(고객) 입장에서 성능**

 - 빨리 받아서 빨리 처리되면 좋음!
 -  **Turnaround time**(소요시간, 반환시간)
	 - 쓰러 들어와서, 다 쓰고  나갈 때까지 걸린 시간
	 - Ready Queue에서 기다린 시간 + Running Time + (선점형 스케줄링에서 CPU를 뺏겨 대기한 시간)
 - **Waiting time**(대기 시간)
   	    - 순수하게 기다린 시간
         - Ready Queue에서 기다린 시간 + (선점형 스케줄링에서 CPU를 뺏겨 대기한 시간)
 - **Response time**(응답 시간)
   	    - 들어와서 **처음으로 받기까지**의 시간
         - Ready Queue에 들어와서 처음으로 CPU를 얻기까지의 시간

        
        
   - ***Waiting time vs Response time***
        - 선점형 스케줄링의 경우는 얻었다 뺏겼다를 반복 = waiting time이 여러 세트
        - 반면 이때 response time은 처음으로 받기까지의 대기 시간 


> cf) 총 시간?, 총 처리량?
>  - 프로세스 시작~종료 X 
>  - CPU 쓰러 들어왔다가 I/O하러 나가기까지의 시간
## Scheduling Algorithms

### 1. FCFS (First-Come Fist-Served)

-   프로세스 도착 순서대로 처리한다.
-   **비선점형 스케줄링**
- 먼저 온 고객한테 먼저 드릴게요!
- 그다지 효율적이지는 않음
	- 짧게 쓸 건데도 늦게 오면 오래 기다려야 함..  

#### FCFS 예시
- P1: 24s, P2: 3s, P3:3s 소요됨  

1. **길게 쓰는 프로세스가 먼저 도착한 경우** 
![](https://k.kakaocdn.net/dn/cEMW6h/btrBXvKM203/RKWBP2oRPQmnye24nQihK1/img.png)
 
	-  Waiting Time : P1 = 0, P2 = 24, P3 = 27
	-   Average Waiting Time = (0 + 24 + 27) / 3 = 17
- **Convoy effect**  : 짧은 프로세스가 긴 프로세스 뒤에 있는 현상
	- 호위 효과: 호위하는 병사들은 오래 생존할 수 있다(전쟁 순간까지 대기 시간이 길다)는 뜻에서 유래 

2. **짧게 쓰는 프로세스가 먼저 도착한 경우**

![](https://k.kakaocdn.net/dn/9yMkc/btrBUTEQ5iq/RcF8s0Wb0ZrnvrFJTzKKT1/img.png)

  
	- Waiting Time : P1 = 6, P2 = 0, P3 = 3
	-   Average Waiting Time = (6 + 0 + 3) / 3 = 3
	

### 2. SJF (Shortest-Job-First)

-   각 프로세스의 다음번  **CPU burst time**을 가지고 스케줄링에 활용
-   **CPU burst time**이 가장 짧은 프로세스를 제일 먼저 스케줄

-   Two schemes
    -   **Nonpreemptive**
        -   일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점(preemption) 당하지 않음
        - 더 짧은 애가 와도 얄짤없음! 
        
    -   **Preemptive**
        -   현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
        -   **Shortest-Remaining-Time-First** (SRTF)이라고도 부른다
        - 남은 시간이 가장 짧은 프로세스에게 할당하기 때문
-   ***SJF는 최적이다=Preemptive ver만 의미***
    -   주어진 프로세스들에 대해  **minimum average waiting time**을 보장
   - **SJF 단점**
	    -   **Starvation**(기아 현상)
		    - *영원히 차례가 오지 않는 경우*도 발생 
	    -   CPU Burst Time의 **예측이 불가**
	        -   추적(estimate)만 가능
	        -   과거의 CPU burst time을 이용해서 추정
            -   exponenitial averaging 방법
		   <img width="226" alt="image" src="https://user-images.githubusercontent.com/102589253/197930686-c84c41a4-5fc3-45b6-af5d-fb794c7e9d54.png">

#### SJF 예시
-   **Nonpreemptive**
![](https://k.kakaocdn.net/dn/cOCmRP/btrB0PUXHwO/M4I70LHjCdy8e06up3Lxq1/img.png)
	- 0초 시점에 도착한 프로세스가 P1뿐
	- 다음 스케줄링 시 가장 짧은 프로세스를 골라 실행
	- - CPU 스케줄링이 **프로세스가 CPU사용을 완료한 시점**에 이루어짐


- **Preemptive**
![](https://k.kakaocdn.net/dn/oly95/btrBZx1IJ45/gAALToGdifjkimvm0WHZ7k/img.png)

	- P1이 7초를 쓰고 싶었지만, 2초 경과 후 P2도착
	- P2는 4초를 쓰려 하고, P1은 5초 남았으므로 P2 선택
	- 이하 반복
	- CPU 스케줄링이 **새로운 프로세스가 도착한 시점**에 이루어짐
	- **optimal average waiting time**
	


### Priority Scheduling

-   우선순위가 높은 프로세스에게 CPU 할당
-   SFJ는 일종의 Priority Scheduling이다
    -   Priority = CPU Burst Time
-   문제
    -   Startvation(기아 현상) : 우선순위가 낮은 프로세스는 영원히 프로세스를 얻지 못할 수 있다.
-   해결
    -   Aging : 대기하는 프로세스가 시간이 지날수록 우선순위를 높여줌

### Round Robin(RR)

-   각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐(일반적으로는 10~100ms)
-   할당 시간이 지나면 프로세스는 선점(preempted)당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다
-   n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻는다
    -   어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다.
-   성능
    -   q가 클 경우 ==> FCFS
    -   q가 적을 경우 ==> context switch 오버헤드가 커진다.

![](https://k.kakaocdn.net/dn/c0izfQ/btrBZ2ApU9Z/tnwrcHhC0AB49DekUZ88b1/img.png)

q가 20일 경우

-   일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧다.
