---
layout: post
title:  "프로세스 동기화 이해하기"
subtitle:   "프로세스 동기화 이해하기"
categories: development
tags: os
comments: true
---

프로세스 동기화에 대해 작성한 글입니다

---

- Process Synchronization
```



```
class Test {

class BankAccount {

class Parent extends Thread {

class Child extends Thread {
```

## 임계구역 문제
---

	- 같이 사용하는 것들을 write, update, change하는 경우
	- Bounded waiting (유한대기): 어느 쓰레드라도 유한 시간 내 진입(유한 시간 내에 Critical section에 진입해야 함)
- 프로세스/쓰레드 동기화
	- Busy wait 등 비효율성 제거
	- Process management
		- CPU scheduling
		- Synchronization
			- 임계구역 문제를 해결해야 하고 프로세스 실행 순서를 제어할 수 있어야 함
			- 비효율성 제거
			- 동기화 하기 위해 만든 도구 : 세마포, 모니터 
	- Memory management
	- File System
	- IO
	- ...
```

- 세부 동작
		block;

```

- 일반적 사용 (1): Mutual exclusion
	|:--------------:|
	|Critical-Section|
	| sem.release(); |
		int temp = balance - amount;
```

	- P1을 먼저 실행했으면 OK, P2가 먼저 실행했으면 sem.acquire()에 의해 -1이 되서 밑으로 못 내려감. Context Switch으로 S1이 진행한 후, sem.release()로 0이 되서 S2가 실행
	
	|        P1      |       P2       |
	|:--------------:|:--------------:|
	|                | sem.acquire(); |
	|       S1       |       S2       |
	| sem.release(); |                |

		- 프로그램이 시작되면 부모 쓰레드는 그대로 실행되게 하고, 자식 쓰레드는 초기 값이 0인 sem2 세마포어에 대해 acquire() 를 호출하게 하도록 deposit(), withdraw() 메소드를 각각 수정
		- 즉 자식 쓰레드가 먼저 실행되면 세마포어 sem2에 의해 블록되고, 블록된 자식 쓰레드는 나중에 부모 쓰레드가 깨워주게 한다.

		|     Parent     |     Child      |
		|:--------------:|:--------------:|
		|                | sem.acquire(); |
		|    deposit     |   withdraw     |
		| sem.release(); |                |
	
		- 상호배타를 위한 sem 세마포어 외에 부모 쓰레드의 블록을 위해 dsem 세마포어를, 자식 쓰레드의 블록을 위해 wsem 세마포어를 각각 사용한다
		
		|     Parent      |      Child      |
		|:---------------:|:---------------:|
		|                 | wsem.acquire(); |
		|    deposit      |    withdraw     |
		| wsem.release(); | dsem.release(); |
		| dsem.acquire(); |                 |
		
		- 상호배타를 위한 sem 세마포어 외에 sem2 세마포어를 사용하여 잔액 부족시 자식 쓰레드가 블록되도록 한다

## 전통적 동기화 예제
- Classical Synchronization Problems
	- producer가 생성하고, 저장 창고(buffer)에 저장하고 consumer가 사용(마치 메세지 큐 시스템 펍섭 같음)

```
class Test {
		"Number of items in the buf is " + b.count);




```

	
```
import java.util.concurrent.Semaphore;
		buf = new int[size];
```	

- Busy-wait
	- 세마포를 사용해 버퍼가 다 찼으면 프로듀서는 세마포에 들어가서 대기
	- 그리고 빈 공간이 생기면 들어감
		- empty.acquire();
		- full.acquire();

```
import java.util.concurrent.Semaphore;
		return item;
```


### Readers-Writers Problem
	- Database : Critical Section, 최대 한놈만 접근하도록 하면 비효율적임. 한 reader가 db에 들어가있는 경우 다른 reader가 들어와도 상관이 없음(읽기만 하니) 그러나 writer는 상호배제. reader가 들어갔으면 writer는 금지
- 젓가락을 들면 주변 사람이 사용 불가

```
import java.util.concurrent.Semaphore;
		this.id = id;

```

- 잘못된 결과: starvation
	- 모두 왼쪽 젓가락을 들면 아무도 식사를 할 수 없음


### 교착상태
- Deadlock
	- 꼬리물기
	- 짝수번 사람은 왼쪽부터 홀수번 사람은 오른쪽부터

### 교착상태 처리
	- 교착상태 방지 (Deadlock Prevention)
		- 원천적으로 불가할 확률이 큼
		- 따라서 교착 상태를 방지하기엔 부적절할 수 있음
		- 동시에 모두 잡도록 설정. 동시에 잡을 수 없으면 아예 잡지 않음 
		- 자원을 뺏어올 수 있도록 
		- 원천적 불가할 수도 (예: 프린터)
			- CPU는 강제로 context switch로 가능하지만...
		- 데드락은 자원 요청에 잘못 응답해서 발생한 것이라고 생각

		| Process | Max needs | Current needs |
		|:-------:|:---------:|:-------------:|
		|    P0   |     10    |       5       |
		|    P1   |     4     |       2       |
		|    P2   |     9     |       2       |

		- 불안전한 할당 (Unsafe allocation)
		
		|:-------:|:---------:|:-------------:|
		|    P0   |     10    |       5       |
		|    P1   |     4     |       2       |
		|    P2   |     9     |       3       |
	- 필요한 자원을 모두 나눠줌
	- 검출
		- 그 전의 상태를 기억해야 함 => 계산, 메모리 사용
		- 프로세스 일부 강제 종료

## 모니터(Monitor)
- 요즘은 세마포보다 모니터를 더 많이 사용
- 자바에서 사용

- 세마포 이후 프로세스 동기화 도구
		- 배타동기 : 한 쓰레드만 접근할 수 있음
		- wait()를 부르면 조건동기 큐로 이동
	- 깨워진 쓰레드는 현재 쓰레드가 나가면 재진입할 수 있다
class C {


```
```
		try {
			wait();
```

- 일반적 사용 (2): Ordering

|        P1      |       P2       |
|:--------------:|:--------------:|
|     wait()     |                |
|       S1       |       S2       |
|                |    notify()    |
			wait();
```
```

## Reference
- [양희재 교수님 운영체제 강의](http://kocw.net/home/search/kemView.do?kemId=978503)
- [introduction to Operating System](https://www.slideshare.net/LukaXavi/introduction-to-operating-system-10938506)










