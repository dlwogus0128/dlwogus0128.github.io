---
title: "[iOS - Swift] UIKit으로 TabBar와 NavigationBar 커스텀하기"
date: 2024-01-26 09:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, tabbar, navigationbar, uikit] 
---

## 들어가기 전에

&nbsp;

이번주는 독감 A형으로 😱.. 매우 아팠던 관계루다가 

스스로 쉬어가는 타임..으루 **탭바와 네비바를 커스텀**했던 개인 기록들을 정리해보려고 합니닷 🤒

&nbsp;

일단 `TabBar` 랑 `NavigationBar` 가 머냐구요?!

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/4941e53a-bc4d-42c7-afa1-56f28e674308" width = 300 alt = "">

&nbsp;

옛날에 진행했던 프로젝트.. 의 이전 디자인!!을 가져와봤는데욥!!

&nbsp;

`탭바 (TabBar)` 는 **앱 하단쪽**에 위치하며, 여러 버튼들로 탭바들을 연결해 이동을 용이하게 해주는 컴포넌트구요,

`네비바 (NavigationBar)` 는 **앱 상단쪽**에 위치하며, **화면 전환**과 같은 기능들을 제공하게 됩니닷 😎

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/0ac98387-2792-4c40-b8a5-19174a0a1374" width = 600 alt = "">

&nbsp;

보통 디자이너 분들께서 편하게 UI 작업 하라고! 컴포넌트들을 이렇게 모아주시는데욥

근데 저 처음에 UI 짜는 것부터 차근차근 배울 때.. **탭바**는 디자인이 하나로 고정되어 있으니까 그렇다 치는데

**네비바**..? 는 뷰마다 애매하게 **조금씩 디자인이 다르단**말이죠..?

&nbsp;

😱 ***이걸 매번 뷰 UI 작업할 때마다 다 만들어?!***

&nbsp;

라고 생각했었는데.. 당연히 그럴 일 없었구요!! 

저렇게 전체 디자인에서 네 종류의 네비바만 사용된다고 디자이너께서 알려주시면,

네 개만 디자인해놓고 공용으로 돌려 쓰면 되는 거였습니다 ㅋ

&nbsp;

특히 **비슷한 뷰**가 계속 등장할 때 **공통된 디자인을 커스텀**해놓고 불러와서 쓰는 방식이 굉장히 많이 쓰이는 것 같아유 😌

&nbsp;

별 거 없지만 간단하게.. 시작해볼가욥?!

(모든 코드는 MVC, UIKit과 코드베이스 기준입니닷)

&nbsp;

***

## TabBar 커스텀하기

&nbsp;

우선 (제 생각에) 더 쉬운 것 같은.. 탭바 커스텀 먼저 적어볼게요!!

&nbsp;

### ✅ TabBarController 생성

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/f3ec0fd1-89f7-49f3-8106-6124d079c225" alt = "">

&nbsp;

처음부터 커스텀하지 않고 이미 만들어져 있는 `UITabBarController` 를 사용해볼게요!

`UITabBarController` 를 상속 받은 **TabBarController** 클래스를 하나 생성합니다.

&nbsp;

```swift
final class TabBarController: UITabBarController { 

     // MARK: - View Life Cycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
}
```

&nbsp;

그리고 **ViewController들을 탭바의 아이템들 (버튼)**에 각각 연결해주어야겠죠?!

이 때 `UINavigationController` 가 화면 전환 기능을 제공하는 점을 활용해, 

**각 ViewController마다 NavigationController 인스턴스를 생성**해 버튼 눌렀을 때 화면 전환이 될 수 잇도록! 할 겁니닷

&nbsp;

### ✅ NavigationController Instance 생성

&nbsp;

탭바에 추가하고 싶은 **아이템 개수만큼** 인스턴스를 생성해주면 되는데요!

저는 3개 예시로 들어보겠습니다 😊

&nbsp;

```swift
let nav = UINavigationController(rootViewController: rootViewController)
nav.tabBarItem.image = unselectedImage
nav.tabBarItem.selectedImage = selectedImage
nav.navigationBar.isHidden = true
```

&nbsp;

우선 `UINavigationController` 인스턴스를 생성하면서 `rootViewController` 를 설정해줍니다.

`rootViewController` 에는 이 **아이템을 눌렀을 때 이동시킬 ViewController**를 써주면 됩니닷

그 다음 tabBarItem의 image를 설정해주면 되는데요,

이 때 **기본 이미지**를 `unselectedImage`, 즉 선택되지 않은 **회색 이미지**로

**선택되었을 때의 이미지**를 `selectedImage`, 선택된 **컬러 들어간 이미지**로 설정합니다.

&nbsp;

### ✅ viewControllers에 추가

&nbsp;

```swift
viewControllers = [ nav ]
```

&nbsp;

그리고 만든 인스턴스를 viewControllers에 추가하면 됩니다!

요걸 세 번 반복하면 되는데...

근데.. 아이템 개수만큼 이 코드를 반복한다면 너무 길어지겠죠?!

그래서 **편리하게 작성하기 위한** `Helper 함수` 를 생성합니닷 😎

