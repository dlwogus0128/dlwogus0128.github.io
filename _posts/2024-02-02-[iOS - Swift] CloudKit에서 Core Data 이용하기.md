---
title: "[iOS - Swift] CloudKit에서 Core Data 이용하기"
date: 2024-02-02 10:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, cloudkit] 
---

- CloudKit
    - 애플에서 호스팅하는 icloud 서버에 대한 접근 권한을 애플리케이션에 제공함.
    - CloudKit의 Core Data를 사용할 때는 프레임워크를 구성하는 컴포넌트를 직접 사용하는 것이 아닌,
        
        CloudKit을 활성화하는 영구 컨테이너를 사용
        
        - CloudKit Container: CKContainer (관리 객체 모델과 유사)
            - 구조
                - Public DB (공용)
                    - 앱의 Storage Data (저장소 할당량)
                - Private DB (개인)
                    - 사용자의 iCloud Data
            - 각 컨테이너 안에는 Record가 여러 개 존재 (CKRecord)
    - CloudKit Reference
        - DB가 서로 다른 Record 간의 관계를 구축
    - CKRecordZone
        - 따로 커스텀하지 않으면 디폴트 존에 등록됨
        - 단일 트랜잭션에서 동시에 여러 레코드를 쓰는 작업이 있을텐데, 이 때 활용될 수 있음
        - CKRecordZoneID 각각 존재
    - CloudKit Console
        - 클라우드 킷 옵션과 애플리케이션 저장 관리소를 보여주기 위한 웹 인터페이스 제공
    - 클라우드킷 공유
        - 공용 클라우드 레코드는 해당 앱의 모든 사용자가 접근 가능한데
        - 개인 클라우드 레코드의 경우, 이 클라우드킷 공유 기능을 사용해 공유가 가능해짐
    - 클라우드킷 구독
        - 설치된 앱에 속한 클라우드 DB가 변하면 사용자에게 푸시 알림을 줄 수 있음

&nbsp;

- 사용법
    - Signing & Capabilities에서 iCloud 추가
    - containers 추가
    - PersistenceContainer에서 container 인스턴스의 타입을 NSPersistentCloudKitContainer로 마이그레이션
    - CloudKit Console에서 쿼리, 수정, 관리 및 모니터링 가능