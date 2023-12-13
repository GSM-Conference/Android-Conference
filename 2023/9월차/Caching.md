**LruCache**
============

## What is LruCache
LruCache 객체는 안드로이드에서 캐시를 관리하기 위해 사용하는 메모리 캐시 객체이다. LruCache 객체는 LRU(Least Recent Used) 알고리즘을 사용하는데 간단하게 최근에 조회된 것을 캐시에서 삭제하는 것을 늦추기 위한 객체이다. 즉, 오랫동안 접근되지 않은 메모리가 우선적으로 삭제된다. 그러므로 LruCache를 사용할 때는 자주 참조되는 객체일 수록 빠르게 캐시를 통해 객체에 접근할 수 있다.   
![pic](https://www.charlezz.com/wordpress/wp-content/uploads/2020/10/www.charlezz.com-lrucache-xlrucache10.png.pagespeed.ic_.sw1s9w-d8m.png)   

```java
public class LruCache<K, V> {
    public LruCache(int maxSize){
        ..
    }
    ..
}
```
이 LruCache는 제네릭으로 선언되어 있는데, K가 캐시에 접근하기 위한 키 값이고, V는 캐시에서 가져올 객체의 타입이다. 또한 LruCache는 생성자의 인자로 maxSize를 int값으로 받는다.   

따라서 이 LruCache에서 String을 키 값으로 Bitmap 객체를 가져오려면 다음과 같이 만들 수 있다.   
```kt
class BitmapLruCache(cacheSize: Int) : LruCache<String, Bitmap>(cacheSize) {
    override fun sizeOf(key: String, bitmap: Bitmap): Int {
        return bitmap.bytecount / 1024
    }
}
```

## Create LruCache

위에서 만든 BitmapLruCache는 다음과 같이 초기화 할 수 있다.
```kt
val maxMemory = (Runtime.getRuntime().maxMemory() / 1024).toInt()
val cacheSize = maxMemory / 8
val bitmapCache = BitmapLruCache(cacheSize)
```
위 코드에서는 전체 메모리 사이즈 중 8분의 1을 LruCache사이즈로 잡는다.   

만약 익명 객체로 선언한다면 다음과 같이 만들 수 있다.   
```kt
val maxMemory = (Runtime.getRuntime().maxMemory() / 1024).toInt()
val cacheSize = maxMemory / 8
val bitmapCache = object : LruCache<String, Bitmap>(cacheSize) {
    override fun sizeOf(key: String?, value: Bitmap?): Int {
        return value.byteCount / 1024
    }
}
```

## Write to LruCache
위에서 생성된 객체를 사용해서 LruCache에 파일을 써야 한다면 put메서드를 사용한다. 첫째 인자로는 key(K)값인 String을 넣고 둘째 인자로는 value(V)값인 Bitmap을 넣는다.
```kt
bitmapCache.put(urlString, bitmap)
```

## Read from LruCache
LruCache에서 파일을 가져오기 위해서는 다음과 같이 get 메서드를 써야 한다.
```kt
val bitmap: Bitmap? = bitmapCache.get(urlString)
```
LruCache는 Java로 작성되어 리턴값이 플랫폼 타입이므로 nullable 하도록 설정해야 한다.   

위에서 bitmap 변수가 null이라면 cache에 값이 없는 것이고, 아니면 있는 것이다. 따라서 비트맵이 있을 때의 처리와 없을 때의 처리를 나눠서 한다.
```kt
if (bitmap != null) {
    doWhenBitmapExists(bitmap)
} else {
    doWhenBitmapNotExists(urlString)
}
```

<br>

### LruCache는 비트맵이 아니더라도 다른 데이터 타입의 객체들도 캐싱이 가능하다.