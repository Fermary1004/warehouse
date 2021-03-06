# Chapter 1

본 내용은 토비의 스프링 3 책의 내용을 정리한 것입니다.

토비의 스프링은 스프링 뿐아니라 객체 지향의 기본 원리, 테스팅, 예외 처리, 디자인 패턴 등 Java 개발자라면 반드시 알아야 하는 내용을 스토리 전개 방식으로 점진적으로 친절하게 설명해주는 명저입니다. 똑같은 내용으로 미국에서 영어로 출간되었다면 Jolt상을 받기에도 충분한 책입니다.

책의 절대적인 가격은 높아보이지만 웬만한 책 두어권 보는 것보다 이 한 권의 책을 여러번 보는 것이 비용 대비 효과가 훨씬 좋을 것입니다.
 
이 책을 읽고나서 아낄 수 있는 많은 시간, 높아질 몸값을 생각하면 충분히 투자할 가치가 있는 책입니다. 

<a href='http://www.acornpub.co.kr/book/toby-spring3-1-set' target='_blank'>http://www.acornpub.co.kr/book/toby-spring3-1-set</a>

## 초난감 DAO

DAO의 메서드 하나에서 DB연결, SQL, CRUD, DB연결자원반납을 다 책임진다.

```java
class UserDao {
    public void crud1(User user) throws ... {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost~~~", "id", "password");

        PreparedStatement ps = c.preparedStatement("SQL with ?, ?, ?, ...");
        ps.setSting(1, user.getId());
        ....

        ps.executeUpdate();

        ps.close();
        c.close();
    }
}
```

![](http://i.imgur.com/KX1k4Os.png)


**먼저 DB연결부터 분리해보자!**

## 템플릿 메서드 패턴

```java
public abstract class UserDao {
    // DB연결 부분은 subclass에서 결정 
    protected abstract Connection getConnection() throws ... ;

    public void crud1(User user) throws ... {        
        Connection c = getConnection();

        PreparedStatement ps = c.preparedStatement("SQL with ?, ?, ?, ...");
        ps.setSting(1, user.getId());
        ....

        ps.executeUpdate();

        ps.close();
        c.close();
    }
}
```


- getConnection()을 abstract로 해서 DB연결 부분을 subclass에서 결정
- UserDao는 더이상 구체적인 DB연결 부분을 신경쓰지 않는다.

## 전략 패턴

```java
interface DataSource {
    Connection getConnection();
}

public class UserDao {
    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    private Connection getConnection() {
        return dataSource.getConnection();
    }

    public void crud1() {
        // Connection 을 얻어오는데 구체적인 JDBCDriver 정보에 의존하지 않고
        // 누군가가 setDataSource()로 주입해준 DataSource에만 의존
        Connection c = getConnection(); 

        PreparedStatement ps = c.preparedStatement("SQL with ?, ?, ?, ...");
        ps.setSting(1, user.getId());
        ....

        ps.executeUpdate();

        ps.close();
        c.close();
    }
}
```

![](http://i.imgur.com/dTB61Sw.png)

- DataSource라는 인터페이스를 context로 가지고 dataSource를 UserDao의 소스 코드 변경 없이 변경 가능
- DataSource에 주입되는 DataSource 인터페이스의 구현체는 외부에서 주입받는다.

**DataSource를 누가 주입해주나?**

## Factory Pattern

```java
public class DaoFactory {
    public UserDao getUserDao() {
        UserDao userDao = new UserDao();
        userDao.setDataSource(new ADataSource());
        return userDao; 
    }
}

public class UserDaoTest {
    public static void main(String[] args) throws ... {
        UserDao dao = new DaoFactory().getUserDao();

        ...
    }
}
```
![](http://i.imgur.com/LYHnFmE.png)

- DaoFactory가 필요한 객체 생성(new ADataSource(), new UserDao()) 및 **관계 설정**(ADataSource를 UserDao에 주입)

객체 지향은 결국 협업이고, 협업의 바탕은 관계 설정인데, 그 **관계 설정을 Factory가 담당하는 것이다. 이렇게 보니 Factory가 중요하다.** Factory를 제대로 만들어보자.

## ApplicationContext

- Factory에서 생성하는 객체들을 Bean이라 하면, Bean을 생성하고 관계를 맺어주는 것은 `BeanFactory의` 책임

	> Bean의 요건
	> - 기본 생성자
	> - get, set
 
- BeanFactory에 Bean을 Singleton 방식으로 관리하는 능력, 의존관계 검색 기능 등을 추가해서 BeanFactory의 기능을 확장한 것이 `ApplicationContext`

    ```java
    public class UserDaoTest {
        public static void main(String[] args) throws ... {
            ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
            UserDao dao = context.getBean("userDao", UserDao.class);
    
            ...
        }
    }
    ```
![](http://i.imgur.com/bFHCIC9.png)
