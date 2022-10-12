# File System (Implementations1듣기)

### File and File System

**File**

- “A named collection of related information”
- 일반적으로 비휘발성의 보조기억장치에 저장
- 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌
- Operation
    - create, read, write, reposition(lseek), delete, open, close 등

**File attribute (혹은 파일의 metadata)**

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
    - 파일 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근 권한 (읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

**File system**

- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
- 파일의 저장 방법 결정
- 파일 보호 등

### Directory and Logical Disk

**Directory**

- 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
- 그 디렉토리에 속한 파일 이름 및 파일 attribute들
- operation
    - search for a file, create a file, delete a file
    - list a directory, rename a file, traverse the file system

**Partition (=Logical Disk)**

- 하나의 (물리적) 디스크 안에 여러 파티션을 두는게 일반적
- 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
- (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음
    - 디스크의 용도 → 1. file system 2. swapping 으로 쓰인다.

### open()

- 파일의 메타데이터를 메모리에 올려놓는 것

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled.png)

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%201.png)

- 운영체제에 저장했다가 사용.. 버퍼 캐시

### File Protection

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%202.png)

- rwx : read, write, excute

### File System의 Mounting

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%203.png)

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%204.png)

- Mount: 다른 파일 시스템을 접근해야할 때, 루트 디렉토리의 하위에 새로운 파일 시스템을 붙이는 것으로 서로 또 다른 마운트된 파일 시스템의 루트 디렉토리로 접근할 수 있게 해주는 것

### Access Methods

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%205.png)

## File System Implementations1 듣기ㅣㅣㅣㅣㅣㅣㅣㅣㅣㅣ

## 디스크에 파일의 데이터를 할당하는 방식

파일은 크기가 균일하지 않다는 특징이 있다. 그래서 파일을 동일한 크기 단위로 sector로 나누어 저장을 한다. 디스크에 파일을 저장하는 방법은 크게 3가지가 존재하는데, 메모리 관리 기법 중 페이징 기법과 유사하다.

- 연속 할당
- 연결 할당
- 인덱스를 이용한 할당

### Contiguous Allocation 연속 할당

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%206.png)

- 하나의 파일이 디스크 상에 연속해서 저장되는 방식으로, 나누어진 각 블록들이 연속된 번호를 부여 받아 저장된다.
- 장점
    - 빠른 입출력
        - 대부분의 접근 시간은 헤더가 움직이면서 읽어 들이는 시간이다.
        - 한 번의 seek/rotation으로 많은 바이트를 한 번에 transfer할 수 있다. (모두 연속해서 붙어 있으므로 추가적인 탐색 비용이 소요되지 않음)
        - 따라서 Realtime 파일 또는 이미 run 중이던 프로세스의 swapping 용도로 사용한다.
    - 임의 접근이 가능하다.
- 단점
    - 외부 단편화가 발생한다.
    - 파일 grow가 어렵다.
        - 파일의 크기는 유동적이다. 이때 파일의 크기가 커질 때 제약 사항이 발생한다.
        - file은 수정되면 중간에 크기가 변할 수 있다. 근데, 크기가 커질 때 제약이 있다(뒤에 빈 블록이 몇개 없다면)그래서 미리 file이 커질것을 대비해서 할당해놓을 수 있다.
        - 근데 그렇게 되면 grow 정도를 크게 할수록 내부 단편화 문제가 커진다.

### Linked Allocation 연결 할당

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%207.png)

- sector들이 각각 node가 되어 Linked List 구조를 취하면서 파일을 저장한다.
- 장점
    - 외부 단편화가 발생하지 않는다.
- 단점
    - 첫 요소부터 차례대로 읽어야 하므로 임의 접근이 불가능하다.
    - 신뢰성 문제
        - 한 sector가 고장나 pointer가 유실되면 그 이후 모든 sector로 접근할 수 없다.
    - 포인터를 위한 공간이 필요하므로 공간 효율성을 떨어뜨리게 된다.
        - 512 bytes/sector, 4 bytes/pointer
- 변형
    - File-allocation table (FAT) 파일 시스템
        - 포인터를 별도의 위치에 보관하여 신뢰성 문제(reliability)와 공간 효율성 문제를 해결한다.

### Indexed Allocation 인덱스를 이용한 할당

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%208.png)

- 파일이 어디에 나눠져 있는지 인덱스를 적어 두는 블록 하나를 활용하고, 이를 인덱스 블록이라 부른다.
- 장점
    - 외부 단편화가 발생하지 않는다.
    - Direct access(임의 접근)이 가능하다.
