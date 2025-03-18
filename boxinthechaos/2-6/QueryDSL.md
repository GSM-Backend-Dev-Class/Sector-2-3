# Query DSL이란?
QueryDSL은 오픈소스 프로젝트로 JPQL을 Java코드로 작성할 수 있게 하는 라이브러리이다 

## QueryDSL을 사용하는 이유
1. 자바코드로 쿼리문을 작성하므로 오류를 컴파일러 시점에서 잡을 수 있다
> 기존 JPQL은 쿼리를 문자열로 작성해야 한다
>
> 만약 오타가 있거나 오류가 있어도 컴파일러 시점에서 알려주지 않고 런타임 시점에 오류가 발생하기 때문에 실행시키기 전에 알 방법이 없다
2. 복잡한 동적 쿼리를 쉽게 사용가능하다
> JPQL을 이용하여 동적 쿼리를 다루기 위해서는 문자열을 조건에 맞게 조합해서 사용해야 한다 
>
> 이는 코드도 복잡해지고 런타임 애러를 발생시키는 치명적인 단점을 가지고 있다

## QueryDSL 설정
QueryDSL은 자바 버전, SpringBoot 버전 등 실행 환경에 따라 설정하는 방법이 상이하므로 각자 환경에 맞는 설정법을 찾아보길 추천한다

이 velog에서는 SpringBoot 3.2.0, Java 17 환경에서 설정하는 방법을 알려준다 한다
```
// build.grdle
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.0'
	id 'io.spring.dependency-management' version '1.1.4'
}

group = 'study'
version = '0.0.1-SNAPSHOT'

java {
	sourceCompatibility = '17'
}

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.9.0'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'

	//test 롬복 사용
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'

	//QueryDSL 추가
	implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
	annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jakarta"
	annotationProcessor "jakarta.annotation:jakarta.annotation-api"
	annotationProcessor "jakarta.persistence:jakarta.persistence-api"
}

tasks.named('test') {
	useJUnitPlatform()
}

clean {
	delete file('src/main/generated')
}
```
`QueryDSL` 추가라고 표시된 주석 아랫 부분을 `build.gradle`에 추가하고 `clean` 부분도 추가한다

그 후 gradle에서 compileJava를 실행시켜주면 Q클래스가 생성된 것을 확인할 수 있다

## QueryDSL 사용
### 기본문법
QueryDSL은 select, from, where 등 쿼리 작성에 필요한 키워드를 메서드 형식으로 제공한다
```
@Autowired
EntityManager em;

JPAQueryFactory queryFactory;

// 테스트 실행 전 데이터 삽입
@BeforeEach
public void before() {

  queryFactory = new JPAQueryFactory(em);
  Team teamA = new Team("teamA");
  Team teamB = new Team("teamB");
  em.persist(teamA);
  em.persist(teamB);

  Member member1 = new Member("member1", 10, teamA);
  Member member2 = new Member("member2", 20, teamA);
  Member member3 = new Member("member3", 30, teamB);
  Member member4 = new Member("member4", 40, teamB);

  em.persist(member1);
  em.persist(member2);
  em.persist(member3);
  em.persist(member4);
}
```
QueryDSL을 사용하기위해선 JPAQueryFactory가 필요하다

여기서는 JPAQueryFactory 객체를 EntityManager 객체로 생성했지만 Bean으로 등록해서 사용하는 방법도 있다
```
@Test
void startQueryDSL() {
	
    // 두 방법 동일 -> QMember를 static import하여 member로 사용
	//QMember qMember = new QMember("m");
    //QMember qMember = QMember.member;
    
    //username이 member1인 member 조회
    Member findMember = queryFactory
    		.select(member)
            .from(member)
            .where(
            	member.username.eq("member1"),
                member.age.eq(10))
            .fetchOne();
}
```
위 코드와 같이 빌더 형식으로 쿼리를 작성할 수 있다

대부분을 쿼리 작성 키워드는 모두 사용 가능하다
(join, on, groupBy, orderBy 등)