&nbsp;

### ✅ Helper 함수 생성

&nbsp;

```swift
private func templateNavigationController(unselectedImage: UIImage?, selectedImage: UIImage?, rootViewController: UIViewController) -> UINavigationController {
    let nav = UINavigationController(rootViewController: rootViewController)
    nav.tabBarItem.image = unselectedImage
    nav.tabBarItem.selectedImage = selectedImage
    nav.navigationBar.isHidden = true
    return nav
}
```

&nbsp;

요렇게~~

&nbsp;

```swift
let homeMainNVC = templateNavigationController(unselectedImage: ImageLiterals.homeIcHome.withRenderingMode(.alwaysOriginal),
                                                selectedImage: ImageLiterals.homeIcHomeFill.withRenderingMode(.alwaysOriginal),
                                                rootViewController: HomeMainVC())
let statisticsMainNVC = templateNavigationController(unselectedImage: ImageLiterals.homeIcStatistics.withRenderingMode(.alwaysOriginal),
                                                    selectedImage: ImageLiterals.homeIcStatisticsFill.withRenderingMode(.alwaysOriginal),
                                                    rootViewController: StatisticsMainVC())
let mypageMainNVC = templateNavigationController(unselectedImage: ImageLiterals.homeIcMypage.withRenderingMode(.alwaysOriginal),
                                                selectedImage: ImageLiterals.homeIcMypageFill.withRenderingMode(.alwaysOriginal),
                                                rootViewController: MyPageMainVC())
```

&nbsp;

하고 활용 예시로 에전 프로젝트에서 긁어왔습니닷 ㅋ

이 때 이미지 뒤에 `withRenderingMode(.alwaysOriginal)` 를 설정하지 않으면

아이템 선택/비선택 시, 원래 이미지 색이 아니라 **기본으로 설정되어있는 틴트 컬러(파랑이)**로 나오니까 주의합시다!!

&nbsp;

### ✅ 디자인하기 ㅋ

&nbsp;

그럼 세팅은 다 됐는데요!!

이제 디자인에 따라서 조금씩 커스텀을 진행해주면 됩니당 😎

아래는 제가 자주 사용했던 몇 가지 코드들을 모아봤어요 ㅋ

&nbsp;

📌 **탭바 아이템과 타이틀 간의 간격 조정**

```swift
let titleSpacing: UIOffset = UIOffset(horizontal: 0, vertical: -3)

for tabBarInfo in [statisticsMainNVC, homeMainNVC, mypageMainNVC] {
    tabBarInfo.tabBarItem.titlePositionAdjustment = titleSpacing
}
```

&nbsp;

아이템에 사진과 이름을 넣으면, 탭바 아이템이랑 타이틀 간의 기본 간격이 묘하게 좁습니닷

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/b418e95b-86dc-424b-90ed-342fa5d3188c" width = 500 alt = "">

&nbsp;

아주 묘하게...? 

그래서 살짝 높이 차이를 주기 위해 `tabBarItem.titlePositionAdjustment` 를 조정해줍니다!

&nbsp;

📌 **탭바 글씨 설정하기**

```swift
let appearance = UITabBarItem.appearance()
let attributes: [NSAttributedString.Key: Any] = [
    NSAttributedString.Key.font: UIFont.tabbar,
    NSAttributedString.Key.foregroundColor: UIColor.font1
]
appearance.setTitleTextAttributes(attributes, for: .normal)
```

&nbsp;

`setTitleTextAttributes` 로 탭바 글씨체랑 색상도 설정할 수 있습니닷

&nbsp;

📌 **모서리 둥글게하기**

```swift
tabBar.layer.cornerRadius = 15
tabBar.layer.maskedCorners = [.layerMinXMinYCorner, .layerMaxXMinYCorner]
```

&nbsp;

위에 첨부한 사진을 참고하면 **약간 모서리가 둥글게** 되어있죠!!

자주 보이는 디자인인데, `layer.maskedCorners` 를 이용해 **일부만 radius**를 줄 수도 있어욥

&nbsp;

📌 **선택된 아이템 위에 탭바 인디케이터 띄우기**

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/801cbac9-d934-46c1-bde3-b6ef5b2f17e1" width = 300 alt = "">

&nbsp;

또 많이 나오는 디자인 중 하나는 선택된 아이템 위에 얇은 줄이 떠있는 `TabBar Indicator` 인데요!

&nbsp;

```swift
private func addTabBarIndicatorView(index: Int, isFirstTime: Bool = false) {
    guard let tabView = tabBar.items?[index].value(forKey: "view") as? UIView else { return }
    if !isFirstTime {
        upperLineView.removeFromSuperview()
    }
    upperLineView = UIView(frame: CGRect(x: tabView.frame.minX + spacing, y: tabView.frame.minY + 0.1, width: tabView.frame.size.width - spacing * 2, height: 2))
    upperLineView.backgroundColor = .mainGreen
    tabBar.addSubview(upperLineView)
}
```

&nbsp;

파라미터로 넘어온 **index번째**의 아이템 위에 `upperLineView` 가 뜰 수 있도록 하는 