- 단점
    - 작은 파일의 경우 공간 낭비가 심하다. (실제로는 많은 파일들이 사이즈가 작음)
    - 매우 큰 파일의 경우 하나의 인덱스 블록으로 커버할 수 없다.
        - 해결 방안: linked scheme, multi-level index
        - 전자는 index block을 여러 개 두는 것이고, 후자는 블록의 마지막에 다음 index 블록을 가리키는 값을 설정하여 서로 연결하는 것이다.

## UNIX 파일 시스템의 구조

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%209.png)

- 유닉스 파일 시스템의 중요 개념
    - Boot block
        - 부팅에 필요한 정보를 담고 있는 블록
        - 모든 파일 시스템에 존재하는 블록
        - boot block은 unix file system만 제일 앞에 나오는 것이 아니라 모든 file system에서 boot block이 가장 먼저 나온다. 왜냐하면 컴퓨터에 어떤 file system이 설치되어있는지 모르는 상태에서 컴퓨터의 전원을 키면 부팅을 해야한다. 어디 있는 data를 메모리로 올려서 부팅을 하는지 결정해줘야 한다. 0번 블록은 항상 boot block이다.
    - Super block
        - 파일 시스템에 관한 총체적인 정보를 담고 있는 블록
        - 어느 부분이 비어 있는 블록인지, 어느 부분이 사용 중인 블록인지, 어디부터가 Inode list이고 Data 블록인지 등을 알려 주는 정보를 가지고 있다.
    - Inode list
        - 파일 이름을 제외한 파일의 모든 메타 데이터를 따로 저장한다.
        - 파일 하나 당 Inode가 하나씩 할당이 되고, 해당 Inode는 그 파일의 메타 데이터를 갖고 있다. 이때 파일의 이름은 디렉토리가 가지고 있는데, 디렉토리는 파일의 이름과 Inode 번호를 저장하고 있다.
        우리는 file의 metadata를 directory에 저장한다고 배웠는데 사실 모든 metadata를 directory에 저장하는건 아니다. directory는 지극히 일부분만 가지고 있고(file의 이름은 directory가 가지고 있음) 실제 file의 metadata들은 Inode(index node) 라는 위치에 보관한다.
        - direct blocks는 파일이 존재하는 인덱스를 저장하는 인덱스 블록이다. 파일의 크기가 크지 않다면 이 블록을 이용하여 파일을 접근할 수 있다.
        - Unix file system은 Indexed Allocation을 사용한다. 작은 file이면 direct blocks를 사용하고 크기가 커질수록 single, double, triple indirect를 사용한다. 이렇게 하면 효율성도 챙길 수 있고(작은 file은 direct block으로 처리) 굉장히 큰 file들도 한정된 inode라는 공간에서 표현할 수 있다.
    - Data block
        - 파일의 실제 내용을 보관하는 블록
        - 이 중 디렉토리 파일은 자신의 디렉토리에 속한 파일들의 이름과 Inode 번호를 가지고 있다.

## FAT 파일 시스템

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2010.png)

- FAT 파일 시스템은 윈도우즈 계열에서 주로 사용된다.
- 파일의 메타 데이터의 일부(위치 정보)를 FAT에 저장하고, 나머지 정보는 디렉토리가 가지고 있다. (파일 이름, 접근 권한, 소유주, 파일의 첫 번째 위치 등)
- FAT에는 그 블록의 다음 블록을 번호로 저장해두고 있다.
위 사진에서 217번이 첫 번째 블록인데, 다음 블록의 위치를 FAT에서 별도로 관리한다. FAT 테이블 전체를 메모리에 올려 놓았으므로 연결 할당의 단점(임의 접근 불가, 신뢰성 문제, 공간 효율성 문제)을 전부 극복하였다. 참고로 FAT는 중요한 정보이므로 2 copy 이상 복제본을 만들어 두어야 한다.

### **Free-space Management** 빈 공간 관리

- sector가 할당되고 나서 발생하는 hole을 어떻게 관리할 것인가?

- **Bit map or Bit vector**
    - 각각의 블럭이 사용중인지 아닌지를 0, 1로 표시
    0이면 비어 있는 값이고, 1이면 sector 저장된 공간이다.
    - 연속된 n개의 free 블록을 찾기 효과적이지만, 0 또는 1을 저장할 부가적인 공간을 필요로 한다.

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2011.png)

