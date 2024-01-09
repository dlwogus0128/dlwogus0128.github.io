---
title: "[iOS - Swift] 감자도 따라하는 Localization (feat. Chat GPT)"
date: 2023-01-05 15:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, localization, chatgpt] 
---

## 들어가기 전에

&nbsp;

다양한 언어를 지원하는 앱을 본 적이 있으신가요?! 

아이폰의 설정 → 일반 → 언어 및 지역에서 기본 언어를 변경하면,
아래처럼 **기기의 언어 설정에 맞게 앱 내부의 텍스트 또한 변경되는 앱**들이 있는데요. 

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/6a3723fd-2dab-4cd8-aa16-4b1a4971e2d3" width = 400 alt = "Github App">

<center>(얘는 Github App 입니다)</center>

&nbsp;


이처럼 **앱 내에서 여러 언어와 지역들을 지원하게 하는 기능**을 `Localization` 이라고 합니다.

&nbsp;

저는 이번에 인턴을 하며 담당한 앱 개발에서 다국어를 지원하는 기능을 추가하게 되었고, 

localizing을 처음 진행하게 되었는데요

뭔가.. 앱개발 감자로서? 다국어 지원 같은 기능은.. 

개발해보기 전에는 언어만 바꿔주면 스스로 알아서 척척 되는..? 느낌을 생각했는데 (당연히 아님)

볼륨이 큰 앱에 로컬라이징을 뚝딱 넣으면.. 

많은 시간과 노력이 들겠다 라는 생각이 들었습니다. (당연함)

&nbsp;

그래서 저는 **ChatGPT**도 사용하고, **키값도 프로퍼티들로 다 선언**해보는 등등.. 

제 나름대로 작업 시간을 줄여보았고!! 

저와 같은 감자 개발자 누군가에게는 도움이 될 것 같아~~ 한 번 적어봅니다 ..

(더 좋은 방법이 있으면 추후 추가하겠습니다 🥹)

&nbsp;

***

## Localization 흐름

&nbsp;

localization의 순서는 간단하게 다음과 같습니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/18d91919-ea2b-4196-bc80-ce35e0e05c6e" alt = "Localization 흐름">

먼저 `Localizable.string` 파일에 “key” = “value” 형태로 **대치할 문장**들을 설정합니다. 
어떤 말이 각 언어별로 어떻게 표현되는지를 파일에 저장하는 과정입니다.

이후, `Bundle` 에서 기기의 기본 설정 언어가 어떤 것인지 값을 가져옵니다. 
한국어는 ko, 영어는 en 등 **각 언어별로 고유의 코드값**으로 분류가 되고, 이 값을 가져옵니다.

그럼 기기의 설정 언어값도 알고, 각 언어별로 어떻게 번역되는지도 설정했으니, 
String들이 언어 세팅이 바뀔 때마다 변할 수 있도록~~ 설정해주면 됩니당.

&nbsp;


***

## Localization 진행시켜!

그럼 제가 작업했던 순서대로 서술해볼게용 🥹

&nbsp;

### ✅ Localizable.string 설정

    
우선 Localization 방식은 **스토리보드**랑 **코드**, 두 종류가 있는데요~ 

저는 **코드** 방식으로 구현했습니다. 

일단 아까 위에서 설명했던 key, value 쌍이 들어가는 파일을 만들어야 하는데요. 

그 파일을 `Localizable.string` 이라고 합니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/6ac417c9-2a96-4976-9581-91df74c1d8f4" alt = "New File → String">

`New File → String` 파일을 만들어줍니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/81aec39f-0327-4eea-b40f-5c16f39348bd" alt = "Localizable">

이 때 주의할 점은 **절! 대! 파일명을 바꾸면 안됩**니다. 

