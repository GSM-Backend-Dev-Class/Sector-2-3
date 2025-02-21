# SOLID란?
SOLID는 모듈식이고 유지 관리가 가능하고 쉬운 소프트웨어 시스템을 만드려는 5가지 설계 규칙이다

개발자가 유연하고 강력하고 확장 가능한 소프트웨어를 설계하는데 도움이 되는 지침 역활을 한다

## SOLID의 원칙
SOLID의 원칙은 총 5가지로 
- Single Responsibility Principle(SPR:단일 책임 원칙)
- Open-Closed Principle (OCP:열림-닫힘 원칙)
- Liskov Substitution Principle (리스코프 치환원칙:LSP)
- Interface Segregation Principle (인터페이스 분리 원칙 : ISP)
- Dependency Inversion Principle (의존성 역전 원칙 : DIP)

이제 원칙에 대해서 자세히 알아보자

### Single Responsibility Principle(SPR:단일 책임 원칙)

📖**단일 클래스는 오직 한가지의 일만을 가져야 한다**📖

---
**단일 클래스는 오직 한가지의 일에만 책임을 가져야 합니다** 만일 하나의 클래스가 하나 이상의 일에 책임을 가지게 된다면 이것을 결합(Coupled)를 불러옵니다

하나의 책임 변경에 대한 변경은 다른 책임의 수정을 발생 시킵니다

**※이 원칙의 적용은 클래스 뿐만이 아닌 소프트웨어, 컴포넌트와 마이크로 서비스에도 적용이 된다※**

### ❌ 잘못된 코드
```
class 강아지 {
    final static Boolean 수컷 = true;
    final static Boolean 암컷 = false;
    Boolean 성별;

    void 소변보다() {
        if (this.성별 == 수컷) {
            // 한쪽 다리를 들고 소변을 보다.
        } else {
            // 뒷다리 두 개를 굽혀 앉은 자세로 소변을 본다.
        }
    }   
}
}
```
위의 코드는 SPR원칙을 위반했다 

소변보다() 메소드에서 수컷,암컷 모두 구현하려고 해서 단일 책임의 원칙을 위반 하였다

### ✅ 좋은 코드
```
abstract class 강아지 {
    abstract void 소변보다();
}

class 수컷강아지 extends 강아지 {
    void 소변보다() {
        // 한쪽 다리를 들고 소변을 본다.
    }
}

class 암컷강아지 extends 강아지 {
    void 소변보다() {
        // 뒷다리 두 개로 앉은 자세로 소변을 본다.
    }
}
```
위의 코드는 SPR원칙을 준수하였다

강아지라는 추상클래스를 두고 수컷강아지, 암컷강아지 클래스가 자신의 특성에 맞게 소변보다() 메소드를 구현해서 리펙토링 할 수 있다

이런 것을 적절히 응용하면 우리 어플리케이션은 높은 응집력을 가지게 된다 

### Open-Closed Principle (OCP:열림-닫힘 원칙)

📖**기존코드를 수정하지 않고도 새로운 기능이나 동작을 추가할 수 있어야 한다**📖

---
소프트웨어 엔터티는 확장에는 열려 있지만 수정에는 닫혀 있습니다 즉 **기존코드를 수정하지 않고도 새로운 기능이나 동작을 추가할 수 있어야 한다** 이것은 추상화, 인터페이스 및 상속을 사용하여 유연하고 재사용 가능한 구성 요소를 생성함으로써 달성됩니다

