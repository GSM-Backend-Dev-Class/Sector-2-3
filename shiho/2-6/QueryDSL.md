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