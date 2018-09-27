## OS(Operating System)

**목차**

- [프로세스 vs 스레드](#프로세스와-스레드의-차이)
- [프로세스 스케줄러](#프로세스-스케줄러)
- [CPU 스케줄링](#cpu-스케줄링)
- [병행 프로세스](#병행-프로세스)
- [동기와 비동기](#동기와-비동기)
- [메모리 관리 전략](#메모리-관리-전략)
- [가상 메모리](#가상-메모리)
- [캐시의 지역성](#캐시의-지역성)
- [Unix](#unix)

### 프로세스와 스레드의 차이

---

**프로세스**

- 실행중인 프로그램 

- 메모리에 적재되어 CPU의 할당을 기다리는 프로그램 

- 프로세스 스택 (지역변수, 매개변수, 복귀주소)+ 데이터 섹션(전역변수) + 힙(동적할당)

**PCB**

- 프로세스에 대한 정보 저장
- OS는 프로세스 생성과 동시에 고유한 PCB 생성 
- CPU 할당시 작업 내역을 PCB에 저장하고, 다시 할당받게되면 PCB에 저장된 내용을 불러와 종료됐던 시점부터 다시 수행 
- PID, 프로세스 상태(new, ready, running, wating, terminated), 프로그램 카운터, CPU 레지스터, CPU 스케줄링 정보, 메모리 관리 정보, 입출력 상태 정보, 어카운팅 정보 

![프로세스상태](https://zetawiki.com/w/images/thumb/d/d5/Process-state.jpg/600px-Process-state.jpg)

**스레드**

- 프로세스의 실행 단위 
- 프로세스 내의 주소 공간이나 자원 공유(코드, 데이터섹션, 열린 파일이나 신호) 
- 스레드 ID, 프로그램 카운터, 레지스터 집합, 스택 
- 멀티스레딩: 하나의 프로세스를 다수의 실행 단위로 구분하여 자원을 공유하고 자원의 생성과 관리의 중복성을 최소화하여 수행능력을 향상시키는 것. 각각의 스레드는 독립적인 작업을 수행해야 하기 때문에 각자의 스택(독립적인 함수 호출)과 PC 레지스터 값을 가짐 



### 멀티스레드

**멀티스레딩의 장점**

- 메모리 공간과 시스템 자원소모가 줄어들음
- 스레드 간 통신이 전역 변수 공간이나 Heap을 이용하기 때문에 프로세스 간 통신 방법에 비해 간단함
- 스레드의 context switching은 프로세스와 달리 캐시 메모리를 비울 필요가 없어서 더 빠름 
- throughput이 향상되고 자원 소모가 줄어들며 응답 시간이 단축 됨 

**멀티 스레딩의 문제점**

- 공유 자원에 동시에 접근하는 것을 신경써야 함 (데이터 영역, 힙 영역)
- 동기화 작업이 필요함
- 동기화 작업으로 인해 병목 현상이 발생하지 않도록 주의해야 함



**멀티 스레드 vs 멀티 프로세스**

- 멀티  스레드는 멀티 프로세스보다 적은 메모리 공간을 차지하고 context switching이 빠르지만, 하나의 스레드가 종료되면 전체 스레드가 종료될 수 있으며 병목 현상 없는 동기화를 신경써주어야 한다. 멀티 프로세스는 하나의 프로세스가 종료되어도 다른 프로세스가 영향을 받지 않지만, 스레드보다 많은 메모리 공간과 CPU 시간을 차지한다. 



***스프링에서의 멀티 스레드***

***AsyncConfig.java***

- 쓰레드를 사용하기 위해 설정 파일에 @EnableAsync를 추가한다. 

```java

import java.util.concurrent.Executor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.task.SimpleAsyncTaskExecutor;
import org.springframework.core.task.SyncTaskExecutor;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

@Configuration
@EnableAsync
public class AsyncConfig {
	
	
	final static int CORE_POOL_SIZE=5;
	final static int MAX_POOL_SIZE=10;
	final static int QUEUE_CAPACITY=100;
	//final static int KEEP_ALIVE_SECONDS=num
	
    @Bean(name = "threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
    	/*
    	 * CORE_POOL_SIZE만큼 기본 쓰레드 생성 
    	 * 넘어가면 QUEUE에 쓰레드를 QUEUE_SIZE만큼 넣는다.
    	 * QUEUE가 꽉차면 MAX_POOL_SIZE만큼 쓰레드 실행이 가능함 
    	 */
    	ThreadPoolTaskExecutor tte=new ThreadPoolTaskExecutor();
    	tte.setCorePoolSize(CORE_POOL_SIZE);//Set the ThreadPoolExecutor's core pool size.
    	tte.setQueueCapacity(QUEUE_CAPACITY);
        return tte;
    }
    
    @Bean(name = "syncTaskExecutor")
    public Executor syncTaskExecutor() {
    	/* 호출한 쓰레드 상에서 쓰레드가 실행됨 */
    	SyncTaskExecutor ste=new SyncTaskExecutor();
        return ste;
    }

    public Executor simpleAsyncTaskExecutor(int threadPriority) {
    	/* 쓰레드를 재사용하지 않고 요청할 때마다 생성하여 수행*/
    	SimpleAsyncTaskExecutor sate=new SimpleAsyncTaskExecutor();
    	sate.setThreadPriority(threadPriority);
        return sate;
    }

} 

```

***ThreadService.java***

```java
package kr.ac.spring;

import java.util.concurrent.Future;

import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.stereotype.Service;

@Service
public class ThreadService {
	@Async("threadPoolTaskExecutor")
	public void asyncMethodWithVoidReturnType() {
		System.out.println("asyncMethodWithVoidReturnType: "+ Thread.currentThread().getName());
	}
	
	@Async("threadPoolTaskExecutor")
	public Future<String> asyncMethodWithReturnType(){
		
		System.out.println("asyncMethodWithReturnType: "+Thread.currentThread().getName());
		
		try {
			Thread.sleep(1000);
			System.out.println("asyncMethodWithReturnType END: "+Thread.currentThread().getName());
		}catch(Exception e) {
			e.printStackTrace();
		}
		return null;
	}
}

```



***ThreadTestController***

```java
package kr.ac.spring;

import org.slf4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ThreadTestController {

	Logger logger=org.slf4j.LoggerFactory.getLogger(ThreadTestController.class);
	
	@Autowired
	ThreadService service;
	
	@RequestMapping(value="/", method=RequestMethod.GET)
	public ModelAndView threadTest() {
		ModelAndView mv=new ModelAndView();
		service.asyncMethodWithReturnType();
		service.asyncMethodWithReturnType();
		service.asyncMethodWithReturnType();
		service.asyncMethodWithReturnType();		
		service.asyncMethodWithReturnType();

		return mv;
	}

}

```

***Result***

```xml
asyncMethodWithReturnType: threadPoolTaskExecutor-3
asyncMethodWithReturnType: threadPoolTaskExecutor-4
asyncMethodWithReturnType: threadPoolTaskExecutor-5
asyncMethodWithReturnType: threadPoolTaskExecutor-2
asyncMethodWithReturnType: threadPoolTaskExecutor-1
asyncMethodWithReturnType END: threadPoolTaskExecutor-4
asyncMethodWithReturnType END: threadPoolTaskExecutor-1
asyncMethodWithReturnType END: threadPoolTaskExecutor-2
asyncMethodWithReturnType END: threadPoolTaskExecutor-5
asyncMethodWithReturnType END: threadPoolTaskExecutor-3
```



### 프로세스 스케줄러

------

그림출처: 본인

![img](https://github.com/SeoJaeyeon/CS/blob/master/img/process_integration.png?raw=true))

### 스케줄러

​	**장기스케줄러**

​	<u>"디스크(secondary storage)의 프로세스를 메모리에 옮겨 CPU의 할당을 기다리도록 하는 스케줄러(admit).</u> 

​	<u>다중프로그래밍의 정도를 제어함."</u>

​	new(프로세스 생성), suspend(메모리의 프로세스가 디스크로 옮겨진 상태) -> ready 

​	cf) 시분할 시스템에서는 장기 스케줄러가 없음.



​	**단기스케줄러**

​	<u>"메모리의 프로세스가 CPU의 할당을 받도록 하는 스케줄러(dispatch)"</u>

​	ready -> running



​	**중기스케줄러**

​	<u>"메모리의 프로세스를 모두 디스크로 옮기는 스케줄러. 멀티프로그래밍 제어(swap out)"</u>

​	ready -> suspend



### 큐의 종류

​	**Job Queue** 

​	<u>"디스크에서 메모리로 옮겨지는 것을 기다리는 프로세스의 집합"</u>

​	**Ready Queue**

​	<u>"메모리에서 CPU의 할당을 기다리는 프로세스의 집합"</u>

​	**Device Queue**

​	<u>"Device I/O 작업을 기다리는 프로세스의 집합"</u>



### CPU 스케줄링

---

<u>"메모리의 프로세스가 CPU의 할당을 받도록 하는 스케줄러(dispatch)"</u>

(= 단기 스케줄러)



​	**FCFS(First Come First Served)**

 - 먼저 온 프로세스를 먼저 처리한다.

 - 비선점형(Non-Preemptive)

 - 중요하지 않거나 처리 시간이 오래 걸리는 프로세스가 먼저 온 경우 비효율적



​	**SJF(Shortest Job First)**

- 처리 시간이 짧은 프로세스를 먼저 처리한다.

- 비선점형 

- 사용시간이 긴 프로세스는 계속 CPU를 할당받지 못할 수 있음



​	**HRN(Highest Response ratio Next)**

- wating time+burst/burst 공식을 통해 우선순위 부여
- SJF 의 단점 보완
- 비선점형
- 처리시간에 비해 기다린 시간이 크면 우선순위를 가질 수 있음



​	**SRT(Shortest Remaining time First)**

- 남은 처리시간이 짧은 프로세스부터 먼저 처리
- 선점형(Preemptive)
- 새로운 프로세스가 도달할때마다 스케줄링을 다시 함
- 스케줄링을 다시하기 때문에 CPU 사용시간을 측정할 수 없고, 남은 CPU 사용 시간이 긴 프로세스는 계속해서 할당받을 수 없음



​	**Priority Scheduling**

- 우선순위에 따라 CPU 할당
- 선점형/비선점형
- 특정 프로세스가 무기한 대기하는 상태가 올 수 있음
- aging: 오래기다리는 프로세스의 우선순위를 높여줌으로써 문제점을 해결하는 방식



​	**Round Robin**

- 각 프로세스에 동일한 할당 시간 부여(들어오는 순서로)
- 할당 시간만큼씩만 CPU를 부여받고, 시간이 지나면 ready queue에 삽입
- CPU 사용 시간이 랜덤한 프로세스들이 섞여있을 경우 효율적
- Response time이 빨라짐
- 공정한 스케줄링 방식
- 할당 시간이 너무 커지면 FCFS와 같아지고, 너무 작아지면 잦은 문맥교환으로 인해 오버헤드가 커진다. 



​	**MLQ(Multi Level Queue)**

- 각 Ready Queue마다 독자적인 스케줄링 알고리즘 사용 
- 상위 Ready Queue -> 하위 ReadyQueue
- 선점형 



​	**MFQ(Multilevel Feedback Queue)**

- 각 Ready Queue마다 할당 시간을 부여하여 비선점형으로 운용

​	

cf) 대기시간: 프로세스가 대기한 평균 시간

​     반환시간: 대기시간 + 실행시간



### 병행 프로세스

------

**언어적 표현과 병행 문장**

- Fork와 Join 구조

  ![img](https://github.com/SeoJaeyeon/CS/blob/master/img/fork-join.PNG?raw=true)



- parbegin-parend 구조

![img](https://github.com/SeoJaeyeon/CS/blob/master/img/parbegin-parend.PNG?raw=true)





### 동기와 비동기

---

**Sync vs Async**

- 동기: 메소드를 실행시키고 반환할 때까지 blocking되어 있어 다른 작업을 수행하지 못하는 상태.
- 비동기: 메소드를 실행시키고 이벤트 큐나 백그라운드 스레드에 task를 넘긴 후, 바로 다음 작업을 수행하는 상태. 언제 반환될지 예측할 수 없음.



### 프로세스 동기화

---

**동기화**

둘 이상의 프로세스가 시간에 따라 실행 순서가 이루어져야 할 경우, 그 순서를 결정하는 것



**임계영역(Critical Section)**

공유 자원을 동시에 접근하는 작업을 실행하는 코드 영역



**임계영역 문제(Critical Section Problem)**

프로세스들이 임계영역을 함께 사용할 수 있는 프로토콜을 설계하는 것

​	<u>해결을 위한 조건</u>

- Mutual Exclusion(상호배제): 어떤 프로세스가 임계 영역에서 실행 중이면, 다른 프로세스들은 그들이 가진 임계영역에서 실행될 수 없음.
- Progress(진행): 임계 영역에서 실행 중인 프로세스가 없다면, 별도의 동작이 없는 프로세스들만 임계 영역 진입 후보로서 참여될 수 있음. 프로세스들 중 하나는 유한한 시간 내에 진입할 수 있어야 함.
- Bounded Waiting(한정된 대기): 프로세스가 임계 영역에 진입 신청 후 부터 받아들여질 때까지, 다른 프로세스들이 임계 영역에 진입하는 횟수는 제한이 있어야 함



**해결책**

`Lock(MUTEX)`

- 하드웨어 기반 해결책으로, critical section에 진입하는 프로세스는 Lock을 획득하고, 나올 때 Lock을 해제함으로써 동시 접근을 방지함
- 단점: 다중 처리기 환경에서 시간 효율성으로 인해 적용할 수 없음. Busy Waiting



`Semaphores`

- 소프트웨어 기반 해결책으로 Counting Semaphores와 Binary Semaphores를 구분함
- Counting Semaphores: 가용한 개수를 가진 자원에 대한 접근 제어. 세마포는 가용한 자원의 개수로 초기화됨. 자원을 사용하면 세마포 감소, 방출하면 세마포 증가
- Binary Semaphores: 0과 1사이의 값만 가능하며 다중 프로세스들 사이의 Critical section 문제를 해결하기 위해 사용. 일종의 Locking 매커니즘.
-  단점: Busy Wating, CPU 시간 낭비/ Deadlock



cf) MUTEX vs Semaphores

공유자원에 대한 접근권한, Lock을 하나만 가질 수 있는 것은 MUTEX, 여러개 가질 수 있는 것은 세마포어



`모니터`

- 고급 언어의 설계 구조물로서, 개발자의 코드를 상호배제 하게끔 만드는 추상화된 데이터 형태
- 자바 Object 클래스의 synchronized 키워드와 wait(), notify(), notifyAll() 함수로 구현된다. synchronized 키워드로 공유자원에 접근하고 wait 함수에 의해 block되며, notify 함수를 통해 block을 해제하는 방식으로 사용된다. 



cf) Semaphores vs Monitor

모니터는 키의 획득과 해제가 세마포어보다 간단함. 



**교착상태**

두 개 이상의 작업이 서로 상대방의 작업이 끝나기 만을 기다리고 있기 때문에 아무것도 완료되지 못하는 상태

​	**필요조건**

- 상호배타(Mutual exclusion)

- 보유 및 대기(Hold and Wait)

- 비선점(No Preemption)

- 환형대기(Circular wait)

  **교착상태 해결 방법**

- 교착상태 방지: 교착상태 필요조건 중 한 가지 이상을 불만족 시키는 방법.

- 교착상태 회피: 자원을 할당할 때 안전하게 할당하는 것

- 교착상태 검출 및 복구 방법: 교착상태를 허용하고 발생 시 복구하는 방법. 프로세스 일부 강제 종료 또는 자원 선점

- 교착상태 무시: 종료 후 재시동



### 메모리 관리 전략

---

**메모리 관리 배경**

- Swapping: CPU 할당 시간이 종료된 프로세스를 보조기억 장치로 보내고, 다른 프로세스를 메모리로 불러 들이는 과정
- 단편화(Fragmentation): 메모리의 swapping의 반복으로 인해 발생하는 메모리의 빈 공간. 이 빈 공간에는 어떤 프로세스도 할당될 수 없음. 
  - 외부 단편화: 메모리 공간 중 사용 못하게 되는 일부 부분
  - 내부 단편화: 메모리가 분할되어 있을 때, 분할된 부분에 프로세스가 할당되고 남은 공간. 



**외부 단편화를 해결하는 방식**

- 연속 메모리 할당
  - 최초 적합(First-fit): 메모리를 순차적으로 탐색하여 가장 먼저 발견하고 들어갈 수 있는 공간에 프로세스 적재
  - 최적 적합(Best-fit): 남는 공간이 가장 없는 공간에 적재
  - 최악 적합(Worst-fit): 가장 많이 넘는 공간에 프로세스 할당
- Compaction(압축): 단편화된 공간을 한 곳에 모음
- 페이징(Paging): 연속 메모리 할당, Compaction의 한계를 보완하기 위한 방식으로 "프로세스를 일정 크기인 페이지로 잘라서 메모리에 적재하는 방식"을 말한다. 메모리의 낭비 공간(hole)과 프로세스 모두 페이지 단위로 나눈다. 여기서 물리 메모리를 나누는 단위는 프레임이라고 한다.
- 세그멘테이션(Segmentation): 메모리를 모든 같은 크기의 페이지로 분리하여 운용하는 페이징과 달리, 서로 다른 크기의 논리적 단위인 세그먼트로 분할하여 운용하는기법으로 외부 단편화의 문제가 있다.
- 문제 풀이 

 ```xml
Q. 페이지 크기가 4bytes 이고 페이지 0 - 3까지 각 5,6,1,2 값을 가질 때, 논리 주소 13에 해당하는 실제 메모리 상의 주소는 무엇인가? 
 ```

13 = 1101(2) 

하위 2비트가 오프셋, 상위 (4-2)비트가 페이지 번호이므로, 페이지는 11(2) =3번

3에 해당하는 물리주소 2 = 10(2) 

1001 (10뒤에 오프셋을 붙임) = 9

정답: 9

  

### 가상 메모리

---

메인 메모리보다 크기가 큰 프로세스를 실행시키기 위해 나온 방식으로, 필요한 부분만 메인 메모리에 올림으로써 그 크기기를 줄임



**요구페이징**

프로세스를 페이지 단위로 자른 후, 페이지 마다 필요한 부분/필요하지 않은 부분으로 나눈다. 여기서 필요한 페이지만 메모리에 적재한다.

- 페이지 부재(Page Fault): CPU에서 메모리를 요청할 때, 페이지 테이블에서 접근하려는 페이지가 메모리에 없다고 표시되어있는 경우 페이지 부재 현상이라고 한다. 이때, Backing store에서 해당 페이지를 가져와서 메모리에 적재한 후 valid 비트를 변경한다.

- pure demand paging: 처음부터 모든 페이지를 적재시키지 않고 CPU가 요구할 때 valid를 바꾸어 페이지를 적재하는 방법. 페이지 부재 현상 잦음
- prepaging: 필요할 것 같은 페이지를 적재시키고 필요할 때 다른 페이지를 적재시키는 방법. 처리 속도는 빠르지만 메모리 낭비.

- 유효접근시간(Effective Access Time): 평균적으로 CPU가 요구할 때 메모리를 통해 읽혀지는 시간 

  `T eff = (1-P)T m + P*Tp`	(P= 페이지 결함이 일어날 확률, Tm= 메인 메모리에서 CPU가 바로 실행할 수 있는 시간, Tp=운영체제가  backing store인 하드디스크에서 페이지를 찾고 메모리에 올리고 실행할 수 있는 시간(seek time, rotational delay, transfer time))

- 지역성의 원리: CPU가 참조하는 주소가 지역에 모여져 있다는 의미. 
  - 시간적 지역성: 한번 읽었던 코드를 다시 읽을확률이 높음
  -  공간적 지역성: 코드를 읽을 때 현재 코드의  주변에 있는 코드를 읽을 확률이 높음

**페이지 교체**

페이지 부재 시 희생 페이지를 결정하는 알고리즘

- FIFO: 먼저 올라온 페이지를 먼저 내보내는 알고리즘
- OPT:  가장 사용하지 않을 페이지를 가장 우선 내려보내는 알고리즘. 실제로 적용되기 어려움
- LRU(Least-Recently-Used): 가장 오랫동안 사용하지 않은 페이지 먼저 내려보내는 알고리즘/ 계수기, 스택
- LFU(Least-Frequently-Used): 참조 횟수가 가장 작은 페이지를 교체하는 알고리즘
- MFU(Most Frequently Used): 참조 횟수가 가장 작은 페이지가 최근에 메모리에 올라왔다는 가정 하에, 사용 횟수가 가장 많은 페이지를 교체하는 알고리즘
- NUR(Not Used Recently): 최근에 사용하지 않은 페이지(참조비트와 변형비트)



### 캐시의 지역성

---

**캐시 메모리**

속도가 빠른 장치(CPU)와 느린 장치(메모리)간의 속도 차에 따른 병목 현상을 줄이기 위한범용 메모리



**캐시의 지역성 원리**

적중율(Hit rate)을 극대화시키기 위해 데이터의 지역성(Locality)의 원리를 사용함



**적중률**

- 캐시 기억장치 성능

  적중률 = 적중수 / 전체 메모리 참조 횟수

  평균 기역장치 접근 시간 = 적중률* 캐시메모리 접근시간 + (1-적중률)*주기억장치 접근시간



### Unix

---

**유닉스의 특징**

- 대화식 운영체제
- 다중 작업 기능
- 다중 사용자 기능
- 이식성
- 계층적 트리 구조 파일 시스템
- 개발 도구 제공
- 통신 유틸리티 제공
- 가상 메모리 기법 사용



**유닉스의 구조**

![img](https://github.com/SeoJaeyeon/CS/blob/master/img/unix.jpg?raw=true)

- 커널: 프로세스 제어블록/ 장치 드라이버/ 파일 서브 시스템/시스템 자원 관리/하드웨어 자원 관리/ 이식성
- 셸: 응용 프로그램으로부터 명령을 받아 커널에전송/ 입력 제어/ 프로그램 수행/ 