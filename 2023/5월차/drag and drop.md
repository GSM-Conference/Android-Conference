**Android Drag and Drop**
==========

Android 드래그 앤 드롭 프레임워크를 사용하면 대화형 드래그 앤 드롭 기능을 앱에 추가할 수 있다.   
드래그 앤 드롭을 사용하여 사용자는 텍스트, 이미지, 객체(URI로 표현할 수 있는 모든 콘텐츠)를 앱 내의 View간에 또는 멀티 윈도우 모드에서 앱 간에 복사하거나 옮길 수 있습니다.   
![pic](https://developer.android.com/static/images/guide/topics/ui/drag-and-drop-within-app.gif?hl=ko)
![pic](https://developer.android.com/static/images/guide/topics/ui/drag-and-drop-between-apps.gif?hl=ko)   
드래그앤 드랍은 Android7.0(API24)이상 부터 지원한다.   
앱 간의 드래그 앤 드랍에서는 SourceApp과 TargetApp으로 구분되는데, 둘다 데이터를 넘기기 위한 코드와 받기 위한 코드가 있어야 한다.   
(Text 데이터를 EditTextView에 넘기는 경우는 TargetApp에서 따로 처리를 안해도 된다.)   
꼭 데이터를 넘기는 작업이 아닌, UI 작업에도 이용할 수 있다.

## **인 앱 드래그 앤 드랍의 프로세스**
### 드래그   

드래그 할 뷰에 전송 이벤트를 전달하기 위해서는 startDragAndDrop() 메소드를 사용해야 한다.   
(데이터 처리후 onLongClickListener에 startDragAndDrop을 넣는다.)   
startDragAndDrop() 메소드에는 아래 파라미터를 통해 데이터를 넘긴다.   
(아래 데이터를 잘 알아야 적절한 데이터를 보낼 수 있다.)   
- data: ClipData - Bundle, Intent와 같이 데이터를 담아 옮겨주는 객체   
- shadowBuilder: DragShadowBuilder - 드래그 시 보여지는 그림자 이미지.   
- myLocalState: Object - 드래그 앤 드롭 관련 데이터를 가지는 객체이며 드래그 뷰와 드롭 뷰간의 정보 교환에 사용한다.   
- flag: int - 옵션 값. 타 앱으로 드래그가 가능하게 해주는 설정도 여기를 통해서 한다.   
드래그 하여 옮겨지는 데이터인 ClipData의 내부는 아래와 같다.
```java
public class ClipData implements Parcelable {
    final ClipDescription mClipDescription; // 담은 데이터를 설명하는 메타 데이터

    final Bitmap mIcon;

    final ArrayList<Item> mItems; // 실제 이동하는 데이터
}
```
여기서 실제 이동하는 데이터인 mItems안에 코드는 아래와 같다.
```java
public static class Item {
    final CharSequence mText; // 문자열
    final String mHtmlText; // HTML데이터
    final Intent mIntent; // Intent 데이터
    Uri mUri; // Uri 데이터
}
```
여기서 중요한 데이터는 Uri 데이터인데, Uri이기 때문에 http: 형식일 수 있지만, content: 로 보내기도 함. Uri를 통해서 공유하면 파일이 크거나 복잡한 파일도 공유가 가능하다.(이미지, 영상)   

이후 데이터를 설명해주는 ClipDescription 에는 아래 데이터를 담는다.
```java
public class ClipDescription implements Parcelable {
    final CharSequence mLabel; // Clip에 대한 설명.
    private final ArrayList<String> mMimeTypes; // Clip의 형식 (jpg, png, Html등)
    private PersistableBundle mExtras; // Clip에 대한 확장 데이터
    private long mTimeStamp;
}
```
여기서 mMimeType에는 기본적으로 제공되는 타입들이 있다. 이를 활용해도 되지만 커스텀으로 드래그앤 드랍을 만들려는 경우, 자신이 만들어도 된다.
```java
public static final String MIMETYPE_TEXT_PLAIN = "text/plain
";
public static final String MIMETYPE_TEXT_HTML = "text/html";
public static final String MIMETYPE_TEXT_URILIST = "text/uri-list";
public static final String MIMETYPE_TEXT_INTENT = "text/vnd.android.intent";
public static final String MIMETYPE_UNKNOWN = "application/octet-stream";
```
이후 drag가 제대로 되었다면 True를 반환, 문제가 생겼으면 false를 반환한다. 