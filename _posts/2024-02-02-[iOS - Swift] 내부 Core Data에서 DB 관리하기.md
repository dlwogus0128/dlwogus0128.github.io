---
title: "[iOS - Swift] 내부 Core Data에서 DB 관리하기"
date: 2024-02-02 09:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, coredata, swiftui] 
---

- Core Data
    - DB를 관리할 때 일반적으로 SQLite와 같은 SQL 언어들이 사용되지만, 해당 언어들을 알아야한다.
    - DB 언어에 대한 지식이 없어도 내부 데이터를 관리할 수 있는 Core Data가 도입된 이유이다.
    - SQLite DB에 wrapper를 두는 방식이다.

&nbsp;

- Core Data Stack
    - 코어 데이터 스택의 구조는 다음과 같다.
        - SwiftUI 앱
        - 영구 컨테이너
        - 관리 객체 콘텍스트 / 관리 객체 ⇒ 관리 객체 모델 / 엔티티 디스크립션
        - 영구 저장소 코디네이터
        - 영구 저장소 객체
        - DB
    - SwiftUI 앱
        - 제일 상위에 위치하며, 관리 객체와 상호작용한다.
        - 스택의 하위 레벨들이 Core Data 관련 상당 부분을 담당하기 때문에
        - Application Code가 DB와 직접 상호작용하지 않는다.
    - 영구 컨테이너
        - 코어 데이터 스택의 생성을 처리
        - 기본적인 코어 데이터에 application method를 쉽게 추가할 수 있도록 서브클래싱 되어있다.
    - 관리 객체
        - 데이터를 저장하기 위해 Application Code에서 작성되는 객체이다.
        - NSManagementObject
    - 관리 객체 콘텍스트
        - 객체의 상태를 유지
        - 관리 객체 모델에 의해 정의된 관리 객체 간의 관계를 관리
        - 해당 콘텍스트는 DB를 변경하라는 지시를 받을 때까지 상태를 유지하고 있다가, 지시를 받으면 변경
    - 관리 객체 모델?
        - Entity를 정의
        - Class Description
            - 관계형 DB에서 테이블을 정의하는 스키마와 유사
        - 관계 (Relationship)
            - 하나의 데이터 객체에서 다른 데이터의 객체 참고
            - 관계형 DB의 Relationship과 동일
        - 가져온 속성 (fetched property)
            - 다른 속성이나 엔티티를 기반으로 동적으로 정의된 속성
            - 다른 데이터 객체에 접근 가능
            - 약한 참조 (weak: 참조가 끝나면 자동으로 메모리 해제가 되는 특징을 지님)
            - 약한 단방향 관계 (one-way relationship)
        - 가져오기 요청 (fetched request)
            - 정의된 predicate (조건)을 기반으로 데이터 객체를 검색, 참조할 수 있게 정의된 쿼리
    - 영구 저장소 코디네이터
        - 여러 영구 객체 저장소에 대한 액세스를 조정
        - (개발자는 이 친구와 직접 상호작용하지 않는다)
    - 영구 객체 저장소
        - 코어데이터가 저장되는 기본 저장소 환경
        - 3개의 디스크 기반 영구 저장소 / 1개의 메모리 기반 영구 저장소로 나뉨
        - DB와 상호작용

&nbsp;

- Core Data 이용 흐름
    
    1) Entity Description 생성 (스키마와 유사)
    
    2) PersistenceController 생성
    
    → 생성한 entity 포함
    
    3) View Context 설정
    
    → 뷰의 현황을 추적: DB에 데이터를 읽고 쓰는 데 사용됨
    
    → 앱 파일에서 환경객체로 view context에게 앱에 접근할 권한을 부여함
    
    → 이로써 View Context를 활용해 View에서 DB의 데이터 읽/쓰가 가능해진다.