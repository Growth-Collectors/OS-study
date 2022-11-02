[ [이화여대 운영체제 - 반효경 교수님 강의](http://kocw.net/home/cview.do?cid=3646706b4347ef09) ]
## System Structure & Program Execution

## 컴퓨터 시스템 구조

![](https://velog.velcdn.com/images/zioo/post/13dbefe3-78ab-4495-afb3-111fb0703001/image.png)
- **device controller**는 각 **io device들의 cpu 역할**을 하며 device들을 컨트롤하고, local buffer를 메모리처럼 사용한다.

- **cpu**는 매 클럭마다 메모리에서 instruction을 읽어서 실행하고, 실행 완료 시 프로그램 카운터 레지스터의 주소 값을 증가시킨 후 다음 instruction을 찾아 실행한다.

- **register** → cpu에서 임시 정보들을 저장하는 공간

- mode bit. → **cpu 안에서 현재 실행되는 instruction**이 운영체제 or 사용자 프로그램 중 어디로부터 왔는지 **구별**해준다.

- **interrupt line** → cpu가 현재 instruction을 실행 완료하고 kernel mode로 전환하게 끔 만들어 device들의 io 작업을 수행하도록 한다.
만약, 중간에 io작업도 없이 무한 루프를 도는 프로그램이 있다면? 계속해서 cpu를 독점하여 사용하게 된다. 이는** cpu의 time sharing을 구현하지 못하도록** 만든다. 이처럼 특정 프로그램이 cpu를 독점하는 것을 막기 위해, **timer**가 사용된다.
### Timer
![](https://velog.velcdn.com/images/zioo/post/3da80800-92a4-44d6-8ad3-29cb5c7ce663/image.png)

- cpu를 독점을 막기 위해 **time sharing**을 구현

- 처음 cpu가 켜졌을 때는 운영체제가 cpu를 가지고 있다가, 
사용자 프로그램이 시작되면 cpu를 넘겨주게 된다. 이때, timer에 해당 프로그램에 대한 세팅을 하고 넘겨준다. 그러면 사용자 프로그램이 cpu를 통해 instruction을 실행되다가도, 맞추어둔 **timer 세팅 시간이 완료되면 timer가 interrupt**를 걸어오고, cpu의 제어권이 사용자 프로그램에서 운영체제로 넘어간다.
- (timer 없이 interrupt를 발생시키는 경우) **사용자 프로그램이 io가 필요하면, 스스로 운영체제에 cpu를 넘겨준다**. 그러면 운영체제가 io작업을 device controller에 맡기고, 그동안 cpu는 다른 instruction을 실행하게 된다.
    
    ex) 만약 사용자 프로그램이 키보드에서 io를 받으려고 한다면 다음 순서로 입력을 받게 된다. 
    1. 사용자가 키보드 입력을 완료
    2. 키보드 컨트롤러가 input을 완료
    3. cpu에 interrupt를 건다. 
    4. cpu는 아까의 작업을 기억하고(pcb를 통함), input 내용을 copy하고, 해당하는 프로그램의 할당된 시간이 남았으면 제어권을 주거나, 시간이 다 되었으면 언젠가 해당 프로그램의 차례가 되었을 때 제어권을 준다.


### Mode bit
![](https://velog.velcdn.com/images/zioo/post/b3c5bb06-e24f-4ca4-9cfa-2d976ccd3f18/image.png)


- mode bit ⇒ **사용자 프로그램에 대한 프로그램 및 운영 체제의 보호를 위함.**
    - **mode bit 0** → 모니터(커널) 모드
        - 무슨 일이든 할 수 있다. 메모리 접근 뿐 아니라, io에도 접근할 수 있다.
        - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 **특권 명령**으로 규정한다.
        - interrupt나 exception 발생 시 mode bit은 0으로 변경된다.
    - **mode bit 1** → 사용자 모드
        - 제한된 instruction만 cpu에서 실행된다. **보안** 상의 목적이 크다.

### Device Controller
![](https://velog.velcdn.com/images/zioo/post/b5a9d1cf-9b44-49bc-9a48-6b471ddf812b/image.png)

- I/O device controller
    - **해당 I/O 장치유형을 관리하는 일종의 작은 CPU**
    
- 제어 정보를 위한 register란, cpu가 device controller에게 일을 시킬 때 사용되는 register를 뜻한다.
- local buffer은 device에서 담은 데이터를 memory에 넘기거나 데이터를 화면에 담아 출력할 때 사용 된다.
 > 어떤 데이터를 file에 저장하고 싶으면 데이터 자체는** local buffer**에 넣고, 데이터를 저장하라는 명령은 **register**를 통해서 cpu가** io 컨트롤러**를 통해 전달하게 된다.

----
**DMA Controller** (Direct Memory Access)

I/O로 인한 성능 감소 방지를 위해 CPU 개입 없이  **I/O 장치와 메모리 간 직접 데이터 전송 방식**

- main 메모리는 원칙적으로는 cpu만 접근 가능. io device들은 local buffer에 저장하고, 쌓이면 cpu의 register에 저장한다. **cpu는 메모리와 local buffer에 둘 다 접근**할 수 있다.
- **cpu는 너무 interrupt를 많이 당한다**. cpu 일하려고 하면 io device가 계속 interrupt를 걸게 된다. 그래서 DMA controller를 둔다.( **직접 메모리에 접근할 수 있는 컨트롤러**. 메모리를 cpu와 dma 둘 다 접근 가능. 그런데 둘 다 접근하면 문제 발생할 수 있음 ⇒ 메모리 컨트롤러가 이를 제어한다.
- dma의 역할은 io device의 interrupt가 왔을 때 모두 cpu에 가면 오버헤드가 크므로, dma가 이를 복사하여 메모리에 저장시키고, 메모리가 한 번만 interrupt를 걸어 **적은 cpu 간섭이 일어나도록 하는 것**이다.
### Main Memory

- CPU의 작업 공간이다



### CPU의 역할
**CPU는 매순간 메모리의 있는 기계어를 읽어와 실행을 하게된다**.

- Program Counter가 가르키고 있는 다음 명령어를 로딩한다.
- 특별한 일이 없으면 CPU는 다음 명령어를 수행한다.
- 제어문이나 특별한 명령어를 만나면 점프를 하거나, 반복한다.
- 즉 CPU는 아주 빠른 일꾼이라고 생각하면 된다.


#### 시스템콜(System Call)

- **사용자 프로그램은 운영체제에게 I/O 요청**
- **Trap**을 사용하여 인터럽트 벡터의 특정 위치로 이동
- 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
- 올바른 I/O 요청인지 확인 후 I/O 수행
- I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김


---------
### 입출력(I/O)의 수행
![](https://velog.velcdn.com/images/zioo/post/818e43ef-79de-455a-b4ac-e5d652d66005/image.png)

- 모든 입출력 명령은 **특권 명령**
- 사용자 프로그램은 어떻게 io를 하는가?
    - 운영체제에 부탁 | **시스템 콜**
    - **프로그램이 직접 interrupt line에 interrupt를 보냄** -> cpu 제어권이 운영체제로 넘어가고, disk controller에 부탁할 수 있게 된다.
- 하드웨어 device들이 cpu한테 정보교신을 위해 interrupt를 걸 수도 있고, 사용자 프로그램이 diskio를 위해 운영체제에 요청하기 위해 interrupt line를 세팅하여 운영체제로 넘길 수도 있다. 후자가 **소프트웨어 인터럽트, trap**이라고 한다. 일반적으로 인터럽트라고 하면 하드웨어가 거는 인터럽트를 말한다. 넓은 의미는 둘 다 포함.
- 시스템 콜 → trap을 사용하여 인터럽트로 특정 주소로 cpu 실행을 옮김.
- 올바른 요청일 경우 io컨트롤러는 일을 한다. 그리고 io가 끝났을 때, 하드웨어가 일 다 끝냈다고 인터럽트를 건다.

---------
### Interrupt

- CPU가 프로그램을 실행하고 있을 때, 입출력 하드웨어 등의 장치에서 이슈나 예외 상황이 발생하여 처리가 필요할 경우에 CPU에 알려서 처리하는 기술

- 인터럽트 당한 시점의 레지스터와 program counter를 save한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다.

**Interrupt(넓은 의미)**
- **하드웨어 인터럽트 : 하드웨어가 발생시긴 인터럽트**
- **Trap** (소프트웨어 인터럽트)
    - Exception: 프로그램이 오류를 범한 경우
    - **System call:** 프로그램이 커널 함수를 호출하는 경우


**Interrupt 관련 용어 ** (in OS)

- 인터럽트 벡터 (주소들의 테이블)
    - 해당 인터럽트의 처리 루틴 주소를 가지고 있음 (ex, 키보드 인터럽트, 타이머 인터럽트) 
- 인터럽트 처리 루틴
    - (=interrupt Service Routine, 인터럽트 핸들러)
    - 해당 인터럽트를 처리하는 커널 함수

`현대의 운영체제는 인터럽트에 의해 구동됨` 

![](https://velog.velcdn.com/images/zioo/post/3f279a17-6cbb-4914-99ff-912bbbdc3537/image.png)
- 프로그램 끝 → timer
- io 끝 → device controller
---------
### System Call

- 사용자 프로그램이 운영체제 코드를 수행하는 것이 불가능 → **운영체제의 서비스를 받기 위해 커널 함수 호출**

컴퓨터 시스템 구조, 인터럽트(Interrupt), 동기식 입출력과 비동기식 입출력, 시스템콜(System Call), DMA(Direct Memory Access), 서로 다른 입출력 명령어, 저장장치 계층 구조, 프로그램의 실행(메모리 load), 커널 주소 공간의 내용, 사용자 프로그램이 사용하는 함수, 프로그램의 실행
 
![](https://velog.velcdn.com/images/zioo/post/9ed96ca3-b421-4c82-adc5-14e55e41fb5b/image.png)


### 컴퓨터 시스템 구조

### Interrupt

- 인터럽트 당한 시점의 레지스터와 program counter를 save한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다.
- 하드웨어 인터럽트 : 하드웨어가 발생시긴 인터럽트
- Trap (소프트웨어 인터럽트)
    - System Call : 프로그램이 커널 함수를 호출하는 경우
    - Exception

Interrupt 관련 용어 

- 인터럽트 벡터
    - 해당 인터럽트의 처리 루틴 주소를 가지고 있음
- 인터럽트 처리 루틴

### System Call

- 사용자 프로그램이 운영체제 코드를 수행하는 것이 불가능 → **운영체제의 서비스를 받기 위해 커널 함수 호출**

### 동기식 입출력과 비동기식 입출력
![](https://velog.velcdn.com/images/zioo/post/2fad4f01-25b3-4576-99d3-3b3fd45fece2/image.png)

- Synchronous I/O 동기식
- Asynchronous I/O 비동기식

synchronous → 시간을 맞추는 것.
ex) 립싱크 - 노래와 입술 모양을 맞추는 것.
![](https://velog.velcdn.com/images/zioo/post/67ffe49f-d9c6-4909-951f-03613220d45d/image.png)
- write는 async 하게 작동되는 것이 자연스러움. 만약 sync하게 작업시키고 싶을 때 sync 쓸 수도 있음. 구현하기 나름인 것.
- 그런데 io는 비교적 (cpu 작업에 비하면) 오래 걸리는 작업이므로 이를 기다리면 cpu가 낭비 될 수 있다. 그래서 다른 동기 프로세스에 cpu를 넘기게 된다. 이것이 위에 있는 구현 방법 2이다. 동기식 입출력이 보통 이 방식으로 구현된다.
- io 작업이 끝나면 cpu에 인터럽트 걸게 된다. 그런데 io 작업이 쉴새 없이 쏟아진다면..?
### DMA (Direct Memory Access)
![](https://velog.velcdn.com/images/zioo/post/15c785d9-7b6d-4e6b-8b8d-2357afc6238f/image.png)

- **빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용**
  - 빠른 입출력 장치일 경우 interrupt를 더 많이 걸 것이므로 중간에 DMA 컨트롤러가 꼭 존재해야 한다.
- 문제: io device로 인한 cpu로의 interrupt가 너무 많아진다면 => cpu가 효율적으로 동작을 할 수 없다.

  ⇒ DMA도 메모리에 접근할 수 있게 해서 IO 작업들을 메모리에 쌓고, 어느 정도 쌓였으면 cpu에 interrupt를 걸어 작업을 하도록 한다.
### IO를 할 수 있는 방법 2가지

![](https://velog.velcdn.com/images/zioo/post/4d308bdd-21ab-4e0f-ba7b-c9971665e083/image.png)

- 왼쪽은 **일반적인 IO **방식이다. 메모리 접근해야 하는 instruction과, 각 io device별로 접근하는 instruction이 각자 따로 있다.
- 오른쪽은 **Memory Mapped I/O **방식으로, io device에다가 메모리 주소의 연장 주소를 붙여서 접근한다.
### 저장 장치 계층 구조

 ![](https://velog.velcdn.com/images/zioo/post/9a71dd2f-0e6b-4278-980c-4be7900daab2/image.png)
 
- 위로 갈수록 빠르고, 비싸고, 용량이 적고, 휘발성이다.
-** primary 영역 **→ cpu가 접근해서 실행이 가능한** 바이트 단위**로 접근이 가능하다. 바이트 단위로 접근이 가능하다.
- **secondary 영역** → byte 단위가 아닌 **섹터 단위**로 접근이 가능하다. executable하지 않은 매체이다.
-** Caching** → 빠른 매체로 정보를 읽어 들여 놓아 사용하는 것이다. 그러나 용량이 작으므로 어떤 것을 지우고 할 지가 주요 이슈가 된다.

### 프로그램이 실행되면 
![](https://velog.velcdn.com/images/zioo/post/fbed0969-e81e-478f-aaf6-be8129ef562b/image.png)


### 커널 주소 공간의 내용

- stack
    - Process A의 커널 스택
    - Process B의 커널 스택
- data
    - 운영체제가 사용하는 자료구조가 들어있음
- code
    - 자원 관리를 위한 코드
    - 시스템콜, 인터럽트 처리 코드
    - 편리한 서비스 제공을 위한 코드

### 사용자 프로그램이 사용하는 함수

**프로세스 Address**

- 사용자 정의 함수
    - 내가 직접 작성한 함수
- 라이브러리 함수
    - 가져다 쓸 수 있는 함수
    

**커널 Address** 

- 커널 함수
    - 운영체제 프로그램의 함수
