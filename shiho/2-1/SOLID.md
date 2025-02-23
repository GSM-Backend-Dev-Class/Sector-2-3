# SOLID
## 정의
**SOLID**란 **객체지향 설계**에서 **지켜**줘야 할 5개의 소프트웨어 **개발 원칙**을 뜻한다
## 장점
코드를 확장하고 유지 보수 관리가 쉬워짐, 복잡성을 제거해 리팩토링 소요시간 감소 -> 개발의 생간성을 높임
## 종류
SRP(Single Responsibility Principle): 단일 책임 원칙  
OCP(Open Closed Principle): 개방 폐쇠 원칙  
LSP(Listov Stubstitution Principle): 리스코프 치환 원칙  
ISP(Interface Segregation Principle): 인터페이스 분리 원칙  
DIP(Dependency Inversion Principle): 의존 역전 원칙  
### SRP
클래스는 **단 하나의 책임(기능)만** 가져야 한다는 원칙  
책임의 범위는 개발자의 기준에 따른다  
#### 장점
만일 많은 기능이 있다면 한 기능을 수정했을떄 다른 기능또한 수정을 해야하는데 그것을 하지않아도 됨  
프로그램의 **유지보수 성**을 높임
#### 코드 예시
나쁜 예시
```Python
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

    def save_to_file(self):
        with open("user.txt", "w") as f:
            f.write(f"{self.name}, {self.email}") #한클래스 안에 2가지 함수가 들어가 2가지 기능을 함
```
좋은 예시
```Python
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class UserRepository:
    def save_to_file(self, user):
        with open("user.txt", "w") as f:
            f.write(f"{user.name}, {user.email}")#클래스를 2개로 나누어 기능(책임)을 분리
```
### OCP
**확장**에는 **열려**있어야 하고 **수정**에는 **닫혀**있어야 한다는 원칙  
- 기능을 추가하면 클래스 확장을 통해 손쉽게 구현, 확장에 따른 클래스 수정은 최소화해야한다  
- 확장에 열려있다: 새로운 변경 사항이 발생했을때 유연하게 코드를 추가함으로서 큰 힘을 들이지 않고 확장가능
- 수정에 닫혀있다: 객체에 직접적인 수정을 제한  
- 간단하게 말해 **추상화 사용**을 통한 관계구축을 권장
**다형성과 확장**을 가능케 하는 객체지향의 장점을 **극대화**
#### 코드 예시
나쁜 예시
```Python
class Discount:
    def apply_discount(self, price, discount_type):
        if discount_type == "student":
            return price * 0.9
        elif discount_type == "vip":
            return price * 0.8
        else:
            return price # 수정을 위해선 코드를 수정해야함
```
좋은 예시
```Python
from abc import ABC, abstractmethod

class DiscountStrategy(ABC):
    @abstractmethod
    def apply_discount(self, price):
        pass

class StudentDiscount(DiscountStrategy):
    def apply_discount(self, price):
        return price * 0.9

class VIPDiscount(DiscountStrategy):
    def apply_discount(self, price):
        return price * 0.8 # 확장 가능능
```
### LSP
**서브 타입**은 언제나 **기반(부모) 타입**으로 **교체**할 수 있어야 한다  
**다형성 원리**를 이용하기 위한 원칙  
업캐스팅된 상태에서 부모의 메서드를 사용해도 **동작이 의도대로 흘러가야 하는 것**  
```업캐스팅: 자식 클래스의 객체가 부모 클래스 타입으로 형변환 되는 것```  
#### 주의점
**부모 메서드의 오버라이딩**을 조심스럽게 따져가며 해야함  
부모 클래스와 **동일한 수준의 선행 조건**을 기대하고 사용하는 프로그램 코드에서 예상치 못한 문제를 일으킬 수 있음  
#### 코드 예시
나쁜 예시
```Python
class Bird:
    def fly(self):
        return "Flying"

class Penguin(Bird):
    def fly(self):
        raise Exception("Penguins cannot fly!")# 부모클래스의 매소드를 재정의함
```
좋은 예시
```Python
class Bird:
    pass

class FlyingBird(Bird):
    def fly(self):
        return "Flying"

class Penguin(Bird):
    def swim(self):
        return "Swimming"
```
### ISP
인터페이스를 각각 **사용에 맞게** 끔 **잘게 분리**한다는 원칙  
SRP = 클래스 단인 원칙, ISP = 인터페이스 단일 원칙  
클라이언트의 **목적과 용도**에 적합한 인터페이스 만을 제공
#### 주의점
한번 인터페이스를 **분리하여 구성**해놓고 **나중에** 무언가 수정사항이 생겨서 **또 인터페이스들을 분리**하는 행위을 해선 안됨
#### 코드 예시
나쁜 예시
```Python
class Animal:
    def fly(self):
        pass

class Dog(Animal):
    def fly(self):
        raise Exception("Dogs cannot fly!") # 모든 동물이 fly메소드를 가짐
```
좋은 예시
```Python
class Animal:
    pass

class FlyingAnimal:
    def fly(self):
        return "Flying"

class Dog(Animal):
    pass

class Bird(Animal, FlyingAnimal):
    def fly(self):
        return "Flying" # 인터페이스를 분리리
```
### DIP
어떤 **Class를 참조**해서 사용해야하는 상황이 생긴다면, 그 Class를 직접 참조하는 것이 아니라 그 대상의 **상위 요소(추상 클래스 or 인터페이스)로 참조**해야한다는 원칙  
**인터페이스에 의존**하라는 뜻  
#### 코드 예시
나쁜 예시
```Python
class Keyboard:
    def type(self):
        return "Typing with a keyboard"

class Computer:
    def __init__(self):
        self.keyboard = Keyboard()  # Keyboard 클래스에 직접 의존

    def use(self):
        return self.keyboard.type()

```
좋은 예시
```Python
from abc import ABC, abstractmethod

from abc import ABC, abstractmethod

class InputDevice(ABC):  # 인터페이스 역할
    @abstractmethod
    def type(self):
        pass

class Keyboard(InputDevice):
    def type(self):
        return "Typing with a keyboard"

class Touchscreen(InputDevice):
    def type(self):
        return "Typing with a touchscreen"

class Computer:
    def __init__(self, input_device: InputDevice):  # 추상화에 의존
        self.input_device = input_device

    def use(self):
        return self.input_device.type()

# 키보드 사용
computer1 = Computer(Keyboard())
print(computer1.use())  # "Typing with a keyboard"

# 터치스크린 사용
computer2 = Computer(Touchscreen())
print(computer2.use())  # "Typing with a touchscreen"
    @abstractmethod
    def connect(self):
        pass

class MySQLDatabase(Database):
    def connect(self):
        return "Connected to MySQL"

class PostgreSQLDatabase(Database):
    def connect(self):
        return "Connected to PostgreSQL"

class UserRepository:
    def __init__(self, db: Database):
        self.db = db  # 의존성 주입

mysql_db = MySQLDatabase()
user_repo = UserRepository(mysql_db)
```