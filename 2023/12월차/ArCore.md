# ARCore
## AR이란?
AR은 증강현실(Augmented Reality)를 얘기하는 것으로 가상현실 우리가 잘 아는 VR의 한 분야이다. AR은 실제로 존재하는 환경에 가상의 사물이나 정보를 합성하여 마치 실제로 존재하는 사물처럼 보이도록하는 컴퓨터 그래픽 기법을 얘기한다.

<br>

## ARCore란?
ARCore는 AR용 Google Play Service로 구글에서 개발한 AR 소프트웨어 개발 키트이다. AR과 관련된 API를 제공하며 최근에 지리 정보 API(Geospatial API)또한 추가되었다. 다른 여타 구글 API와 마찬가지로 Google Cloud를 통해 API를 관리할 수 있다.


### 지원기기

ARCore를 이용하기 위해선 OpenGL ES가 필요하고 Android7.0(에뮬레이터 8.1이상)이상이 필요하다. 따라서 API 수준은 필수로 ARCore를 사용한다면 24이상, 선택적인 ARCore라면 19이상으로 제한을 둬야 한다.

**반드시 테스트하기 전에 공식 문서로 가서 테스트 기기가 지원하는지 확인해야 한다.** 안 그러면 테스트 자체가 불가능할 수 있다. ARCore도 비교적 고사양 스마트폰을 필요로 해서 지원하는 기기가 일반적인 API보다 훨씬 적다. ARCore를 지원해도 AR 관련 API 중 특정 하드웨어 장치가 필요한 (특히 depth API) API가 존재한다.

<br>
<br>
<br>

## ARCore 사용법
---
```gradle
//ARCore 라이브러리
implementation 'com.google.ar:core:1.37.0'
//obj 파일을 읽기 위한 라이브러리
implementation 'de.javagl:obj:0.2.1'
```

<br>

```xml
//카메라 권한
<uses-permission android:name="android.permission.CAMERA"/>
  <!-- Limits app visibility in the Google Play Store to ARCore supported devices
       (https://developers.google.com/ar/devices). -->
  <uses-feature android:name="android.hardware.camera.ar" android:required="true"/>
  <uses-feature android:glEsVersion="0x00020000" android:required="true" />
  <uses-feature
      android:name="android.hardware.camera"
      android:required="true" />

<application
...
...
...>
	<activity
		...
		.../>
	//ARCore 사용 명시 및 ARCore 사용이 필수(required)인지 선택(optional)인지 명시 
	<meta-data android:name="com.google.ar.core" android:value="required" />
</application>
```

ARCore를 기본 제공 라이브러리로 다루는 예제로 Open GL를 다루는 함수를 통해 그래픽 렌더링과 ARCore를 다루면서 발생하는 세션의 LifeCycle을 다룬다. 예제의 코드는 복잡하고 관련 지식이 없다면 파악하기 어렵다는 특징을 가지고 있다.

참고로 ARCore는 XML과 Compose 모두 상호호환이 가능하다고 하다.


<br>
<br>
<br>

## SceneView 라이브러리

https://github.com/SceneView/sceneview-android

Open GL없이 ARCore 앱을 쉽게 빌드할 수 있도록 만들어진 3D 프레임워크로 구글이 관리한다. 기본적인 상황에서 ARCore를 다루는 것보다 편의성이 높고 AR를 다루는데 필수적으로 필요한 요소들을 자동으로 import해주며 권한 설정을 해준다는 특징이 있다.

GitHub의 README가 공식 문서라고 봐도 됩니다.

<br>
<br>
<br>

## assets 디렉토리 추가
### 이유
assets에 저장된 파일은 읽기만 가능하므로 보안에서 일정 부분 유리하다. (외부나 내부에서 쓰기를 당하지 않음)
기본적으로 파일을 압축해 저장하기 때문에 데이터를 읽을 때 압축하는 과정이 없다.
읽기 속도가 빠르고 CPU 부하가 적음
압축 제외 설정
android{
...
...
aaptOptions{
	noCompress '확장자명'
	}

}
### 추가하는 법
안드로이드 스튜디오의 Project에서 app을 우클릭하여 new→Folder→Assets Folder 선택
저장하는 데이터를 분류하기 위한 패키지 생성 후 데이터 분류하여 저장

<br>
<br>
<br>

## 3D 모델 다운로드시 주의사항
저작권 위반을 피하기 위해선 모든 사이트에서 저작물에 대한 라이센스를 주의해야 한다.

라이센스별로 제한된 사항을 확인하기 힘들 경우 CC0 라이센스(저작권 없는 무료 저작물)를 가진 저작물만 사용해야한다.