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
    }2

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
# 사전 준비
**gradle 설정**
```Java
 dependencies {
    implementation 'com.querydsl:querydsl-jpa:5.0.0'
    annotationProcessor 'com.querydsl:querydsl-apt:5.0.0:jpa'
    annotationProcessor 'jakarta.persistence:jakarta.persistence-api'
    annotationProcessor 'jakarta.annotation:jakarta.annotation-api'
}
    tasks.withType(JavaCompile).configureEach {
    options.annotationProcessorGeneratedSourcesDirectory = file("$buildDir/generated/sources/annotationProcessor/java/main")
}

sourceSets {
    main {
        java {
            srcDirs = ["$buildDir/generated/sources/annotationProcessor/java/main"]
        }
    }
}

```
**yml 설정**
```yml
spring:
  jpa:
    properties:
      hibernate:
        format_sql: true
    show-sql: true
```
bean 등록
```Java
package com.example.demo.config;

import com.querydsl.jpa.impl.JPAQueryFactory;
import jakarta.persistence.EntityManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class QueryDslConfig {

    @Bean
    public JPAQueryFactory jpaQueryFactory(EntityManager entityManager) {
        return new JPAQueryFactory(entityManager);
    }
}
```
# QClass
## 정의
- Querydsl를 사용하면 기본적으로 QClass라는 자식이 생성됨
- 엔티티 클래스의 메타 데이터를 가지고 있는 클래스
## 만들어지는 과정
- QClass는 컴파일 단계에서 엔티티를 기반으로 생성
- JPA_APT가 @Entity와 같은 특정 어노테이션을 갖고 해당 클래스를 분석후 만듬
## 사용이유
- Querydsl은 이를 이용하여 타입 안정성(Type safe)를 보장하며 쿼리를 작성
- 엔티티 속성을 직접 참조하고 조합하여 쿼리를 작성 가능
- 컴파일 시점에 오류를 확인할 수 있음
- 타입에 맞지 않는 연산이나 비교를 시도하면 컴파일러가 오류를 감지할 수 있음
- 속성 이름을 직접 기억하지 않고 쿼리 작성을 보다 편리하게 할 수 있음