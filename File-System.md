# File System

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

![](https://github.com/Growth-Collectors/OS-study/blob/main/images/File%20System/Untitled.png?raw=true)

![](https://github.com/Growth-Collectors/OS-study/blob/main/images/File%20System/Untitled%201.png?raw=true)

- 운영체제에 저장했다가 사용.. 버퍼 캐시

### File Protection

![](https://github.com/Growth-Collectors/OS-study/blob/main/images/File%20System/Untitled%202.png?raw=true)

- rwx : read, write, excute

### File System의 Mounting

![](https://github.com/Growth-Collectors/OS-study/blob/main/images/File%20System/Untitled%203.png?raw=true)

![](https://github.com/Growth-Collectors/OS-study/blob/main/images/File%20System/Untitled%204.png?raw=true)

- Mount: 다른 파일 시스템을 접근해야할 때, 루트 디렉토리의 하위에 새로운 파일 시스템을 붙이는 것으로 서로 또 다른 마운트된 파일 시스템의 루트 디렉토리로 접근할 수 있게 해주는 것

### Access Methods

![](https://github.com/Growth-Collectors/OS-study/blob/main/images/File%20System/Untitled%205.png?raw=true)
