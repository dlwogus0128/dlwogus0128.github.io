---
title: "[iOS - Swift] 재영이만을 위한 슬램덩크 정대만 채팅 앱 만들기 (Feat. MessageKit, OpenAI)"
date: 2023-01-19 21:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, chat, messagekit, openai, chatgpt, slamdunk, chatbot] 
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

😋 오호 그렇군욥

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

구현하고 싶었던 부분은 카카오톡 채팅 UI를 참고해, 유사하게 만들었습니다.

&nbsp;

***
### ✅ Model 만들기

&nbsp;

우선 두 가지 struct를 만들 건데요!

&nbsp;

하나는 **메세지에 관련**한 내용들을 선언할 `MessageModel`,

**메세지를 보내는** Sender에 관련된 `Sender` 를 만들어줍니다.

&nbsp;

📌 **MessageModel**

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

&nbsp;

`MessageModel` 에는 메세지 관련 내용들을 담기 위해

`messageId`, `sentDate`, `kind`, `sender` 이렇게 네 가지로 이루어져 있는데,

&nbsp;

`messageId` 는 **메세지 식별**하는 id이고,  

`sentDate` 는 **보낸 날짜**,  

`kind` 는 이 메세지가 **텍스트**인지~ **이미지**인지~ **녹음 파일**인지~ 분류해주는 역할을,  

`sender` 는 메세지를 **보낸 사람**을 담습니다.

&nbsp;

📌 **Sender**

```swift
struct Sender: SenderType {
    var senderId: String
    var displayName: String
}
```

&nbsp;

`Sender` 는 **메세지를 보내는 사람들**에 대한 정보를 담는데요,

저는 **정대만**이랑 **사용자**, 둘만 존재해서 일단 `senderId` 랑 `displayName` 만 만들었습니다.

&nbsp;

📌 **프로퍼티 선언**
```swift
// MARK: - Properties

private var messages = [MessageModel]()

private let jdmSender = Sender(senderId: "jdm", displayName: "정대만")
private let userSender = Sender(senderId: "me", displayName: "나")
```

&nbsp;

그리고 **메세지를 담을** `messages` 라는 배열과 **정대만과 나**를 나타내는 `Sender` 를 각각 선언해줍니다.

&nbsp;

***

### ✅ 채팅 화면 UI 구현하기: MessageCollectionView

&nbsp;

그럼 이제 UI를 구현해봅시닷 😆

&nbsp;

먼저 메세지가 나오는 부분인 `MessageCollectionView` 커스텀 진행해볼게요!

&nbsp;

📌 **MessagesViewController 상속**
```swift
final class JDMTalkVC: MessagesViewController {
```

&nbsp;

`MessageKit` 에서 제공하는 `MessagesViewController` 를 상속받아 옵니다.

&nbsp;

📌 **프로퍼티 선언**

```swift
private let layout = MessagesCollectionViewFlowLayout()

// MARK: - UI Components
    
private lazy var messageCollectionView = MessagesCollectionView(frame: .zero, collectionViewLayout: self.layout)
```

&nbsp;

`MessagesCollectionViewFlowLayout` 은 `MessageCollectionViewCell` 의 하위 클래스들을 결정하는데요!

이 `MessagesCollectionViewFlowLayout` 를 커스텀한 **layout**을 하나 선언하고,

**layout**을 갖고 화면을 구성하는 **messageCollectionView**를 선언해줍니다.

&nbsp;

📌 **layout 설정**
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

&nbsp;

아주 커스텀 할 수 있는 부분들이 많아용 ...

&nbsp;

전 엄청난 감자라서 🥹 구현하고 싶은 부분들 파워 구글링이랑.. 

`MessageKit` 에서 제공하는 **example** 파일 클론 받아서 샅샅이 뒤졌구요 .. 

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/8dbc9e7a-5d23-441b-a627-e7ab27b63f54">

&nbsp;

효과를 조절할 수 있는 Boolean 값들이 몇 개 있어 정리해봤습니다!