### Projection
기존 JPA에서 프로젝션을 하기 위해서는 패키지 명을 모두 적어줘야 했습다만 QueryDSL에서는 쉽게 DTO로 데이터를 직접 뽑아내는 방법이 있다
```
@Data
@NoArgsConstructor
public class MemberDto {

    private String username;
    private int age;

    @QueryProjection
    public MemberDto(String username, int age) {
        this.username = username;
        this.age = age;
    }
}
```
**1. Projections 메서드 사용**
```
/**
 * Setter로 값 주입
 */
@Test
void findDtoBySetter() {

    List<MemberDto> result = queryFactory
            .select(Projections.bean(MemberDto.class, member.username, member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}

/**
 * Field에 바로 주입 (필드명)
 */
@Test
void findDtoByField() {

    List<MemberDto> result = queryFactory
            .select(Projections.fields(MemberDto.class, member.username, member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}
    
/**
 * 생성자로 주입 (필드 타입)
 */
@Test
void findDtoByConstructor() {

    List<UserDto> result = queryFactory
            .select(Projections.constructor(UserDto.class, member.username, member.age))
            .from(member)
            .fetch();

    for (UserDto userDto : result) {
        System.out.println("memberDto = " + userDto);
    }
}
```
**2. @QueryProjection**
- DTO 생성자에 `@QueryProjection` 애노테이션을 붙여주고 `compileJava`를 실행하면 DTO의 Q클래스가 생성되는 것을 확인할 수 있다
- DTO가 QueryDSL에 의존하게 되기 때문에 이런 사항을 고려하고 사용해야한다

```
@Test
void findDtoByQueryProjection() {

    List<MemberDto> result = queryFactory
            .select(new QMemberDto(member.username, member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}
```
### 동적 쿼리 작성
JPQL에서는 동적 쿼리를 문자열 방식으로 처리해 많은 불편함이 있었습니다

QueryDSL에서는 `BooleanBuilder`와 `where절 다중 파라미터`를 이용해 처리할 수 있습니다

더 많이 사용하고 이점이 많은 방식인 `where절 다중 파라미터` 방식에 대해 알아보자
```
@Test
void dynamicQuery_WhereParam() {

    String usernameParam = "member1";
    Integer ageParam = 10;

    List<Member> result = searchMember(usernameParam, ageParam);
    assertThat(result.size()).isEqualTo(1);
}

private List<Member> searchMember(String usernameCond, Integer ageCond) {

    return queryFactory
            .selectFrom(member)
            .where(usernameEq(usernameCond), ageEq(ageCond))
            .fetch();
}

private BooleanExpression usernameEq(String usernameCond) {

    return usernameCond != null ? member.username.eq(usernameCond) : null ;
}

private BooleanExpression ageEq(Integer ageCond) {

    return ageCond != null ? member.age.eq(ageCond) : null;
}

private BooleanExpression allEq(String usernameCond, Integer ageCond) {

    return usernameEq(usernameCond).and(ageEq(ageCond));
}
```
where절에서 BooleanExpression을 반환하는 메서드를 사용한다

그리고 usernameCond, ageCond에서 값이 없다면 null을 반환하고, 아닐 경우 값을 반환한다

where절에서 null 값인 경우 조건을 무시한다

`usernameEq` 메서드와 `ageEq` 메서드를 조합하여 `allEq` 메서드를 생성할 수 있다 이처럼 재활용 할 수 있고 객체지향적으로 관리할 수 있다는 점이 `where절 다중 파라미터` 방식의 장점이다

### UPDATE
```
@Test
void bulkUpdate() {

    long count = queryFactory
            .update(member)
            .set(member.username, "비회원")
            .where(member.age.lt(28))
            .execute();

    em.flush();
    em.clear();
}
```
벌크 연산은 영속성 컨텍스트를 거치지 않고 바로 DB에 접근하기 때문에 벌크 연산 수행 후 영속성 컨텍스트를 초기화 해야한다

