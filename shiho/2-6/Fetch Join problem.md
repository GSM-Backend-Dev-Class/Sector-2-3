# 문제
- Fetch Join을 사용하면서 페이징 처리(Pageable 사용) 를 하면 **예상치 못한 문제**가 발생할 수 있음
- JPQL의 Fetch Join은 기본적으로 페이징 쿼리에 **적합하지 않기 때문**
```Java
@Query("SELECT u FROM User u JOIN FETCH u.posts")
List<User> findAllWithPosts(Pageable pageable);
```
- 의도는 User와 Post를 Fetch Join으로 한 번에 가져오면서 페이징 처리
```sql
SELECT u.*, p.* FROM users u 
JOIN posts p ON u.id = p.user_id;
```
- 실 쿼리는 모든 데이터를 가져와서, 메모리에서 페이징 처리 = **성능저하**
# 해결방법
## 두 번의 쿼리로 나눠서 처리
```Java
@Query("SELECT u FROM User u")
Page<User> findAllUsers(Pageable pageable);

@BatchSize(size = 100)
@OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
private List<Post> posts;
```
## @EntityGraph + 페이징 사용
- @EntituGraph는 Fetch Join과 비슷하게 사용가능해 정상작동 함
```JAVA
@EntityGraph(attributePaths = {"posts"})
@Query("SELECT u FROM User u")
Page<User> findAllWithPosts(Pageable pageable);
```
- 하지만 또 **성능저하 가능성**있음
## Native Query 사용
- 쿼리 최적화 가능
```Java
@Query(value = "SELECT * FROM users u LIMIT :limit OFFSET :offset", nativeQuery = true)
List<User> findUsersWithLimit(@Param("limit") int limit, @Param("offset") int offset);
```
- 문제는 이러면 JPA를 사용해서 **얻는 이점이 없어**짐
