# Chapter 06 - AOP

본 내용은 토비의 스프링 3 책의 내용을 정리한 것입니다.

토비의 스프링은 스프링 뿐아니라 객체 지향의 기본 원리, 테스팅, 예외 처리, 디자인 패턴 등 Java 개발자라면 반드시 알아야 하는 내용을 스토리 전개 방식으로 점진적으로 친절하게 설명해주는 명저입니다. 똑같은 내용으로 미국에서 영어로 출간되었다면 Jolt상을 받기에도 충분한 책입니다.

책의 절대적인 가격은 높아보이지만 웬만한 책 두어권 보는 것보다 이 한 권의 책을 여러번 보는 것이 비용 대비 효과가 훨씬 좋을 것입니다.
 
이 책을 읽고나서 아낄 수 있는 많은 시간, 높아질 몸값을 생각하면 충분히 투자할 가치가 있는 책입니다. 

<a href='http://www.acornpub.co.kr/book/toby-spring3-1-set' target='_blank'>http://www.acornpub.co.kr/book/toby-spring3-1-set</a>

## Proxy

### 프록시(Proxy)

- 프록시는 클라이언트가 사용하려는 객체인 target 객체처럼 자신을 위장(target과 같은 인터페이스 구현)해서 클라이언트의 요청을 target 객체보다 먼저 받는다.
- 프록시는 이처럼 위장을 통해 target의 앞단에서 클라이언트의 요청을 가로채서 부가 기능을 추가하거나 접근 방식을 제어하는 객체를 통칭한다.

### 데코레이터(Decorator) 패턴

- 데코레이터는 프록시 중에서 런타임에 동적으로 target에 부가 기능을 추가하는 역할을 담당하는 객체
- 데코레이터 패턴은 target 코드를 건드리지 않고, 클라이언트의 호출하는 방식도 변경하지 않으면서 동적으로 새로운 기능을 추가할 때 유용 
- 데코레이터 패턴에서는 프록시가 하나로 제한되지 않으며, 여러 개의 데코레이터가 사용될 수 있다.
- 대표적인 예 : BufferedReader
    
    ```java
    try {
        BufferedReader br = new BufferedReader(new FileReader("a.txt"));
    } catch (IOException e) {
        ...
    } finally {
        // 프록시의 br.close()를 호출하면 내부적으로 target의 FileReader의 close()를 호출
        // 프록시를 직접 구현한다면 이처럼 target의 자원 반환 코드가 프록시에 구현되어 있어야 안전하다.
        if (br != null) try { br.close(); } catch (IOException e) {}
    } 
    ```

### 프록시(Proxy) 패턴

- 새로운 기능은 추가하지 않지만, target에 대한 접근 방식을 변경할 수 있게 해주는 프록시
- target의 지연 바인딩에 활용
- 대표적인 예 : Collections.unmodifiableCollection()이 반환하는 객체
    
    ```java
    Collection<List<User>> immutableList = Collections.unmodifiableCollection(list);
    immutableList.add(user); // immutable인 Collection에 add로 원소를 추가하면 UnsupportedOperationException 발생
    ```

### Dynamic Proxy

#### 프록시의 단점

- 프록시는 target이 구현하는 인터페이스의 특정 메서드가 아니라 모든 메서드를 구현해야함
    - 부가 기능이 추가되지 않는 메서드라도 단순히 target의 메서드를 호출하도록 구현해야함
- 프록시는 메서드 수준에서 관리 포인트를 확보
    - 동일한 부가 기능을 여러 메서드에 추가해야 한다면, 여러 메서드 부가 기능 코드를 모두 추가해야 하므로 중복 발생 
- 이런 단점을 해결해주는 것이 JDK의 Dynamic Proxy

#### JDK에서 제공하는 Dynamic Proxy

```java
UserService userTx = (UserService)Proxy.newProxyInstance(
    getClass().getClassLoader(), // 클래스로더
    new Class[] {UserService.class}, // 구현할 인터페이스
    new UserServiceTx(new UserServiceImpl()) // target(UserServiceImpl)과 부가 기능 프록시(UserServiceTx) 
);
```

- 프록시는 `InvocationHandler` 인터페이스를 구현해야함

#### JDK의 Dynamic Proxy 학습 테스트

