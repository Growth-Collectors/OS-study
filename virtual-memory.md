

# Virtual Memory 1

지난 시간 

- 물리메모리의 주소변환은 OS가 관여하지 않음
- Virtual Memory 기법은 전적으로 OS가 관여하고 있음

## Demand Paging

요청이 있을 때 page를 메모리에 올림

![](https://velog.velcdn.com/images/zioo/post/7aa3369e-d71d-4d17-8aef-6e3cfadb5c35/image.png)


### Memory에 없는 Page의 Page Table

![](https://velog.velcdn.com/images/zioo/post/3d1bd7b7-7bf1-4844-8807-097e1e5af7fd/image.png)

- page fault = 1번 page 메모리에 없음
- Disk , Packing store , Swap area = 원기둥

### Page Fault

- **invalid** 에 접근하면 **MMU** (주소 변환 처리해주는 하드웨어)가 **trap** 발생 시킴

물리적인 메모리의 주소 변환은 운영체제가 관여하지 않는다. 그에 반에 virtual memory 기법은 전적으로 운영체제가 관리하고 있다.

가상 메모리라는 것은 프로세스 전체가 메모리 내에 올라오지 않더라도 실행이 가능하도록 하는 기법이다. 가상 메모리는 물리 메모리로부터 논리 메모리를 분리해, 메인 메모리를 균일한 크기의 저장 공간으로 구성된 엄청나게 큰 배열로 추상화 시켜준다. 이 기법을 통해 프로그래머는 메모리의 크기 제약으로부터 자유로워지고, 공유 메모리 구현도 가능하게 해주며, 프로세스 생성을 효율적으로 처리할 수 있는 기법도 제공한다.

그러나 이러한 가상 메모리는 구현하기 어렵고, 만약 잘못 사용하게 되면 성능이 현저히 저하될 수 있다.

# Demand Paging

말 그대로 **요청(demand)이 있으면 그 페이징을 메모리에 올리겠다는 것**(페이징 기법을 사용한다고 가정. 실제에서도 페이징 기법이 많이 사용됨).

즉, 실제로 필요할 때 page를 메모리에 올리는 것 (접근되지 않은 페이지는 무리 메모리로 적재되지 않음)

- I/O 양의 감소
- Memory 사용량 감소
- 빠른 응답 시간
- 더 많은 사용자 수용

#### Valid/Invalid bit의 사용

- Invalid의 의미 - 사용되지 않는 주소 영역인 경우 / 페이지가 물리적 메모리에 없는 경우
    - 유효 비트 - 해당 페이지가 메모리에 있다는 의미
    - 무효 비트 - 해당 페이지가 유효하지 않거나(즉, 가상 주소 공간상에 정의되지 않았거나) 유효하지만 보조저장장치에 존재한다는 것
- 처음에는 모든 page entry가 invalid로 초기화
- address traslation 시에 invalid bit이 set 되어 있으면 **"page fault"(요청한 페이지가 메모리에 없는 경우) →** CPU는 자동으로 운영체제에게 넘어감(page fault trap이 걸린다라고 표현)

![https://blog.kakaocdn.net/dn/cBTGsM/btryb17WwMS/RM9DBTfcKs6AmKf5hPqra1/img.png](https://blog.kakaocdn.net/dn/cBTGsM/btryb17WwMS/RM9DBTfcKs6AmKf5hPqra1/img.png)

### 메모리에 없는 page의 page table

- 하나의 프로그램을 구성하는 논리적인 메모리는 여러개의 페이지들로 구성
- 페이지 테이블을 통해 주소 변환 정보가 저장됨
- 디스크에서는 swap area를 볼 수 있음
- 처음 프로그램이 구동이 되면, page table에는 모두 invalid bit만 올라가 있을 것임
- 당장 필요한 페이지는 demand paging에 의해 물리 메모리에 올라가 있고, 그렇지 않은 것들은 backing store에 올라가 있음
- A, C, F는 현재 쓰이고 있기 때문에 valid bit으로 올라가 있지만, 나머지 페이지들은 backing store에 내려가 있기 때문에 invalid bit으로 되어있음
- 위 그림은 어떤 페이지를 무효로 설정하는 것은 그 페이지를 접근하기 전까지는 어떠한 영향도 끼치지 않는 다는 점을 보여줌

---

# Page Fault

**Invalid page를 접근하면, MMU(주소 변환 처리해주는 하드웨어)가 trap을 발생**시킴(= page fault trap)

Kernel mode로 들어가서 page fault handler가 invoke됨

- 다음과 같은 순서로 page fault를 처리한다.
1. Invalid reference? (eg. bad address, protection violation) → abort process
2. Get an empty page frame. (빈 페이지가 없으면? 뺏어온다 : Replace)
3. 해당 페이지를 disk에서 memory로 읽어온다.
    1. disk I/O가 끝나기까지 이 프로세스는 CPU를 선점당함(block)
    2. disk read가 끝나면 page tables entry 기록, valid/invalid bit = "valid"
    3. ready queue에 process를 insert → dispatch later
4. 이 프로세스가 CPU를 잡고 다시 running
5. 아까 중단되었던 instruction 재개

![https://blog.kakaocdn.net/dn/btV0LF/btrydfyaHYR/uc7Nhbicat7ckguMPfbi7K/img.png](https://blog.kakaocdn.net/dn/btV0LF/btrydfyaHYR/uc7Nhbicat7ckguMPfbi7K/img.png)

#### Steps in handling a page fault

1. 메모리 reference가 있었는데, 주소 변환을 하고자 하니 invalid 표시가 되어 있었음.(이 페이지가 메모리에 올라와 있지 않음)
2. trap이 걸려, CPU가 자동으로 운영체제에게 반환
3. 운영체제는 backing store에 있는 페이지를 물리적인 메모리에 올려 놓음
4. 만약 빈 프레임이 없으면, 쫓아내서라도 물리적인 메모리에 올려놓음
5. 올려놓는 작업이 다 끝나면, 해당하는 프레임 번호를 엔트리에다 적어놓고, invalid였던 것을 valid로 바꿈
6. CPU를 다시 얻어서 주소 변환을 하면, valid로 되어있고, 주소변환을 정상적으로 해서 원하는 물리적인 메모리의 프레임에 접근

---

# Performance of Demand Paging

![https://blog.kakaocdn.net/dn/bZlCQ2/btrygugiNDI/kCL83N807SJIZHAY1krdP0/img.png](https://blog.kakaocdn.net/dn/bZlCQ2/btrygugiNDI/kCL83N807SJIZHAY1krdP0/img.png)

- page fault가 발생했을 때, disk로 접근하는 것은 굉장히 오래 걸리는 작업
- page fault가 얼마나 크게 났는냐에 따라 메모리 접근 시간이 달라짐
- 대부분의 경우는 page fault가 일어나지 않고, 메모리로 부터 직접 주소변환을 할 수 있음 : (1−p)
- 그 외의 경우에 page fault가 발생하여 disk 접근을 해야하는 것(overhead가 큼) : p
    
    

---

# Free Frame이 없는 경우

**Page Replacement**

- OS가 하는 업무
- 요구 페이징의 기본
- **어떤 frame을 빼앗아올지 결정**해야 함
- 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
- 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음

#### Replacement Algorithm

- page-fault rate을 **최소화**하는 것이 목표(가급적 0에 가깝도록)
- 알고리즘의 평가 - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사(페이지 폴트 발생 횟수를 계산하여 평가)
- reference string(메모리 주소의 나열)의 예
    
    1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5
    

![https://blog.kakaocdn.net/dn/2LLQF/btryg4houND/muP2dDkOQlEkQxRyohAFCk/img.png](https://blog.kakaocdn.net/dn/2LLQF/btryg4houND/muP2dDkOQlEkQxRyohAFCk/img.png)

**Page Replacement**

1. 희생자(victim)가 선정되면, 디스크로 쫓아버림. (만약, 희생자가 내용이 변경됐다면, backing store에 변경된 내용을 써줘야함.)
2. 쫓겨난 페이지에 대해서는 invalid로 바꿔줌
3. 새로운 메모리를 물리적 주소에 올려줌
4. 해당 메모리를 valid로 바꿔줌

---

# Optimal Algorithm

![https://blog.kakaocdn.net/dn/o9bRu/btryg4VZTmK/F3fMR6czdW5ZxiAHyJby3K/img.png](https://blog.kakaocdn.net/dn/o9bRu/btryg4VZTmK/F3fMR6czdW5ZxiAHyJby3K/img.png)

- 알고리즘 중에서 제일 좋은 알고리즘으로, **page fault를 가장 적게 함**
- 하지만, 시스템에서는 미래를 알 수 없음. optimal algorithm은 미래를 다 알고 있다고 가정한 상태에서 실행하기 때문에, 실제 시스템에서는 사용될 수 없음.
- Offline optimal algorithm이라고도 함
- 가장 먼 미래에 참조되는 page를 replace 하는 방법으로, 5번 요청이 되었을 때, 메모리에 없기 때문에 page fault가 나고 4가 가장 먼 미래에 참조될 것이라는 걸 알기에 쫓겨나는 것(replace)을 볼 수 있음
- 빨간색 - page fault 가 된 경우, 연보라색 - 메모리에서 직접 참조가 된 경우
- 아무리 좋은 알고리즘을 만들어도 optimal algorithm보다 성능이 좋을 수는 없음
- 알고리즘은 실제 구현이 어려움

---

# FIFO(First In First Out) Algorithm

![https://blog.kakaocdn.net/dn/rrZ6H/btryg0FYwjn/WYFgHfAumlCk2XRLe4vj8K/img.png](https://blog.kakaocdn.net/dn/rrZ6H/btryg0FYwjn/WYFgHfAumlCk2XRLe4vj8K/img.png)

- 가장 **간단한** 페이지 교체 알고리즘
- 어떤 페이지를 교체해야 할 때, 메모리에 올라온 지 가장 오래된 페이지를 내쫓음
- **FIFO Anomaly** - 메모리 frame 수를 늘렸는데, 오히려 page fault수가 늘어나는 (기이한) 현상

---

# LRU(Least Recently Used) Algorithm

![https://blog.kakaocdn.net/dn/biqj3X/btrydImyGzd/2kOJ0kKKQKpQnIt8lzreq0/img.png](https://blog.kakaocdn.net/dn/biqj3X/btrydImyGzd/2kOJ0kKKQKpQnIt8lzreq0/img.png)

- **가장 오랜 기간 동안 사용되지 않은 페이지를 교체**하는 것
- 페이지마다 마지막 사용 시간을 유지
- 가장 오래전에 참조된 것이 3번 이므로, 3번을 지우는 것을 확인
- 가장 먼저 들어온 1번을 지우는 FIFO 알고리즘과의 다른 점 확인

---

# LFU(Least Frequently Used) Algorithm

![https://blog.kakaocdn.net/dn/mJ6we/btrye25Gn0s/3bxWthRUSM4QJmhUkQ66RK/img.png](https://blog.kakaocdn.net/dn/mJ6we/btrye25Gn0s/3bxWthRUSM4QJmhUkQ66RK/img.png)

- **참조 횟수가 가장 적은 페이지를 교체**하는 것

---

# LRU와 LFU 알고리즘 예제

![https://blog.kakaocdn.net/dn/d8EyfW/btryfZ8HaNi/DSmLUOdUN984iTTkPqfCI0/img.png](https://blog.kakaocdn.net/dn/d8EyfW/btryfZ8HaNi/DSmLUOdUN984iTTkPqfCI0/img.png)

- LRU 알고리즘은 현재 시점부터 마지막 시점까지를 보았을 때, 4번 페이지가 가장 최근에 쓰였으므로 우선순위가 제일 높고, 1번 페이지가 제일 오래 전에 참조되었기 때문에 1번 페이지를 삭제하려고 할 것
- LFU 알고리즘은 참조횟수가 가장 높은 1번 페이지를 최우선시하고, 4번 페이지가 참조횟수가 제일 적으므로 4번 페이지를 삭제함

---

# LRU와 LFU 알고리즘의 구현

![https://blog.kakaocdn.net/dn/d8j4Mg/btrye3Duqgl/Vx3aD3nvCddKKRHstAuu10/img.png](https://blog.kakaocdn.net/dn/d8j4Mg/btrye3Duqgl/Vx3aD3nvCddKKRHstAuu10/img.png)

- LRU 알고리즘은 참조 시간의 순서에 따라 줄세우기를 하며, **링크드 리스트 형태**로 운영체제가 시간 순서를 관리하게 됨. 만약 어떤 페이지가 들어오거나 재참조를 하게 되면, 무조건 참조시점이 제일 최근 시점에 쓰인 것으로 제일 아래쪽으로 보내면 됨
- LRU 알고리즘은 시간 복잡도가 O(1)로 쫓아내기 위해 비교 연산이 필요하지 않다는 것을 의미함
- LFU 알고리즘은 한 줄로 줄세우기가 애매함. 또, 한 줄로 줄세우기 하면, O(n)의 시간이 소요됨

![https://blog.kakaocdn.net/dn/bbuWHQ/btryd8ehgWq/kS9IkT88jifi5V3QlVsOk0/img.png](https://blog.kakaocdn.net/dn/bbuWHQ/btryd8ehgWq/kS9IkT88jifi5V3QlVsOk0/img.png)

LFU 알고리즘

- 때문에 LFU 알고리즘은 **Heap으로** 구현을 하고, 이로 O(log n)의 시간으로 줄일 수 있음. 이로써 참조횟수가 적은 것이 들어와도 자리바꿈이 수월하게 이루어질 수 있음

---------

# Virtual Memory 2 


## 다양한 캐싱 환경

###  **캐싱 기법**

- **한정된 빠른 공간(=캐시)** 에 요청된 데이터를 저장해 두었다가 후속 요청 시 캐시로부터 직접 서비스하는 방식
- 가능하면 **물리적 메모리**에서 해결을 하고, page fault가 나면 처리 속도가 느린 backing store을 참조하는 것
- Paging system 외에도 cache memory, buffer caching, Web caching 등 다양한 분야에서 사용 (LRU, LFU 사용 가능)

###  **캐시 운영의 시간 제약**
- 교체 알고리즘에서 삭제할 항목을 결정하는 일(replacement)에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
- **Buffer caching** 이나 **Web caching**의 경우
    - **O(1)** 에서 **O(logn)** 정도까지 허용
        
* 프레임: 물리 메모리를 일정한 크기로 나눈 블록
* 페이지: 가상 메모리를 일정한 크기로 나눈 블록



### LRU와 LFU 알고리즘의 구현 

![](https://velog.velcdn.com/images/zioo/post/1e4bcc1f-653c-48de-b7a2-a9774f151fe2/image.png)

![](https://velog.velcdn.com/images/zioo/post/c29d2a56-ea6b-4dd9-992f-65992291f0d2/image.png)

LFU 알고리즘을 min heap으로 구현 


       
- **Paging system**인 경우
    - **page default**인 경우에만 OS가 관여함
    - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
    - O(1)인 LRU의 list 조작조차 불가능
    



### Paging system에서 LRU, LFU 가능한가?
![https://blog.kakaocdn.net/dn/bquRf7/btrygugnh3A/CHixOWIK7YwHt2JLsROKh1/img.png](https://blog.kakaocdn.net/dn/bquRf7/btrygugnh3A/CHixOWIK7YwHt2JLsROKh1/img.png)


- Process A가 실행 중인 경우임
- Process A의 논리적인 메모리에서 매 순간마다 instruction을 읽어와서 실행을 함

- page fault 일어날 때 replace가 일어나는데 운영체제가 page system 에서는 LRU, LFU 작업을 못함
---

##  Clock Algorithm

### **Clock Algorithm**

- **LRU**의 근사(approximation) 알고리즘
- 여러 명칭으로 불림 
  - **Second chance algorithm**
  - **NUR(Not Used Recently), NRU(Not Recently Used)**
- **Reference bit**을 사용해서 교체 대상 페이지 선정(circular list)
- Reference bit이 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
- 포인터 이동하는 중에 Reference bit 가 1인 것은 모두 0으로 바꿈
- 한 바퀴 되돌아와서도(=second chance) 0이면 그 때는 replace 당함
- 자주 사용되는 페이지라면 second chance가 올 때 1

![https://blog.kakaocdn.net/dn/cb3gbF/btrydH2heaw/1WjKTXU3zXXFLTKWs3orAK/img.png](https://blog.kakaocdn.net/dn/cb3gbF/btrydH2heaw/1WjKTXU3zXXFLTKWs3orAK/img.png)

###  Clcok Algorithm의 개선

- **Reference bit**과 **Modified bit(dirty bit)**을 함꼐 사용 (hardware 가 수행)
- Reference bit = 1 : 최근에 참조된 페이지
- Modified bit = 1 :최근에 변경된 페이지 (I/O를 동반하는 페이지)

운영체제 역할
- Reference bit 가 원래 1이면 0로 바꿔준다. 
- 0 인 것(참조가 안된 것)은 쫓아낸다. 
- 하드웨어의 도움을 받음 (bit 사용)

어떤 페이지 쫓아낼 때 
- Modified bit 0 
  - 한 번도 CPU에서 사용된 적 없음 
   - 그냥 삭제 가능
- Modified bit 1 
  - CPU에서 사용된 적 있음 (내용 수정된 적 있음)
  - backing store에 수정된 내용 반영 

---

##  Page Frame의 Allocation

###  Allocation Problem   
- 각 Process에 얼마만큼의 Page frame을 할당할 것인가?

###  **Allocation의 필요성**

- 메모리 참조 명령어 수행 시 명령어, 데이터 등 여러 페이지 동시 참조 
  - 명령어 수행을 위해 **최소**한 할당되어야 하는 **Frame**의 수가 있음
- **Loop**를 구성하는 **Page**들은 한꺼번에 allocate되는 것이 유리함 
  - 최소한의 **allocation** 이 없으면 매 **loop** 마다 **page fault**

###  Allocation Scheme

- ***Equal allocation*** : 모든 프로세스에 똑같은 개수 할당
- ***Proportional allocation*** :  프로세스 크기에 비례하여 할당
- ***Priority allocation*** :  프로세스의 Priority에 따라 다르게 할당

---

##  Global vs. Local Replacement
###  Global replacement
- **Replace** 시 다른 프로세스에 할당된 **frame** 을 빼앗아 올 수 있다
- 다른 프로그램의 페이지도 쫓아낸다 (계속 경쟁)
- **Process** 별 할당량을 조절하는 또 다른 방법
- **FIFO, LRU, LFU **등의 알고리즘을 **global replacement** 로 사용시에 해당
- **Working set, PFF** 알고리즘 사용

###  Local replacement
- 자신에게 할당된 **frame** 내에서만 **replacement**
- **FIFO, LRU, LFU** 등의 알고리즘을 **process** 별로 운영 시

---
##  Thrasing

- **프로세스의 원활한 수행에 필요한 최소한의 페이지 프레임 수를 할당 받지 못한 경우 발생**
- **Page fault rate**이 매우 높아짐
- **CPU utilization**이 낮아짐 
  - thrasing 발생,한 번 cpu가 instruction 발생하려고 하면 page fault 나서 메모리에 없음 -> 계속 i/o 하러 가야함 
  -  CPU 가 할 일이 없음 
- **OS** 는 **MPD**(Multiprogramming degree)를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가됨 **(higher MPD)**
- 프로세스 당 할당된 **프레임** 의 수가 더욱 감소
- 프로세스는 **page** 의 **swap in / swap out** 으로 매우 바쁨
- 대부분의 시간에 **CPU** 는 한가함
- low throughput

![https://blog.kakaocdn.net/dn/7voXd/btryeyX62kj/nKT5rCpgPwJVQ5x0K2TzKK/img.png](https://blog.kakaocdn.net/dn/7voXd/btryeyX62kj/nKT5rCpgPwJVQ5x0K2TzKK/img.png)

Thrasing Diagram

- **동시에 메모리에 올라가 있는 프로세스의 개수**(degree of multiprogramming)를 조정해 줘서, 메모리 확보를 하게끔 해야한다

---

##  Working-Set Model

### **Locality of reference**

- 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다 (Locality)
  - 어떤 함수가 실행되면 그 함수만 집중적으로 참조 
- 집중적으로 참조되는 해당 **page** 들의 집합을 **locality set** 이라 함

###  **Working-set Model**

- Locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 
    
    한꺼번에 메모리에 올라와 있어야하는 페이지들의 집합을 ***Working Set***
    이라 정의
- **Working Set** 모델에서는 프로세스의 Working Set 전체가 메모리에 올라와 있어야 수행되고, 그렇지 않을 경우 모든 **프레임** 을 반납한 후 **swap out(suspend)** ⭐️ 
- **Thrasing** 을 방지함
- **Multiprogramming degree** 를 결정함

---

##  Working-Set Algorithm

### **Working set** 의 결정

- **Working set window** 를 통해 알아냄
- **Window size**가 Δ(델타,윈도우)인 경우
    
    
    
   - 시각 ti에서의 Working set *WS* (ti) 
     - **Time interval** [ti, -Δ, ti] 사이에 참조된 서로 다른 페이지들의 집합
        
    
        
   - Working set에 속한 **page** 는 메모리에 유지, 속하지 않은 것은 버림
   (즉, 참조된 후 Δ 시간 동안 해당 page를 메모리에 유지한 후 버림)
        
   

![https://blog.kakaocdn.net/dn/JJSx7/btryeyRo29l/p1gF2kFzs4ok5qzBXDIP9K/img.png](https://blog.kakaocdn.net/dn/JJSx7/btryeyRo29l/p1gF2kFzs4ok5qzBXDIP9K/img.png)

---

##   PFF(Page-Fault Frequency) Scheme

![https://blog.kakaocdn.net/dn/blPC8f/btryifwbj40/EykuVt2JnfPXxMrQPX3ZYk/img.png](https://blog.kakaocdn.net/dn/blPC8f/btryifwbj40/EykuVt2JnfPXxMrQPX3ZYk/img.png)

- **Page-fault rate** 의 상한값과 하한값을 둔다
    - Page fault rate이 상한값을 넘으면 **frame**을 더 할당한다
    - Page fault rate이 하한값 이하이면 할당 **frame** 수를 줄인다
- 빈 **frame** 이 없으면 일부 프로세스를 **swap out** (메모리를 더 줘야 하는 상황일 때)

---

##   Page size의 결정

### **Page size** 를 감소시키면

- 페이지 수 증가
- 페이지 테이블 크기 증가
- **Internal fragmentation** 감소
- **Disk transfer** 의 효율성 감소
   - **Seek / rotation vs. transfer**
     - seek 시간이 오래 걸림, 가능한 한 번 디스크 헤드가 이동해서 많은 양의 뭉치를 읽어서 메모리를 올리는 것이 좋다. -> 페이지가 큰 것 효율적
     
- 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
   - **Locality** 의 활용 측면에서는 좋지 않음 
     - 함수가 실행되면 함수를 구성하는 코드들이 순차적으로 참조가 됨, 페이지 폴트가 났을 때 통채로 올려놓으면 처음에는 페이지 폴트가 났지만 그 아래 올라오는 메모리는 페이지 폴트가 발생하지 않는다. 

### **Trend**

- Larger page size

[출처]

https://velog.io/@zioo/Virtual-Memory

https://velog.io/@yuhyerin/kocw-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EB%B0%98%ED%9A%A8%EA%B2%BD-9.-Virtual-Memory1
