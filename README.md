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