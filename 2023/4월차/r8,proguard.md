**R8, Proguard로 앱 경량화**
=============
## **왜 경량화 하는가?**
APK 파일에는 실행가능한 바이트코드가 DEX(Dalvik Executable) 형태로 저장되어 있다. DEX파일에는 앱에서 실행되는 Android Framework메서드, 개발자가 직접 작성한 메서드 등의 목록이 저장되어 있다.   

그런데 하나의 DEX파일에는 메서드가 최대 65536개까지만 포함될 수 있다. 이 한도를 넘으면 DEX파일을 여러개 작성해야 한다. 그런데 원칙적으로 DEX파일은 하나만 존재해야 하며, DEX파일이 여러개 존재하면 앱의 성능이 크게 나빠질 수 있다.   

이런 이유로 APK에서 불필요한 메서드를 최대한 제거해야 한다.   

## **R8, Proguard**
R8컴파일러는 Java 바이트코드를 최적화하여 DEX포멧으로 저장한다. 사용되지 않는 메서드, 리소스 등을 제거햐여 DEX를 경량화하고, 난독화 등의 기능도 제공한다.   
AGP(Android Gradle Plugin)3.4.0 이상에서는 Proguard 대신 R8이 기본으로 사용된다.

Proguard또한 불필요한 메서드를 제거하여 DEX를 경량화 하고 코드 난독화를 통해 디컴파일시 소스코드가 노출되는 것을 방지할 수 있다.

## **R8 vs. Proguard**
Proguard는 앱을 Java로 개발하던때에 쓰던 것이기 때문에 Kotlin을 주로 사용하는 지금 상황에는 부족한 점이 있다. R8은 최근에 개발된 컴파일러답게 Kotlin컴파일러가 내놓은 바이트코드를 더 잘 최적화 한다.

![pic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbaBx5w%2FbtrGLy9AvNg%2FW6MjN1GHEzabBJs76AULEK%2Fimg.png)
proguard   
![pic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcPGQVf%2FbtrGM386XAb%2FVgKEWXDe7tCkAnWucBKdW0%2Fimg.png)
R8   

R8은 Java 바이트 코드를 바로 DEX로 바꾸기 때문에 더 빠르고, 경량화 성능도 더 좋다.   
따라서 R8이 절대적으로 더 좋고, 하위 호환성도 갖추고 있어 proguard-rules 파일을 R8이 그대로 이용할 수 있다.   

## **경량화 과정**
축소단계는 크게 코드 축소와 리소스 축소로 나뉜다.   

코드 축소는 런타임에 필요하지 않은 코드를 제거하는 과정이다. 외부 라이브러리 중 사용되지 않는 부분을 제거할 수 있다. 라이브러리의 개수는 많지만 사용하는 부분이 매우 적을 때 큰 효과를 볼 수 있다.   

코드 축소는 마치 트리의 탐색과 유사하게 진행된다. 우선 앱의 동작이 시작되는 지점(entry point)를 찾는다. Activity나 Service 등이 시작점이 될 수 있다. R8은 시작점으로부터 출발하여 앱에서 사용되는 코드를 탐색하고, 사용된 코드를 엮어 트리를 만든다.   

모든 시작점에 대해 트리를 완성했다면, 어떠한 트리에도 포함되지 않는 코드를 불필요한 코드로 판정하여 삭제한다.   

특정한 코드가 삭제되지 않게 지정할 수도 있다. proguard-rules 파일을 수정하면 된다. 특히 JNI(Java Native Interface)나 런타임에 동적으로 실행되는 코드는 실행 여부와 상관없이 삭제될 수 있기 때문에 keep 옵션으로 보존해야 한다.
```
-keep class Myclass
```
또는 AndroidX Annotations 라이브러리의 @Keep 어노테이션을 적용해도 된다. 이 어노테이션은 R8을 활성화 해야지만 적용할 수 있다.   

