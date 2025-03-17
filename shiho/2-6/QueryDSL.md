# 정의
- JPA에서 **타입 안전하고 직관적인** SQL 쿼리를 생성할 수 있도록 **도와주는 라이브러리**
- 기존의 **JPQL, Criteria API**보다 **더 가독성**이 좋고 **유지보수**가 **편리**
# 비교
- 기존 sql
```Java
@Query("SELECT u FROM User u WHERE u.name = :name")
List<User> findByName(@Param("name") String name);
```
- 문자열 기반이라 오타 검사 불가, 유지보수 어려움
```Java
List<User> users = queryFactory
    .selectFrom(user)
    .where(user.name.eq("홍길동"))
    .fetch();
```
- 타입 **안전성 보장** (컴파일 타임에 오류 감지)
- 메서드 체이닝 방식이라 **가독성이 좋음**
- 복잡한 동적 **쿼리 작성이 용이**
# 예제
```Java
import com.querydsl.jpa.impl.JPAQueryFactory;
import org.springframework.stereotype.Repository;
import java.util.List;
import static com.example.demo.Entity.QGame.game;

@Repository
public class GameRepository {
    private final JPAQueryFactory queryFactory;

    public GameRepository(JPAQueryFactory queryFactory) {
        this.queryFactory = queryFactory;
    }

    // ✅ 1. 게임 이름으로 검색
    public List<Game> findByName(String name) {
        return queryFactory
            .selectFrom(game)
            .where(game.name.eq(name))
            .fetch();
    }

    // ✅ 2. appId로 검색 (Natural ID 사용)
    public Game findByAppId(Long appId) {
        return queryFactory
            .selectFrom(game)
            .where(game.appId.eq(appId))
            .fetchOne();
    }
}
```
```Java
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class GameService {
    private final GameRepository gameRepository;

    public GameService(GameRepository gameRepository) {
        this.gameRepository = gameRepository;
    }

    public void testQueryDSL() {
        System.out.println("🔹 게임 이름이 'Minecraft'인 게임 찾기");
        List<Game> games = gameRepository.findByName("Minecraft");
        games.forEach(game -> System.out.println(game.getName()));

        System.out.println("🔹 appId가 123456인 게임 찾기");
        Game game = gameRepository.findByAppId(123456L);
        System.out.println(game.getName());
    }
}
```
# TCL
## 정의
- DCL(Data Control Language)에서 트랜잭션을 제어하는 명령인 **COMMIT**과 **ROLLBACK만**을 따로 **분리**해서 TCL이라고 표현
- 데이터 변경 작업(INSERT, UPDATE, DELETE 등)을 확정(Commit)하거나 취소(Rollback)할 때 사용하는 명령어
## COMMIT
- 트랜잭션 처리가 **정상적으로 종료**되어 트랜잭션이 수행한 **변경 내용**을 데이터베이스에 **반영**하는 연산
- 내용을 변경한 트랜잭션이 **완료**되면 그 트랜잭션에 의해 데이터베이스는 **새롭게 일관된 상태**로 **변경**되며, 이 상태는 **시스템 오류**가 발생하더라도 **취소되지않음**
## ROLLBACK
- 하나의 트랜잭션 처리가 **비정상적으로 종료**되어 데이터베이스의 **일관성이 깨졌을 때**, 트랜잭션이 행한 모든 변경 **작업을 취소**하고 **이전 상태로 되돌리**는 연산
- 해당 트랜잭션은 받았던 자원과 잠금(LOCK)을 모두 반환하고, 재시작(Restart)되든지 폐기
## SAVEPOINT
- 현재의 트랜잭션을 **작게 분할**하는 명령어
- 저장된 SAVEPOINT는 ROLLBACK TO SAVEPOINT문을 사용하여 **지정한 곳까지 ROLLBACK가능**
- 여러개의 SQL문을 수반하는 트랜잭션의 경우, 사용자가 트랜잭션 **중간 단계**에서 **SAVEPOINT를 지정가능**
- 이 SAVEPOINT는 차우 **ROLLBACK과 함께 사용**하며, 현재 트랜잭션 내의 특정 **SAVEPOINT까지 ROLLBACK가능**
## 예시
```sql
START TRANSACTION;  -- 트랜잭션 시작

INSERT INTO users (name, age) VALUES ('홍길동', 25);
UPDATE users SET age = 26 WHERE name = '홍길동';

COMMIT;  -- 변경 사항을 DB에 영구 저장
```
```sql
START TRANSACTION;

DELETE FROM users WHERE name = '홍길동';

ROLLBACK;  -- 삭제 취소 (원래 상태로 되돌림)
```
```sql
START TRANSACTION;

UPDATE users SET age = 30 WHERE name = '김철수';
SAVEPOINT sp1;  -- 저장 지점 설정

UPDATE users SET age = 35 WHERE name = '이영희';
ROLLBACK TO sp1;  -- sp1 지점으로 롤백 (이영희 변경 사항은 취소, 김철수는 유지)

COMMIT;  -- 김철수 변경 사항만 반영

```
```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;

UPDATE users SET age = 40 WHERE name = '박지성';

COMMIT;

```