### 사용자 정의 레포지토리 사용
![qwer](https://velog.velcdn.com/images/evan523/post/290d1af1-6c99-44b8-82b4-35ad59604b82/image.png)
- `MemberRepository`에 `JpaRepository`와 `MemberRepositoryCustom`을 다중 상속받아 SpringDataJPA 기능과 `MemberRepositoryCustom`의 구현체인 `MemberRepositoryCustomImpl`에서 구현한 메서드를 사용한다
- 사용자 정의 클래스인 경우 파일명이 `사용자 정의 인터페이스명 + Impl`인 클래스를 찾아 삽입해준다. 따라서, 사용자 정의 레포지토리 구현체의 파일명은 `인터페이스명 + Impl` 이여야 한다
**MemberRepository**
```
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {

    List<Member> findByUsername(String username);
}
```

**MemberRepositoryCustom**
```
public interface MemberRepositoryCustom {

    List<MemberTeamDto> search(MemberSearchCondition condition);
    Page<MemberTeamDto> searchPageSimple(MemberSearchCondition condition, Pageable pageable);
    Page<MemberTeamDto> searchPageComplex(MemberSearchCondition condition, Pageable pageable); // 카운트 쿼리 별도로
    
}
```

**MemberRepositoryCustomImpl**
```
public class MemberRepositoryCustomImpl implements MemberRepositoryCustom {

    private final JPAQueryFactory queryFactory;

    public MemberRepositoryCustomImpl(EntityManager em) {
        this.queryFactory = new JPAQueryFactory(em);
    }

    @Override
    public List<MemberTeamDto> search(MemberSearchCondition condition) {

        return queryFactory
                .select(new QMemberTeamDto(
                        member.id.as("memberId"),
                        member.username,
                        member.age,
                        team.id.as("teamId"),
                        team.name.as("teamName")
                ))
                .from(member)
                .leftJoin(member.team, team)
                .where(
                        usernameEq(condition.getUsername()),
                        teamNameEq(condition.getTeamName()),
                        ageGoe(condition.getAgeGoe()),
                        ageLoe(condition.getAgeLoe())
                )
                .fetch();
    }

    @Override
    public Page<MemberTeamDto> searchPageSimple(MemberSearchCondition condition, Pageable pageable) {

        QueryResults<MemberTeamDto> result = queryFactory
                .select(new QMemberTeamDto(
                        member.id.as("memberId"),
                        member.username,
                        member.age,
                        team.id.as("teamId"),
                        team.name.as("teamName")
                ))
                .from(member)
                .leftJoin(member.team, team)
                .where(
                        usernameEq(condition.getUsername()),
                        teamNameEq(condition.getTeamName()),
                        ageGoe(condition.getAgeGoe()),
                        ageLoe(condition.getAgeLoe())
                )
                .offset(pageable.getOffset())
                .limit(pageable.getPageSize())
                .fetchResults();

        List<MemberTeamDto> content = result.getResults();
        long total = result.getTotal();

        return new PageImpl<>(content, pageable, total);
    }

    @Override
    public Page<MemberTeamDto> searchPageComplex(MemberSearchCondition condition, Pageable pageable) {

        List<MemberTeamDto> content = queryFactory
                .select(new QMemberTeamDto(
                        member.id.as("memberId"),
                        member.username,
                        member.age,
                        team.id.as("teamId"),
                        team.name.as("teamName")
                ))
                .from(member)
                .leftJoin(member.team, team)
                .where(
                        usernameEq(condition.getUsername()),
                        teamNameEq(condition.getTeamName()),
                        ageGoe(condition.getAgeGoe()),
                        ageLoe(condition.getAgeLoe())
                )
                .offset(pageable.getOffset())
                .limit(pageable.getPageSize())
                .fetch();

        JPAQuery<Member> countQuery = queryFactory
                .selectFrom(member)
                .leftJoin(member.team, team)
                .where(
                        usernameEq(condition.getUsername()),
                        teamNameEq(condition.getTeamName()),
                        ageGoe(condition.getAgeGoe()),
                        ageLoe(condition.getAgeLoe())
                );

        return PageableExecutionUtils.getPage(content, pageable, countQuery::fetchCount);
    }

    private BooleanExpression usernameEq(String username) {

        return hasText(username) ? member.username.eq(username) : null;
    }

    private BooleanExpression teamNameEq(String teamName) {

        return hasText(teamName) ? team.name.eq(teamName) : null;
    }

    private BooleanExpression ageGoe(Integer ageGoe) {

        return ageGoe != null ? member.age.goe(ageGoe) : null;
    }

    private BooleanExpression ageLoe(Integer ageLoe) {

        return ageLoe != null ? member.age.loe(ageLoe) : null;
    }
}
```