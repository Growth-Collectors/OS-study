# ch12. Disk Management & Scheduling


## 1. Disk Structure

 ![](https://velog.velcdn.com/images/zioo/post/4aa90044-16ff-4fa6-a50c-17402ae55f0d/image.png)


### logical block
- **정보를 전송하는** **최소 단위**
- 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들 (512Byte)
- 주소를 가진 **1차원 배열**처럼 취급

### sector
- **디스크를 관리하는 최소의 단위**
- **logical block**이 물리적인 디스크에 매핑된 위치
- **Sector 0**은 최외각 실린더의 첫 트랙에 있는 첫 번째 섹터이다. (약속)

디스크 내부적으로는 정보가 sector 단위로 저장되어 있지만,
외부에서 접근할 때는 logical block 단위로 바라본다.


## 2. Disk Management
#### Physical formatting (low level formatting)
- 디스크를 컨트롤러가 읽고 쓸 수 있도록 **섹터들로 나누는 과정**
- 각 섹터는 **header + 실제 data**(보통 512 Bytes) + **trailer로** 구성
- **header** 와 **trailer** 는 **sector number, ECC(Error-Correcting Code)** 등의 정보가 저장되며 controller가 직접 접근 및 운영 (데이터 요약 -> ECC)

#### Partitioning
- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- 이미 섹터로 나눠저 있음.
- OS는 이것을 **독립적 disk**로 취급 (logical disk)

#### Logical Formatting
- logical disk에 파일시스템을 만드는 것
- **FAT, inode, free space** 등의 구조 포함

#### Booting
- **ROM** 에 있는 **small bootstrap loader**의 실행
- **sector 0(boot block)** 을 load하여 실행
- sector 0은 **"full Bootstrap loader program**" 임
- **OS**를 디스크에서 load하여 실행

참고) ROM (Read-only Memory) : 컴퓨터에 지시사항을 영구히 저장하는 비휘발성 메모리다. 컴퓨터 전원이 꺼져도 ROM에는 영향을 주지 않는다. 읽기 전용 메모리이므로, 사용자가 변경할 수 없다.

