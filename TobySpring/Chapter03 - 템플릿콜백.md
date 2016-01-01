# Chapter 03

## DB 관련 자원 반납

- DB연결을 담당하는 DataSource를 인터페이스로 분리하고, DataSource 구현체를 DI 받아오도록 개선되었지만, JDBC 리소스의 반납 관련 예외 처리 코드가 남아있다.

```java
public class UserDao {

    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public deleteAll() throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = dataSource.getConnection();
            ps = c.prepareStatement("delete from users");
            ps.executeUpdate();
        } catch(SQLException e) {
            throw e;
        } finally {
            if (ps != null) { try { ps.close(); } catch(SQLException e) {} }
            if (c != null) { try { c.close(); } catch(SQLException e) {} }
        }
    }

    public getAll() throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;
        ResultSet rs = null;

        try {
            c = dataSource.getConnection();
            ps = c.prepareStatement("select from users");
            rs = ps.executeQuery();
        } catch(SQLException e) {
            throw e;
        } finally {
            if (rs != null) { try { rs.close(); } catch(SQLException e) {} }
            if (ps != null) { try { ps.close(); } catch(SQLException e) {} }
            if (c != null) { try { c.close(); } catch(SQLException e) {} }
        }
    }    
}
```

## 자원 반납 코드의 중복

- 위와 같이 DAO 메서드마다 자원 반납 코드가 계속 중복된다.
- DAO 메서드를 다음과 같은 흐름이 있다.
    - dataSource에서 DB연결을 가져오고
    - 쿼리 생성
    - 쿼리 실행
    - 예외 발생 시 던지기
    - 모든 처리 후 자원 반납 처리
- 여기에서 변하는 부분(전략)은 쿼리 생성, 나머지는 변하지 않는다(컨텍스트).

### 전략 패턴을 통한 전략 분리

- 전략을 분리하자!

```java
public class UserDao {

    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public deleteAll() throws SQLException {
        // 비즈 로직을 담고 있는 전략을 생성해서 컨텍스트에 주입해주고
        // 로직의 실행 및 뒤처리는 jdbcContextWithStatementStrategy에 위임
        // 자원 반납 등의 지저분한 코드를 jdbcContextWithStatementStrategy로 모아서 중복 제거
        StatementStrategy strategy = new DeleteAllStatement();
        jdbcContextWithStatementStrategy(strategy);
    }

    public getAll() throws SQLException {
        // 비즈 로직을 담고 있는 전략을 생성해서 컨텍스트에 주입해주고
        // 로직의 실행 및 뒤처리는 jdbcContextWithStatementStrategy에 위임
        // 자원 반납 등의 지저분한 코드를 jdbcContextWithStatementStrategy로 모아서 중복 제거
        StatementStrategy strategy = new SelectAllStatement();
        jdbcContextWithStatementStrategy(strategy);   
    }

    public void jdbcContextWithStatementStrategy(StatementStrategy strategy) throws SQLException {
        // 비즈 로직 외에 변하지 않는 컨텍스트를 모아두고 재사용
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = dataSource.getConnection();
            // 주입 받은 비즈 로직으로 쿼리 생성
            ps = strategy.makeStatement(c);
            ps.executeUpdate();
        } catch(SQLException e) {
            throw e;
        } finally {
            if (ps != null) { try { ps.close(); } catch(SQLException e) {} }
            if (c != null) { try { c.close(); } catch(SQLException e) {} }
        }    
    }
}

public interface StatementStrategy {
    PreparedStatement makeStatement(Connection c) throws SQLException;
}

public class DeleteAllStatement implements StatementStrategy {
    // 실제 비즈 로직을 담는다.
    public PreparedStatement makeStatement(Connection c) throws SQLException {
        return c.prepareStatement("delete from users");
    }
}

public class SelectAllStatement implements StatementStrategy {
    // 실제 비즈 로직을 담는다.
    public PreparedStatement makeStatement(Connection c) throws SQLException {
        return c.prepareStatement("select * from users");
    }
}
```

- 조금 더 최적화해서 전략을 익명 클래스로 바꿔보자

