# Spring Validation
Spring Validation은 **입력값을 검증**하는 기능으로, 주로 **DTO 객체에서 유효성 검사**를 수행할 때 사용한다

Spring Boot에서는 **Bean Validation**(`javax.validation` 패키지)과 **Spring Validator** 두 가지 방식으로 검증을 지원한다

## Bean Validation (@Valid, @Validated)
**Spring Boot에서는** `javax.validation`**을 활용하여 DTO에서 유효성 검사를 수행할 수 있다**
DTO 클래스에 **어노테이션을 추가**하면 자동으로 유효성 검사가 수행된다

### ✅ 예제: DTO에 유효성 검증 추가
```
import jakarta.validation.constraints.*;

public class UserRequestDto {
    @NotBlank(message = "이름은 필수 입력값입니다.")
    private String name;

    @Email(message = "이메일 형식이 올바르지 않습니다.")
    private String email;

    @Min(value = 18, message = "나이는 18세 이상이어야 합니다.")
    @Max(value = 100, message = "나이는 100세 이하만 가능합니다.")
    private int age;

    @Pattern(regexp = "^010-\\d{4}-\\d{4}$", message = "전화번호 형식이 올바르지 않습니다.")
    private String phone;

    // 기본 생성자 및 Getter/Setter 추가
}
```
- `@NotBlank` -> 값이 비어있거나 공백이면 안 됨
- `@Email` -> 이메일 형식인지 검증
- `@Min`,`@Max` -> 숫자의 최소, 최대값 제한
- `@Pattern` -> 정규식을 이용한 유효성 검사

### 컨트롤러에서 @Valid 사용
Spring에서는 `@Valid` 또는 `@Validated`를 사용하여 **DTO 객체의 유효성 검사**를 수행할 수 있다
```
import jakarta.validation.Valid;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public String createUser(@RequestBody @Valid UserRequestDto userDto, BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            return bindingResult.getFieldError().getDefaultMessage();
        }
        return "회원 가입 성공!";
    }
}
```
- `@Valid` -> DTO에 있는 유효성 검사를 실행
- `BindingResult` -> 유효성 검사 실패 시 에러 메시지를 확인 가능

### Spring Validator (Custom Validator)
Spring의 `Validator` 인터페이스를 사용하면 **커스텀 유효성 검증 로직**을 만들 수 있다

#### ✅ Validator 구현
```
import org.springframework.stereotype.Component;
import org.springframework.validation.Errors;
import org.springframework.validation.Validator;

@Component
public class CustomUserValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return UserRequestDto.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        UserRequestDto user = (UserRequestDto) target;

        if (user.getName() != null && user.getName().contains("admin")) {
            errors.rejectValue("name", "invalid.name", "이름에 'admin'을 포함할 수 없습니다.");
        }
    }
}
```
- `supports()` -> 검증할 DTO 타입 지정
- `validate()` -> 실제 검증 로직 구현

#### ✅ 컨트롤러에서 Validator 적용
```
import org.springframework.validation.annotation.Validated;

@RestController
@RequestMapping("/users")
public class UserController {

    private final CustomUserValidator customUserValidator;

    public UserController(CustomUserValidator customUserValidator) {
        this.customUserValidator = customUserValidator;
    }

    @PostMapping
    public String createUser(@RequestBody @Validated UserRequestDto userDto, BindingResult bindingResult) {
        customUserValidator.validate(userDto, bindingResult);

        if (bindingResult.hasErrors()) {
            return bindingResult.getFieldError().getDefaultMessage();
        }
        return "회원 가입 성공!";
    }
}
```

### 글로벌 예외 처리 (ControllerAdvice)
유효성 검사에서 에러가 발생할 경우 **ControllerAdvice를 활용하여 예외 처리를 전역적으로 관리할 수 있다**

#### ✅ 글로벌 예외 처리 예제
```
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.util.HashMap;
import java.util.Map;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Map<String, String> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        return errors;
    }
}
```
- ✅ `@ExceptionHandle(MethodArgumentNotValidException.class)` -> DTO 유효성 검증 예외 처리
- ✅ 클라이언트에게 **유효성 검사 에러 메시지를 JSON 형식으로 반환**

## 정리
| 검증 방식 | 설명 |
| -------- | ---- |
| `@Valid` | DTO에서 기본적인 유효성 검사를 수행 |
| `@Validated` | 그룹(Group) 검증이 필요한 경우 사용 |
| `Validator` | 인터페이스	커스텀 유효성 검증 로직을 적용할 때 사용 |
| `ControllerAdvice` | 전역적인 예외 처리를 적용 |

# Javax와 Jakarta 차이점
Java에서 `javax`와 `jakarta`는 **Java EE (Enterprise Edition)** 관련 기술을 의미하는 패키지 네임스페이스다

## Javax란?
- `javax`는 **"Java Extension"**의 줄임말로, JDK의 표준 라이브러리가 아닌 **추가적인 API**를 제공하는 네임스페이스
- 예전 Java EE (지금의 Jakarta EE)에서 **Java 기업용 기술**을 제공하는 패키지로 사용됨
- 예제:
```
import javax.servlet.http.HttpServletRequest;
import javax.validation.constraints.NotBlank;
```
- 대표적인 패키지
  - `javax.servlet` -> 서블릿 API (웹 애플리케이션) 
  - `javax.validation` -> Bean Validation (입력 검증)
  - `javax.persistence` -> JPA (데이터 저장)

## Jakarta란?
- `Jakarta`는 **Java EE의 새로운 명칭**이다
- **2017년, 오라클(Oracle)이 Java EE를 Eclipse Foundation에 기부**하면서 "Java EE"가 "Jakarta EE"로 변경됨
- 기존의 `javax` 패키지가 `jakarta`**로 변경됨** (Java EE 9부터 적용)
- 예제
    ```
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.validation.constraints.NotBlank;
    ```
- 대표적인 변경 사항
  - `javax.servlet` -> `jakarta.servlet`
  - `javax.persistence` -> `jakarta.persistence` 
  - `javax.validation` -> `jakarta.validation`

## Javax vs Jakarta 차이점 정리
| 비교 항목 | `javax` (이전) | `jakarta` (현재) |
| ----- | ------- | ------- |
| 소속 | Oracle | Eclipse Foundation |
| Java EE 버전 | Java EE 8 이하 | Jakarta EE 9 이상 |
| 패키지 명 | `javax.servlet`, `javax.persistence` 등 | `jakarta.servlet`, `jakarta.persistence` 등 |
| 지원 여부 | 더 이상 업데이트 없음	최신 | Jakarta EE 표준에서 사용됨 |