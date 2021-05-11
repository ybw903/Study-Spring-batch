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