- **Linked list**
    - 모든 free 블록을 링크로 연결 (free list)
    - 연속적인 가용 공간을 찾기 어렵다.
    - 공간의 낭비가 없다.

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2012.png)

- **Grouping**
    - Linked list의 변형
    - 첫 번째 free 블록이 n 개의 포인터를 갖는다.
        - n-1 pointer는 free data block을 가리킴마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2013.png)

- **Counting**
    - 프로그램들이 종종 여러 개의 연속적인 블록을 할당하고 반납한다는 성질에 착안하였다.
    - (first free block, # of contiguous free blocks)을 유지

## Directory Implementation 디렉토리 구현

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2014.png)

- **선형 리스트**
    - <file name, file의 메타 데이터>의 리스트
    - 구현이 간단하다.
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search(선형 탐색)이 필요하다. (O(N))
- **해시 테이블**
    - 선형 리스트 + 해싱
    - 해시 테이블은 file name을 이 파일의 선형 리스트의 위치로 바꾸어 준다.
    - search time을 없앴다. 탐색 시간이 O(1)이다.
    - 해시 충돌(collision)이 발생할 수 있다.
- **파일의 메타 데이터의 보관 위치**
    - 디렉토리 내에 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - Inode, FAT 등
- **Long file name의 지원**
    
    ![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2015.png)
    
    - <file name, file의 메타 데이터>의 리스트에서 각 엔트리는 일반적으로 고정 크기이다.
    - 하지만 file name이 고정 크기의 엔트리 길이보다 길어지는 경우 엔트리의 마지막 부분에 file name의 뒷 부분이 위치한 곳의 포인터를 둔다.
    - file 이름의 나머지 부분은 동일한 directory file의 일부에 존재한다.

## VFS와 NFS

- **Virtual File System (VFS)**
    - 서로 다른 다양한 파일 시스템에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해 주는 OS의 레이어
    - VFS가 필요한 이유
        - 복잡한 개별 file system에 대한 추상화
        - layer of abstraction(매우 중요)
- **Network File System (NFS)**
    
    ![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2016.png)
    
    - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있다.
    - NFS는 분산 환경에서 대표적인 파일 공유 방법이다.
- 어떤 파일 시스템을 쓰든 상관 없이 VFS 인터페이스를 사용한다.
- 분산 시스템에서는 네트워크를 통해 파일을 공유하기 위해 NFS 클라이언트와 NFS 서버가 이용된다.

## 페이지 캐시와 버퍼 캐시

운영체제가 file입출력을 할 때 사용자 프로그램의 요청을 받아서 disk에서 읽어온 내용을 그냥 전달하는 게 아니라 자신의 buffer cache 영역에 읽어놓고, 그 내용을 copy로 넘겨주기 때문에 다음번에 동일한 file data에 대한 read, write system call이 오면, disk까지 가지않고 buffer cache에서 처리한다.

예전에는 sector단위는 512byte였다. 최근에는 buffer cache가 page cache와 통합이 되면서 buffer cache에서 사용하는 단위도 4KB를 사용한다(unified buffer cache).

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2017.png)

- 페이지 캐시
    - 가상 메모리의 페이징 시스템에서 사용하는 페이지 프레임을 캐싱의 관점에서 설명하는 용어
    - Memory-Mapped I/O를 쓰는 경우 파일의 I/O에서도 페이지 캐시를 사용한다.
- Memory-Mapped I/O
    - 파일의 일부를 가상 메모리에 매핑한다.
    - 매핑한 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 한다.
- 버퍼 캐시
    - 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 버퍼 캐시를 사용한다.
    - 파일 사용의 지역성(locality) 활용
        - 한 번 읽어 온 블록에 대한 후속 요청 시, 버퍼 캐시에서 즉시 전달
    - 모든 프로세스가 공용으로 사용
    - 교체 알고리즘 필요 (LRU, LFU 등)
- 통합 버퍼 캐시 (unified buffer cache)
    - 최근의 OS에서는 기존의 버퍼 캐시가 페이지 캐시에 통합됨.
        - buffer cache도 page 단위로 관리한다.

