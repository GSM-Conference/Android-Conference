**build.gradle**
=============

## .gradle파일이란?
>Gradle파일은 작성한 소스코드와 의존성 라이브러리들을 묶어서 배포할 수 있는 형태로 패키징 해주는 **빌드 배포 도구(Build Toolkit)** 이다.

Gradle은 안드로이드 스튜디오와는 독립적으로 실행된다.   
안드로이드 스튜디오에는 별도의 빌드 툴킷이 존재하지 않으므로, 안드로이드 스튜디오는 빌드 툴킷으로 Gradle을 채택하여 사용한다.

![pic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcEQYfv%2FbtrkJ6MPc7e%2FSPQKwfHI0XXSwYzhmew9y1%2Fimg.png)   

      위 그림에서 점선 부분이 Gradle과 플러그인이 관리하는 부분이다.   
      컴파일러는 소스코드를 덱스파일로 변환하고, 그 외에는 컴파일된 리소스 형태로 변환한다.   
      이를 모아 APK형태로 패키징 한다.
##### **Dex File : Android Runtime에서 궁극적으로 실행되는 코드가 포함된 파일.**      


Gradle파일은 JVM용 동작언어인 Groovy를 기반으로 하기 때문에 Groovy를 사용하는 빌드 로직을 설명, 조작하기 위해 DSL(Domain Specific Language)로 되어있다.   

### **(1) build.gradle(project)**
프로젝트의 모든 모듈에 적용되는 빌드의 구성을 정의한다.   
buildscript블록 안에 프로젝트의 모든 모듈에 적용되는 항목을 정의한다.   
또한, 코드 내 classpath와 같이 build.gradle(module)에서 사용할 플러그인의 환경을 정의한다.   

### **(2) build.gradle(module)**
해당 build.gradle파일이 위치해있는 특정 모듈에 해당빌드의 구성을 정의한다.
``` gradle
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
    id 'com.google.gms.google-services'
}

android {
    compileSdk 32

    defaultConfig {
        applicationId "com.bankaccount.sagi_market"
        minSdk 21
        targetSdk 32
        versionCode 1
        versionName "1.0"
        multiDexEnabled true

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
    
    buildFeatures{
        dataBinding = true
    }
    packagingOptions {
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/ASL2.0'
        exclude 'META-INF/DEPENDENCIES.txt'

        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/NOTICE.txt'
        exclude 'META-INF/notice.txt'
        exclude 'META-INF/license.txt'
        exclude 'META-INF/LGPL2.1'
    }

}

dependencies {

    implementation 'androidx.core:core-ktx:1.7.0'
    implementation 'androidx.appcompat:appcompat:1.5.1'
    implementation 'com.google.android.material:material:1.7.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'com.google.firebase:firebase-firestore-ktx:24.4.1'
    implementation 'com.google.firebase:firebase-auth-ktx:21.1.0'
    implementation 'com.google.firebase:firebase-database-ktx:20.1.0'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.4'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.5.0'
}
```
### **apply plugin : 'com.android.application'**   
- android 블록 사용, android 전용 빌드 옵션 등을 위한 Android Plugin을 gradle에 적용.   

### **android {}**
 - 모든 안드로이드 관련 설정을 구성.   

### **compileSdk**
 - Gradle이 사용해야 하는 Android API 레벨을 지정. 앱은 해당 API레벨 이하에 포함된 API 기능 이용 가능.   

 ### **minSdk**
 - 앱 실행에 필요한 최소 API레벨 지정.

 ### **targetSdk**
 - 앱 테스트에 사용되는 API레벨 지정.

 ### **buildTypes {}**
 - dev, alpha, release 등 빌드 타입 종류 지정.

 ### **dependencies {}**
 - 라이브러리 종속성 추가 시 해당 블록에서 작성


**DSL**
========
#### DSL이란 Domain Specific Language의 약자로 특정 영역(도메인)에 국한해 사용하는 언어이다. 반대개념으로는 General Purpose Language가 있으며 일반적으로 사용되는 C, JAVA, Kotlin등의 프로그래밍 언어들이 이에 해당한다.   
#### 흔히 사용되는 SQL, 정규식이 대표적인 DSL이라고 할 수 있다.   
#### 모든 문제를 풀 수 있는 범용 프로그래밍 언어와는 달리, 특정 영역(도메인)에 초점을 맞추고 기능을 제한함으로써 효율적으로 목표를 달성하기 위한 언어이다.   
#### 이 목표를 위해 DSL은 일반 프로그래밍 언어와는 달리 선언적 방식의 문법을 가지고 있다. 

# **Kotlin DSL**
Kotlin DSL은 가독성 좋고 간략한 코드를 사용하는 코틀린의 언어적인 특성을 사용하여 Gradle스크립팅을 하는 것을 목적으로 하는 DSL이다.   
Kotlin DSL을 사용하기 위해 기존 gradle 파일의 확장자를 **gradle.kts로 변경**하면 된다.   
확장자명 변경 후 다음과 같이 스크립트를 작성하면 Kotlin DSL을 사용할 수 있다.

```
repositories {
    mavenCentral()
}

plugins {
    `kotlin-dsl` // java dsl 설정
}
```
Kotlin DSL의 **장점**은 다음과 같다.
 - Code highlighting
 - 자동완성 지원
 - 오류코드 강조
 - 변수 리팩토링 가능

 - 멀티 모듈 사용시 중복 의존성 선언이 필요가 없다.

 Kotin DSL의 **단점**은 다음과 같다.
 - 빌드 캐시가 Invalidation 되거나 클린 빌드시에 Groovy DSL보다 느리다.

 - 새로운 라이브러리 버전 Inspection 기능 미지원

 - Java8 이상에서 동작