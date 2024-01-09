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

우선, 미디어 중에서도 카메라로 비디오 정보를 입력 받아오기 위한 기본적인 내용을 세팅해줍니다.
카메라는 전면인지 후면인지, 기기 타입은 무엇인지 등등에 관련된 내용입니다.

이렇게 설정한 카메라로 비디오를 입력 받습니다. 이 때, Vision의 얼굴 인식 기능을 활용해 사용자의 얼굴을 인식하고, 
인식한 레이어의 크기에 맞게 원하는 사진을   



- 흐름
    - 카메라 권한 허용 받아오기
    - 전면 카메라 설정 및 비디오 입력 받아오기
    - 입력 받은 비디오를 출력하기
    - 버튼이 눌렸을 경우, 해당 화면을 캡처해 image로 렌더링하여 갤러리에 저장

&nbsp;

***

## 포차코 Face Filter 만들기

그럼 제가 작업했던 순서대로 서술해볼게용 🥹

&nbsp;

### ✅ UI 구현

***

### ✅ 카메라 액세스 권한 허용

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/d81b93ab-ed6d-47f6-ad66-09b1e06d1047" alt = "Photo Library Usage Description">

Info.plist에서 권한 추가

카메라를 사용하기 위해서는 사용자에게 액세스 권한 허용을 받아야 한다. Info.plist에서 추가해주면 되며, 권한 허용을 받을 때 띄울 문구를 Value에 넣을 수 있다. (앱을 배포할 경우, 권한 허용을 받는 이유를 명확히 적지 않으면 심사 리젝 사유가 될 수 있으므로 주의)

액세스 권한을 받았는지, 받지 않았는지 확인

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/7b09aed6-319f-4399-9d59-ee0a926ff486" width = 200 alt = "Requesting authorization to capture and save media">

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

