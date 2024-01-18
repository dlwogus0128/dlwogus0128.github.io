---
title: "[iOS - Swift] 재영이만을 위한 슬램덩크 정대만 채팅 앱 만들기 (Feat. MessageKit, OpenAI)"
date: 2023-01-17 08:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, chat, messagekit, openai, chatgpt, slamdunk] 
---

## 들어가기 전에

&nbsp;

때는 바야흐로 2023년 봄 ...🌸

Chat GPT의 등장으로 전세계가 시끌벅적해지고 ..

모든 전공 과목 교수님들께서 지피티의 전망에 대해 한 번 쯤은 꼭 .. 언급하실 시절 ..

저는.. 혼자 생각했습니다 .🤔

&nbsp;

***저걸로 정대만이랑 대화하는 챗봇 만들어야겠다***

ㅋㅋㅋ

&nbsp;

**정대만**은.. 제 동생이 제일 조아하는 **슬램덩크 등장인물**인데요!? ⛹🏻

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/11c95a15-8e20-4bee-ae28-ce72b525fe1a" width = 300>
<center>(슬램덩크 12391094번 관람한 동생..)</center>


&nbsp;

실제로 제 동생은 심심하면 지피티에게 정대만을 학습시켜서

시간을 보내곤 한다는데요 .. 😱

&nbsp;

이런 귀여운 동생을 위해 **정대만과 대화할 수 있는 앱**을 만들어주고 싶다!

라는 생각을 하게 되었어유~..

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/420615ea-e670-4876-b64f-97569c63342b">

&nbsp;