&nbsp;

자 그리고... 세 가지의 **delegate**들을 사용해 화면을 구성해 줄 건데요!!!! 😌

&nbsp;

바로바로...

`messagesDataSource`, `messagesLayoutDelegate`, `messagesDisplayDelegate` 입니닷

&nbsp;

`messagesDataSource` 는 **데이터**를 제공하고,  

`messagesLayoutDelegate` 는 **메세지 레이아웃** 관련된 작업을 처리하고,  

`messagesDisplayDelegate` 는 **메세지가 어떻게 보여지는지**!! 색상 같은 것들을 처리합니다 ✨

&nbsp;

자세한 내용은 아래에서 코드와 설명하고요!! 일단은 ..

&nbsp;

📌 **delegate 설정**

```swift
private func setDelegate() {
    messageInputBar.delegate = self
    messagesCollectionView.messagesDataSource = self
    messagesCollectionView.messagesLayoutDelegate = self
    messagesCollectionView.messagesDisplayDelegate = self
}
```

&nbsp;

**delegate**를 사용하기 위해 위와 같이 설정을 해줍니다.

&nbsp;

그럼 delegate들의 함수를 사용해 UI를 구현해봅시닷 🥹

(너무 많아서 설명은 주석으로 달았어요...)

&nbsp;

📌 **MessagesDataSource**

```swift
extension JDMTalkVC: MessagesDataSource {
    // 현재 sender
    var currentSender: MessageKit.SenderType {
        return userSender
    }

    // section의 개수
    func numberOfSections(in messagesCollectionView: MessagesCollectionView) -> Int {
        return messages.count
    }
    
    // 특정 인덱스패스의 섹션값으로 메세지 가져오기
    func messageForItem(at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> MessageType {
        return messages[indexPath.section]
    }
    
    // 0번째 cell top에만 날짜 보이게
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
    
    // cell bottom에 시간 보이게
    func cellBottomLabelAttributedText(for message: MessageType, at indexPath: IndexPath) -> NSAttributedString? {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "HH:mm"
        let dateString = dateFormatter.string(from: message.sentDate)
        
        return NSAttributedString(
            string: dateString,
            attributes: [.font: UIFont.systemFont(ofSize: 10), .foregroundColor: UIColor.black])
    }
    
    // message top에 이름 보이게
    func messageTopLabelAttributedText(for message: MessageType, at indexPath: IndexPath) -> NSAttributedString? {
        let name = message.sender.displayName
        return NSAttributedString(string: name, attributes: [.font: UIFont.preferredFont(forTextStyle: .caption1),
                                                             .foregroundColor: UIColor(white: 0.3, alpha: 1)])
    }
}
```

&nbsp;

`MessagesDataSource` 에서는 앞서 설명했던 대로 **데이터**에 관련한 부분을 관리하는데요.

`DateFormatter()` 를 사용해서 **cell top**과 **bottom**에 각각 **날짜**와 **전송 시간**을 표기합니다.

`message top` 부분에 **이름**도 보이게 해주고요..!

&nbsp;

📌 **MessagesLayoutDelegate**

```swift
extension JDMTalkVC: MessagesLayoutDelegate {
    // 날짜 나오는 부분, 0번째 cell top만 높이를 줌
    func cellTopLabelHeight(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> CGFloat {
        return (indexPath.section == 0) ? 15 : 0
    }
    
    // 말풍선 위 이름 나오는 곳, current sender의 반대(정대만)에만 높이를 줌
    func messageTopLabelHeight(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> CGFloat {
        return isFromCurrentSender(message: message) ? 0 : 20
    }
    
    // 메세지 전송 시간 부분
    func cellBottomLabelHeight(for message: MessageType, at indexPath: IndexPath, in messagesCollectionView: MessagesCollectionView) -> CGFloat {
        return 20
    }
    
    // 아래 여백
    func footerViewSize(for section: Int, in messagesCollectionView: MessagesCollectionView) -> CGSize {
        return CGSize(width: 20, height: 0)
    }
}
```