- 통합 버퍼 캐시를 이용하지 않을 때와 이용할 때의 차이
    
    ![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2018.png)
    
    - unified buffer cache를 이용하지 않는 File I/O
        1. read, write system call을 쓸 때는 그 내용이 buffer cache에 있든 없든 항상 운영체제에게 요청해서 받아와야 한다. 
        2. mmap을 쓰게 되면 page cache에 올라온 내용은 운영체제의 도움을 받지 않고 사용자 프로세스가 직접 메모리접근을 통해 I/O를 한다(인터페이스의 차이)
            - mmap을 사용하는 이유: mmap을 사용하면 이미 메모리에 올라온 내용에 대해서는 kernel의 도움을 받지 않고 (운영체제를 호출하지 않고) 자신이 직접 자신의 메모리에 접근하듯이 읽고 쓸 수 있다.
    - unified buffer cache를 이용한 File I/O
        - unified buffer cache를 사용하면 기존처럼 buffer cache를 따로 두지 않고, 필요에 따라 page cache에서 공간을 할당해서 쓰는 방식을 사용한다.
        1. read, write system call을 하는 경우 해당  내용이 buffer cache에 올라와 있든 아니든 상관없이 운영체제에게 CPU 제어권이 넘어간다. 
        CPU는 이미 올라와 있는 data는 사용자 프로그램에 copy해주고, 메모리에 없다면 disk에서 읽어와서 사용자프로그램에 copy해서 전달한다.
        2. mmap인 경우, 자신의 주소영역 중에 일부를 file에 mapping하는 단계를 거치고 나면 사용자 프로그램의 주소영역에 page cache가 mapping된다. 
        - 이전처럼 buffer cache의 내용을 copy하는 것이 아니라 그냥 page cache 자체가 사용자 프로세스의 논리적 주소영역에 mapping되어 사용된다(page cache에 직접 읽고 쓴다).
        

사용자 프로그램이 file을 접근하는 방식은 인터페이스가 2가지 존재한다. 

1. read, write system call (copy)
2. memory mapped
(copy가 아니라 매핑해서 그대로 사용 → 오버헤드 줄어듦, 한번 메모리에 올라오면 커널 도움없이 사용가능)
    - mmap의 주의할 점: 다른 친구도 mmap을 써서 share한다면 consistency가 깨질 수 있다. 
    read, write system call에서는 copy로 전달하기 때문에 그런 문제는 발생하지 않는다.

### 프로그램의 실행

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2019.png)

**code 부분**

- code 부분은 별도의 swap area 를 가지고 있지 않고, file system의 file을 가진 상태로 매핑된 상태이다.
- memory에 올라간 다음, 쫓겨날 때 swap area로 내려가지 않는다. 
read-only이기 때문에 swap area에 써줄 필요가 없다.
- 실행파일을 실행시킬 때 loader라는 software가 이 프로그램을 메모리에 올리는데, 이 때 memory-mapped file을 쓰는 것이 대표적인 방식이다.

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2020.png)

- 프로세스가 일반 데이터 파일을 I/O하고 싶을 수 있다.
- 이때 `mmap()` 을 호출하면 Memory Mapped I/O 방식으로 파일을 I/O할 수 있고, `mmap()` 은 시스템 콜이므로 운영 체제에 CPU의 제어권이 넘어간다.
- mmap 을 사용하면 datafile 일부를 내 주소공간에 mapping해달라고 system call을 하는 것이다. 
그렇게 되면 아래와 같이 된다.

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2021.png)

![Untitled](File%20System%20(Implementations1%E1%84%83%E1%85%B3%E1%86%AE%E1%84%80%E1%85%B5)%203da93353e9dd44659cc6b09bc45d3d07/Untitled%2022.png)

- 운영 체제는 데이터 파일의 일부를 프로세스 주소 공간에 매핑을 한다.
- 만약 데이터 파일이 매핑된 영역을 접근했을 때, 실제 물리적인 메모리에 페이지가 올라와 있지 않다면 page fault가 발생한다. 그렇지 않으면 가상 메모리의 매핑된 영역은 물리적 메모리의 페이지 프레임과 일치가 되므로 프로세스가 데이터 파일에 대해 I/O를 하고 싶을 때 운영 체제의 도움 없이 독자적으로 I/O를 수행할 수 있다.
- 물리적 메모리에 올라간 데이터 파일과 매핑된 페이지 프레임을 쫓아내야 할 때는, 스왑 영역으로 내리는 것이 아니라 수정된 사항을 파일 시스템에 적용하고 물리적 메모리에서 지운다.
- 현재 프로세스 B가 데이터 파일에 대해 Memory Mapped I/O를 수행하여 물리적 메모리에 페이지 프레임을 올려 두었는데, 프로세스 A도 이 페이지 프레임을 공유하여 사용할 수 있다.