코드 축소 과정이 완료되면 리소스 축소 과정이 진행된다.   
리소스 축소 과정을 수행하려면 build.gradle 에서 shrinkResources를 true로 설정해야 한다.
```gradle
android {
    ...
    buildTypes{
        release {
            shrinkResources true
            minifyEnabled true
            proguardFiles
                getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

## **난독화**
난독화는 코드를 읽기 힘들게 바꾸는 것이다. 예를 들어 androidx.compose.ui.Row를 a.b.c.d로 바꾸면 읽기 어려워진다. 이렇게 클래스나 변수의 이름을 바꾸고, 의미 없는 로직을 섞어 코드를 읽기 어렵게 만들 수 있다.   

특히 JVM(Java Vertual Machine)계열 언어에서는 난독화가 매우 중요하다. 컴파일 결과로 나오는 바이트코드를 디컴파일하면 원본코드를 대부분 복원할 수 있기 때문이다. 난독화를 이용하면 정적 분석을 어느정도 방어할 수 있다.   

긴 클래스 이름을 짧게 바꾸기 때문에 DEX파일의 용량이 줄어드는 효과도 있다.

하지만 바이트코드가 난독화 되기 때문에 디버깅이 어려워 진다. proguard-rules 파일에 다음 옵션을 추가하면 소스파일의 이름과 line number 정보가 별도의 mapping파일로 보존되고, stack tree의 가독성을 방어할 수 있다.
```
-keepattributes LineNumberTable, SourceFile
-renamesourcefileattribute SourceFile
```
## **최적화**
최적화를 적용하면 앱의 사이즈를 더 줄일 수 있다.  
- 절대로 실행되지 않는 else문을 제거한다.
- 어떤 메서드가 한 곳에서만 호출된다면, 함수의 본문을 코드에 직접 붙여넣고 해당 함수를 제거한다.

R8을 활성화 하면 최적화 과정은 반드시 수행된다. 옵션으로 끄려고 해도 무시된다.   

## **Proguard 사용하기**

1. 빌드 타입을 나눠준다.   
build.gradle   
```gradle
buildTypes {
    debug{
        // Proguard 비활성화
        minifyEnabled false
        // 기본 Proguard 설정
        proguardFiles gerDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }

    release {
        // Proguard 활성화
        minifyEnabled true
        // 기본 Proguard 설정
        proguardFiles gerDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
}
```
2. Proguard 활성화 및 debug.pro 설정
```gradle
buildTypes {
    debug{
        // Proguard 비활성화
        minifyEnabled false
        // 기본 Proguard 설정
        proguardFiles gerDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        // 프로젝트에 필요한 Proguard 설정
        proguardFile 'proguard-rules.pro'
    }

    release {
        // Proguard 활성화
        minifyEnabled true
        // 기본 Proguard 설정
        proguardFiles gerDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        // 프로젝트에 필요한 Proguard 설정
        proguardFile 'proguard-rules.pro'
    }
}
```

3. 추가적인 Proguard 설정 방법은 
proguard-rules.pro
```
# Add project specific ProGuard rules here.
# You can control the set of applied configuration files using the
# proguardFiles setting in build.gradle.
#
# For more details, see
#   http://developer.android.com/guide/developing/tools/proguard.html

# If your project uses WebView with JS, uncomment the following
# and specify the fully qualified class name to the JavaScript interface
# class:
#-keepclassmembers class fqcn.of.javascript.interface.for.webview {
#   public *;
#}

# Uncomment this to preserve the line number information for
# debugging stack traces.
#-keepattributes SourceFile,LineNumberTable

# If you keep the line number information, uncomment this to
# hide the original source file name.
#-renamesourcefileattribute SourceFile

// 이곳에 원하는 방식을 작성해주면 된다.
-keep class Myclass
```

4. Proguard 옵션

-keepattribute SourceFile, LineNumberTable : 소스파일의 라인을 섞지 않는 옵션 (stacktrace를 통해 어느 라인에서 오류가 난 것인지 확인)   

-renamesourcefileattribute SourceFile : 소스 파일 변수 명 바꾸는 옵션 (보통 라이브러리는 딱히 난독화 할 필요없을 때 이렇게 적어준다.)   

-keep class 라이브러리패키지명.** {*;} : 딱히 난독화 할 필요없을 때 이렇게 적어준다.   

-ignorewarnings : warning 무시   

-dontwarn 패키지명.** : 지정한 패키지의 warning 무시

-dontoptimize : 난독화 하지 않는다.
-dontobfuscate : 최적화 하지 않는다.
-keepresourcexmlattributenames menifest/** : manifest를 난독화 하지 않는다.   

## **R8 사용하기**
1. gradle.properties 수정
```
android.enableR8=true
android.enableR8.fullMode=true
```

2. build.gradle 수정
```gradle
buildTypes {
        debug {
            debuggable true
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        release {
            debuggable false
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
```

R8을 사용할 때는 @Keep 어노테이션을 사용할 수 있습니다.