![OCP](https://velog.velcdn.com/images/harinnnnn/post/10489f56-5d4a-4ba8-a134-8da46e88283c/image.png)

### ❌ 잘못된 코드
```
public class NOT_OCP운전자 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		
		소나타 운전자 = new 소나타();
		  운전자.drive();
		
		그랜저 운전자2 = new 그랜저();
		  운전자2.drive();
		
		BMW 운전자3 = new BMW();
		  운전자3.drive();
	}

}

class 소나타{	
	void drive() {
		System.out.println("나는 소나타를 운전한다.");
	}
}

class 그랜저{	
	void drive() {
		System.out.println("나는 그랜저를 운전한다.");
	}
}

class BMW{	
	void drive() {
		System.out.println("나는 BMW를 운전한다.");
	}
}
```
위의 코드는 OPR원칙을 위반했다

이 코드에서는 운전자 객체가 생성 되었을때 부터 각 차량 클래스에 과하게 의존하고 있습니다

만약 운전자3의 차가 소나타로 바뀌면 객체를 다시 생성해 주어야 한다 즉 **코드 자체가 수정되는 상황이니 OPR상황에 위배된다**

### ✅ 좋은 코드
```
public class OCP운전자 {

	public static void main(String[] args) {
		
		자동차[] 운전자 = new 자동차[3];
		
		운전자[0] = new 소나타();
		운전자[1] = new 그랜저();
		운전자[2] = new BMW();
		
		for(int i=0; i<운전자.length; i++) {
			운전자[i].drive();
		}

	}

}

class 자동차{
	String myCar="자동차";
	void drive() {
		System.out.printf("나는 %s 를 운전할 수 있다. \n",  myCar);
	}
}

class 소나타 extends 자동차{
	public 소나타() {
		myCar = "소나타";
	}	
}

class 그랜저 extends 자동차{	
	public 그랜저() {
		myCar = "그랜저";
	}
}

class BMW extends 자동차{	
	public BMW() {
		myCar = "BMW";
	}
}
```
위의 코드는 OPR원칙을 준수했다

이 코드에서의 drive() 매서드는 자동차가 바뀌더라도 변경되지 않는다 자동차의 변경과 확장에 대해서는 개방 되어 있고 **어떤 차를 타든 운전 방법이 수정되지 않게 수정에는 폐쇄되어 있는 코드이다**

만약 여기에에 현대 자동차를 운전하는 운전자를 하나 더 만들어 코드를 확장 하려 한다
```
    Class 현대 extends 자동차 {
        public 현대() {
            myCar = "현대";
        }
    }
```
라는 현대 클래스를 추가하고 다음과 같은 메인 메소드를 작성한다
```
	public static void main(String[] args) {
    
        운전자[3] = new 현대(); //추가
		운전자[3].drive();

	}
```

새로운 자동차가 추가하는 상황에서도 기존 drive() 매서드나 자동차 클래스의 코드가 전혀 변경 되지 않음을 알 수 있다

### Liskov Substitution Principle (리스코프 치환원칙:LSP)
---
📖**하위 클래스틑 반드시 상위 클래스와 대체 가능해야한다**📖

이 원칙이 지향하는 것은 하위클래스가 상위 클래스의 자리를 에러 없이 맡을 수 있는지 확인하는 것 입니다 만약 
**코드가 스스로 자신의 클래스 타입을 확인한다면, 그건 정말로 원칙을 위반 한 것이다**

### ❌ 잘못된 코드

```
class Bird {
    public void fly() {
        System.out.println("새가 날아갑니다.");
    }
}

class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("펭귄은 날 수 없습니다!");
    }
}

public class Main {
    public static void main(String[] args) {
        Bird bird = new Penguin();
        bird.fly(); // 예외 발생! LSP 위반!
    }
}
```
위 코드는 LSP원칙을 위반하였다

`Penguin`이 `Brid`를 상속받았지만 `fly()` 메서드를 제대로 구현 할 수 없기 때문이다
즉 **부모 클래스가 사용하는 `(Bird bird = new Penguin())`가 예외를 발생시키므로** LSP원칙을 위반하였다

### ✅ 좋은 코드
```
// 새의 기본 인터페이스
interface Bird {
    void makeSound();
}

// 날 수 있는 새의 인터페이스
interface Flyable {
    void fly();
}

// 참새: 날 수 있음
class Sparrow implements Bird, Flyable {
    @Override
    public void makeSound() {
        System.out.println("참새가 짹짹 울어요.");
    }

    @Override
    public void fly() {
        System.out.println("참새가 날아갑니다.");
    }
}

// 펭귄: 날 수 없음
class Penguin implements Bird {
    @Override
    public void makeSound() {
        System.out.println("펭귄이 꽥꽥 울어요.");
    }
}

public class Main {
    public static void main(String[] args) {
        Bird sparrow = new Sparrow();
        Bird penguin = new Penguin();

        sparrow.makeSound(); // 참새가 짹짹 울어요.
        penguin.makeSound(); // 펭귄이 꽥꽥 울어요.

        // Flyable 인터페이스를 통해 날 수 있는 새만 처리
        Flyable flyingBird = new Sparrow();
        flyingBird.fly(); // 참새가 날아갑니다.
    }
}
```
위의 코드는 LSP원칙을 준수하였다

`Brid`인터페이스와 `Flyable` 인터페이스를 분리하여 펭귄이 불필요한 fly()를 가지지 않도록 했다

이제 `Penguin`과 `Sparrow`모두 부모 타입을 `(Brid)`로 다룰 수 있고 `Flyable`을 사용하는 곳에서는 날 수 있는 새를 다룰 수 있기 때문에 **불필요한 예외를 방지하는 설게를 할 수 있다**

### Interface Segregation Principle (인터페이스 분리 원칙 : ISP)
---
📖**클라이언트가 사용하지 않는 인터페이스에 의존하도록 강요해서는 안 됩니다**📖

ISP는 여러 메서드가 있는 큰 인터페이스를 사용하는 대신 더 작고 구체적인 인터페이스로 나눌 것을 제안합니다 이는 느슨한 결합을 촉진하고 클라이언트가 특정 인터페이스에 의존할 수 있게 하며 불필요한 종속성을 방지합니다

### ❌ 잘못된 코드
```
// 동물 인터페이스 (너무 많은 기능을 포함하고 있음)
interface Animal {
    void eat();
    void sleep();
    void fly();  // 🛑 모든 동물이 날 수 있을까?
    void swim(); // 🛑 모든 동물이 수영할 수 있을까?
}

// 새 (문제 없음)
class Bird implements Animal {
    @Override
    public void eat() {
        System.out.println("새가 먹이를 먹습니다.");
    }

    @Override
    public void sleep() {
        System.out.println("새가 잠을 잡니다.");
    }

    @Override
    public void fly() {
        System.out.println("새가 날아갑니다.");
    }

    @Override
    public void swim() {
        throw new UnsupportedOperationException("새는 수영하지 않습니다!");
    }
}

// 강아지 (fly()를 구현할 수 없음)
class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("강아지가 밥을 먹습니다.");
    }

    @Override
    public void sleep() {
        System.out.println("강아지가 잠을 잡니다.");
    }

    @Override
    public void fly() {
        throw new UnsupportedOperationException("강아지는 날 수 없습니다!");
    }

    @Override
    public void swim() {
        System.out.println("강아지가 수영을 합니다.");
    }
}
```
위의 코드는 ISP원칙을 위반했습니다

`Animal` 인터페이스는 모든 동물의 행동을 담고 있는데 
- `Dog` -> `fly()`를 구현할 수 없고
- `Bird` -> `swim()`를 구현할 수 없다
특정 동물에 불필요한 의존성이 생긴다

### ✅ 좋은 코드
```
// 공통 동물 인터페이스 (모든 동물이 공통적으로 가질 기능)
interface Animal {
    void eat();
    void sleep();
}

// 날 수 있는 동물만을 위한 인터페이스
interface Flyable {
    void fly();
}

// 수영할 수 있는 동물만을 위한 인터페이스
interface Swimmable {
    void swim();
}

// 새 클래스 (날 수 있음)
class Bird implements Animal, Flyable {
    @Override
    public void eat() {
        System.out.println("새가 먹이를 먹습니다.");
    }

    @Override
    public void sleep() {
        System.out.println("새가 잠을 잡니다.");
    }

    @Override
    public void fly() {
        System.out.println("새가 날아갑니다.");
    }
}

// 강아지 클래스 (수영할 수 있음)
class Dog implements Animal, Swimmable {
    @Override
    public void eat() {
        System.out.println("강아지가 밥을 먹습니다.");
    }

    @Override
    public void sleep() {
        System.out.println("강아지가 잠을 잡니다.");
    }

    @Override
    public void swim() {
        System.out.println("강아지가 수영을 합니다.");
    }
}
```
위의 코드는 ISP원칙을 준수하였습니다

`Animal` 인터페이스 에는 기본적인 동물의 행동인 `eat()`,`sleep()`만 포함하고 있고 `Flyable` 인터페이스를 날 수 있는 동물에만 적용하고 `swimmable` 인터페이스도 수영 할 수 있는 동물에만 적용하면 불필요한 의존성을 줄일 수 있다

### Dependency Inversion Principle (의존성 역전 원칙 : DIP)
---
📖**의존(종속)은 구체가 아닌 추상과 이뤄져야 한다**📖

고수준(High-Level)의 모듈은 저수준(Low-Level)의 모듈에 의존하면 안됩니다 둘다 추상화에 의존해야합니다 추상은 세부사항(Details)에 의존해서는 안됩니다 세부사항은 추상에 의존해야 합니다

### ❌ 잘못된 코드
```
// EmailNotification 클래스 (구체적인 구현)
class EmailNotification {
    public void send(String message) {
        System.out.println("이메일 전송: " + message);
    }
}

// UserService 클래스 (EmailNotification에 직접 의존)
class UserService {
    private EmailNotification emailNotification;

    public UserService() {
        this.emailNotification = new EmailNotification(); // 🔴 구체적인 구현 클래스에 직접 의존!
    }

    public void notifyUser() {
        emailNotification.send("회원님, 새로운 알림이 있습니다!");
    }
}
```

`UserService`가 `EmailNotfication`에 직접 의존 하고 있기 때문에 위의 코드는 DIP 원칙에 위반된다

다른 알림 수단을 추가하려면 UserService 코드 또한 수정해야 하므로 이 코드는 OCP 원칙도 위반 하고 있다

### ✅ 좋은 코드
```
// NotificationService 인터페이스 (추상화)
interface NotificationService {
    void send(String message);
}

// EmailNotification (구체적인 구현)
class EmailNotification implements NotificationService {
    @Override
    public void send(String message) {
        System.out.println("이메일 전송: " + message);
    }
}

// SMSNotification (새로운 구체적인 구현)
class SMSNotification implements NotificationService {
    @Override
    public void send(String message) {
        System.out.println("SMS 전송: " + message);
    }
}

// UserService 클래스 (NotificationService 인터페이스에 의존)
class UserService {
    private NotificationService notificationService; // 🔵 추상화에 의존!

    public UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    public void notifyUser() {
        notificationService.send("회원님, 새로운 알림이 있습니다!");
    }
}

public class Main {
    public static void main(String[] args) {
        // 이메일 알림을 사용
        NotificationService emailNotification = new EmailNotification();
        UserService userService1 = new UserService(emailNotification);
        userService1.notifyUser();

        // SMS 알림을 사용
        NotificationService smsNotification = new SMSNotification();
        UserService userService2 = new UserService(smsNotification);
        userService2.notifyUser();
    }
}
```

`UserService`는 구체적인 클래스에 직접 의존하지 않고 `NotificationService` 인터페이스에 의존하고 있기 때문에 위 코드는 DIP원칙을 잘 따르고 있음

## 원칙의 이점
- 코드 중복을 줄인다
- 코드 재사용을 개선한다
- 테스트 가능성을 높인다
- 소프트웨어가 변경 사항에 더 잘 적응하도록 만드는 데 도움이 된다

SOLID 원칙은 시간이 지남에 따라 확장 가능하고 견고하며 유지 관리가 가능한 고품질 소프트웨어 시스템 개발에 기여합니다