```java
import org.junit.Test;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;

public class DynamicProxyTest {    

    @Test
    public void dynamicProxy() throws Exception {
        Target dynamicProxy =
                (Target)Proxy.newProxyInstance(
                        getClass().getClassLoader(),
                        new Class[] {Target.class},
                        new AsteriskProxy(new Target() {
                            @Override
                            public String sayHello(String name) {
                                return "Hello " + name;
                            }
                        })
                );
        assertThat(dynamicProxy.sayHello("Homo Efficio"), is("***Hello Homo Efficio***"));
    }
}

// target 객체의 interface
interface Target {
    String sayHello(String name);
}

// Dynamic Proxy가 되려면 java.lang.InvocationHandler를 구현해야함
class AsteriskProxy implements InvocationHandler {

    private Target target;

    public AsteriskProxy(Target target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 실제 target 객체의 메서드를 실행
        // target 객체의 모든 메서드가 이 invoke()에 의해 호출
        String realResult = (String)method.invoke(target, args);

        // 부가 기능 추가
        // target 객체의 모든 메서드가 이 invoke()에 의해 호출되므로
        // 부가 기능은 여기에만 추가하면 target 객체의 모든 메서드에 적용
        String decoratedResult = "***" + realResult + "***";

        return decoratedResult;
    
```

#### JDK의 Dynamic Proxy의 단점

- Proxy.newProxyInstance()로 생성되는 dynamic proxy는 일반적인 스프링 빈으로 등록 불가
    - FactoryBean 인터페이스를 구현한 클래스에서 getObject()의 구현부에서 Proxy.newProxyInstance()로 dynamic proxy를 만들면 빈으로 등록 가능
- 프록시를 이용해서 부가기능을 추가하는 것은 메서드 단위로 일어나는 일
    - 공통적인 부가 기능을 한 번에 여러 클래스에 추가 불가
- 프록시는 target을 프로퍼티로 가지고 있으므로
    - 트랜잭션 설정이라는 동일한 기능을 추가하더라도 target이 달라지면 별도의 프록시를 생성해야 한다.
    - 새로운 부가기능을 추가할 떄마다 프록시와 프록시 팩토리 빈을 함께 추가해야 한다.

### 스프링의 Dynamic Proxy

#### ProxyFactoryBean

- 프록시를 생성해서 Bean 객체로 등록시켜주는 팩토리 빈

#### MethodInterceptor

- InvocationHandler.invoke()는 target 객체에 대한 정보를 제공하지 않아서 target 객체 마다 프록시를 만들어야 했지만,
- MethodInterceptor.invoke()는 ProxyFactoryBean으로부터 target 객체에 대한 정보도 함께 제공받으므로, 여러 프록시에서 재사용될 수 있다.

#### Advice

- 부가 기능을 제공하는 객체

#### PointCut

- 부가 기능을 추가할 메서드를 선정하는 알고리듬을 담은 객체+






## 트랜잭션 관리 코드의 분리

- UserService 내에 Tx 관리가 남아있어 SRP에 위배
- biz 로직 메서드를 분리하고, Tx 메서드에서 biz 로직 메서드를 호출하도록 변경
- Tx 처리 클래스와 Biz 처리 클래스로 분리

![](http://i.imgur.com/Gijy9Yv.png)

### 트랜잭션 서비스 데코레이터

- UserServiceTx는 실제 비즈니스 로직을 담고 있는 target인 UserServiceImpl의 구현한 인터페이스와 동일한 UserService 인터페이스를 구현
- UserDaoTest의 요청을 target보다 먼저 받아서 트랜잭션 설정이라는 부가 기능을 동적으로 추가  

- 

## Mock 테스트

- updateLevels()를 테스트하려면 userDao, mailSender가 필요
- userDao가 동작하려면 DataSource, MailSender의 구현체와 설정 정보, 실제 연결 필요
- 외부 의존 오브젝트를 mock 오브젝트로 대체하면 실제 구현체와 독립되어 biz 메서드의 기능만 검증할 수 있는 테스트 작성 가능

### Mockito

- Mock 오브젝트 생성과 Mock 오브젝트 내의 메서드 실행을 쉽게 Mocking 해주는 라이브러리
    
    ```java
    // Service Test 메서드 내에서

    // DAO에 대한 Mock 오브젝트 생성
    UserDao mockUserDao = mock(UserDao.class);

    // Mock 오브젝트 주입
    userService.setUserDao(mockUserDao);

    // Mock 오브젝트의 메서드 호출 시 반환값 Stubbing
    // when(실제 수행 코드).thenReturn(반환되는 Mocking 값) 의 형식으로 사용
    when(mockUserDao.getAll()).thenReturn(this.users);
    
    // 테스트 할 서비스의 메서드를 호출하면
    // 서비스의 메서드가 내부적으로 DAO의 메서드를 호출하고,
    // 실제 DAO 메서드가 아니라 When().thenReturn()으로 Stubbing된 값이 반환됨
    List<User> users = userService.getAll();

    // 실제 호출되었는지 확인 가능
    verify(mockUserDao).getAll();

    // 값 테스트
    assertThat( ... );
    
    ```