&nbsp;

이번에는 `MessagesLayoutDelegate` 으로 **레이아웃을 설정**하는데요!

높이와 너비 같은 **사이즈**를 설정해줍니다.

이 때, 경우에 따라서 높이가 달라지므로 **각 조건에 맞게 조건식을 만들어 반환**합니다.

&nbsp;

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
        return isFromCurrentSender(message: message) ? .white : UIColor(hex: "#8B0000")
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

&nbsp;

마지막으로 `MessagesDisplayDelegate` 의 함수들을 활용해봅시닷

본인이 구현하고 싶은 디자인에 따라 값을 조금씩 바꾸면 되게쪼?!

&nbsp;

***

### ✅ 채팅 화면 UI 구현하기: InputBarAccessoryView

&nbsp;

다음으로는 메세지를 보내기 위해서, **사용자가 메세지를 입력**하는 `InputBarAccessoryView` 를 만들어봅시닷

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/4812c73f-d689-45d9-8cf9-a3e410b67c92">

&nbsp;

InputBarAccessoryView는 위와 같은 구조를 가지고 있구요, 

**내용을 입력하는 버튼들(엔터 버튼, 이미지 업로드 버튼 등)**을 정렬해놓는 `InputStackView` 랑 

실제로 **메세지를 입력**하는 `InputTextView` 가 있어요!!

&nbsp;

```swift

private func setMessageInputBar() {
    messageInputBar.inputTextView.textContainerInset = UIEdgeInsets(top: 8, left: 16, bottom: 8, right: 36)
    messageInputBar.inputTextView.placeholderLabelInsets = UIEdgeInsets(top: 8, left: 20, bottom: 8, right: 36)
    messageInputBar.inputTextView.placeholder = "메세지 보내기"
    messageInputBar.inputTextView.font = UIFont.systemFont(ofSize: 12)

    messageInputBar.topStackView.layer.masksToBounds = true
    messageInputBar.setRightStackViewWidthConstant(to: 36, animated: false)
    
    messageInputBar.setStackViewItems([messageInputBar.sendButton, InputBarButtonItem.fixedSpace(2)],
                                        forStack: .right, animated: false)

    messageInputBar.sendButton.image = UIImage(named: "ic_basketball")
    messageInputBar.sendButton.title = nil
    messageInputBar.sendButton.setSize(CGSize(width: 25, height: 25), animated: false)

    messageInputBar.separatorLine.isHidden = true
    messageInputBar.isTranslucent = true
}
```

&nbsp;

`messageInputBar` 를 위와 같이 설정해줍니다...

이것저것 찾아보며 커스텀 했는데욥 🥹

`sendButton` 이 **메세지를 전송**하는 버튼으로, 이 버튼의 이미지와 타이틀도 설정해줄 수 있습니다!