**파일명을 `Localizable` 로** 해야 돼요!

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/11292df4-494a-4ff1-82ec-b94eae928989" width = 300 alt = "Localize...`">

그러면 만들어진 `Localizable.string` 파일을 누르고, 
오른쪽 인스펙터를 보면 `Localize...` 버튼이 보입니다. 

이 버튼을 눌러주세요.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/23b16375-947c-491c-ba86-8eefa122ee0a" alt = "Project → Info → Localization">

그런 다음에 `Project → Info → Localization` 에 있는 **+ 버튼**을 눌러줍니다. 

누르면 여러 언어들 목록이 나타나는데요! 
그 중에서 **Localizing을 지원하고 싶은 언어**를 누르면 댑니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/5bcae8e3-ca1e-4935-9862-3f4002998800" alt = ".strings 파일만 체크">

저는 코드로 다국어 처리를 할 거니까요! **.strings 파일만 체크**하고 Finish 눌러줍니다.

&nbsp;

자 그러면... 이제 Localizable 파일 내부를 채우면 되는데요.

한 프로젝트 내에 쓰이는 string이 진짜 많자나요 ...? 😱

모든 문장에 키값 부여하고.. 번역하고.. 너무너무 귀찮으니까

**지피티의 힘을 빌립니다** ㅋ

&nbsp;

(별 거 없음 주의..)

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/fc267562-760d-4f1c-b441-d70404bca572" alt = "localizing을 적용할 문장">


localizing을 적용할 문장들을 적고, 사진에서와 같이 자세하게 요청을 해주면

지피티가 키값도 적절히 설정해주고.. 딱 요청한 대로 출력해줍니다. 

좋지 않나요.. (아닌가)

물론 대량의 스크립트를 돌리면 수정해야 할 부분들이 좀 있지만, 아주 편리했습니다 🥹

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/90869548-78db-4c10-a228-10b9c853c473" alt = "원하는 언어로 번역">

바로 이어서 이렇게 명령해봅니다. 

저의 경우에는 이미 번역 스크립트가 있어서, 스크립트를 활용해 바꿔달라고 했는데요. 

이렇게 원하는 언어로 번역해달라고 부탁하면 깔끔하게 잘 번역해줍니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/dc358352-23e6-4302-bea9-558d3feff1a1" alt = "Localizable 파일">

출력된 내용들을 각 언어에 맞는 Localizable 파일 안에 붙여넣습니다.

&nbsp;


***

### ✅ LanguageManager 설정

&nbsp;

Localizable.string 파일도 다 세팅했으니 활용하면 되겠죠!

이제 Bundle에 접근해 기기의 기본 언어 설정 가져오고, 문장마다 localizing 적용해주면 되는데요...

언어 관련된 모든 함수들을 관리하는 `LanguageManager` 파일을 만들어주었습니다.

하나씩 적어볼게요 😅

&nbsp;

📌 **Bundle 설정하기**
```swift
/// 언어 관련 리소스를 처리하는 번들을 설정하는 함수입니다.
func setBundle() -> Bundle {
    let language = UserDefaults.standard.array(forKey: "AppleLanguages")?.first as! String
    let index = language.index(language.startIndex, offsetBy: 2)
    let languageCode = String(language[..<index]) // "ko", "en" 등
    let path = Bundle.main.path(forResource: languageCode, ofType: "lproj")
    guard let bundle = Bundle(path: path!) else { return Bundle() }
    return bundle
}
```
우선, 언어 관련된 리소스를 처리해 줄 Bundle을 설정하는 함수입니다.

- `UserDefaults` 에서 현재 기기 사용자가 어떤 언어를 설정했는지 가져오기
- 언어 코드값을 가져와 앞의 두 자리만 가져옴 → **languageCode**에 저장 
(처음에 "ko-KR" 이런 식으로 저장되어 있기 때문)
- 이를 기반으로 bundle 경로를 가져 옴 (이 번들이 어디에 위치해 있는지!!) 
- 그 경로를 기반으로 bundle를 return

&nbsp;

📌 **각 문장을 번역해서 return**
```swift
/// 언어 타입과 key 값을 입력하면, 해당하는 string 값을 return 합니다. VC에서 Bundle 선언 이후 호출합니다.
func setTextWithLanguage(bundle: Bundle, forKey: String) -> String {
    return bundle.localizedString(forKey: forKey, value: nil, table: nil)
}
```
가져온 **번들**과 해당 문장 **키**를 파라미터로 넘겨주면, 언어에 맞게 해석된 String 값을 리턴해주는 함수입니다.

&nbsp;

📌 **설정 언어 변경**
        
```swift
@frozen
enum LanguageType {
    case ko // 한국어
    case en // 영어
}

