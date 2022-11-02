## 프로세스 생성 (Process Creation)

Copy-on-Write (COW) : wirte 가 발생하면서 수정될 때 copy 하겠다

- 부모 프로세스가 자식 프로세스 생성
- 프로세스의 **트리(계층 구조) 형성**
- 수행 (Execution)
    - 부모와 자식은 공존하여 수행되는 모델
    - 자식이 terminate 될 때 까지 부모가 wait 하는 모델
- 유닉스 예
    - fork()
        - 시스템 콜이 새로운 프로세스 생성
        - 자식은 부모를 그대로 **복사 (복제 생성)**
        - 주소 공간 할당
    - exec ()
        - 시스템 콜을 통해 새로운 프로램을 메모리에 올림
    
    > 자식은 부모의 공간을 그대로 복사한다.
    > 
    
    fork()가 복제 생성을 하는 단계고, exec()은 덮어 씌우는 단계이다.
    

## fork( ) 시스템 콜

A process is created by fork() system call 

- 자식 프로세스는 포크가 일어난 다음 시점 부터 실행이 된다.

- 복제할 때는 자식과 부모 구분
    - return value 가 다름
    - 부모 프로세스 return 값 양수
    - 자식 프로세스 return 값 0

## exec( ) 시스템 콜

A process execute a different program by exec() system call

- **execip()** 이후의 코드는 실행되지 않음

![01DFDB88-0CA1-41BF-8029-B2CF54B23764.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8d780b72-a7b9-4e21-8898-e91824d6a8ae/01DFDB88-0CA1-41BF-8029-B2CF54B23764.jpeg)

 

## wait ( ) 시스템 콜

- 부모 프로세스가 block 상태가 된다.
- 자식 프로세스가 종료될 때 까지 기다리는 system call

## exit ( ) 시스템 콜

프로세스의 종료

- 자발적 종료
- 비자발적 종료
    - 사람이 종료 시킴
    - 부모 프로세스가 종료 시킴
    - 부모 프로세스 본인이 죽음
        - 원래는 자식 프로세스가 먼저 죽어야 됨 그래서 자식까지 다 죽어버림
    

## 프로세스와 관련한 시스템 콜

- fork ( )
    - 부모 프로세스 복제 생성
- exec ( )
    - 새로운 프로세스로 덮어 씌움
- wait ( )
    - 자식이 종료될 때 까지 기다림
- exit ( )
    - 프로세스가 죽는다고 알림
    

## 프로세스 간 협력

![F5854971-339A-449A-9D88-31282A81E42A.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8765aebc-7321-48f9-b307-1096532af04a/F5854971-339A-449A-9D88-31282A81E42A.jpeg)

## Message Passing

- Message system
    - 운영체제 커널을 통해 메세지 보냄

- 통신 방법
    - Direct Communication
    - Indirect Communication
