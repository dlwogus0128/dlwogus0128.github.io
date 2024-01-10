---
title: "[iOS - Swift] 봄이도 좋아하는 Vision으로 포차코 페이스 필터 만들기"
date: 2023-01-10 15:00:00 +09:00
categories: [iOS, Swift]
tags: [swift, ios, vision, face filter, face tracking] 
---

## 들어가기 전에

&nbsp;

보정을 해준다거나, 귀여운 필터를 입혀주는 등 

요즘 사람의 얼굴을 인식하는 기능을 활용하는 카메라 앱이 많잖아요?! 😎

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/f51eced6-42d1-48b1-9d5c-d628f1d2ad97" alt = "macOS Photo Booth">
<center>(macOS Photo Booth 필터처럼 말이죵)</center>

&nbsp;

저도 포토부스 필터 참 조아하는데요...

요렇게 내 얼굴을 인식해 필터를 씌우는 기능!을 스위프트로 구현한다면

어떻게 할 수 있을까요? 🤔

&nbsp;

바로바로....

`Vision Framework` 를 사용하면 댑니다 ㅋ 🥹

&nbsp;

오... `Vision` 이 몬대요 .?

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/50031605-67ee-44a2-bb80-3aceb35c5a71" alt = "vision">

&nbsp;

"**컴퓨터 비전 알고리즘**을 적용하여 입력 이미지 및 비디오에서 다양한 작업을 수행"

하는 프레임워크라고 나와있네용

&nbsp;

`컴퓨터 비전(Computer Vision)` 은 말 그대로 **눈에 보이는 것** 👀 인 

이미지나 비디오 등에서 정보를 추출하도록 하는 인공지능 분야인데요!

&nbsp;


이 Vision 프레임워크를 활용하여 **얼굴을 인식**하거나, **사진 속에서 텍스트를 검출**하거나, 

**바코드를 인식**하는 등의 기능들을 구현할 수 있다고 합니다.

카메라 필터에는 **얼굴 인식 기능**을 이용할 수 있겠져?ㅋ

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/69dc1f6c-5669-4701-976c-1e2b45aa3c4a" width = 400 alt = "포차코 페이스 필터 앱">

<center>(버튼을 누르면 갤러리에 사진이 저장돼요)</center>

&nbsp;

그래서 오늘은 이 `Vision` 을 활용해 

**내 얼굴에 포차코 사진을 띄워주는 카메라 만들기!!** 를 소개해보겠습니다~~✨

&nbsp;

***

## 포차코 Face filter 흐름

&nbsp;

개발 흐름은 간단하게 다음과 같습니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/2433c250-36e7-456d-892e-265f64a47719" alt = "face filter 흐름">

&nbsp;

우선, 미디어 중에서도 **카메라로 비디오 정보를 입력 받아오기 위한 기본적인 내용**을 세팅해줍니다.  
카메라는 전면인지 후면인지, 기기 타입은 무엇인지 등등에 관련된 내용입니다.

이렇게 설정한 **카메라로 비디오를 입력** 받습니다. 

이 때, `Vision` 의 얼굴 인식 기능을 활용해 **사용자의 얼굴을 인식**하고,  
인식한 레이어의 크기에 맞게 **원하는 사진** (저는 포차코 ㅋ) 을 얹어줍니다.

그리고 얼굴 위에 사진을 더한 이 **영상을 화면에 출력**합니다.

만약에 **촬영 버튼이 눌렸을 경우**, 화면에 보여지는 **이미지 레이어를 저장**합니다.

&nbsp;

***

## 포차코 Face Filter 만들기

&nbsp;

그럼 포차코 필터를 만들어보까요!!! 🐾

&nbsp;

### ✅ 카메라 액세스 권한 허용

&nbsp;

우선, 카메라를 사용하기 위해서는 사용자에게 **카메라에 액세스 할 수 있는 권한**을 받아야 합니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/cee8096a-143a-4742-878b-a71548640b4e" alt = "Privacy - Camera Usage Description">

`Targets → Info` 에서 아무 줄에서나 **+ 버튼**을 누른 뒤, 

`Privacy - Camera Usage Description` 을 Key 값으로 선택합니다.