```java
public void deleteAll() throws SQLException {
    jdbcContextWithStatementStrategy(
        new StatementStrategy(
            public PreparedStatement makeStatement(Connection c)
            throws SQLException {
                return c.prepareStatement("delete from users");
            }
        }
    }
}

public void add(final User user) throws SQLException {
    jdbcContextWithStatementStrategy(
        new StatementStrategy(
            public PreparedStatement makeStatement(Connection c)
            throws SQLException {
                PreparedStatement ps = 
                    c.prepareStatement("inser into users(id, name, password) values (?, ?, ?)");
                // 내부 클래스를 쓰면 outer의 변수인 user에 접근 가능
                ps.setString(1, user.getId());
                ps.setString(2, user.getName());
                ps.setString(3, user.getPassword());
                return ps;
            }
        }
    }
}
```

### JdbcContext로 컨텍스트 분리

- jdbcContextWithStatementStrategy()는 UserDao 뿐 아니라 다른 DAO에서도 사용될 수 있다.
- 분리해서 별도의 클래스로 만들어보자.

```java
public class JdbcContext {
    DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public void processStatement(StatementStrategy strategy) throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = this.dataSource.getConnection();
            // 주입 받은 비즈 로직으로 쿼리 생성
            ps = strategy.makeStatement(c);
            ps.executeUpdate();
        } catch(SQLException e) {
            throw e;
        } finally {
            if (ps != null) { try { ps.close(); } catch(SQLException e) {} }
            if (c != null) { try { c.close(); } catch(SQLException e) {} }
        }  
    }
}

public class UserDao {
    JdbcContext context;

    public void setJdbcContext(JdbcContext context) {
        this.context = context;
    }

    public void deleteAll() throws SQLException {
        // 컨텍스트에 있는 템플릿 메서드에
        // 콜백 오브젝트 주입
        this.context.processStatement(
            new StatementStrategy(
                public PreparedStatement makeStatement(Connection c)
                throws SQLException {
                    return c.prepareStatement("delete from users");
                }
            )
        );
    }
}
```
- 중복은 아직도 남아있다. 바로, 익명 클래스 생성 부분이다.
- 메서드로 빼서 최적화하자.

```java
public class JdbcContext {
    DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public void processStatement(StatementStrategy strategy) throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = this.dataSource.getConnection();
            // 주입 받은 비즈 로직으로 쿼리 생성
            ps = strategy.makeStatement(c);
            ps.executeUpdate();
        } catch(SQLException e) {
            throw e;
        } finally {
            if (ps != null) { try { ps.close(); } catch(SQLException e) {} }
            if (c != null) { try { c.close(); } catch(SQLException e) {} }
        }  
    }

    public void executeSql(final String query) {     
        // 쿼리 문자열만 받아서 익명 클래스 생성 후
        // 템플릿 메서드 호출       
        processStatement(
            new StrategyStatement(
                public PreparedStatement makeStatement(Connection c) throws SQLException {
                    return c.prepareStatement(query);
                }
            )
        )
    }
}

public class UserDao {
    JdbcContext context;

    public void setJdbcContext(JdbcContext context) {
        this.context = context;
    }

    public void deleteAll() throws SQLException {
        // 컨텍스트에 단순히 쿼리 문자열만 넘겨주면 된다!!
        this.context.executeSql("delete from users");
    }
}
```

![](http://i.imgur.com/EgUCT0U.png)


## Spring의 JdbcTemplate

- 위에서 JdbcContext가 하는 역할 담당
- update(), queryForInt(), queryForObject(), query() 등 메서드 지원
- 최근에는 JdbcTemplate를 확장한 NamedParameterJdbcTemplate 주로 사용


public class UserDao {
    JdbcTemplate jdbcTemplate;

    public void setJdbcTempate(JdbcTemplate template) {
        this.template = template;
    }

    public void deleteAll() throws SQLException {
        // 컨텍스트에 단순히 쿼리 문자열만 넘겨주면 된다!!
        this.template.update("delete from users");
    }
}
```