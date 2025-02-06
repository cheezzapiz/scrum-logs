# 🚀 **Java 코드 컨벤션 및 예외처리 규칙**

📌 **컨벤션 적용으로 Clean Code, 협업의 효율성을 높이기** 🚀

## 📌 **1. 기본 코드 스타일**

### 🔹 **1.1 네이밍 규칙**

- 📌 **클래스명** : `PascalCase` 사용 → `UserService`, `OrderController`
- 📌 **메서드명** : `camelCase` 사용 → `getUserById`, `createOrder`
- 📌 **변수명** : `camelCase` 사용 → `userName`, `orderId`
- 📌 **상수명** : `UPPER_SNAKE_CASE` 사용 → `MAX_USERS`, `DEFAULT_TIMEOUT`
- 📌 **패키지명** : 모두 소문자로 작성하고, 단어는 `.`으로 구분 → `com.example.service`

---

## 📌 **2. 클래스 및 메서드 구조**

### 🔹 **2.1 클래스 구조**

📌 **클래스 내부 구성 요소 순서**  
1️⃣ `field` (상수, 정적 필드, 인스턴스 변수 순서)  
2️⃣ `getter, setter, constructor` → **Lombok 사용 (`@Getter`, `@Setter`, `@NoArgsConstructor`, `@AllArgsConstructor`)**  
3️⃣ `public method`  
4️⃣ `private method`

### 🔹 **2.2 메서드 작성 규칙**

- ✅ 한 개의 메서드는 **하나의 책임만 가지도록 작성**
- ✅ 메서드 네이밍은 **동사형으로 작성** → `findUser`, `updateOrder`
- ✅ `public` 메서드에는 **Javadoc 주석 작성**

---

## 📌 **3. Lombok 사용 규칙**

✅ **클래스의 Getter, Setter, 생성자는 Lombok을 활용하여 자동 생성**  
✅ **Entity 클래스에서는 `@Setter`를 지양하고, `@Builder`를 활용하여 불변성을 유지**  
✅ **DTO 및 VO에서는 `@Getter`, `@NoArgsConstructor`, `@AllArgsConstructor`를 사용하여 간결하게 정의**  
✅ **Spring에서 기본 생성자가 필요한 경우 `@NoArgsConstructor(access = AccessLevel.PROTECTED)`로 제한**

🚫 **잘못된 예제 (`@Setter` 남용, 가변 객체 문제 발생 가능)**

````java
@Getter
@Setter // ❌ 모든 필드에 Setter 제공 (데이터 변경 위험)
@NoArgsConstructor
@AllArgsConstructor
public class UserDto {
    private String name;
    private int age;
}

✅ 올바른 예제 (@Setter 사용 지양, DTO는 불변 객체로 유지)

@Getter
@NoArgsConstructor
@AllArgsConstructor
public class UserDto {
    private String name;
    private int age;
}

✅ Entity에서 @Setter를 사용하지 않고 @Builder 활용

@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)  // JPA 기본 생성자
@Builder
@AllArgsConstructor(access = AccessLevel.PRIVATE)   // 빌더 전용 생성자, 빌더로만 변수할당가능
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
}

사용 방법

