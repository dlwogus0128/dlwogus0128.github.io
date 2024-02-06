---
title: "[iOS - Swift] WidgetKit 정리"
date: 2024-02-06 10:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, swiftui, widgetkit] 
---

- WidgetKit
    - 위젯 띄우기 위해 사용
    - 위젯 익스텐션을 추가해서 생성함
    - 세 가지 기본 구성요소
        - 위젯 종류: 프로젝트 내의 위젯을 선별
        - 위젯 구성: 표시할 정보나 설명, 타임라인 프로바이더
            - 정적 구성 (Static Configuration): 위젯에 사용자가 구성할 수 있는 속성이 없을 때
            - 인텐트 구성 (Intent Configuration): 사용자가 위젯을 구성할 때
                - ex. 사용자가 위젯에서 기사 헤드라인을 표시할 때, 어떤 언론사를 할 지? 선택
        - 엔트리뷰: 위젯이 표시될 때 사용자에게 보여질 레이아웃 포함하는 SwiftUI 뷰에 참조
            - non-interactive
            - 디스플레이 전용 뷰로, 버튼이나 토글 같은 구성요소가 없음
    - 위젯 타임라인
        - 언제 위젯의 내용이 업데이트 될 지
    - 위젯 프로바이더: 위젯에 표시할 콘텐츠를 전달
        - getSnapShot(): 위젯을 등록하면 어떻게 보여지는지 (샘플 데이터를 통해 나타냄)
        - getTimeline(): Reload 정책으로 위젯이 언제 업데이트 되는지, 타임라인 끝에 도달하면 어떻게 하는지?
    - Relevance (관련성)을 조정해 사용자에게 중요한 정보가 업데이트 되면, 스택의 맨 위로 올라갈 가능성이 높아지도록 값을 조정할 수 있음
    - 위젯 크기 설정 가능
    - 위젯 플레이스홀더: 데이터가 들어오는 동안 네모 표시 도로롱.. 뜨는 것..

- WidgetDemoProject
    - trouble shooting
        - Embedded binary is not signed with the same certificate as the parent app. Verify the embedded binary target's code sign settings match the parent app's.
            
            → 부모 앱 인증서와 동일한 인증서로 서명되지 않아서 발생한 문제
                        
            <img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/153e9e75-412e-46d9-ad08-a4266994c7f8" width = 600 alt = "">
            
            - 계정을 Automatically에서 전부 developer 인증 받은 계정 (현재 부모 앱에서 서명된 계정)으로 변경
            - Clean Build Folder 후 재빌드