`addTabBarIndicatorView` 함수를 생성해주고요,

&nbsp;

```swift
private func setUpperLine() {
    DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) {
        self.addTabBarIndicatorView(index: 0, isFirstTime: true)
    }
}
```

&nbsp;

그리고 `viewDidLoad` 에서 위의 함수를 호출해서 탭바 인디케이터가 처음 보여질 수 있도록 합니닷 😎

그렇다면 매번 아이템을 클릭할 때마다 이 `addTabBarIndicatorView` 를 호출해줘야겠죠?!

&nbsp;

```swift
// MARK: - UITabBarControllerDelegate

extension TabBarController: UITabBarControllerDelegate {
    func tabBarController(_ tabBarController: UITabBarController, didSelect viewController: UIViewController) {
        addTabBarIndicatorView(index: self.selectedIndex)
    }
}
```

&nbsp;

`UITabBarControllerDelegate` 를 채택해서 아이템이 `didSelect` 될 때마다

그 **index을 값을 넘겨주며** 호출해주면 댑니다 ㅋ

&nbsp;

***

## NavigationBar 커스텀하기

&nbsp;

자 여기까지 해서 탭바를 커스텀해봤구요!

이제 네비바를 커스텀해보겠습니답~😋

&nbsp;

### ✅ CustomNavigationBar 생성

&nbsp;

우선 네비바의 경우에는 종류가 다양하잖아요?!

`열거형 enum` 을 활용해 각 네비바 **종류별로 케이스를 분류**해놓은 `NaviType` 를 하나 만들어봅시닷 😎

&nbsp;

```swift
@frozen
enum NaviType {
    case home // 홈에 존재하는 네비바
    case singleTitle // 타이틀이 한줄인 네비바
    case singleTitleWithPopButton // 타이틀 한 줄 + 뒤로가기 버튼 (티오랑 대화하기)
    case singleTitleWithBackButton // 타이틀 한 줄 + 뒤로가기 버튼 (마이페이지)
}
```

&nbsp;

이번에도 옛날에 썼던 예시 그대로 갖고 와 봤습니닷

&nbsp;

```swift
final class CustomNavigationBar: UIView {
    // MARK: - initialization
    
    init(_ vc: UIViewController, type: NaviType) {
        super.init(frame: .zero)
        self.vc = vc
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

&nbsp;

그리고 `CustomNavigationBar` 라는 이름의 `UIView` 를 하나 만들어줄게요!

&nbsp;

이 때, `이니셜라이저` 에서 **이 네비바가 어떤 타입인지** 받아올 수 있도록 하는데요,

네비바를 상속받아 생성할 때 **무조건** 이 이니셜라이저를 구현해주게 하기 위하여

`required` 수식어를 사용했습니닷 ✨

&nbsp;

### ✅ case별 UI 및 Layout 적용

&nbsp;

```swift
private func setLayout(_ type: NaviType) {
    switch type {
    case .home:
        setHomeLayout()
    case .singleTitle:
        setSingleTitleLayout()
    case .singleTitleWithPopButton:
        setSingleTitleWithPopButtonLayout()
    case .singleTitleWithBackButton:
        setSingleTitleWithBackButtonLayout()
    }
}
```

**NaviType이 무엇인지**를 파라미터로 받아와서, **각 case 별로 UI와 Layout을 구현**해 줄 수 있습니다!

&nbsp;

### ✅ 제목, 버튼 디자인 등 적용 함수

&nbsp;

그리고 네비바에 들어가는 제목 텍스트를 바꾸거나,

버튼 등이 들어갔을 때 addTarget을 추가해야 될 수도 있겠죠?!

&nbsp;

```swift
@discardableResult
func setTitle(_ title: String) -> Self {
    self.centerTitleLabel.text = title
    return self
}
```

setTitle 함수를 만들어 프로퍼티 생성 시 제목 값을 설정해 주는 예시를 들어보겠습니답

&nbsp;

```swift
private lazy var naviBar = CustomNavigationBar(self, type: .singleTitle).setTitle("제목 값ㅋ")
```

사용은 이렇게 ㅋ

&nbsp;

### ✅ 이전 페이지로 돌아가는 버튼

&nbsp;

그리고 많이 등장하는!! 이전 페이지로 돌아가는 버튼!!

&nbsp;

```swift
self.backButton.addTarget(self, action: #selector(popToPreviousVC), for: .touchUpInside)
```

```swift
@objc private func popToPreviousVC() {
    self.vc?.navigationController?.popViewController(animated: true)
}
```

버튼에다가 addTarget으로 `popViewController` 를 설정해주면 됩니다 ㅋ

***

## 마치며

&nbsp;

자 이번주는 이렇게! 간단하게 **탭바와 네비바 커스텀 방법**을 정리해봤구요!

더 공부해서 SwiftUI에서는 어떤 식으로 커스텀하는지두 나중에 정리해보겠습니다~~🥹

잘못된 점이 있으면 댓글로 알려주세요!!

그럼 안녕~~👋🏻

&nbsp;

[🌿 참고 자료](https://medium.com/geekculture/customize-tabbar-in-swift-ios-bf14a8e02d6)