공식 문서에 권한 허용을 받는 코드가 친절하게 적혀있어 그대로 가져왔다. [참고 링크](https://developer.apple.com/documentation/avfoundation/capture_setup/requesting_authorization_to_capture_and_save_media) 

카메라에 액세스 할 수 있는 권한을 받았는지, 받지 않았는지 비동기적으로 확인하는 프로퍼티를 선언하고 이를 setUpCaptureSession()에서 실행한다.

***

### ✅ 카메라 시작하기

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/43c7d245-b908-47ee-a57d-28bf6ec276c5" alt = "AVFoundation">

카메라를 사용하기 위해 오디오, 비디오 관련 기능들을 제공하는 프레임워크인 AVFoundation을 사용하려 한다. 공식 문서에 나와 있는 capture architecture를 살펴보면 input → output 으로 이루어지고, 이 둘을 관리해주는 session이 있음을 알 수 있는데, 차근차근 따라가면 된다!

📌 session: 비디오의 입력과 출력을 관리하는 세션 선언

```swift
private let captureSession = AVCaptureSession() // 비디오의 입력과 출력을 관리하는 세션
```
고냥~~ input과 output을 관리해주는 captureSession을 하나 선언했다!

그러면 입력을 받아야겠지!

📌 input: 카메라로부터 비디오 입력 받기

```swift
private let captureSession = AVCaptureSession() // 비디오의 입력과 출력을 관리하는 세션

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
한 줄씩 살펴보자!

- 먼저 captureSession에게 ‘나 이제 촬영할 거라 config 시작한다~’ 알린다 ㅋ
- 그리고 내가 원하는 카메라 디바이스 설정을 videoDevice에 설정해둔다!
    
    <img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/c816630a-7828-49b8-b8c1-a388c2d65267" alt = "default">
    
    - deviceType은 .builtInWideAngleCamera (말 그대로 wide-angle camera)
    - mediaType은 .video (비디오를 쓸 거니까)
    - position은 .front (전면 카메라를 쓸 거니까)
- 그 설정한 디바이스로 input 받는 걸 try 해볼 거심 ㅇㅇ 만약에 input이 된다면?
- 그걸 captureSession에 넣어줄 거다
- 그러면 입력 받은 애를 화면에 출력해보자 → setUpPreviewLayer() 호출

📌 input: 입력 받은 비디오 화면에 프리뷰 띄우기

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

아웅 길다 이제 previewLayer를 세팅해보자

- previewLayer.frame은 화면에 대충 어떻게 위치할지? 설정한 건데, 정사각형 모양이고 화면 정중앙에 위치한다는 거다
- 그리고 이 previewLayer를 view에 넣는다
- 아까 input에 넣을 videoDevice를 설정한 것처럼, previewLayer에 띄울 videoDataOutput을 설정해보자
    - .videoSettings에서 출력 비디오의 형식을 설정한다. 나는 32bit BGRA 형식으로 설정한 건데, 일반적으로 이걸 사용한다하더라~
    - 비디오 출력에 관련한 sampleBuffer를 처리하기 위한 delegate를 설정한다. 이게 머냐고?? .. 일단 delegate는 이 샘플 버퍼와 관련된 메서드를 호출하게 하는 것이다. queue는 모든 delegate 메서드들이 큐 안에서 호출되기 때문인데! 대강 비동기 처리를 메모리에서 효율적으로 하도록~ 머 하는 거다 그래서 비동기 처리용 큐인 DisaptchQueue를 하나 넣은 거)
        
- 자 output을 설정했으니까 captureSession에 집어 넣는다!
- videoConnection 설정 부분은 .portrait로 해서 세로 방향으로 비디오를 캡처하기 위해서다
- captureSession에 output을 설정했으니 commit 한다
- 자!! 다 설정했다 그럼 captureSession을 실행하자~~ (비동기 처리)

***

### ✅ 얼굴을 감지하자

> The Vision framework performs face and face landmark detection, text detection, barcode recognition, image registration, and general feature tracking. Vision also allows the use of custom Core ML models for tasks like classification or object detection.
>

swift 프레임워크 중 Vision은 얼굴 눈코입, 텍스트, 바코드를 감지하고, 이미지 정합이나 요소 트래킹 같은 기술을 !!! 제공한다. (엄청나~~) 

나는 landmark까지는 아니고 (해봤는데 사람 눈코입 위에 캐릭터 눈코입이 있으니까 기괴하다 ㅜ.ㅜ) 그냥 얼굴 감지해서 포차코를 그 위에 띄워주는 필터를 만들었다

```swift
extension PochacoFilterVC: AVCaptureVideoDataOutputSampleBufferDelegate {
    func captureOutput(_ output: AVCaptureOutput, didOutput sampleBuffer: CMSampleBuffer, from connection: AVCaptureConnection) {
        
        guard let imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer) else { return }  // CMSampleBuffer에서 이미지 버퍼를 가져옴
        
        let faceDetectionRequest = VNDetectFaceLandmarksRequest(completionHandler: { [weak self] (request: VNRequest, error: Error?) in
            guard let self = self else { return }  // self가 nil이면 이미 해제된 상태이므로 더 이상 작업을 수행하지 않음
            
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

- AVCaptureVideoDataOutputSampleBufferDelegate 프로토콜 채택:
    - 이게 몬대?
        
    - 공식 문서에 따르면, 이 프로토콜은 AVCaptureVideoDataOutput에서 video sample buffers들을 받아 오고 지연된 샘플들이 누락되면 알림을 받을 수 있는? 인터페이스를 정의한다고 한다..
    - 간단히 말하자면 video data의 output에서 sample buffer를 받아오는데, 이와 관련된 기능들을 관리하는 프로토콜이다.
- 그럼 Sample Buffer는 먼대?
    
    - Sample Buffer는 media sample data를 미디어 파이프라인을 통해 이동시키는 Core Foundataion 객체이다…?
    - 미디어 파이프라인은 미디어 데이터를 처리하고, 전달하는 구조, 과정을 가르키는 용어
    - 미디어 샘플을 담아두고 전달해줌!!
- 이 프로토콜의 captureOutput 함수로 비디오 프레임에 접근할 수 있음
    - captureOutput: capture output 객체
    - sampleBuffer: 비디오 프레임을 갖고 있는 샘플 버퍼
    - connection: 비디오를 받은 connection
- 샘플 버퍼에는 미디어 관련된 것들이 등등이 들어있고 그중에서 이미지 관련 버퍼를 추출할 수 있음
- VNDetectFaceLandmarksRequest: 기본적으로, 얼굴 감지 요청이 오면 입력 이미지에서 얼굴을 찾고, 얼굴을 분석해 특징을 감지함 → 결국 얼굴 감지를 요청하는 함수!
- 감지된 얼굴에 대한 처리를 메인 스레드에서 수행, 현재 표시된 얼굴들의 레이어를 제거
    - 새로운 이미지를 업데이트하거나 그릴 때, 이전에 그려진 얼굴 이미지를 제거, 새로운 얼굴을 그림
    - 얼굴을 감지해서 observations에 가져다 넣고, 이를 들고 handleFaceDetectionObservations(observations: observations)에 전달해 복작복작 처리
- VNImageRequestHandler에 얼굴 감지 요청 수행
    - Vision은 얘를 통해 요청을 감지하고 처리함!!
    - 이미지 버퍼, 이미지 방향 설정
    - 그 결과를 받아오고 현재 샘플 버퍼를 저장함

***

### ✅ 얼굴에 이미지를 넣어보자 ㅋ

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

- 감지된 얼굴들을 가지고 속샥 처리하는 부분~
- observation.boundingBox: 얼굴이 감지된 경계 상자, layerRectConverted로 화면에 보이는 좌표로 변환시켜
    - CALayer로 얼굴을 나타내는 faceLayer 생성
        - bounds: 바운딩 박스 크기랑 똑같이
        - position: 얼굴의 중심점도 똑같이~~
    - 이미지를 포함한 faceImageLayer 추가하기 → 얘를 faceLayer의 서브 레이어로 넣음으로써 위에 얹음
    - faceLayers에 넣어주고~
    - previewLayer에 넣어서 화면에 표시될 수 있게 함!!

***

### ✅ 촬영 버튼을 누르면! 사진을 저장하자

```swift
self.takePictureButton.addTarget(self, action: #selector(takePictureButtonDidTap), for: .touchUpInside)

@objc private func takePictureButtonDidTap() {
    savePhoto()
}
```

- 버튼에 addTarget 추가
- 버튼을 누르면 savePhoto 함수 호출

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

- 현재 프레임을 가지고 UIImage를 생성함
    - currentSampleBuffer에에서 imageBuffer를 가져와 이를 CIImage로 변환, 이를 UIImage로 다시 생성
- UIGraphicsImageRenderer로 새 이미지 렌더링
    - 렌더링하면서 비율 유지, 화면에 보이는 부분을 잘라내 새로운 이미지 생성
- 이미지 저장, 저장이 완료되면 saveImage 호출됨

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

이미지가 성공적으로 저장될 경우 살짝쿵 토스트 메세지 띄워줌 (생략해도 됨!!1)

***

### ✅ 동작 영상


***

## 마치며

&nbsp;

<img src = "https://github.com/dlwogus0128/swift-example/assets/79050615/934931cf-82dc-496b-8dab-fb565dd2beb2" width = 300 alt = "귀여운 봄이">

<center>봄이도 포차코 필터를 좋아해~~🤍</center>
