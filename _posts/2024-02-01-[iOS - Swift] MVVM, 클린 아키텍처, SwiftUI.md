---
title: "[iOS - Swift] MVVM, 클린 아키텍처, SwiftUI"
date: 2024-02-01 09:00:00 +09:00
categories: [iOS, Swift]
tags: [mvvm, ios, cleanarchitecture, swiftui] 
---

- MVVM의 구성
    - Model ↔ View Model ↔ View 로 구성
    - Model은 데이터의 구조가 들어감
        - ex. 우리 서버에서 받는 데이터의 구조
    - ViewModel은 받은 데이터를 가공해 View로 보냄
    - View는 데이터를 받아 화면에 띄워줌
        - ex. UIKit, SwiftUI를 활용해 화면에 보여줌

&nbsp;

- 왜 View Model이 필요한가?
    - Model에서 데이터를 바로 받아 그대로 View에 보여주면 되는데, 왜 굳이 View Model을 중간에 거칠까?
    - Model의 정제되지 않은 데이터를 View에 바로 보여줄 수 없기 때문
        - ex. Model의 데이터 중 Date 타입의 birthday가 있다고 하자.
            
            나는 View에 해당 사용자의 나이를 띄워주고 싶다.
            
            birthday 값으로부터 나이를 계산해야 한다.
            
            → 이 때 값을 가공하기 위해서 View Model이 사용된다.
            
    - 이 때 주의할 점은 api 요청을 View Model에서 하지 않는 것이다.
        
        Client Player 혹은 Web Service 에게 데이터 호출을 요청하면, 얘네가 서버로 요청하게 한다.
        
&nbsp;

- SwiftUI란?
    - 기존 명령형 UI를 제공하던 프레임워크 UIKit에서 탈피한 선언적 UI 제공
        - 명령형 UI와 선언적 UI의 차이?
            - 명령형은 “버튼에 텍스트는 이렇게, 색상은 이렇게, 폰트는 이렇게, 레이아웃은 이렇게 해서 view에 추가해라” 라고 하나하나 명령을 한다 라면
            - 선언적은 “(어떤 Stack 위에) 텍스트랑 색상, 폰트가 이러한 버튼을 만들 거다” 라고 목적을 정의함
        - 선언적 UI는?
            - UI를 작성할 때 변경할 State(상태)를 지정
                - 뷰의 특정한 상태를 저장하는 State, 모델의 객체 변화 관찰하는 ObservableObject로 데이터 변화를 바로 반영
                    - 기존 UIKit에서 데이터가 변화되면 reload 해주어야 하는 불편함을 대폭 감소
            - 이 상태가 어떻게 변경될지 정의하는 것 (위에서 설명한 목적 정의)
        - 기타 플랫폼에서의 선언적 UI
            - 안드로이드: JetPack Compose
            - 웹: Rx

&nbsp;

- 여담: SwiftUI에서 MVVM을 사용하는 것이 옳은가?
    - SwiftUI.View는 View+ViewModel의 역할을 하기 때문에, 직접 model에서 가져온 값을 Reactive하게 View에 반영하는 것이 가능함
    - 오히려 (MVVM 및 클린 아키텍처를 준수하려) 불필요한 데이터바인딩을 위한 ViewModel을 만드는 일이 생김
    - 안드로이드, 웹 등에서도 선언적 UI를 사용할 때 MVVM을 사용하지 않는 추세
    - Apple 또한 데이터 플로우를 단방향으로 하는 것이 권장됨
    - 그럼에도 MVVM이 오랜 시간동안 표준 아키텍처로 사용되어 왔고, 이를 아직 활용하고 있는 기업들이 많기 때문에 MVVM이 사용되는 듯
        
        → 더 공부해보겠다 .... .

&nbsp;