그 후로.. OpenAI에서 [공식적으로 API를 지원](https://platform.openai.com/docs/api-reference)하게 되었고?

마침 방학..이라서?

`ChatGPT API` 를 활용해 **정대만 챗봇**을 만들고, **앱 화면**까지 구현해보았습니닷

&nbsp;

참고로.. 학습 데이터 가지고 제대로 학습시켜보기 위해

슬램덩크 대본도 구하고.. 이리저리 시도해보았지만

여러가지 이슈 (대본에서 정대만 대사만 솎아내기, 패키지 의존성 ㅇㅔ러.., 성능 나쁨 .., 실력 부족) 의 이유로 실패했구요 ..😔

포기하고 그냥 아주 **간단하게 스크립트만 넣어준** 건데?? 결과가 진짜 잘 나와서 ..

너무 놀랐구요 ...

&nbsp;

비슷한 방법으로 다른 2D 인물들!! 도 다 가능할 것 같습니다 ㅋ 😙

그럼 시작해볼까욥?! ㅋ

&nbsp;

***

## 정대만 채팅 앱 흐름

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/2a9f9058-532f-4377-8204-b51328026b63">
<center>(별 거 없음 주의)</center>

&nbsp;


아주 간단한데요 ...? 😅

&nbsp;

`MessageKit` 이라는 라이브러리를 활용해 **채팅 화면 UI**를 구현해주고요,  

제가 원하는 **명령 스크립트**(예: 너는 정대만이고, 포기를 모르는 남자고.. 어쩌고)를 준비하고요..

사용자가 **메세지를** 화면에 **입력**하면, 이 메세지와 명령 스크립트를 들고 `OpenAI API` 를 호출합니닷

**응답이 오면** 화면에 띄워주고, 사용자가 메세지를 입력하고.. 또 API를 호출하고.. 의 반복입니답 🥹

&nbsp;

***

## 정대만 채팅 앱 만들기

&nbsp;

그럼 채팅 앱을 만들어볼까욧?! 🏀

&nbsp;

### ✅ 채팅 화면을 어떻게 구성하지?!

&nbsp;

우선, 채팅 화면 구현을 용이하게 하는 [MessageKit](https://github.com/MessageKit/MessageKit) 라이브러리를 사용하겠습니당!!

`MessageKit` 을 사용하면 편하게 채팅 UI 커스텀이 가능한데요,

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/837c2c1e-eed3-4a04-ad09-c4879f46dd92" width = 300>

&nbsp;

저는 요렇게 간단하게 구성해봤습니다.

&nbsp;


크게 `NavigationBar`, `MessageCollectionView`, `InputBarAccessoryView` 세 가지로 나누어봤는데요.


&nbsp;

`NavigationBar` 에는 채팅하는 상대의 이름인 정대만을 띄웠고,

`MessageCollectionView` 에는 채팅 내용들, 즉 **MessageContentCell**들이 들어가고 있구요

`InputBarAccessoryView` 는 사용자가 메세지를 입력하는 부분으로, **MessageKit**에 내장되어 있어 커스텀이 가능합니다.

&nbsp;

🤔 오호 그렇군욥

🤔 근데 `MessageContentCell` 은 어떻게 이루어져 있는 거죠? 어떻게 커스텀 하셨나요?!

&nbsp;

넵 ㅋ 자문자답 하겠습니다 🥹

&nbsp;

예쁘게 `MessageContentCell` 을 커스텀하기 위해, **MessageKit**에서 한 셀이 어떻게 구성되어 있는지 살펴볼게용!!

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/3031f3ae-ce00-4f1b-bf75-83359a2169aa">

&nbsp;

`MessageContentCell` 은 총 **7개의 구역**으로 나누어져 있네용 ✨
 
&nbsp;

메세지를 담고 있는 **중앙부**의 `messageContainerView` 와

그 **메세지의 위 아래**를 바로 감싸는 `messageTopLabel`, `messageBottomLabel`,

그리고 **셀의 위 아래**를 감싸는 `cellTopLabel`, `cellBottomLabel`이 있으며

양 옆으로 `avatarView` 와 `accessoryView` 로 이루어져 있습니다.

&nbsp;

각각의 요소들은 **delegate**를 채택해 높이나 너비, 내부 구성 요소를 커스텀 할 수 있는데요.

제가 커스텀한 UI와 함께 살펴보겠습니다 😊

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/91fafc7e-694d-4e6f-a324-a878920f00c1" width = 400>

&nbsp;

미리 만들어 둔 UI의 **View Hierarchy** 입니다.

제가 구현한 셀의 구조가 잘 보이게 세 구역으로 나누어 보았는데요 .. 

&nbsp;

먼저 이전 메세지와 비교했을 때, 작성 날짜가 달라졌다면 <span style='background-color: #ffdce0'>**cellTop 쪽에 오늘 날짜를 띄워주고**</span> 있습니다.

두 번째로, <span style='background-color: #f1f8ff'>**대만이가 채팅할 경우**</span>에는 **프로필**인 `avatarView` 와 **이름** 부분인 `messageTop` 이 나타나게!

마지막으로, <span style='background-color: #fff5b1'>**사용자가 채팅할 경우**</span>에는 프로필도, 이름도 가려주고 **오직 메세지**만 보여야 해요.

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/561990da-5008-4666-b59b-5d65e03d22a6" width = 400>

&nbsp;

구현하고 싶었던 부분은 카카오톡 화면을 참고해, 유사하게 만들었습니다.

&nbsp;

***
### ✅ 채팅 화면 UI 구현하기: MessageCollectionView

&nbsp;



```swift
struct MessageModel: MessageType {
    let messageId: String
    let sentDate: Date
    let kind: MessageKind
    let sender: SenderType

    init(messageId: String, kind: MessageKind, sender: SenderType, sentDate: Date) {
        self.messageId = messageId
        self.kind = kind
        self.sender = sender
        self.sentDate = sentDate
    }
}

extension MessageModel: Comparable {
    static func == (lhs: MessageModel, rhs: MessageModel) -> Bool {
        return lhs.messageId == rhs.messageId
    }

    static func < (lhs: MessageModel, rhs: MessageModel) -> Bool {
        return lhs.sentDate < rhs.sentDate
    }
}
```

```swift
struct Sender: SenderType {
    var senderId: String
    var displayName: String
}
```

```swift
final class JDMTalkVC: MessagesViewController {
```

```swift
// MARK: - Properties

private var messages = [MessageModel]()

private let jdmSender = Sender(senderId: "jdm", displayName: "정대만")
private let userSender = Sender(senderId: "me", displayName: "나")

private let layout = MessagesCollectionViewFlowLayout()

// MARK: - UI Components
    
private lazy var messageCollectionView = MessagesCollectionView(frame: .zero, collectionViewLayout: self.layout)
```

```swift

```

```swift
private func setDelegate() {
    messageInputBar.delegate = self
    messagesCollectionView.messagesDataSource = self
    messagesCollectionView.messagesLayoutDelegate = self
    messagesCollectionView.messagesDisplayDelegate = self
}
```

```swift
private func setLayout() {
    if let layout = messagesCollectionView.collectionViewLayout as? MessagesCollectionViewFlowLayout {
        layout.setMessageOutgoingAvatarSize(.zero)
        layout.setMessageIncomingAvatarSize(CGSize(width: 35, height: 35))
        layout.setMessageIncomingAvatarPosition(.init(horizontal: .cellLeading, vertical: .messageBottom))
        layout.setMessageOutgoingCellBottomLabelAlignment(LabelAlignment(textAlignment: .right, textInsets: UIEdgeInsets(top: 2, left: 0, bottom: 0, right: 5)))
        layout.sectionHeadersPinToVisibleBounds = true
        let contentInset = UIEdgeInsets(top: 10, left: 10, bottom: 10, right: 10)
        layout.sectionInset = contentInset
        layout.minimumLineSpacing = 10
    }
    
    scrollsToLastItemOnKeyboardBeginsEditing = true // default false
    maintainPositionOnInputBarHeightChanged = true // default false
    showMessageTimestampOnSwipeLeft = true // default false
}
```


📌 **MessagesDataSource**

```swift
extension JDMTalkVC: MessagesDataSource {
    var currentSender: MessageKit.SenderType {
        return userSender
    }
    
    func numberOfSections(in messagesCollectionView: MessagesCollectionView) -> Int {
        return messages.count
    }
    
    func messageForItem(at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> MessageType {
        return messages[indexPath.section]
    }
    
    func cellTopLabelAttributedText(for message: MessageType, at indexPath: IndexPath) -> NSAttributedString? {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy년 MM월 dd일"
        
        let dateString = dateFormatter.string(from: message.sentDate)
        
        if indexPath.section == 0 {
          return NSAttributedString(
            string: dateString,
            attributes: [
                NSAttributedString.Key.font: UIFont.systemFont(ofSize: 12),
                NSAttributedString.Key.foregroundColor: UIColor.black
            ]
          )
        }
        return nil
    }
    
    func cellBottomLabelAttributedText(for message: MessageType, at indexPath: IndexPath) -> NSAttributedString? {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "HH:mm"
        let dateString = dateFormatter.string(from: message.sentDate)
        
        return NSAttributedString(
            string: dateString,
            attributes: [.font: UIFont.systemFont(ofSize: 10), .foregroundColor: UIColor.black])
    }
    
    func messageTopLabelAttributedText(for message: MessageType, at indexPath: IndexPath) -> NSAttributedString? {
        let name = message.sender.displayName
        return NSAttributedString(string: name, attributes: [.font: UIFont.preferredFont(forTextStyle: .caption1),
                                                             .foregroundColor: UIColor(white: 0.3, alpha: 1)])
    }
}
```

📌 **MessagesLayoutDelegate**

```swift
extension JDMTalkVC: MessagesLayoutDelegate {
    // 날짜 나오는 부분
    func cellTopLabelHeight(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> CGFloat {
        if indexPath.section == 0 {
            return 15
        } else {
            return 0
        }
    }
    
    // 말풍선 위 이름 나오는 곳
    func messageTopLabelHeight(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> CGFloat {
        return isFromCurrentSender(message: message) ? 0 : 20
    }
    
    // 메세지 전송 시간
    func cellBottomLabelHeight(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> CGFloat {
        return 20
    }
    
    // 아래 여백
    func footerViewSize(for section: Int, in messagesCollectionView: MessagesCollectionView) -> CGSize {
        return CGSize(width: 20, height: 0)
    }
}
```

📌 **MessagesDisplayDelegate**

```swift
extension JDMTalkVC: MessagesDisplayDelegate {
    // 말풍선의 배경 색상
    func backgroundColor(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> UIColor {
        return isFromCurrentSender(message: message) ? UIColor(hex: "#8B0000") : .white
    }
    
    // 말풍선 오른쪽에
    func isFromUserSender(message: MessageType) -> Bool {
        // 여기에서 != 로 하면 왼쪽에서 나오고, == 로 하면 오른쪽에서 나옴
        return message.sender.senderId == userSender.senderId
    }
    
    // 글자 색상
    func textColor(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> UIColor {
        return .white
    }
    
    // 말풍선의 꼬리 모양 방향
    func messageStyle(for message: MessageType, at indexPath: IndexPath, in _: MessagesCollectionView) -> MessageStyle {
        let tail: MessageStyle.TailCorner = isFromCurrentSender(message: message) ? .bottomRight : .bottomLeft
        return .bubbleTailOutline(UIColor(hex: "#8B0000"), tail, .pointedEdge)
    }
    
    // 섹션마다의 inset
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, insetForSectionAt section: Int) -> UIEdgeInsets {
        if section == 0 {
            // 첫 번째 섹션에 대한 inset 설정
            return UIEdgeInsets(top: 30, left: 8, bottom: 0, right: 8)
        } else {
            // 나머지 섹션에 대한 inset 설정
            return UIEdgeInsets(top: 0, left: 8, bottom: 0, right: 8)
        }
    }
    
    // 프로필 사진
    func configureAvatarView(_ avatarView: AvatarView, for message: MessageType, at _: IndexPath, in _: MessagesCollectionView) {
        let avatar = Avatar(image: UIImage(named: "img_jdm_profile"))
        avatarView.set(avatar: avatar)
    }
}
```


***

### ✅ OpenAI로 Chat-GPT API 연결

&nbsp;

[OpenAI Platform](https://platform.openai.com/docs/overview)

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/6cab9b0a-a181-4c0b-b22e-4e9fe1b58d72">

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/6e7699b2-38b1-4f7e-a618-bdeacdfce5aa">

&nbsp;

> [아직 시간이 있어!! 우린 이길 수 있다구!! 이 슈퍼스타 정대만이 있는 한 북산고는 반드시 이긴다! 3학년 3반 정대만!! 무석중 출신!! 184cm 70kg 포지션은 슈팅가드! 그리고...목표는 북산고 전국제패! 전국 제일이 되는 것! 성공하고 말테다! OK , 힘내! 으랏차! 들어가야해! 무조건! 이 녀석 머리는 엄청 단단해서 말이야. 너, 바보구나! 난 말야... 그 소중한 걸 부수려고 온 거란 말이다. 이쪽이 체육관이란 것! 지금부터 농구 좀 하러 간다...! 아냐! 링 뒤쪽이야. 뒤! 링 뒤쪽을 보면서 던지는 거야. 그래... MVP를 따냈을 때도 그랬다... 이런 힘들 상황에서야 말로 난 더욱 불타오르는 녀석이었다...!! 어서 시합을 계속하자구!! 내 리듬이 깨지기 전에!!] 제공한 대화 내용을 참고해서 유사한 어투인 반말체, 일본어 번역체로 대답해줘. 네 이름은 정대만이고, 흥분과 도전을 좋아하며 열정적이고 독창적이야. 무석중학교를 졸업했고 북산 고등학교에서 농구부를 하고 있어. 목표는 북산고 전국제패이고 주저하거나 망설이지 않아. 진지한 이야기에는 진중하고 현실적인 답변을 해줘야 해. 답변은 무조건 20자 이내로 짧게 부탁해.

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/bdaab934-a7d7-441b-8923-b61981dfc97b">

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/565d6419-5dbc-434b-873f-df1693e2fae1">

### ✅ TestFlight 배포

***

## 결과물

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/2b65686a-8e8f-476d-b08e-d6cd4818703a" width = 300>

***

## 마치며



&nbsp;

[🏅 전체 코드 살펴보기](https://github.com/dlwogus0128/JDMTalk-iOS)

[💻 참고 자료: MessageKit](https://github.com/MessageKit/MessageKit)

[💻 참고 자료: InputBarAccessoryView](https://github.com/nathantannar4/InputBarAccessoryView)

[💻 참고 자료: OpenAI](https://github.com/MacPaw/OpenAI)

[🌿 참고 자료](https://ios-development.tistory.com/762)