Value에는 권한 허용을 받을 때 띄울 문구를 넣을 수 있습니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/7b09aed6-319f-4399-9d59-ee0a926ff486" width = 200 alt = "Requesting authorization to capture and save media">
<center>권한 받을 때 요런 팝업 뜨잖아용!!</center>

&nbsp;

저는 **The app needs to access the Camera to face tracking.** 라고 적었구요!!

앱을 배포할 경우, 저 value에 권한 허용을 받는 이유를 명시하지 않으면

**심사 리젝 사유**가 될 수 있으므로 주의해야 한다고 합니다.

&nbsp;

그 다음, 카메라 기능이 필요한 부분에서 이 **권한이 허용되어 있는지 확인하는 코드**를 작성합니다.

&nbsp;

```swift
var isAuthorized: Bool {
      get async {
          let status = AVCaptureDevice.authorizationStatus(for: .video)
          var isAuthorized = status == .authorized    // 사용자가 이전에 카메라 접근 권한을 가지고 있었는지 아닌지 판단
          
          if status == .notDetermined {   // 만약 접근 권한을 받은 적이 없다면
              isAuthorized = await AVCaptureDevice.requestAccess(for: .video)
          }
          
          return isAuthorized
     }
}

/// 카메라 접근 권한 확인 및 처리
private func setUpCaptureSession() async {
    guard await isAuthorized else { return }
}
```

