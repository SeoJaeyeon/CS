## OS(Operating System)

**목차**

- [프로세스 vs 스레드](#프로세스와-스레드의-차이)
- [프로세스 스케줄러](#프로세스-스케줄러)

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

**장기스케줄러**

"디스크(secondary storage)의 프로세스를 메모리에 옮겨 CPU의 할당을 기다리도록 하는 스케줄러(admit). 다중프로그래밍의 정도를 제어함."

new(프로세스 생성), suspend(메모리의 프로세스가 디스크로 옮겨진 상태) -> ready 

cf) 시분할 시스템에서는 장기 스케줄러가 없음.

**단기스케줄러**

"메모리의 프로세스가 CPU의 할당을 받도록 하는 스케줄러(dispatch)"

ready -> running



**중기스케줄러**

"메모리의 프로세스를 모두 디스크로 옮기는 스케줄러. 멀티프로그래밍 제어(swap out)"

ready -> suspend



### 큐의 종류

**Job Queue** 

"디스크에서 메모리로 옮겨지는 것을 기다리는 프로세스의 집합"

**Ready Queue**

"메모리에서 CPU의 할당을 기다리는 프로세스의 집합"

**Device Queue**

"Device I/O 작업을 기다리는 프로세스의 집합"

