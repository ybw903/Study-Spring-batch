## 스프링 배치 학습

이 저장소는 스프링 배치 학습한 내용을 기록해둡니다.

#### 프로젝트 개발 환경
* JAVA 8
* Spring Boot 2.45
* Gradle

#### 의존성
* Lombok
* JPA
* H2
* MySQL
* Batch

#### 1. Job
스프링 Batch에서 Job은 하나의 배치 작업 단위입니다.

Job 내부에는 여러 Step이 존재하고, Step 안에는 Tasklet 또는 Reader&Processor&Writer 묶음이 존재합니다.

#### 2. 메타테이블

**BATCH_JOB_INSTANCE**

BATCH_JOB_INSTANCE 테이블은 Job Parameter에 따라 생성되는 테이블입니다.

Job Parameter는 Spring Batch가 실행 될 때 외부에서 받을 수 있는 파라미터입니다. 

동일한 Job이 Job Parameter가 달라지면 그때마다 Batch_Job_INSTANCE에 생성되며, 동일한 Job Parameter는 여러개 존재 할 수 없습니다.

**BATCH_JOB_EXECUTION**

JOB_EXECUTION와 JOB_INSTANCE는 부모-자식 관계입니다.

JOB_EXECUTION은 자신의 부모 JOB_INSTACNE가 성공/실패했던 모든 내역을 갖고 있습니다.

Spring Batch는 동일한 Job Parameter로 성공한 기록이 있을때만 재수행이 안됩니다.

**JOB, JOB_INSTANCE, JOB_EXECUTION**

Job은 작성한 Spring Batch Job입니다.

BATCH_JOB_EXECUTION_PARAM 테이블은 BATCH_JOB_EXECUTION 테이블이 생성될 당시에 입력받은 Job Parameter를 담고 있습니다.

#### 3. Spring Batch Job Flow
Step은 실제 Batch 작업을 수행하는 역할입니다.

Step에서는 Batch로 실제 처리하고자 하는 기능과 설정을 모두 포함하는 장소로 봅니다.

**Next**

next()는 순차적으로 Step들 연결시킬때 사용됩니다.

**지정한 Batch Job만 실행하는 방법**

application.yml에 아래 설정을 추가해줍니다.

```spring.batch.job.names: ${job.name:NONE}```

Spring Batch가 실행될때, Program arguments로 job.name 값이 넘어오면 해당 값과 일치하는 Job만 실행하겠다는 것입니다.

**조건별 흐름제어(Flow)**

앞의 step에서 오류가 나면 나머지 뒤에 있는 step 들은 실행되지 못합니다.

하지만 상황에 따라 정상일때는 Step B로, 오류가 났을때는 Step C로 수행해야할때가 있습니다.

- ```.on()```
    - 캐치할 ExitStatus 지정
    - ```*```일 경우 모든 ExitStatus 지정

- ```.to()```
    - 다음으로 이동할 Step 지정
    
- ```.from()```
    - 일종의 이벤트 리스너 역할
    - 상태값을 보고 일치하는 상태라면 ```to()```에 포함된 step을 호출합니다.
    - step1의 이벤트 캐치가 FAILED로 되있는 상태에서 추가로 이벤트 캐리하려면 from을 써야만 함
    
- ```.end()```
    - end는 FlowBuilder를 반환하는 end와 FlowBuilder를 종료하는 end 2개가 있음
    - ```on("*")```뒤에 있는 end는 FlowBuilder를 반환하는 end
    - ```build()``` 앞에 있는 end는 FlowBuilder를 종료하는 end
    - FlowBuilder를 반환하는 end 사용시 계속해서 from을 이어갈 수 있음

**Batch Status vs. Exit Status**

BatchStatus는 Job 또는 Step 의 실행 결과를 Spring에서 기록할 때 사용하는 Enum입니다.

ExitStatus는 Step의 실행 후 상태를 얘기합니다.

**Decide**

위에서 진행했던 방식은 2가지 문제가 있습니다.

1. Step이 담당하는 역할이 2개 이상이 됩니다.

   실제 해당 Step이 처리해야할 로직외에도 분기처리를 시키기 위해 ExitStatus 조작이 필요합니다.
   
2. 다양한 분기 로직 처리의 어려움

   ExitStatus를 커스텀하게 고치기 위해선 Listener를 생성하고 Job Flow에 등록하는 등 번거로움이 존재합니다.

Spring Batch에서는 Step들의 Flow속에서 분기만 담당하는 JobExecutionDecider 타입이 있습니다.