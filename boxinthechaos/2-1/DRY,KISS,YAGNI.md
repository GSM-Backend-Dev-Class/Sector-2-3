# DRY란?
> ## Don't Repeat Yourself
> 같은 행동을 반복하지 마라

똑같은 행동을 반복하지 마라는 뜻으로 중복되는 함수나 코드는 하나의 공통 컴포넌트에 넣고 사용한다는 의미이다

시스템이 크고 개념도 많아지면 복잡도는 늘어 나기 때문에 이런 복잡도를 줄여야지 유지보수 비용과 개발비용이 줄어든다

특정 코드를 붙여넣기 한 경우 한 문제를 여러번 고쳐야 할 수도 있다

**DRY를 위반한 것을 WET(We Enjoy Typing:우린 타이핑을 즐겨요 ^^)가 있다**

## ❌ 잘못된 코드
```
class UserService {
    public void registerUser(String username, String email) {
        if (email.contains("@")) {
            System.out.println(username + " 회원가입 완료!");
        } else {
            System.out.println("잘못된 이메일 형식입니다.");
        }
    }

    public void updateUserEmail(String username, String newEmail) {
        if (newEmail.contains("@")) {
            System.out.println(username + " 이메일 변경 완료!");
        } else {
            System.out.println("잘못된 이메일 형식입니다.");
        }
    }
}
```

`registerUser()`와 `updateUserEmail()`에서 이메일 인증 로직이 중복이기 때문에 DEY를 위반했다

## ✅좋은 코드
```
class UserService {
    // 이메일 검증 로직을 따로 분리하여 재사용
    private boolean isValidEmail(String email) {
        return email.contains("@");
    }

    public void registerUser(String username, String email) {
        if (isValidEmail(email)) {
            System.out.println(username + " 회원가입 완료!");
        } else {
            System.out.println("잘못된 이메일 형식입니다.");
        }
    }

    public void updateUserEmail(String username, String newEmail) {
        if (isValidEmail(newEmail)) {
            System.out.println(username + " 이메일 변경 완료!");
        } else {
            System.out.println("잘못된 이메일 형식입니다.");
        }
    }
}
```
`isValidEmail()`이라는 메서드로 나눠서 중복 부분을 제거함 이메일 검증 방식이 변경 되더라도 한곳만 변경하면 됨

# KISS
> ## Keep it simple, stupid
> 바보같이 복잡하게 하지 말고 단순하게

단순하게 하라는 의미로 프로젝트를 복잡하게 만드는 것은 그 프로젝트를 이해하지 못했다는 증거로 프로젝트가 진행되기 전에 최대한 기반과 배경과 추진되는 목적 자체를 이해하고 어떻게 단순하게 구현할지 회의를 해서 개선해야한다

## ❌잘못된 코드
```
class DiscountCalculator {
    public double calculateDiscount(double price, String userType) {
        if (userType.equals("VIP")) {
            if (price > 100) {
                return price * 0.9; // 10% 할인
            } else {
                return price * 0.95; // 5% 할인
            }
        } else if (userType.equals("Regular")) {
            if (price > 100) {
                return price * 0.95; // 5% 할인
            } else {
                return price; // 할인 없음
            }
        }
        return price;
    }
}
```
if-else문이 너무 많아서 가독성이 떨어지고 각 조건이 중복된 점이 많아서 유지보수 하기 어려움
따라서 위의 코드는 KISS를 어겼다

## ✅좋은 코드
```
class DiscountCalculator {
    public double calculateDiscount(double price, String userType) {
        double discountRate = 1.0;

        if (userType.equals("VIP")) {
            discountRate = (price > 100) ? 0.9 : 0.95;
        } else if (userType.equals("Regular") && price > 100) {
            discountRate = 0.95;
        }

        return price * discountRate;
    }
}
```

불필요한 중복이 없고 if-else를 단순화 시켜 가독성을 증가 시켰고 수정이 쉽다

# YAGNI
> ## YAGNI - You Ain't Gonna Need it
> 너는 그것을 필요로 하지 않을꺼야

정말 필요할때 까지 그 기능을 만들지 말라는 의미이다 현재 필요없는 코드를 나중를 대비해서 미리 만들지 말라는 것이다  
