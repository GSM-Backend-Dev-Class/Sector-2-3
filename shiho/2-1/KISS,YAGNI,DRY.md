# KISS
## 정의
- Keep It Simple,Stupid(Short,Simple/Small,Simple)등  
- 심플하고 멍청하게 유지하라는 뜻  
## 작성방법
- 복잡한 정규표현식,높은 레벨의 코드등 복잡한 기술을 사용하지않고 심플하게 구현  
- 기존 라이브러리 사용 하는 것을 고려
- 과도하게 최적하 하지 않기기
## 장점
- 가독성이 좋아짐  
- 유지 보수성이 좋아짐
## 코드예시
나쁜 예시  
```Python
def is_even(number):
    if number % 2 == 0:
        return True
    else:
        return False
```  
좋은 예시
```Python
def is_even(number):
    return number % 2 == 0 # 이런식으로 하면 알아서 True 와 False 가 리턴됨
```
# YAGNI
## 정의
- You Ain't Gonna Need It 필요한 것만 만들라는 뜻
- 지금 만들었다가 나중에 필요없어질 수 있음
## 장점
- 개발 비용,유지보수 비용,수리 비용이 더나가는 문제를 예방 가능  
- 다른 코드에 영향을 끼치지 않을 수 있음
## 코드 예시
나쁜 예시  
```Python
class User:
    def __init__(self, name, email, age):
        self.name = name
        self.email = email
        self.age = age
        self.address = None  # 현재 사용되지 않음
        self.phone_number = None  # 현재 사용되지 않음
```
좋은 예시  
```Python
class User:
    def __init__(self, name, email, age):
        self.name = name
        self.email = email
        self.age = age
```
# DRY
## 정의
- Don't Repeat Yourself 중복을 피해라라는 뜻
- 그냥 말그대로 중복되는 것은 하지 말라는 뜻
## 장점
유지 보수 비용등이 절감 가능
## 코드 예시
나쁜 예시  
```Python
def dnjsdmlqnvl(r):
    return 3.141592 * r * r

def sjfadl(r):
    return 2 * 3.141592 * r
```
좋은 예시  
```Python
P = 3.141592
def dnjsdmlqnvl(r):
    return P * r * r

def sjfadl(r):
    return 2 * P * r
```