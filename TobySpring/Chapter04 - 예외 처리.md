# Chapter 04 - 예외 처리

## Unchecked

- 시스템 오류나 에러에 가까운 예외
- RuntimeException을 상속한 예외
- 명시적으로 try/catch나 throws를 해줄 필요 없음
- 항상 복구할 수 있는 에러가 아니면 Unchecked로 하고 바로 통보되게 하는 것이 좋음 

## Checked

- 비즈니스 흐름 상에서 예상할 수 있는 예외, 또는 애플리케이션 로직으로 해결할 수 있는 예외
- RuntimeException을 상속하지 않은 예외
- 명시적으로 try/catch나 throws를 해줘야함


## 예외 처리 전략

### 예외 회피

- 발생한 예외를 외부로 그냥 던져서 외부에서 처리하도록 함
- 메서드에 throws 문 선언으로 처리
- 처리해야할 곳으로 제어를 이전할 수 있게 해주나, 
- 처리해야할 곳에서도 throws를 쓰는 부작용

### 예외 복구

- try/catch로 예외를 잡아서 처리 후 정상 흐름으로 복귀시킨다.

    ```java
    try {
        file.open()
    } catch (IOException e) {
        errorCount++;
    }
    ```     

### 예외 전환

- try/catch로 예외를 잡아서 적절한 처리와 함께 다른 예외로 전환해서 외부로 던진다.

    ```java
    try {
        file.open()
    } catch (IOException e) {
        logger.warn(e);
        throw new UncheckedException(e);
    }
    ```     
- Checked 예외를 잡아서 Unchecked 예외로 전환해서 던지면 예외 발생 메서드를 호출한 외부 메서드에서는try/catch나 throws가 필요없어진다.

## DataAccessException

- SQLException은 JDBC에서 사용되는 Checked 예외로, 
- 대부분의 경우 복구가 불가능하므로 Unchecked 예외로 감싸서 던지는 것이 바람직하다.
- DBMS 벤더 별로 예외 코드도 다를 수 있다.
- JDO, JPA, Hibernate 등은 SQLException 대신 Unchecked 예외를 사용한다.
- 따라서 DAO 클래스에서 SQLException에 직접 의존하게 하면 다른 datasource를 적용하기 어려워진다.
- 따라서 DAO 클래스도 인터페이스와 구현체로 분리하는 것이 좋다. 
- 스프링에서는 DB에 독립적으로 적용할 수 있는 추상화된 Unchecked 예외인 DataAccessException 제공
- JdbcTemplate에서는 Unchecked 예외인 DataAccessException을 이용해서 호스트 코드에서 try/catch를 할 필요 없게 만들어준다. 

## JdbcTemplate의 3대 기능

> - 자원 반납 코드를 DAO 코드에서 제거
> - 예외 처리 코드를 DAO 코드에서 제거
> - 트랜잭션 관리 코드를 DAO 코드에서 제거(5장 참고) 