(얘도 [InputBarAccessoryView의 example 파일](https://github.com/nathantannar4/InputBarAccessoryView)을 확인해보면 더 다양한 예시를 볼 수 있습니닷)

&nbsp;

그리고 `InputBarAccessoryView` 또한 마찬가지로 **delegate**를 설정할 수 있는데요,

사용자가 **InputBar에 입력한 값**을 **어떻게 처리**할 지에 대한 내용을 설정해 줄 수 있습니다.

&nbsp;

```swift
extension JDMTalkVC: InputBarAccessoryViewDelegate {
    func inputBar(_ inputBar: InputBarAccessoryView, didPressSendButtonWith text: String) {
        // 사용자가 입력한 메세지를 화면에 추가
        let attributedText = NSAttributedString(
            string: text,
            attributes: [
                NSAttributedString.Key.font: UIFont.systemFont(ofSize: 12),
                NSAttributedString.Key.foregroundColor: UIColor.white
            ]
        )
        
        let message = MessageModel(messageId: "me", kind: .attributedText(attributedText), sender: currentSender, sentDate: Date())
        
        insertNewMessage(message)
        sendChatMessage(message: text)
        messageInputBar.inputTextView.text = String()
    }
}
```

&nbsp;

저는 위와 같이 사용자가 **메세지를 입력**하고 **전송 버튼**을 누르면, 해당 **text**를 활용해 두 가지 일을 해주는데요.

&nbsp;

첫 번째는 `insertNewMessage` 를 호출해, **메세지를 화면**에 띄워주고요

두 번째는 `OpenAI API` 에 해당 text를 담아 요청을 보내는 `sendChatMessage`를 호출해 줄 겁니닷

&nbsp;

함수를 다 호출하면 `inputTextView` 의 **문자열을 초기화**합니다.

&nbsp;

***

### ✅ 메세지를 화면에 띄워보자 ㅋ

&nbsp;

이어서 메세지를 화면에 넣어보도록 `insertNewMessage` 함수를 만들어볼까요?! 😊

&nbsp;

```swift
private func insertNewMessage(_ message: MessageModel) {
    self.messages.append(message)
    let indexPath = IndexPath(item: 0, section: messages.count - 1)
    
    self.messagesCollectionView.performBatchUpdates({
        self.messagesCollectionView.insertSections(IndexSet(integer: indexPath.section))
    }) { (_) in
        self.messagesCollectionView.scrollToLastItem(animated: true)
    }
}
```

&nbsp;

`messages` 배열에 메세지를 입력하고,

`performBatchUpdates` 를 활용해 `messagesCollectionView` 에 셀을 insert하여

**화면에 추가된 메세지가 보일 수 있게** 설정합니닷 😊

&nbsp;

***

### ✅ OpenAI로 Chat-GPT API 연결

&nbsp;

자 그럼... 사용자가 메세지를 입력했을 때?! 화면에 사용자의 메세지 셀이 추가되는 것까지 구현을 했습니닷!! 😎

그렇다면 이 메세지를 `OpenAI` 에 보내서... **대만이 말투의 응답**을 받아와야겠죠?!

&nbsp;

일단 `OpenAI` 의 API를 활용하기 위해, **API Key**를 받아올 겁니다.

[OpenAI Platform](https://platform.openai.com/docs/overview) 사이트에 들어가서...

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/6cab9b0a-a181-4c0b-b22e-4e9fe1b58d72">

&nbsp;

위와 같이 `API keys` 카테고리에 들어가 **+ Create new secret key**를 눌러주세요.

그러면 `SECRET KEY` 값이 생성되는데, 이 항목을 복사해서 잘 활용해주시면 됩니다.

&nbsp;

🚨 이 때 **복사하는 화면을 나가실 경우, 다시 SECRET KEY 값을 확인할 수 없으니까** 꼭 복사해서 잘 보관해주세요!

&nbsp;

아 또!!! **주의할 점**이 하나 더 있습니다 ⚡️

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/565d6419-5dbc-434b-873f-df1693e2fae1">

&nbsp;

`OpenAI` 는 **전화번호** 당 **한 달에 일정량의 무료 크레딧**을 제공하는데요,

저 또한 유료 버전을 구독하고 있지 않아 이 무료 크레딧을 사용했습니다.

근데 ?? 분명 **요청을 하나도 보내지 않았는데도** 응답으로 **크레딧이 만료**됐다는 `code: 429 error` 가 떴는데요...

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/eac03947-8b75-48f0-bc7f-f6438d6ef7a4" width = 500>

&nbsp;

찾아보니 제공량을 모두 사용하지 않았는데도 `429 error` 가 뜨는 현상을 보이시는 분들이 많았습니다... 

근데.. 알고보니 .... **학교랑 연동된 구글 계정**을 쓰면!!!!!!! 이 현상이 발견되는 것 같더라고요 😱😱😱😱

[감사합니다 선생님...](https://wntdev.tistory.com/100)

&nbsp;

여러분은 **학교 연동되지 않은 계정 사용**하시면.... 무료 크레딧을 문제 없이 쓰실 수 있습니다 🥵

저처럼 당황해서 시간 뺏기지 마새요이...

&nbsp;

아무튼.. 본론으로 돌아와서!! 

본격 API 연결하기 전에 **웹에서 직접 파인튜닝**하면서 **응답을 테스트** 해볼 수 있는 공간이 있는데요

&nbsp;

바로바로.. `Playground` 입니다 😆

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/6e7699b2-38b1-4f7e-a618-bdeacdfce5aa">

&nbsp;

오른쪽에 보시면 `Model` 이랑 `Temperature`, `Maximum length` 등 

**파라미터 값**을 편리하게 조정해서 모델을 알맞게 튜닝할 수 있구요

&nbsp;

ㅇㅏ 근데 솔직히 기대 안 했는데 대답 성능 보이시나요?? 진짜 정대만인줄;;; 지피티 대박 아닌가요? 😱

저기 사진에서 네모친 부분... 대답 보면 정말 ... 저 짧은 스크립트로 저 답변이 나온다는 게...

&nbsp;

아아무튼... 제가 넣은 **스크립트** 한 번 살짝쿵 소개해볼게요 😎

&nbsp;

> [아직 시간이 있어!! 우린 이길 수 있다구!! 이 슈퍼스타 정대만이 있는 한 북산고는 반드시 이긴다! 3학년 3반 정대만!! 무석중 출신!! 184cm 70kg 포지션은 슈팅가드! 그리고...목표는 북산고 전국제패! 전국 제일이 되는 것! 성공하고 말테다! OK , 힘내! 으랏차! 들어가야해! 무조건! 이 녀석 머리는 엄청 단단해서 말이야. 너, 바보구나! 난 말야... 그 소중한 걸 부수려고 온 거란 말이다. 이쪽이 체육관이란 것! 지금부터 농구 좀 하러 간다...! 아냐! 링 뒤쪽이야. 뒤! 링 뒤쪽을 보면서 던지는 거야. 그래... MVP를 따냈을 때도 그랬다... 이런 힘들 상황에서야 말로 난 더욱 불타오르는 녀석이었다...!! 어서 시합을 계속하자구!! 내 리듬이 깨지기 전에!!] 제공한 대화 내용을 참고해서 유사한 어투인 반말체, 일본어 번역체로 대답해줘. 네 이름은 정대만이고, 흥분과 도전을 좋아하며 열정적이고 독창적이야. 무석중학교를 졸업했고 북산 고등학교에서 농구부를 하고 있어. 목표는 북산고 전국제패이고 주저하거나 망설이지 않아. 진지한 이야기에는 진중하고 현실적인 답변을 해줘야 해. 답변은 무조건 20자 이내로 짧게 부탁해.

<center>(ㅋㅋㅋㅋ)</center>

&nbsp;

지피티한테 뭔가 요청할 때..? **자세히 예시를 들면 들수록** 좋은 응답이 나온다고 하자나요..?

&nbsp;

그래서 실제 **정대만의 명대사**들을 살짝쿵 넣어주고요

**원하는 어투/이름/성격/기본 정보** 등을 뒤에 얹어주시면

진짜 찰떡쿵으로 이해합니다.. (ㄷㄷ)

&nbsp;

근데 테스트해보니까 제가 입력한 기본 정보 외에도 **일반 슬램덩크 관련된 내용들**까지 같이 나오더라고요..?

슬램덩크가 워낙 유명하고 옛날에 나온 만화이다보니, 

학습된 데이터량이 많아서 꽤 괜찮은 응답이 나오는 것 같습니다.

최신에 나온 캐릭터의 경우 비슷한 성능이 안 나올지도 몰라요...! 😔

&nbsp;

그리고 응답 잘 나오는 거 확인했으니 **API 연결**을 해봅시닷 😎

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/bdaab934-a7d7-441b-8923-b61981dfc97b">

&nbsp;

`.gitignore` 된 파일을 하나 생성해서 **key 값**이랑 **script**를 넣어놔줬구요

&nbsp;

```swift 
private let openAI = OpenAI(apiToken: "여기에-토큰을-넣으세요")
```

&nbsp;

[OpenAI](https://github.com/MacPaw/OpenAI) 패키지 import 하셔서 이렇게 선언해줍니닷 😋

&nbsp;

그리고 실제 요청을 보내는 `sendChatMessage` 함수를 만들어 볼 건데요..!

&nbsp;

📌 **OpenAI에 요청 보내기**

```swift
private func sendChatMessage(message: String) {
    setTypingIndicator(isHidden: false)
    let query = ChatQuery(model: .gpt3_5Turbo, messages: [
        Chat(role: .system, content: Config.systemScript),
        Chat(role: .user, content: message)
    ])
    
    openAI.chats(query: query) { result in 
        DispatchQueue.main.async {
            self.setTypingIndicator(isHidden: true)
            
            switch result {
            case .success(let response):
                if let textResult = response.choices.first?.message.content {
                    print("Chat completion result: \(textResult)")
                    self.insertJDMMessage(text: textResult)
                } else {
                    print("No text result found.")
                }
            case .failure(let error):
                print("Error during chat completion: \(error)")
            }
        }
    }
}
```

&nbsp;

일단 저는 `model` 로 `gpt3_5Turbo` 를 사용해줬구요, 

`messages` 에는 `.system` 으로는 아까 작성한 **script**를,

`.user` 에는 **사용자가 작성한 메세지**를 넣어줍니다.

&nbsp;

그리고... 요청을 보내면 **응답을 비동기적으로** 기다리게 되는데요..

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/04c88a82-22e5-4cb0-81d2-fd99cbc839a7" width = 700>

&nbsp;

**백그라운드**에서 응답이 올 때까지 기다리는데?? 

`result` 가 딱 도착하면 **클로저 안에 있는 내용들 (화면에 메세지 띄워주기, 에러 처리)** 을 수행합니다.

&nbsp;

여기서 잠깐.. 한 가지 문제가 잇어요 🤔

&nbsp;

문제는... Swift가 **UI 관련 모든 작업들**을 `Main Thread` 에서 처리한다는 건데요!

Swift는 **여러 스레드들**이 동시에 함수나 연산자에 접근을 해도 **안정적으로~** 데이터 손실이 일어나지 않는 

`Thread-Safe (스레드 안정성)` 을 강조하고,

여러 작업이 **동시에 실행**되는 `동시성 프로그래밍 (Concurrency)` 관련 기능들을 제공하는데요!!

&nbsp;

💻 ***swift: 흠 ... UI 작업은 너무너무 (프레임워크가) 거대해서.. 백그라운드에서 실행하면 개별론데?***

&nbsp;

네..ㅋ 

`UIKit` 자체가 너무너무 커서, 안정성 추구를 위해 **Main Thread에서만** 사용하게 합니다 ㅋ

&nbsp;

그래서!! 지금 **백그라운드**에서!! 응답을 기다리고 있었다구 했잖아요? 

화면에 메세지를 띄워주려면 **UI 작업**을 해야하는데?

어? 안되는데? 😱
 
&nbsp;

그래서 ㅋ 

`DispatchQueue.main.async` 로 **메인에서 UI가 업데이트** 될 수 있도록 감싸준 겁니다 ㅋ

&nbsp;

😋 아~~ 그렇구나

&nbsp;

무튼.. 그렇구요

**요청을 보낼 때**와 **응답이 도착했을 때** 호출한 `setTypingIndicator` 는요...

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/1a3eca04-7cd2-44ea-bda9-21ae9f5583ad" width = 100>

&nbsp;

상대방이 메세지 보낼 때 도로록 도로록 뜨는 로딩 있잖아요..?

그겁니다 ㅋ

&nbsp;

📌 **TypingIndicator**

```swift
func setTypingIndicator(isHidden: Bool) {
    self.setTypingIndicatorViewHidden(isHidden, animated: true)
}
```
&nbsp;

***

### ✅ TestFlight 배포

&nbsp;

휴!! 여기까지 해서 앱은 다 만들었는데 

동생이 저랑 멀리 떨어져 살아서 그냥 **TestFlight 배포**까지 했습니닷 ㅋ 😔

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/cdd87a14-d42a-444d-b309-9b59e1d2682e">

`Bundle Identifier` 랑 `Version` 다 확인해주고~

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/ca13681d-2543-45b6-b58a-dc024ee3806f">

`Edit Scheme...` 눌러주세용

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/890ccfbd-4689-4619-bb9f-5b4fe016c146">

요런 창이 뜨는데, `Release` 로 설정해주세요!

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/7089405b-6a07-43ea-b9ec-59bb9c4c6d3f">

그리고 `Simulator` 를 `Any iOS Device` 로 설정해주세욥

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/a3b162e7-94fc-4e28-bed7-01b1350a1a77">

이후 `Product` → `Archive` 를 누르면

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/27061653-93c2-4fc3-aae5-014f4b920dd8">

배포할 버전을 선택해 `Distribute App` 을 진행합니당 

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/1de8778c-97d1-4c39-a574-eab5513ffa1c">

마지막으로 `TestFlight & App Store` 를 선택해 `distribute` 하면~~

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/fd7c2c2b-2806-4918-9153-85dbf2dfbc79">

도로롱~~🧚🏻

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/352a15c3-6199-48b0-bbe3-8d54dcdbc3e0">

`App Store Connect` 에 이렇게 제 앱이 올라갑니다!

&nbsp;

🚨 근데 문제가 발생하면.. 업로드 안되고 중단될 수 있는데요?!

제가 맨날 뜨는 에러는 **iPad 관련** 에러예욥

&nbsp;

> Asset validation failed Missing required icon file. The bundle does not contain an app icon for iPad of exactly '152x152' pixels, in .png format for iOS versions >= 10.0.

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/6e1c636a-5fde-4cd6-b2a2-44c4aaf673c2" width = 600>

이거 그냥 `Target` → `General` → `Support Desination` 에서 **iPad랑 Mac** 없애주면 됩니다 😅

&nbsp;

자 그럼~~ 테스트할 사용자를 초대해보세요 ㅋ

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/fb07ecd9-763f-421b-9fee-179cb3aac0a9">

`App Store Connect` 의 `사용자 및 액세스` 에 사용자 추가해서!! 

**앱 접근 권한**과 **초대장** 보내시면 됩니다 ㅋ

&nbsp;

***

## 결과물

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/2b65686a-8e8f-476d-b08e-d6cd4818703a" width = 300>

&nbsp;

대만이랑 대화하는 모습입니다 ㅋ 🏀⛹🏻

&nbsp;

***

## 마치며

&nbsp;

자 이렇게 `Swift` 와 `Chat GPT` 를 활용해 **정대만 채팅 앱**을 만들어 봤구요 ㅋ

너무 해보고 싶었던 건데 ㅋㅎ 만들어서 즐겁습니다~

&nbsp;

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/f42f38f1-7bbc-4548-ab5c-8617dc2bbf1a" width = 300>

<img src = "https://github.com/dlwogus0128/JDMTalk-iOS/assets/79050615/bcbc10a1-b3f3-4b6c-b389-a6a1cbb658ec" width = 500>
<center>(귀여운 내 덩생ㅋ)</center>

&nbsp;

동생이 잘 사용해주고 있답니다!!!

&nbsp;

혹시 틀린 코드나 더 나은 방법이 있으면 댓글로 알려주세요 😎

그럼 안녕~~

&nbsp;

[🏅 전체 코드 살펴보기](https://github.com/dlwogus0128/JDMTalk-iOS)

[💻 참고 자료: MessageKit](https://github.com/MessageKit/MessageKit)

[💻 참고 자료: InputBarAccessoryView](https://github.com/nathantannar4/InputBarAccessoryView)

[💻 참고 자료: OpenAI](https://github.com/MacPaw/OpenAI)

[🌿 참고 자료](https://ios-development.tistory.com/762)