```Java
User user = User.builder()
                .name("Bob")
                .email("bob@example.com")
                .build();

````

📌 Lombok 사용 시 주의 사항
🚨 Entity 클래스에서는 @Setter를 사용하지 않고 @Builder 패턴 활용
🚨 DTO 클래스에서는 @Setter를 최소화하고, 필요한 경우 생성자 또는 빌더 패턴을 이용해 값을 설정
🚨 불변성이 필요한 경우 @Value(Lombok)를 활용

## 📌 4. 로깅 작성 가이드

🔹 4.1 로깅 규칙

🚫 System.out.println() 사용 금지
✅ 로깅 프레임워크(SLF4J, Logback) 사용

🔹 4.2 로그 레벨 구분
• ℹ️ info → 정상 흐름에서 발생한 중요 이벤트 (예: DB 저장)
• ⚠️ warn → 예외가 발생할 가능성이 있는 상황
• ❌ error → 실제 예외 발생

## 📌 5. Spring Boot 프로젝트 규칙

🔹 5.1 컨트롤러, 서비스, 레포지토리 계층 분리

✅ 컨트롤러, 서비스, 레포지토리 계층을 명확하게 구분
✅ 컨트롤러는 서비스 레이어만 호출, 직접 레포지토리에 접근하지 않음
✅ DTO(Data Transfer Object)와 Entity는 분리하여 사용

🔹 5.2 트랜잭션 관리

✅ DB 변경이 발생하는 서비스 메서드에는 @Transactional 적용
✅ 읽기 전용 트랜잭션(@Transactional(readOnly = true))을 활용하여 성능 최적화

🔹 5.2 의존성 주입 방식
✅ 필드 주입 지양, 생성자 주입 방식 권장

## 📌 6. 예외 처리 규칙

🔹 6.1 예외 처리 기본 원칙

✅ 서비스(Service) 레이어에서는 try-catch를 사용하지 않고 throw를 사용하여 예외를 전달
✅ 컨트롤러(Controller) 또는 글로벌 예외 처리기(@RestControllerAdvice)에서 예외를 처리
✅ 모든 사용자 정의 예외는 RuntimeException을 상속받아 구현
✅ 예외 메시지는 의미 있는 내용을 포함하도록 작성

### 🔔 **추가 사항(협의 후 결정)**

## 📌7. JPA

🔹 7.1 EnumType

✅ `EnumType.STRING` 로 통일

`EnumType.ORDINAL` 을 사용할 경우, enum 값의 순서(index)가 저장되는데, enum에 새로운 값이 추가되거나 순서가 변경되면 기존 데이터의 의미가 바뀌는 문제가 발생

```java
@Entity
public class User {

    @Enumerated(EnumType.STRING)
    private UserRole role;

}
```

🔹 7.2 FetchType

✅ `fetchJoin` 으로 해결하기

`@OnetoOne`,`@ManytoOne` FetchType 기본 설정이 즉시 로딩으로 되어있는데 **직접** 모두 지연 로딩으로 통일 시키기 → N+1 문제 방지 위해

```java
@Entity
public class Order {

    @ManyToOne(fetch = FetchType.LAZY) // Lazy로 설정
    @JoinColumn(name = "customer_id")
    private Customer customer;

}

@Entity
public class User {

    @OneToOne(fetch = FetchType.LAZY) // Lazy로 설정
    @JoinColumn(name = "profile_id")
    private Profile profile;

}
```

## 📌8. 검증

🔹 8.1 DTO (Validation 중심)

클라이언트 요청 데이터의 유효성을 검증하는 데 초점

```java

@Data
public class UserDto {

    @NotBlank(message = "이름은 필수 입력 값입니다.")
    @Size(max = 50, message = "이름은 최대 50자까지 입력 가능합니다.")
    private String name;

    @NotBlank(message = "이메일은 필수 입력 값입니다.")
    @Email(message = "올바른 이메일 형식이어야 합니다.")
    private String email;

}`
```

🔹 8.2 Entity (데이터베이스 제약 중심)

데이터 무결성을 보장하기 위해 컬럼 제약 조건을 설정

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false, length = 50) // 데이터베이스 컬럼 제약 조건
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

}
```

## 📌9. Querydsl

| **메서드**           | **상태** | **설명**                                                                 |
| -------------------- | -------- | ------------------------------------------------------------------------ |
| **`fetchResults()`** | ❌       | 페이징 처리 및 전체 카운트를 한 번에 가져오지만 성능 문제로 인해 비권장. |
| **`fetchCount()`**   | ❌       | 전체 결과 수를 가져오지만 복잡한 쿼리에 적합하지 않아 비권장.            |
| **`fetch()`**        | ✅       | 결과 리스트를 반환하는 기본 메서드로 여전히 권장됨.                      |
| **`fetchOne()`**     | ✅       | 단일 결과를 반환하며, 여러 개의 결과가 나오면 예외 발생.                 |
| **`fetchFirst()`**   | ✅       | 첫 번째 결과만 반환하며, 내부적으로 SQL의 LIMIT 1을 활용함.              |
| **`iterate()`**      | ✅       | 반복자(iterator)를 반환하며 대량 데이터 처리에 유용함.                   |

```java
List<Member> content = queryFactory
    .selectFrom(member)
    .where(member.username.contains("test"))
    .offset(pageable.getOffset())
    .limit(pageable.getPageSize())
    .fetch();

// 전체 카운트 조회
long total = queryFactory
    .select(member.count())
    .from(member)
    .where(member.username.contains("test"))
    .fetchOne();



// 따로따로 조회하여 Page 객체 생성
return new PageImpl<>(content, pageable, total);
```
