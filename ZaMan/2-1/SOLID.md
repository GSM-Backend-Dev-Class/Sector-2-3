# SOLID 원칙
## S - 단일 책임 원칙
모든 클래스는 각각 하나의 책임만 져야 한다는 원칙입니다.
<br>**즉, 클래스가 한가지 이상의 역할을 가지면, 그 역할 중 하나가 바뀔때 클래스 전체가 변경될 수 있어서 위험합니다.**
## O - 개방-폐쇄 원칙
확장에는 열려있고, 수정에는 닫혀 있어야 한다는 원칙입니다.
<br>**즉, 기능을 확장하더라도 기존 코드를 수정하지 않도록 설계해야 합니다.**
## L - 리스코프 치환 원칙
자식 클래스는 언제나 부모 클래스를 대체할 수 있다는 원칙입니다.
<br>**즉, 부모 클래스 타입으로 자식 객체를 문제 없이 사용할 수 있어야합니다.**
## I - 인터페이스 분리 원칙
한 클래스에 자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다는 원칙입니다.
<br>**즉, 하나의 거대한 인터페이스 보다는, 필요한 작은 인터페이스 여러개로 나누는 것이 좋습니다.**
## D - 의존 역전 원칙
의존 관계를 맺을 때 변화하기 쉬운 것 또는 자주 변화하는 것보다는 변화하기 어려운 것, 거의 변화가 없는 것에 의존하는 원칙입니다.
<br>**즉, 구체적인 클래스보다 인터페이스나 추상 클래스와 관계를 맺으라는 것입니다.**