공식 문서에 [관련 코드](https://developer.apple.com/documentation/avfoundation/capture_setup/requesting_authorization_to_capture_and_save_media)가 있어서 사용했습니다.

&nbsp;

**카메라에 액세스** 할 수 있는 권한을 받았는지, 받지 않았는지를 **비동기적으로 확인**하는 코드입니닷

권한이 없을 경우 `AVCaptureDevice` 의 `requestAccess` 로 비디오 사용을 위한 설정 권한을 가져옵니다!

아까 그 **권한 팝업**을 띄워주는 것이지유~~

&nbsp;

🤔 헉 그럼 `AVCaptureDevice` 는 뭘까요?!

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/74b9c911-8e78-40c0-ae35-724e3dfa3537" alt = "AVCaptureDevice">

&nbsp;

"**카메라**나 **마이크**와 같은 하드웨어 또는 가상 캡처 장치를 나타내는 객체"

라고 나와있는데, 쉽게 말해

**비디오, 오디오**와 같은 AV를 캡처하는 장치들!! 

카메라나 마이크 같은 **장치들을 참조**하는 객체를 의미합니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/2b43a540-1b43-4ab9-8fc3-b791629cde51" alt = "requestAccess">

&nbsp;

`AVCaptureDevice` 의 메소드 중 `requestAccess` 는 

이렇게 장치가 데이터를 캡처할 수 있도록 권한 허용을 접근하는 역할을 합니다.

&nbsp;

***

### ✅ 카메라 시작하기

&nbsp;

카메라를 사용하기에 앞서, **디바이스에서 데이터를 캡처해 출력하기**의 과정을 살펴볼까요?!✨

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/43c7d245-b908-47ee-a57d-28bf6ec276c5" alt = "AVFoundation">

&nbsp;

캡처 아키텍처를 살펴보면 **input**, **output**, **session** 세 가지로 나뉨을 알 수 있는데요.

캡처 디바이스에서 입력값을 받고, 출력값을 내보내는 **input → output** 순서이고,

이 둘을 관리하는 **session**이 있음을 알 수 있습니다.

어렵지 안쵸!!!?!

&nbsp;

그러면 필터 카메라를 만들기 위해 디바이스 입출력을 구현해봅시닷 🥹

&nbsp;

📌 **session: 비디오의 입력과 출력을 관리하는 세션 선언**

&nbsp;

먼저 카메라의 input과 output을 관리해주는 `AVCaptureSession` 의 인스턴스를 선언해줍니다.

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/3f3a39be-f80b-41ab-a27e-b7b5c6619bb9" alt = "AVCaptureSession">


앞서 설명했던 대로 입출력 데이터의 흐름을 관리해주는 객체이구요!

&nbsp;

```swift
private let captureSession = AVCaptureSession() // 비디오의 입력과 출력을 관리하는 세션
```
저는 **captureSession**이라는 이름으로 만들었습니답

그 다음은 카메라로 입력을 받아줘야겠죠?!

&nbsp;

📌 **input: 카메라로부터 비디오 입력 받기**

```swift
/// 카메라로부터 비디오를 입력받기
private func configureVideoCapture() {
    captureSession.beginConfiguration() // AVCaptureSession의 설정 변경 시작
    let videoDevice = AVCaptureDevice.default(.builtInWideAngleCamera,
                                              for: .video,
                                              position: .front)   // 카메라 옵션 설정
    guard let videoDeviceInput = try? AVCaptureDeviceInput(device: videoDevice!),
          captureSession.canAddInput(videoDeviceInput) else { return }  // AVCaptureDeviceInput 생성 시도, 성공 시 세션에 입력 추가할 수 있는지 확인
    captureSession.addInput(videoDeviceInput)   // 세션에 생성된 비디오의 입력을 추가
    setUpPreviewLayer() // 비디오 프리뷰 출력
}
```

앱이 실행되면 바로 호출할 **configureVideoCapture**라는 함수를 만들었습니다.

- `beginConfiguration` 을 통해 세션에게 ‘카메라 config 시작한다~' 알림
- 내가 원하는 디바이스 옵션을 **videoDevice**에 설정
    - `deviceType` 은 **.builtInWideAngleCamera** (말 그대로 wide-angle camera)
    - `mediaType` 은 **.video** (비디오를 쓸 거니까)
    - `position` 은 **.front** (전면 카메라를 쓸 거니까)
- 설정한 디바이스로 **input** 받는 걸 try 해볼 거심 ㅇㅇ 만약에 input이 된다면?
- 그걸 captureSession에 **add** 해 줄 거다
- 그러면 입력 받은 애를 화면에 출력해보자 → **setUpPreviewLayer** 호출

[공식 문서의 코드](https://developer.apple.com/documentation/avfoundation/avcapturesession)와 함께 볼 수 있을 것 같습니답

&nbsp;

다음으로는 화면에 입력값을 프리뷰로 출력해주는 **setUpPreviewLayer** 함수를 만들어볼까요!? 🥹

&nbsp;

📌 **output: 입력 받은 비디오 화면에 프리뷰 띄우기**

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/db2f2260-75ee-425a-b933-d78f83862a0b" alt = "AVCaptureVideoPreviewLayer">

`AVCaptureVideoPreviewLayer` 는 카메라 디바이스로 입력받은 비디오를 보여주는 프리뷰 레이어인데요,

&nbsp;

```swift
private lazy var previewLayer = AVCaptureVideoPreviewLayer(session: self.captureSession).then {    // captureSession의 시각적 출력을 프리뷰
    $0.videoGravity = .resizeAspectFill
}
```
요렇게 **previewLayer**라는 이름으로 선언해 줄 겁니닷

그리고 **setUpPreviewLayer**라는 함수를 만들어서 **previewLayer**에 영상을 출력해볼까욥?!

&nbsp;

```swift
/// 입력받은 비디오 출력
private func setUpPreviewLayer() {
    let width: CGFloat = 300
    let height: CGFloat = 300

    self.previewLayer.frame = CGRect(
        x: (view.bounds.width - width) / 2,
        y: (view.bounds.height - height) / 2,
        width: width,
        height: height
    )
    
    view.layer.addSublayer(previewLayer)    // 현재 뷰의 하위 레이어로 추가
    
    let videoDataOutput = AVCaptureVideoDataOutput()
    videoDataOutput.videoSettings = [(kCVPixelBufferPixelFormatTypeKey as NSString) : NSNumber(value: kCVPixelFormatType_32BGRA)] as [String : Any]    // [Core Viedo에서 사용되는 픽셀 형식 : 32bit BGRA 형식, 일반적으로 사용되는 형식]
    videoDataOutput.setSampleBufferDelegate(self, queue: DispatchQueue(label: "camera queue")) // 비디오 출력에 대한 샘플 버퍼 딜리게이트 설정
    self.captureSession.addOutput(videoDataOutput) // captureSession에 output 추가
    
    // input과 output 간의 connection 생성
    let videoConnection = videoDataOutput.connection(with: .video)
    videoConnection?.videoOrientation = .portrait   // 비디오 방향 세로 설정
    
    self.captureSession.commitConfiguration() // AVCaptureSession의 설정 변경 완료
    // AVCaptureSession를 백그라운드 스레드에서 시작
    DispatchQueue.global().async {
        self.captureSession.startRunning()
    }
}
```
- `previewLayer.frame` 으로 이 레이어가 화면에 어떻게 위치할 지 설정한 건데, 정사각형 모양이고 화면 정중앙에 위치시킴
- 그리고 previewLayer를 view에 **add**
&nbsp;
- 아까 input에 넣을 videoDevice를 설정한 것처럼, previewLayer에 띄울 **videoDataOutput**을 설정
    - `.videoSettings` 에서 출력 비디오의 형식을 설정 (저는 32bit BGRA 형식으로 설정한 건데, 일반적으로 이 형식을 사용한다 합니다.)
    - `.setSampleBufferDelegate` 으로 비디오 출력에 관련한 `sampleBuffer` 를 처리하기 위한 **delegate 설정**

&nbsp;


여기서 잠깐!! `sampleBuffer` 요..? 🥹

`sampleBuffer` 는 캡처된 데이터들을 담아두는 버퍼입니다. 

이 `sampleBuffer` 랑 관련된 메서드들을 호출하기 위해서 **delegate**를 쓰는데요, 

`setSampleBufferDelegate` 를 살펴 보면 `queue` 를 파라미터로 넘겨주도록 되어있습니다.

&nbsp;   

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/50f37a93-526e-4c44-8bb3-7452f7596bf4" alt = "setSampleBufferDelegate">

&nbsp;

delegate에서 호출되는 메서드들을 이 큐 안에서 **비동기적으로 처리**하기 때문인데요. (메모리 효율성 등의 이유로)

연속적으로 **비디오 프레임이 입력**되면, **들어온 순서대로 처리**를 해줘야겠죠!!

따라서 이 queue에는 도착 순서대로 처리하는 직렬큐인 `DispatchQueue` 를 넘겨주는 겁니닷

&nbsp;

이어서 살펴보면...
        
- 이렇게 output을 설정했으니까 `addOutput` 으로 captureSession에 **add**
- **videoConnection**에서 `.videoOrientation` 을 `.portrait` 로 해서 **세로 방향**으로 비디오를 캡처
- output 관련 내용을 `.commitConfiguration`으로 **commit**
- 자!! input과 output 모두 됐으니까 captureSession을 **비동기적**으로 `.startRunning`

&nbsp;

***

### ✅ 얼굴을 감지하자

&nbsp;

input, output, session을 설정해서 이제 **비디오 데이터**가 생겼습니닷 😎

그럼 이제 **얼굴을 인식**해서 스티커를 붙이는 챕터 투로 넘어옵니다 ..

`Vision` 에서는 눈코입 landmark를 트래킹하는 것까지 가능하던데, 눈코입 따로 붙이니까 뭔가 기괴해서 (ㅎㅎ..)

그냥 얼굴 전체를 감지하고 그 위에 스티커를 뙇 붙이려고 해욥!!

&nbsp;

방금 위에서 `sampleBuffer` 에 **캡처된 데이터**들이 들어 있고,

`SampleBufferDelegate` 를 통해 **관련 메서드들을 활용**한다고 했잔아요?!

&nbsp;

그래서 **얼굴 감지**를 `sampleBuffer` 안에 있는 데이터들로 하면 댑니다 ✨

&nbsp;

```swift
private var faceLayers: [CALayer] = []
```
얼굴을 감지한 결과들을 저장해주는 **faceLayers**를 만듭니다.

&nbsp;

```swift
extension PochacoFilterVC: AVCaptureVideoDataOutputSampleBufferDelegate {
    func captureOutput(_ output: AVCaptureOutput, didOutput sampleBuffer: CMSampleBuffer, from connection: AVCaptureConnection) {
        
        guard let imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer) else { return }  // CMSampleBuffer에서 이미지 버퍼를 가져옴
        
        let faceDetectionRequest = VNDetectFaceLandmarksRequest(completionHandler: { [weak self] (request: VNRequest, error: Error?) in
            guard let self = self else { return }
            
            DispatchQueue.main.async {
                self.faceLayers.forEach({ $0.removeFromSuperlayer() })
                
                if let observations = request.results as? [VNFaceObservation] {
                    self.handleFaceDetectionObservations(observations: observations)
                }
            }
        })
        
        let imageRequestHandler = VNImageRequestHandler(cvPixelBuffer: imageBuffer, orientation: .rightMirrored, options: [:])
        
        do {
            try imageRequestHandler.perform([faceDetectionRequest])
            currentSampleBuffer = sampleBuffer
        } catch {
            print(error.localizedDescription)
        }
    }
}
```
- `AVCaptureVideoDataOutputSampleBufferDelegate` 프로토콜 채택
    - `AVCaptureVideoDataOutput`에서 **video sample buffers**들을 받아 오고 관련 인터페이스를 정의하는 프로토콜
- 이 프로토콜의 `captureOutput` 함수로 비디오 프레임에 접근
    - `captureOutput`: capture output 객체
    - `sampleBuffer`: 비디오 프레임을 갖고 있는 샘플 버퍼
    - `connection`: 비디오를 받은 connection
- `CMSampleBufferGetImageBuffer` 로 샘플 버퍼에서 **imageBuffer**를 추출
- `VNDetectFaceLandmarksRequest` 로 **얼굴 감지 요청** 💡
- **감지된 얼굴에 대한 처리**를 메인 스레드에서 수행, 현재 표시된 얼굴들의 레이어를 제거
    - 새로운 이미지를 업데이트하거나 그릴 때, `.removeFromSuperlayer` 로 **이전에 그려진 얼굴 이미지를 제거**  
    (이거 안 하면 이전 레이어가 남아있어서, 에러난 것처럼 스티커 겁나 많이 떠요!!!!)
    - 얼굴을 감지해서 **observations**에 가져다 넣고, 이를 들고 **handleFaceDetectionObservations**에 전달해 처리 💡
- `VNImageRequestHandler` 에서 아까의 **얼굴 감지 요청을 처리** 💡

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/997d9aa4-c4ee-47e0-bf00-dd810c26507d" alt = "face detection">
<center>(내가 보려고 정리한..)</center>

&nbsp;

정리하자면, **얼굴 감지 요청**을 생성하고 그 요청을 받아 **얼굴을 감지**합니다.  
그리고 얻은 결과를 처리하는 (저의 경우 스티커 붙이기) **handleFaceDetectionObservations**를 호출하고,  
이 과정에서 **faceLayers**에 저장된 이전 얼굴 레이어을 제거하고 새로운 레이어을 추가합니다!

&nbsp;

***

### ✅ 얼굴에 이미지를 넣어보자 ㅋ

&nbsp;

이제 감지된 얼굴에 포차코 이미지를 넣을 차례입니닷ㅋ 🫢

얼굴 인식된 결과를 처리할 **handleFaceDetectionObservations** 함수를 만듭니다.

&nbsp;

```swift
/// Face Observation 값들 처리
private func handleFaceDetectionObservations(observations: [VNFaceObservation]) {
    for observation in observations {
        let faceRectConverted = self.previewLayer.layerRectConverted(fromMetadataOutputRect: observation.boundingBox)

        let faceLayer = CALayer()
        faceLayer.bounds = faceRectConverted
        faceLayer.position = CGPoint(x: faceRectConverted.midX, y: faceRectConverted.midY)

        // 얼굴 범위에 이미지 추가하기
        let faceImage = UIImage(named: "pochaco_face_img")
        let faceImageLayer = CALayer()
        faceImageLayer.contents = faceImage?.cgImage
        faceImageLayer.bounds = faceLayer.bounds
        faceImageLayer.position = CGPoint(x: faceLayer.bounds.midX, y: faceLayer.bounds.midY)
        faceLayer.addSublayer(faceImageLayer)

        // preview layer에 face layer 더하기
        self.faceLayers.append(faceLayer)
        self.previewLayer.addSublayer(faceLayer)
    }
}
```

- `.boundingBox` 은 얼굴이 감지된 **경계 상자**로, 이를 `.layerRectConverted` 를 통해 화면에 보이는 **좌표로 변환**
- 감지한 얼굴을 나타내는 **faceLayer** 생성
    - `bounds`: 바운딩 박스 범위랑 똑같이
    - `position`: 위치도 똑같이~~
- **faceLayer**와 `bounds` 가 같은 **faceImageLayer** 추가하기 
    - 원하는 이미지를 `contents` 로 넣어줌 (저는 포차코 이미지)
- 이 **faceImageLayer**를 **faceLayer**에 **add**
- 완성된 **faceLayer**를 **faceLayers**에 넣어주고~
- **previewLayer**에 넣어서 화면에 표시될 수 있게 함!!

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/4b67ecdc-4df2-4683-9d91-d7160961d000" alt = "add image on face">
<center>(내가 보려고 정리한..투투)</center>

&nbsp;

***

### ✅ 촬영 버튼을 누르면! 사진을 저장하자

&nbsp;

그러면 이제 마지막으로 **촬영 버튼이 눌렸을 경우**, **사진을 저장**하도록 구현해보겠습니다.

&nbsp;

```swift
self.takePictureButton.addTarget(self, action: #selector(takePictureButtonDidTap), for: .touchUpInside)

@objc private func takePictureButtonDidTap() {
    savePhoto()
}
```

우선 버튼에 `addTarget` 으로 **버튼이 눌렸을 때**의 액션을 추가해줍니다.

버튼이 눌리면, 사진을 저장하는 **savePhoto** 함수를 호출하도록 할 거예유

&nbsp;

```swift
private func savePhoto() {
    guard let currentSampleBuffer = self.currentSampleBuffer else { return }
    guard let imageBuffer = CMSampleBufferGetImageBuffer(currentSampleBuffer) else { return }
		// 현재 프레임에서 UIImage 생성
    let ciImage = CIImage(cvPixelBuffer: imageBuffer)
    let uiImage = UIImage(ciImage: ciImage)
    
    let rect = previewLayer.bounds
    let renderer = UIGraphicsImageRenderer(size: rect.size)
    let renderedImage = renderer.image { context in
        // 비디오 프레임을 렌더링 (비율을 유지하며 잘라내기)
        let aspectRatio = uiImage.size.width / uiImage.size.height
        let targetWidth = rect.width
        let targetHeight = targetWidth / aspectRatio
        let yOffset = (rect.height - targetHeight) / 2.0
        let targetRect = CGRect(x: 0, y: yOffset, width: targetWidth, height: targetHeight)
        uiImage.draw(in: targetRect)

        // previewLayer를 렌더링
        previewLayer.render(in: context.cgContext)
    }
    
    // 캡쳐한 이미지를 저장
    UIImageWriteToSavedPhotosAlbum(renderedImage, self, #selector(saveImage(_:didFinishSavingWithError:contextInfo:)), nil)
}
```

- **현재 프레임**을 가지고 UIImage를 생성함
    - **currentSampleBuffer**에서 **imageBuffer**를 가져와 이를 `CIImage` 로 변환, 이를 `UIImage` 로 다시 생성    
    (`CMSampleBuffer` 에서 직접 `UIImage` 를 생성하는 방법이 불가능하므로)
- `UIGraphicsImageRenderer` 로 새 이미지 렌더링
    - 렌더링하면서 비율 유지, **previewLayer 화면에 보이는 부분을 잘라내** 새로운 이미지 생성
- 이미지 저장, 저장이 완료되면 **saveImage** 호출됨

&nbsp;

```swift
@objc private func saveImage(_ image: UIImage, didFinishSavingWithError error: NSError?, contextInfo: UnsafeRawPointer) {
    if let error = error {
       print("Error saving image: \(error.localizedDescription)")
   } else {
       print("Image saved successfully!")
       showToast(message: "포차코를 저장했어요")
   }
}
```

이미지가 성공적으로 저장될 경우, 살짝 토스트 메세지를 띄우는 부분까지 구현했습니닷 (안해도 됨)

&nbsp;

***

## 마치며

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/934931cf-82dc-496b-8dab-fb565dd2beb2" width = 300 alt = "귀여운 봄이">

<center>봄이도 포차코 필터를 좋아해~~🤍</center>
