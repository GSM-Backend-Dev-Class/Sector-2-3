# JPA_N+1
## N+1 Select문제
### 정의
N+1문제는 ORM 기술에서 특정 객체를 대상으로 수행한 쿼리가 해당 객체가 가지고 있는 연관관계 또한 조회하게 되면서 N번의 추가적인 쿼리가 발생하게 되는 문제를 이야기 한다
### 원인
N+1문제가 발생하게 되는 근본적인 원인은 관계형 데이터베이스와 객체지향 언어간의 패러다임 차이로 발생하게 된다 

객체는 연관관계를 통해 래퍼런스를 가지고 있으면 언제든지 메모리 내에서 Random Access를 통해 연관 객체에 접근할 수 있지만 RDB의 경우 Select 쿼리를 통해서만 조회가 가능하다

### N+1이 문제가 되는 이유
N+1 문제가 발생하면 쿼리가 배수적으로 증가하기 때문에 DB에 큰 부담이 발생하고 장애요소가 될 수 있습니다 또한 사용자 관점에 따라 지연율이 크게 증가 할 수도 있습니다

## N+1 Select 문제 해결법
### Eager Loading으로 N+1문제를 해결하려고 하면 안됩니다
- Eager Loading은 연관된 Entity객체를 한번에 조회하도록 하는 기능으로 특정 경우에 N+1문제를 부분적으로 해결해줄 수 있지만 사용하지 않는 것이 좋습니다
#### EAGER Loading을 사용해서는 안되는 이유
- 어떤 Entity 연관관계 범위까지 Join쿼리로 조회 해올지 예상하기가 힘들어지기 때문에 오히려 필요없는 데이터까지 로딩하여 비효율적일 수 있습니다
- 또한 Entity관계가 복잡해지면 N+1문제가 해결 되지 않는 경우가 많습니다
- **그 예시로 제가 작성한 코드에서도 다른 연관관계들로 인해EAGER Loading으로 설정해도 N + 1문제가 그대로 발생합니다**
```
@Entity
public class Article extends BaseEntity{
    @OneToMany(mappedBy = "article", fetch = FetchType.EAGER, cascade = CascadeType.REMOVE)
    private List<Opinion> opinions = new ArrayList<>();
}
```
> OneToOne관계에서 연관관계의 주인이 아닌 Entity에서는 Lazy Loading으로 설정하는 것이 불가능합니다
> 이 경우를 제외하곤 모두 Lazy Loading으로 설정해야 합니다

### Fetch Join + Lazy Loading
Fetch Join은 Root Entity에 대해서 조회 할 때 Lazy Loading 으로 설정 되어있는 연관관계를 Join쿼리를 발생시켜 한번에 조회할 수 있는 기능입니다

#### 적용사례
```
@Query("select Distinct a from Article a join fetch a.opinions")
List<Article> findAllArticleFetchJoinOpinion();
```

#### 결과
- 다음과 같이 총 3번 발생하던 쿼리가 한번의 join쿼리조회 됨을 알 수 있습니다
```
select
        distinct article0_.article_id as article_1_2_0_,
        opinions1_.opinion_id as opinion_1_11_1_,
        article0_.created_at as created_2_2_0_,
        article0_.updated_at as updated_3_2_0_,
        opinions1_.article_id as article10_11_1_,
        ...
        opinions1_.opinion_id as opinion_1_11_0__ 
    from
        article article0_ 
    inner join
        opinion opinions1_ 
            on article0_.article_id=opinions1_.article_id
```

### default_batch_fetch_size, @BatchSize
- Lazy Loading시 프록시 객체를 조회할 때 where in절로 묶어서 한번에 조회 할 수 있게 해주는 옵션입니다
- yml에 전역 옵션으로 적용할 수 있고 @BatchSize를 통해 연관관계 BatchSize를 다르게 적용할 수 있습니다

```
spring:
  jpa:
    properties:
        default_batch_fetch_size: 100
```
- Batch Size 100~1000정도로 적용하고 DBMS에 따라서 where in 절은 1000까지 제한하는 경우가 있으므로 1000이상은 설정을 잘 하지 않는다
- WAS는 BatchSize가 크면 어처피 데이터를 메모리에 로딩해야하는 것을 똑같기 때문에 좋지만, DB에서는 부담이 될 수 있기 때문에 적절하게 조절해야합니다

#### Fetch Join vs Batch Size
**Fetch Join의 한계를 Batch Size는 해결할 수 있습니다**
- Collection Fetch Join시 paging문제나 1개까지만 Fetch Join을 할 수 있는 문제를 해결할 수 있습니다
**쿼리 개수 관점**
- 쿼리 개수는 Fetch Join이 유리합니다. Batch Size의 경우 몇번의 쿼리가 더 발생될 수 있습니다
**데이터 전송량 관점**
- 데이터 전송량 관점에서는 Batch Size가 유리합니다. Fetch Join은 Join을 하고 나서 가져오기 때문에 중복 데이터를 많이 가져와야하기 때문입니다
- Fetch Join의 경우

| 레코드 | Article | Opinion |
| ----- | ------- | ------- |
| 1 | Article1 | Opinion1 |
| 2	| Article1 | Opinion2 |
| 3	| Article2 | Opinion3 | 
| 4 | Article2 | Opinion4 |
- BatchSize의 경우

| 레코드 | Article |
| ----- | ------- |
| 1 | Article1 |
| 2 | Article2 |
-----------------
| 레코드 | Opinion |
| -- | -- |
| 1	| Opinion1 |
| 2 | Opinion2 |
| 3 | Opinion3 |
| 4 | Opinion4 |

### @EntityGraph
처음에는 이 기능을 FetchJoin을 Annotation방식으로 편리하게 사용하는 기능으로 알고 있었는데 사실은 Lazy Loading을 Eager Loading으로 부분적으로 전환하는 기능입니다
#### EntityGraph가 FetchJoin보다 나은점
여러 1:N 연관관계를 한번에 Join해 올 수 있습니다. FetchJoin의 경우 1개의 Collection까지만 같이 Join하여 조회할 수 있습니다

#### 적용 사례
- Spring Data Jpa를 사용할 경우 다음과 같이 사용하면 됩니다
```
public interface ArticleRepository extends JpaRepository<Article, Long> , ArticleRepositoryCustom {

    void deleteByApiId(String apiId);

    @EntityGraph(attributePaths = {"articleMatchConditions"})
    Optional<Article> findEntityGraphArticleMatchConditionsByApiIdAndIsDeletedIsFalse(String articleId);

    @EntityGraph(attributePaths = {"articleMembers"})
    Optional<Article> findEntityGraphArticleMembersByApiIdAndIsDeletedIsFalse(String articleId);
    }
```
### 일반 join후 Projection하여 특정 컬럼만 Dto로 조회
```
select new 패키지 경로.ArticleDto(원하는 필드) 
from Article ar
join ar.opinions op
where op.article_id = ar.id
```
1. 장점: Entity Column이 많을 때 Projection하여 특정 컬럼만 조회할 수 있음, 커버링 인덱스 활용가능성 상승
2. 단점: 영속성 컨텍스트와 무관하게 동작하고 Repository가 Dto에 의존하게 되기 때문에 API변경에 DAO도 수정되어야 할 수 있음
3. 이 방식을 사용하는 쿼리는 DAO를 분리하는 것이 좋음