/// 언어 변경 시
func changeLanguage(languageType: LanguageType) {
    var languageCode = String()
    
    switch languageType {
    case .ko:
        languageCode = "ko"
    case .en:
        languageCode = "en"
    }
    
    UserDefaults.standard.set([languageCode], forKey: "AppleLanguages")
    UserDefaults.standard.synchronize()
}
```

언어 설정을 변경하는 버튼 등을 만들 때 사용하는 함수입니다.

- 설정한 언어 타입을 모아둔 열거형 **LanguageType** 생성
- UserDefaults에 설정된 언어 바꾸기
- 변경 사항 저장


&nbsp;

📌 **현재 언어 설정 코드 가져오기**

```swift
/// 현재의 언어 설정 상태의 코드를 string 값으로 return 해주는 함수입니다.
func getCurrentLanguageCodeAsString() -> String {
    let language = Locale.current.languageCode!
    let index = language.index(language.startIndex, offsetBy: 2)
    let languageCode = String(language[..<index])
    return languageCode
}
```
이건 그냥.. 현재의 languageCode를 가져오는 함수입니다. 

저는 TTS도 필요해서 넣어뒀는데, 지금은 필요 없을 것 같습니다. 😅


&nbsp;

***

### ✅ LanguageLiterals 설정

&nbsp;

LanguageManager를 뷰컨에서 활용한 내용은 아래와 같습니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/470ef326-8e4a-4f5e-bf93-1246d087a076" alt = "LanguageManager 활용">

기본 언어 설정은 ko구요.

"niceToMeetYou" 키에 맞는 문장을 출력해보았는데, 

"만나서 잘 부탁드립니다" 라고 잘 출력됐죠?!

&nbsp;

***아니 근데 .. 언제 저 키를 다 입력하고 있어?!***

&nbsp;


저 키를 하나하나.. 찾아서.. 입력하면 힘들고.. 오타나면 어떻게 찾고.. 그렇잖아요?!

그래서 키들만 따로 모아 프로퍼티를 정의해주는 

`Languageliterals` 라는 이름의 구조체를 만들어 주었습니다.

그렇게 하면 좀 더 편리하거등요

&nbsp;

```swift
import Foundation

struct LanguageLiterals {
    static let hello = "hello"
    static let myName = "myName"
    static let niceToMeetYou = "niceToMeetYou"
    static let weatherIsClear = "weatherIsClear"
}
```
이런 식으로.. (사용 모습은 나중에)

&nbsp;

***아니 근데 .. 언제 저 키의 프로퍼티를 다 지정하고 있어?! (22)***

&nbsp;

그렇자나요..? 편리하자고 하는 일인데.. 저걸 다 하나하나 지정해주는 게 더 귀찮으면 안되자나요

그래서 한 번 더 **지피티를 소환**해줍니다

&nbsp;

<img src = "https://github.com/Ajou-FromUS/ToME-iOS/assets/79050615/fe8e693c-6f3c-4bac-906b-ff4d54ffa0b4" alt = "키의 프로퍼티 지정">

오호 ㅋ

&nbsp;

<img src = "https://github.com/Ajou-FromUS/ToME-iOS/assets/79050615/0150936e-f433-4e56-9a38-ca54a59530eb" alt = "끝">
LanguageLiterals를 사용하면 이렇게 자동 완성이 되는 효과를!!! 😉

&nbsp;

***

## 마치며

&nbsp;

이렇게 많이 부족하지만.. swift로 iOS Localizing 할 때 썼던 방법을 적어보았는데요!

틀린 내용이 있거나 더 좋은 방법이 있으시면 댓글 남겨주세요~

&nbsp;

<img src = "https://github.com/Ajou-FromUS/ToME-iOS/assets/79050615/8af82af6-416e-495e-be17-8f3bf7219d4e" width = 300 alt = "감자">

<center>기요미 감자 사진으로 마무리합니다 ㅋ ✨</center>

&nbsp;

[🏅 전체 코드 살펴보기](https://github.com/dlwogus0128/swift-example.git)

[🌿 참고 자료](https://babbab2.tistory.com/59)

[💻 공식 문서](https://developer.apple.com/documentation/xcode/localization)
