# Virtual Memory


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

### 페이지 교체 알고리즘의 종류

OPT - Optimal : 앞으로 가장 오랫동안 사용되지 않을 페이지 교체

**FIFO - First In First Out**

**LRU - Least Recently Used : 가장 오랫동안 사용되지 않은 페이지 교체**

**LFU - Least Frequently Used : 참조 횟수가 가장 작은 페이지 교체**

MFU - Most Frequently used : 참조 횟수가 가장 많은 페이지 교체

NUR - Not Used Recently : 최근에 사용하지 않은 페이지 교체


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
- **Working Set **모델에서는 프로세스의 Working Set 전체가 메모리에 올라와 있어야 수행되고, 그렇지 않을 경우 모든 **프레임** 을 반납한 후 **swap out(suspend)** ⭐️ 
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