## 3. Disk Scheduling
![](https://velog.velcdn.com/images/zioo/post/7dfe8c4c-970a-432d-90e5-905fc9d7f7f7/image.png)

하드디스크에 데이터를 읽거나 쓰는 행위를 디스크 액세스(Disk Access)

실린더(cylinder) : 여러 개의 원판에서 상대적 위치가 동일한 트랙들의 집합



### Access Time의 구성
**Seek Time**
- 헤드를 해당 실린더(track의 집합)로 움직이는데 걸리는 시간
- arm을 앞뒤로 움직이는 시간
- **가장 오랜 시간이 걸림**

**Rotational Latency**
- 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
- disk 또는 platter를 rotation하는데 걸리는 시간

**Transfer Time**
- 실제 데이터의 전송 시간
- seek time과 rotational latency에 비해 매우 짧은 시간이 걸림.

### Disk Bandwidth
- 단위 시간 당 전송된 바이트의 수
### Disk Scheduling
- Seek Time을 최소화하는 것이 목표
- **Seek Time => Seek distance**

## 4.  Disk Scheduling Algorithm
Seek Time을 최소화하는 것이 목표!

특정 실린더 위치에 head (arm)을 위치시켜야하는 것

### 4-1. FCFS (First Come First Service)
![](https://velog.velcdn.com/images/zioo/post/61a0287e-98f8-4504-9133-2e2488e855a7/image.png)
- head 이동 거리가 길다

### 4-2. SSTF (Shortest Seek Time First)
문제점: starvation 문제가 발생할 수 있다.
![](https://velog.velcdn.com/images/zioo/post/cd25473a-127b-4276-ad40-57da03115e33/image.png)
- 현재 head 위치에서 가장 가까운 요청 먼저 처리 

### 4-3. SCAN
- 엘리베이터 알고리즘
![](https://velog.velcdn.com/images/zioo/post/2544a19f-71a1-4d36-aa3d-06ff89fe8515/image.png)

- **disk arm** 이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동한다.
- 장점 : 디스크 헤드의 이동 거리도 짧아지고, starvation 문제도 발생하지 않는다.
- 문제점
 -  **실린더 위치에 따라 대기 시간이 다르다.**
 -  가운데 영역은 양끝에 있는 경우보다 예상 대기 시간이 더 짧다.

### 4-4. C-SCAN
![](https://velog.velcdn.com/images/zioo/post/4901ff3c-5d48-42c0-885c-f56356d54055/image.png)

- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동한다.
- 그래서 **SCAN**보다 **균일한 대기 시간을 제공**한다.
- 이동 거리는 길어질 수 있음, 균일한 대기 시간 제공

### 4-5. N-SCAN
- SCAN을 변형한 알고리즘
-  일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 **되돌아올 때** **service한다.**
- 
### 4-6~7. LOOK and C-LOOK
SCAN이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동

#### LOOK
LOOK과 C-LOOK은 헤드가 진행 중이거나 그 방향에 더 이상 기다리는 **요청이 없으면 헤드의 이동방향을 즉시 반대로 이동한다.**
#### C-LOOK
C-LOOK (C: circular)은 LOOK에서 곧바로 출발점으로 다시 이동한다.
LOOK보다 균일한 대기 시간을 제공해줄 수 있다는 장점이 있다.

#### C-LOOK에 해당하는 그림
- 끝(199)까지 안가고 처음으로 되돌아가는 것을 확인할 수 있다.
![](https://velog.velcdn.com/images/zioo/post/b32e0893-26d6-4298-abe4-361e81e0748d/image.png)


## 5. Disk Scheduling Algorithm의 결정
- **SCAN, C-SCAN** 및 그 응용 알고리즘인 **LOOK, C-LOOK** 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있다. (head 이동 거리 줄일 수 있다)

* 알려져 있다는 표현의 뜻: 전반적으로 성능이 괜찮지만 반드시 성능이 좋다고 말할 순 없다
- **File** 의 **할당 방법에 따라 디스크 요청이 영향을 받는다.**
- 디스크 스케쥴링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직하다.

## 6. Swap Space Management
![](https://velog.velcdn.com/images/zioo/post/f2675f3b-6067-4d22-a1ee-924f469bd408/image.png)

#### Disk를 사용하는 두 가지 이유
- **memory의 volatile(뜻: 휘발성의)한 특성 → file system**
- 프로그램 실행을 위한 **memory** 공간 부족 → **Swap Space (swap area)**
#### Swap-Space
- Virtual Memory System에서는 디스크를 **memory** 의 연장 공간으로 사용
- 파일 시스템 내부에 둘 수도 있으나 별도 **partition** 사용이 일반적
  - 공간 효율성보다는 **속도 효율성이 우선** (프로세스가 끝나면 사라질 거니까)
  - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
  - 따라서, **block** 의 크기 및 저장 방식이 일반 파일시스템과 다름


- file system은 보통 512B 정도지만 swap area는 다양한 크기의 메모리 블락을 할당받음.


## 7. RAID
#### RAID (Redundant Array of Independent Disks)
- 여러 개의 디스크를 묶어서 사용
- 하나의 논리적 디스크로 만든다
![](https://velog.velcdn.com/images/zioo/post/4125baec-fa6c-4a34-8d0a-ad154f4c9363/image.png)

#### RAID의 사용 목적
- **디스크 처리 속도 향상 (RAID 0)**
  - 여러 디스크에 block의 내용을 **분산 저장**
  - 병렬적으로 읽는다. **(interleaving, Striping)**
- **신뢰성(Reliability) 향상 (RAID 1)**
  - 동일 정보를 여러 디스크에 중복 저장
  - 하나의 디스크가 고장(failure)시 다른 디스크에서 읽어옴 **(Mirroring, Shadowing)**
  - 단순한 중복 저장이 아니라 일부 디스크에 **parity**(데이터 복구 수단)  를 저장하여 공간의 효율성을 높일 수 있다.
