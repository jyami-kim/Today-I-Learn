# Item19. knowledge를 반복하여 사용하지 말라

> 실용주의 프로그래머 : DRY원칙 - 모든 지식은 시스템내에서 단일하고, 애매하지 않고, 정말로 믿을 만한 표현 양식을 가져야한다.
>
> Every piece of knowledge must have a single, unambiguous, authoritative representation within a system



### knowledge : 의도적인 정보

- 로직 (logic) : 프로그램이 어떠한 식으로 동작하는지와 프로그램이 어떻게 보이는지
  - 비즈니스 로직 : 시간이 지나면서 계속해서 변한다.
- 공통 알고리즘 (common algorithm) : 원하는 동작을 하기 위한 알고리즘
  - 한 번 정의된 이후에는 크게 변하지 않는다.



### 모든 것은 변화한다

knowledge가 변화하는 이유

- 회사가 사용자의 요구 또는 습관을 더 많이 알게 되었다.
- 디자인 표준이 변화했다
- 플랫폼, 라이브러리, 도구 등이 변화해서 대응해야한다

변화하는 부분 : 가장 적은 knowledge가 반복되어있는 부분

knowledge의 반복 : 프로젝트의 확장성(scalable)을 막고 쉽게 깨지게(fragile) 만든다 > 추상화를 표현 할 수 있는 여러 솔루션 사용학



### 언제 코드를 반복해도 될까?

얼핏보면 knowledge 반복처럼 보이지만, 실질적으로 다른 knowledge를 나타내므로 추출하면 안 되는 부분

두 코드가 같은 knowledge인지 판단방법 "함께 변경될 가능성이 높은가? 따로 변경될 가능성이 높은가?"



### 단일 책임 원칙(single responsibility principle, SRP)

>  클래스를 변경하는 이유는 단 한 가지여야 한다.
>
> A class should have only one reason to change

코드를 추출해도 되는지를 확인하는 원칙

두 액터(actor)[변화를 만들어 내는 존재 (source of change)]가 같은 클래스를 변경하는 일은 없어야 한다.

- 서로 다른 곳에서 사용하는 knowledge는 독립적으로 변경할 가능성이 많다. 따라서 비슷한 처리를하더라도, 완전히 다른 knowledge로 취급하는 것이 좋다.
- 다른 knowledge는 분리해 두는 것이 좋다. 그렇지 않으면, 재사용해서는 안 되는 부분을 재사용하는 유혹이 발생할 수 있다.

