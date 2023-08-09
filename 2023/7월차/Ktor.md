**Ktor**
==========
### Ktor은 웹 앱, HTTP서비스, 모바일 및 브라우저 앱과 같은 연결된 앱을 쉽게 구축할 수 있는 프레임워크다. 최신 연결 앱은 사용자에게 최고의 경험을 제공하기 위해 비동기 방식이어야 하며 코틀린 코루틴은 쉽고 간단하게 이를 수행할 수 있는 기능을 제공한다. Ktor의 목표는 연결된 앱을 위한 종단 간(end-to-end) 멀티플랫폼 프레임워크를 제공하는 것이다.

### Ktor은 개발자가 코틀린으로 비동기 클라이언트 및 서버 앱을 작성할 수 있게 하는 코틀린 프레임 워크이다. 완전히 호환되지는 않지만 JVM, 자바스크립트, 안드로이드, iOS 같은 여러 플랫폼을 대상으로 한다.   

## Ktor의 기능
- 라우팅
- 요청, 응답 처리
- 템플릿(Templating)
- 컨텐츠 협상(negotiation)및 직렬화
- 인증 및 권한 부여
- ions
- HTTP
- 소켓
- 모니터링
- 관리

# **예제**

```gradle
 // Ktor
implementation "io.ktor:ktor-client-core:1.6.3"
implementation "io.ktor:ktor-client-android:1.6.3"
implementation "io.ktor:ktor-client-serialization:1.6.3"
implementation "io.ktor:ktor-client-logging:1.6.3"
implementation "io.ktor:ktor-client-gson:1.6.3"
```

## PostRequest
```kt
import kotlinx.serialization.Serializable

@Serializable
data class PostRequest(
    val body: String,
    val title: String,
    val userId: Int
)
```

## PostResponse
```kt
import kotlinx.serialization.Serializable

@Serializable
data class PostResponse(
    val body: String,
    val title: String,
    val id: Int,
    val userId: Int
)
```

요청, 응답 모두 @Serializable을 사용한다.

## HttpRoutes
```kt
object HttpRoutes {
    const val POSTS = "https://exampleurl.com/post"
}
```

## PostService
```kt
import io.ktor.client.*
import io.ktor.client.engine.android.*
import io.ktor.client.features.json.*
import io.ktor.client.features.json.serializer.*
import io.ktor.client.features.logging.*

interface PostService {
    companion object {
        fun create(): PostService {
            return PostServiceImpl(
                client = HttpClient(Android) {
                    install(Logging) {
                        level = LogLevel.ALL
                    }
                    install(JsonFeature) {
                        serializer = KotlinxSerializer()
                    }
                }
            )
        }
    }

    suspend fun getPosts(): List<PostResponse>

    suspend fun createPost(postRequest: PostRequest): PostResponse?
}
```
companion object로 create()를 만드는 부분이 기존 레트로핏과는 차이가 있다. 대신 좀 더 직관적으로 보인다. 어떤 레벨의 로그까지 표시할 것인지와 JSON직렬화를 저렇게 정의하니 좀 더 직관적으로 보인다. 그 외에는 suspend를 붙인 함수 2개 뿐이다.       
그리고 이 인터페이스를 정의하는 클래스를 하나 생성한다.
## PostServiceImpl
```kt
import android.util.Log
import io.ktor.client.*
import io.ktor.client.features.*
import io.ktor.client.request.*
import io.ktor.http.*

class PostServiceImpl(
    private val client: HttpClient
) : PostService {
    private val TAG = this.javaClass.simpleName

    override suspend fun getPosts() : List<PostResponse> {
        return try {
            client.get {
                url(HttpRoutes.POSTS)
            }
        } catch (e: RedirectResponseException) {
            // 3xx response
            Log.e(TAG, "Error : ${e.response.status.description}")
            emptyList()
        } catch (e: ClientRequestException) {
            // 4xx responses
            Log.e(TAG, "Error : ${e.response.status.description}")
            emptyList()
        } catch(e: ServerResponseException) {
           // 5xx responses
            Log.e(TAG, "Error : ${e.response.status.description}")
            emptyList()
         } catch(e: Exception) {
            Log.e(TAG, "Error : ${e.message}")
            emptyList()
          }
    }
    
    override susepnd fun createPost(postRequest: PostRequest): PostResponse? {
        return try {
            client.post<PostResponse> {
                url(HttpRoutes.POSTS)
                contentType(ContentType.Application.Json)
                body = postRequest
            }
        } catch (e: RedirectResponseException) {
            // 3xx responses
            Log.e(TAG, "Error : ${e.response.status.description}")
            null
        } catch (e: ClientRequestException) {
            // 4xx responses
            Log.e(TAG, "Error : ${e.response.status.description}")
            null
        } catch(e: ServerResponseException) {
            // 5xx responses
            Log.e(TAG, "Error : ${e.response.status.description}")
            null
        } catch(e: Exception) {
            Log.e(TAG, "Error : ${e.message}")
            null
        }
    }
}
```

## MainActivity
```kt
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import com.example.ktorpractice.R
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.Job
import kotlinx.coroutines.launch
import kotlin.coroutines.CoroutineContext

class MainActivity : AppCompatActivity(), CoroutineScope {

    private val TAG = this.javaClass.simpleName
    private lateinit var job: Job
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.IO + job

    private val client = PostsService.create()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        job = Job()
        setContentView(R.layout.activity_main)

        CoroutineScope(Dispatchers.IO).launch {
            val response: List<PostResponse> = client.getPosts()
            for (i in response.indices) {
                Log.e(TAG, "response[i].id : ${response[i].id}")
                Log.e(TAG, "response[i].body : ${response[i].body}")
                Log.e(TAG, "response[i].title : ${response[i].title}")
                Log.e(TAG, "response[i].userId : ${response[i].userId}")
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        job.cancel()
    }

}