- MVVM + Clean Architecture
    - Clean Architecture
        - Present Layer → Domain Layer ← Data Layer의 dependency를 가진다.
            - Dependency를 가진다는 것은 객체를 생성한다고 이해하면 될 것 같다.
                
                → 외부에서 내부의 코드를 꺼내 쓸 수는 있지만, 내부에서 외부의 코드를 쓸 수는 없다.
                
            - Clean Architecture는 Dependency Rules를 지킨다.
            - Dependency Inversion(의존성 역전)은 해당 의존성을 protocol을 통해 역전해 사용하는 것
        - 아래 세 가지의 Layer를 가진다.
            - Present Layer: MVVM
                - View: 사용자가 실제로 보는 화면이다.
                - View Model: UseCase를 사용하여 Entity와 통신한다.
                
                → View Model에서 데이터를 가공해 View에 보여준다.
                
                - DI Container Interface, Flow Router (옵션)
            - Domain Layer: Business Logic ⇒ 다른 layer와의 의존성이 없이 독립됨.
                - Entity: 사용자가 사용할 데이터 모델
                - UseCase (핵심): 도메인 관점에서의 사용자 시나리오
                - Repository Interface: UseCase에서 사용할 Repository의 명세
                    - UseCase에서 Data Layer의 Repository를 갖다 써야 하기 때문에 해당되는 것의 Interface를 갖고 있어야 됨.
            - Data Layer: Data Repository
                - Repository: 하나 이상의 Data Source 관리
                - Dto: 계층 간 데이터 교환을 위한 Model
                - Data Source: 외부와 Dto를 연결
                    
                    → 서버와 연결
                    
        - 흐름 예시
            
            1) 사용자의 Touch Action이 View에서 발생한다.
            
            2) View Model에 해당 Action이 넘어간다. 
            
            3) View Model은 Use Case에게 해당 Action에 대한 Output을 요청한다.
            
            4) Data Layer에서 해당 DB, Network의 Data를 가져오기 된다.
            
            5) 가져온 Data를 UseCase로 보내고 (여기에서 데이터 전달 시 Protocol을 통한 계층 간 의존성 역전)
            
            6) View Model에서 해당 데이터를 받아 가공해 표시한다.

&nbsp;

- Clean Architectue의 Dependency Rule
    - Clean Architectue에서 Dependency Rule을 잘 지키게 하려면 어떻게 해야 할까?
        
        → DI Container / Flow Router
        
    - DI Container: Dependency Injection Container
        - 클래스의 내부에서 필요한 객체의 인스턴스를 외부에서 생성한 뒤, 내부로 주입하는 의존성 주입 객체들이 Container에 모여있는 것 ⇒ 여러 코드에서 접근해야 하므로 싱글톤 패턴
        - 의존성 / 의존성 주입
            - 의존성이란?
                - ex. View Model에 Int, String 타입의 변수가 각각 존재한다고 가정
                    
                    근데 사실 Int 타입이 아니라 Date였다면? 
                    
                    View Model을 가져다 쓰는 View에 에러가 뜰 것이다.
                    
                    → 이는 View(상위 클래스)가 View Model(하위 클래스)에 의존하고 있기 때문에 
                    
                    이 문제를 해결하려면 View Model 뿐만 아니라 View 코드까지 수정해주어야 하므로 번거로움
                    
                - 즉, 상위 클래스가 하위 클래스에 의존하는 문제를 해결해주어야 함
                - 이를 위해 나온 것이 Dependency Injection (의존성 주입)
            - 의존성 주입이란?
                - 외부에서 객체를 생성해 내부에 주입하는 것
                - ex. init () 어쩌고
                    
                    이렇게 되면 위와 같은 문제가 발생할 경우 View에 오기 전에 ViewModel에서 문제가 생김
                    
        - 원래 의존성 주입을 하면 인스턴스를 만드는 위치가 전부 떨어져 있음
            
            코드마다 init의 내용이 같을 경우, 중복 코드가 늘어나는 문제
            
        - 구조
            - Register (write)
                - 사용할 인스턴스들을 키값과 함께 보관
            - Resolve (read)
                - 키값을 통해 만들어 둔 인스턴스를 읽어옴