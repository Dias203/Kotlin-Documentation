### 7. Higher-order Function & Lambda
##### 7.1. Hàm là tham số của hàm khác
    -

##### 7.2. Lambda Expression ({x, y -> x + y})
    - Lambda expressions trong Kotlin là các hàm ẩn danh nhỏ gọn, 
    cho phép bạn truyền logic như một tham số hoặc trả về từ một hàm khác.
    
    - Cấu trúc của lambda gồm:
        + Danh sách tham số nằm trong dấu ngoặc nhọn
        + Dấu mũi tên ->
        + Thân hàm (các biểu thức)

```kotlin
// Cú pháp cơ bản
val sum = { a: Int, b: Int -> a + b }
val result = sum(5, 3)  // Kết quả: 8

// Tham số ngầm định 'it' áp dụng cho lambda 1 tham số
val square: (Int) -> Int = { it * it }
println(square(4))  // Kết quả: 16

// Truyền lambda làm tham số => higher-order function
fun processNumber(value: Int, action: (Int) -> Int): Int {
    return action(value)
}

val result = processNumber(5) { it * it }  // Kết quả: 25

// Lambda với nhiều dòng
val calculateArea = { width: Int, height: Int ->
    val area = width * height
    println("Diện tích: $area")
    area
}
```

**Một số ví dụ phổ biến**
###### Với collections
```kotlin
// Filter
val numbers = listOf(1, 2, 3, 4, 5)
val evens = numbers.filter { it % 2 == 0 }  // [2, 4]

// Map
val squared = numbers.map { it * it }  // [1, 4, 9, 16, 25]

// ForEach
numbers.forEach { println(it) }
```

###### Với Android
```
button.setOnClickListener { 
    // Xử lý sự kiện click
    textView.text = "Button clicked"
}
```



##### 7.3. Inline Function
```
* Inline Functions trong Kotlin
    - Inline functions là một tính năng quan trọng trong 
    Kotlin giúp tối ưu hóa hiệu năng khi làm việc với higher-order functions (các hàm nhận hàm khác làm tham số).
* Khái niệm
    - Khi bạn đánh dấu một hàm với từ khóa inline, trình biên dịch sẽ "copy" nội dung của hàm đó và lambda được truyền vào trực tiếp tại nơi gọi hàm, thay vì tạo các đối tượng hàm mới.
```
***Cú pháp cơ bản***
```kotlin
inline fun executeWithLog(action: () -> Unit) {
    println("Executing...")
    action()
    println("Executed.")
}
```

```
Lợi ích chính
* Tối ưu hiệu năng:
    - Không tạo đối tượng lambda mới
    - Tránh chi phí gọi hàm virtual
    - Giảm overhead của bộ sưu tập rác (garbage collection)
* Hỗ trợ non-local returns:
    - Cho phép sử dụng return trong lambda để thoát khỏi hàm gọi
```

```
* Khi nào nên dùng inline functions
    - Hàm nhận lambda làm tham số nhưng rất ngắn
    - Cần sử dụng non-local returns trong lambda
    - Cần sử dụng reified type parameters
    - Hàm được gọi rất thường xuyên và hiệu năng là ưu tiên

* Lưu ý
    - Không nên inline các hàm lớn hoặc phức tạp (tăng kích thước mã)
    - Không tối ưu quá sớm - chỉ dùng inline khi thực sự cần thiết
    - Hầu hết các hàm standard của Kotlin đã được inline khi cần thiết

=> Inline functions là một công cụ mạnh mẽ trong Kotlin để tối ưu hóa hiệu năng, đặc biệt khi làm việc với higher-order functions và lambdas.
```

### 8. Coroutine
**_Khái niệm về Coroutines_**
   * Coroutines là một tính năng trong Kotlin cho phép bạn viết mã bất đồng bộ một cách dễ dàng và trực quan.
   * Coroutines giúp bạn tránh việc sử dụng callback phức tạp và giúp mã nguồn trở nên dễ đọc hơn.

**_Cấu trúc cơ bản của Coroutines_**
   * Coroutine Scope: Xác định phạm vi mà coroutine sẽ hoạt động. Ví dụ: GlobalScope, CoroutineScope, lifecycleScope trong Android. (Chi tiết tại phần 8.4. Scope)
   * Coroutine Builder: Các hàm như launch, async, runBlocking được sử dụng để khởi tạo coroutine.
     * launch: Khởi tạo một coroutine trả về 1 job nhưng job này **KHÔNG CHỨA GIÁ TRỊ**.
     * async: Khởi tạo một coroutine và trả về một Deferred (một giá trị có thể lấy sau).
     * runBlocking: Chạy một coroutine và chặn luồng hiện tại cho đến khi coroutine hoàn thành.

**_Cú pháp cơ bản: Dưới đây là một ví dụ đơn giản về cách sử dụng coroutines trong Kotlin:_**
```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch {
        delay(1000L) // Tạm dừng 1 giây
        println("World!")
    }
    println("Hello,")
}
```
**_Sử dụng async và await:_** Khi bạn cần thực hiện nhiều tác vụ bất đồng bộ và chờ kết quả, bạn có thể sử dụng async và await:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    //val deferred1 = async { doSomething() }
    //val deferred2 = async { doSomethingElse() }
    
    val result1 = deferred1.await()
    val result2 = deferred2.await()
    
    println("Results: $result1, $result2")
}

suspend fun doSomething(): Int {
    delay(1000L)
    return 42
}

suspend fun doSomethingElse(): Int {
    delay(1000L)
    return 24
}
```

*** Quản lý lỗi trong Coroutines:*** Khi làm việc với coroutines, bạn có thể sử dụng try-catch để xử lý lỗi:
```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    try {
        launch {
            throw Exception("Something went wrong!")
        }
    } catch (e: Exception) {
        println("Caught an exception: ${e.message}")
    }
}
```
**6. Sử dụng với Android:** Trong Android, bạn có thể sử dụng lifecycleScope và viewModelScope để quản lý vòng đời của coroutines, giúp tránh rò rỉ bộ nhớ:
```kotlin
class MyViewModel : ViewModel() {
    fun fetchData() {
        viewModelScope.launch {
            val data = fetchDataFromNetwork()
            // Cập nhật UI với dữ liệu
        }
    }
}
```

##### 8.1. Suspend Function (suspend fun fetchData())
```
* Suspend Functions trong Kotlin
    - Suspend functions là một tính năng cốt lõi của Kotlin Coroutines, 
    cho phép viết code bất đồng bộ theo phong cách tuần tự, dễ đọc.

* Khái niệm cơ bản
    - Từ khóa suspend đánh dấu một hàm có thể tạm dừng thực thi mà 
    không chặn thread, và sau đó tiếp tục từ điểm đó khi sẵn sàng.
```
```kotlin
suspend fun fetchData(): Data {
    // Có thể tạm dừng thực thi ở đây
    val result = networkService.getData()
    return result
}
```
```
* Đặc điểm chính
    - Không chặn thread: Thread hiện tại được giải phóng khi hàm suspend tạm dừng
    - Hàm suspension point: Nơi hàm có thể tạm dừng thực thi
    - Tiếp tục thực thi: Khi có kết quả, hàm tiếp tục từ điểm đã tạm dừng
    - Chỉ gọi được từ context bất đồng bộ: Từ coroutine scope hoặc suspend function khác
```
***Cách sử dụng***
###### 1. Gọi từ Coroutine Scope
```kotlin
fun loadData() {
    // Tạo coroutine scope
    lifecycleScope.launch {
        try {
            val data = fetchData() // Gọi suspend function
            showData(data)
        } catch (e: Exception) {
            showError(e)
        }
    }
}
```
###### 2. Kết hợp nhiều hàm suspend
```kotlin
suspend fun getCompleteUserData(userId: String): UserWithPosts {
    // Gọi đồng thời 2 hàm suspend và đợi cả hai hoàn thành
    val (userInfo, userPosts) = coroutineScope {
        val userDeferred = async { fetchUserInfo(userId) }
        val postsDeferred = async { fetchUserPosts(userId) }
        
        Pair(userDeferred.await(), postsDeferred.await())
    }
    
    return UserWithPosts(userInfo, userPosts)
}
```

###### 3. Ví dụ thực tế với API
```kotlin
// Interface API service
interface UserApiService {
    suspend fun getUserById(id: String): User
    suspend fun updateUserProfile(user: User): User
    suspend fun deleteUser(id: String): Boolean
}

// Repository
class UserRepository(private val apiService: UserApiService) {
    suspend fun getUserById(id: String): Result<User> {
        return try {
            Result.success(apiService.getUserById(id))
        } catch (e: Exception) {
            Result.failure(e)
        }
    }
}

// ViewModel
class UserViewModel(private val repository: UserRepository) : ViewModel() {
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> = _user
    
    fun loadUser(id: String) {
        viewModelScope.launch {
//            when (val result = repository.getUserById(id)) {
//                is Result.Success -> _user.value = result.data
//                is Result.Failure -> handleError(result.error)
//            }
        }
    }
}
```

###### 4. Suspend vs Regular Functions
```kotlin
// Regular function - chặn thread hiện tại
fun getData(): Data {
    Thread.sleep(1000) // Chặn thread trong 1 giây
    return Data()
}

// Suspend function - giải phóng thread khi đợi
suspend fun getDataSuspending(): Data {
    delay(1000) // Tạm dừng coroutine trong 1 giây mà không chặn thread
    return Data()
}
```

###### 5. Withcontext - Chuyển Context
```kotlin
suspend fun saveUserToDatabase(user: User) {
    // Chuyển sang Dispatchers.IO để thực hiện thao tác database
    withContext(Dispatchers.IO) {
        database.insert(user)
    }
    // Trở lại context trước đó sau khi hoàn thành
}
```

```
Lưu ý quan trọng
    1. Không thể gọi suspend function từ function thông thường - phải từ coroutine scope hoặc suspend function khác
    2. Các thư viện Android hiện đại (Retrofit, Room, etc.) đều hỗ trợ suspend functions
    3. Suspend functions không đảm bảo thread cụ thể nào sẽ thực thi
    4. Mã bên trong suspend function có thể là đồng bộ hoặc bất đồng bộ

=> Suspend functions là nền tảng của lập trình bất đồng bộ hiện đại trong Kotlin, đặc biệt hữu ích cho các thao tác mạng, I/O, hoặc xử lý dữ liệu nặng.
```

##### 8.2. Launch & Async
```
Cả launch và async là các function tạo coroutine trong Kotlin, nhưng có những mục đích và cách sử dụng khác nhau.

* launch
    - launch tạo một coroutine mới và trả về một Job để quản lý nó. Thường sử dụng khi:
        + Bạn muốn thực hiện một tác vụ bất đồng bộ mà không cần kết quả trả về
        + Bạn muốn "fire-and-forget" (khởi động và quên đi)
```
```kotlin
fun main() = runBlocking { // this: CoroutineScope
    launch { // launch a new coroutine and continue
        delay(1000L) // non-blocking delay for 1 second (default time unit is ms)
        println("World!") // print after delay
    }
    println("Hello") // main coroutine continues while a previous one is delayed
}
```
```
* async
    - async tạo một coroutine mới và trả về một Deferred<T> - một promise cho kết quả trong tương lai. Thường sử dụng khi:
        + Bạn cần kết quả trả về từ coroutine
        + Bạn muốn thực hiện các tác vụ song song
```
```kotlin
val deferred = scope.async {
    // Tác vụ bất đồng bộ trả về kết quả
    delay(1000)
    "Result"
}

// Chờ và lấy kết quả
val result = deferred.await() // "Result"
```
###### Xử lý lỗi
***launch***
```kotlin
val job = scope.launch {
    try {
        // Code có thể gây lỗi
        throw Exception("Error occurred")
    } catch (e: Exception) {
        // Xử lý lỗi
        println("Caught exception: ${e.message}")
    }
}
```

***async***
```kotlin
val deferred = scope.async {
    // Code có thể gây lỗi
    throw Exception("Error occurred")
    "Result"
}

try {
    val result = deferred.await()
} catch (e: Exception) {
    // Xử lý lỗi
    println("Caught exception: ${e.message}")
}
```
***Thực thi song song***
```kotlin
val result = coroutineScope {
    val first = async { fetchFirstData() }
    val second = async { fetchSecondData() }
    
    // Đợi cả hai hoàn thành và kết hợp kết quả
    CombinedResult(first.await(), second.await())
}
```
**So sánh Launch và Async**

| Đặc điểm             |      launch       |                 async |
|:---------------------|:-----------------:|----------------------:|
| Trả về               |        Job        |           Deferred<T> |
| Mục đích             | Khởi động tác vụ  |           Lấy kết quả |
| Xử lý lỗi            |  Xử lý bên trong  | Propagate khi await() |
| Xử dụng khi thực thi | Không cần kết quả |          Cần kết quả  |

```kotlin
class UserRepository(
    private val api: UserApi,
    private val database: UserDatabase
) {
    suspend fun refreshUserData(userId: String) {
        coroutineScope {
            // Khởi động tác vụ không cần kết quả
            launch { logUserActivity(userId) }
            
            // Lấy dữ liệu từ API và cập nhật database
            val userData = async { api.getUserData(userId) }
            val userPosts = async { api.getUserPosts(userId) }
            
            // Đợi dữ liệu và lưu vào database
            database.updateUser(userData.await())
            database.updatePosts(userPosts.await())
        }
    }
}
```

```
!Lưu ý
    1. Sử dụng launch khi chỉ muốn thực hiện một tác vụ mà không quan tâm kết quả
    2. Sử dụng async khi cần kết quả trả về hoặc khi muốn thực hiện nhiều tác vụ song song
    3. Sử dụng coroutineScope để đảm bảo tất cả coroutines con hoàn thành trước khi hàm suspend trả về
    4. Lỗi trong launch cần được xử lý bên trong, trong khi lỗi trong async được propagate khi gọi await()

=> Hiểu rõ sự khác biệt giữa launch và async giúp bạn viết code bất đồng bộ hiệu quả và dễ bảo trì hơn.
```
##### 8.3. Dispatcher
***Dispatchers trong Kotlin Coroutines:***
Dispatchers là các thành phần quyết định coroutine được thực thi trên thread nào. Chúng giúp điều phối và quản lý các tác vụ đồng thời một cách hiệu quả.

**Dispatchers.Main**
* **Mục đích**: Dùng cho các thao tác trên UI thread (main thread)
* **Đặc điểm**:
  * Chỉ có một thread duy nhất 
  * Không nên chạy các tác vụ nặng trên Main dispatcher 
  * Thích hợp cho việc cập nhật UI, xử lý sự kiện người dùng
* **Ứng dụng**: Trong ứng dụng Android, iOS, hoặc desktop UI

**Dispatchers.IO**
* **Mục đích**: Dùng cho các thao tác I/O, không block CPU
* **Đặc điểm**:
  * Sử dụng thread pool có số lượng thread giới hạn
  * Tối ưu cho các tác vụ I/O như đọc/ghi file, thực hiện network calls
  * Không thích hợp cho các tác vụ tính toán nặng

* **Ứng dụng:** Đọc/ghi file, gọi API, truy cập database
  
**Các Dispatcher khác**

* **Dispatchers.Default:** Dùng cho các tác vụ tính toán nặng
* **Dispatchers.Unconfined:** Không ràng buộc vào thread cụ thể nào
* **newSingleThreadContext("name"):** Tạo một thread mới và riêng biệt

***Ví dụ sử dụng***
```kotlin
// Sử dụng Dispatchers.Main để cập nhật UI
CoroutineScope(Dispatchers.Main).launch {
    // Cập nhật UI
    //textView.text = "Hello World"
}

// Sử dụng Dispatchers.IO để thực hiện network call
CoroutineScope(Dispatchers.IO).launch {
    // Thực hiện network call
    val response = api.getData()
    
    // Chuyển sang Main dispatcher để cập nhật UI
    withContext(Dispatchers.Main) {
        //textView.text = response.data
    }
}
```

**! Lưu ý quan trọng**
```
1. Không thực hiện tác vụ nặng trên Dispatchers.Main
2. Sử dụng withContext() để chuyển đổi giữa các dispatcher
3. Dispatchers.IO không thích hợp cho các tác vụ tính toán nặng, thay vào đó hãy dùng Dispatchers.Default
4. Trong Android, Dispatchers.Main chỉ khả dụng khi có thư viện UI (như Android)
```
##### 8.4. Scope

* **GlobalScope**
  * GlobalScope là một CoroutineScope có phạm vi toàn cục và tồn tại trong suốt thời gian sống của ứng dụng.
* **Đặc điểm:**
  * Tồn tại độc lập với vòng đời của các component
  * Không bị hủy khi component (như Activity, Fragment) bị hủy
  * Coroutine được launch từ GlobalScope sẽ chạy cho đến khi hoàn thành hoặc bị cancel thủ công

* **Nhược điểm:**
  * Có thể gây rò rỉ bộ nhớ (memory leak)
  * Khó kiểm soát vòng đời
  * Không được khuyến khích sử dụng trong production code

**Ví dụ**
```kotlin
GlobalScope.launch {
    // Coroutine này sẽ tiếp tục chạy ngay cả khi Activity bị hủy
    delay(1000L)
    println("Coroutine vẫn chạy")
}
```

* **Coroutine Scope**
    * CoroutineScope là một interface để định nghĩa phạm vi cho các coroutine, giúp quản lý vòng đời và hủy các coroutine khi cần.    
* **Đặc điểm:**
  * Có thể tùy chỉnh theo vòng đời của các component 
  * Dễ dàng hủy tất cả coroutines trong scope khi không cần thiết 
  * Giúp tránh memory leak

***Cách tạo và sử dụng***
```kotlin
// Tạo scope với dispatcher cụ thể
val scope = CoroutineScope(Dispatchers.Main)

// Launch coroutine trong scope
val job = scope.launch {
    // Thực hiện công việc
}

// Hủy tất cả coroutines trong scope
scope.cancel()
```

***Tích hợp với lifecycle***
```kotlin
class MyActivity : AppCompatActivity(), CoroutineScope {
    // Tạo job để quản lý tất cả coroutines
    private lateinit var job: Job
    
    // Ghi đè coroutineContext
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Main + job
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Khởi tạo job
        job = Job()
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Hủy tất cả coroutines khi Activity bị hủy
        job.cancel()
    }
    
    fun doSomething() {
        // Launch coroutine trong scope của activity
        launch {
            // Coroutine này sẽ bị hủy khi Activity bị hủy
        }
    }
}
```
* **lifecycleScope và viewModelScope**
    * Kotlin cung cấp các scope tiện lợi tích hợp sẵn với lifecycle:
* **lifecycleScope:**
  * Gắn liền với lifecycle của component (Activity, Fragment)
  * Tự động hủy coroutines khi lifecycle kết thúc
```kotlin
// Trong Activity hoặc Fragment
lifecycleScope.launch {
    // Coroutine này sẽ bị hủy khi lifecycle owner bị hủy
}
```

* **viewModelScope:**
  * Gắn liền với ViewModel
  * Tự động hủy coroutines khi ViewModel bị clear
```kotlin
class MyViewModel : ViewModel() {
    fun loadData() {
        viewModelScope.launch {
            // Coroutine này sẽ bị hủy khi ViewModel bị clear
        }
    }
}
```

**Bảng so sánh**

|             | GlobalScope        | CoroutineScope |      lifecycleScope | viewModelScope |
|:------------|:-------------------|:--------------:|--------------------:|---------------:|
| Phạm vi     | Toàn bộ ứng dụng   |   Tùy chỉnh    | Component lifecycle |      ViewModel |
| Tự hủy      | Không              |    Thủ công    |             Tự động |        Tự động |
| Phù hợp với | Tác vụ độc lập     |  Custom scope  |       UI components |      ViewModel |
| Khuyến nghị | Không khuyến khích |  Khuyến khích  |        Khuyến khích |   Khuyến khích |

## 9. Advance

#### 9.1. Sealed Class & Enum Class
* **Enum Class:** Enum Class trong Kotlin là kiểu dữ liệu dùng để định nghĩa một tập hợp các hằng số có tên.

* **Đặc điểm của Enum Class:**
  * Định nghĩa một tập hợp cố định các giá trị
  * Mỗi enum constant là một instance của lớp enum
  * Có thể chứa properties và methods
  * Tự động có các phương thức như values() và valueOf()
  * Có thể implement interfaces (nhưng không thể kế thừa từ các lớp khác)

**Ví dụ cơ bản**
```kotlin
enum class Direction {
    NORTH, SOUTH, EAST, WEST
}

// Sử dụng
val dir = Direction.NORTH
```

**Enum với properties và methods:**
```kotlin
enum class Day(val isWeekend: Boolean) {
    MONDAY(false),
    TUESDAY(false),
    WEDNESDAY(false),
    THURSDAY(false),
    FRIDAY(false),
    SATURDAY(true),
    SUNDAY(true);
    
    fun daysUntilWeekend(): Int {
        return when (this) {
            MONDAY -> 5
            TUESDAY -> 4
            WEDNESDAY -> 3
            THURSDAY -> 2
            FRIDAY -> 1
            SATURDAY, SUNDAY -> 0
        }
    }
}
```
* **Sealed Class:** Sealed Class trong Kotlin là một lớp đặc biệt được sử dụng để biểu diễn các phân cấp hạn chế, khi một giá trị chỉ có thể là một trong số các kiểu cụ thể đã biết.
* **Đặc điểm của Sealed Class:**
  * Các lớp con, đối tượng (object declarations) kế thừa **_phải được định nghĩa trong cùng file với sealed class hoặc lồng trong sealed class_** (từ Kotlin 1.5)
  * Tập hợp các lớp con là hữu hạn và biết trước
  * Rất hữu ích cho pattern matching với when expression
  * Thích hợp cho việc biểu diễn trạng thái, kết quả hoặc sự kiện
  * Có thể chứa state và behavior khác nhau cho từng con

**Ví dụ cơ bản**

```kotlin
sealed class Result {
    data class Success(val data: String) : Result()
    data class Error(val message: String) : Result()
    object Loading : Result()
}

// Sử dụng
fun handleResult(result: Result) {
    when (result) {
        is Result.Success -> println("Success: ${result.data}")
        is Result.Error -> println("Error: ${result.message}")
        is Result.Loading -> println("Loading...")
        else -> {}
    }
}
```

***So sánh Sealed Class và Enum Class***

| Đặc điểm         | Enum Class                      |                     Sealed Class                      | 
|:-----------------|:--------------------------------|:-----------------------------------------------------:|
| Các instance     | Cố định, không thể tạo thêm     |           Không giới hạn số lượng instance            | 
| State            | Mỗi constant có cùng loại state |        Mỗi lớp con có thể có state riêng biệt         | 
| Kế thừa          | Không thể kế thừa               |        Lớp con có thể mở rộng theo nhiều cách         | 
| Pattern matching | Yêu cầu else branch trong when  | Không yêu cầu else nếu đã xử lý tất cả các trường hợp |
| Tính linh hoạt   | Ít linh hoạt hơn                |     Linh hoạt hơn, có thể có các lớp con phức tạp     |

**Khi nào sử dụng**
* **Sử dụng Enum khi:**
  * Cần một tập hợp cố định, đơn giản các giá trị
  * Tất cả các giá trị đều có cùng loại thuộc tính
  * Số lượng giá trị nhỏ và không thay đổi

* **Sử dụng Sealed Class khi:**
  * Cần biểu diễn các kiểu dữ liệu khác nhau nhưng liên quan
  * Mỗi lớp con cần có state và hành vi riêng
  * Muốn tận dụng smart cast trong when expression
  * Thích hợp cho việc mô hình hóa state machine, response API, hoặc event handling

**Ví dụ thực tế**

***API Response với Sealed Class:***

```kotlin
sealed class ApiResponse<out T> {
    data class Success<out T>(val data: T) : ApiResponse<T>()
    data class Error(val errorCode: Int, val message: String) : ApiResponse<Nothing>()
    object Loading : ApiResponse<Nothing>()
    object Empty : ApiResponse<Nothing>()
}

fun handleUserResponse(response: ApiResponse<User>) {
    when (response) {
        is ApiResponse.Success -> displayUser(response.data)
        is ApiResponse.Error -> showError(response.message)
        is ApiResponse.Loading -> showLoading()
        is ApiResponse.Empty -> showEmptyState()
        else -> {}
    }
}
```

***Status với Enum Class:***
```kotlin
enum class TaskStatus(val color: Int) {
    TODO(Color.RED),
    IN_PROGRESS(Color.YELLOW),
    DONE(Color.GREEN),
    CANCELLED(Color.GRAY);
    
    fun isActive(): Boolean = this == TODO || this == IN_PROGRESS
}
```
```=> Cả Sealed Class và Enum Class đều là công cụ mạnh mẽ để tạo code rõ ràng, an toàn về kiểu dữ liệu và dễ bảo trì trong Kotlin.```

#### 9.2. Generics (fun<T> add(a:T, b:T))
    * Generics trong Kotlin cho phép bạn viết code có thể 
    làm việc với nhiều kiểu dữ liệu khác nhau, giúp tăng tính tái sử dụng và an toàn kiểu dữ liệu.
    * Khái niệm cơ bản
        - Type Parameters: Được khai báo bằng dấu <T> (hoặc chữ cái khác) và đại diện cho kiểu dữ liệu chưa xác định
        - Type Arguments: Kiểu dữ liệu cụ thể được cung cấp khi sử dụng lớp hoặc hàm generic
##### 9.2.1. Lớp Generic
```kotlin
class Box<T>(var value: T) {
    fun getValue(): T {
        return value
    }
    
    fun setValue(newValue: T) {
        value = newValue
    }
}

// Sử dụng
val stringBox = Box<String>("Hello")
val intBox = Box<Int>(42)
```
##### 9.2.2. Hàm Generic
```kotlin
fun <T> printValue(value: T) {
    println(value)
}

// Sử dụng
printValue("Hello")  // T = String
printValue(42)       // T = Int
```

##### 9.2.3. Ràng buộc kiểu (Type Constrains)
```kotlin
fun <T : Comparable<T>> findMax(a: T, b: T): T {
    return if (a > b) a else b
}

// Sử dụng
findMax("apple", "banana")  // Trả về "banana"
findMax(5, 10)             // Trả về 10
```

##### 9.2.4. Variance
    - Invariant: Mặc định, generic types là invariant (không thay đổi)
    - Covariant: Sử dụng 'out' để cho phép kiểu trả về có thể là lớp con
    - Contravariant: Sử dụng 'in' để cho phép kiểu tham số có thể là lớp cha
```kotlin
// Covariant
interface Producer<out T> {
    fun produce(): T
}

// Contravariant
interface Consumer<in T> {
    fun consume(item: T)
}
```

    - Type safety: Phát hiện lỗi tại thời điểm biên dịch
    - Tái sử dụng code: Viết code một lần, sử dụng với nhiều kiểu dữ liệu
    - Không cần ép kiểu: Tránh được việc ép kiểu thủ công và các lỗi liên quan
    - Hiệu năng tối ưu: Không tạo ra thêm overhead khi chạy

    => Generics là một phần quan trọng trong lập trình Kotlin hiện đại, giúp xây dựng các API linh hoạt và an toàn.

#### 9.3. Kotlin Reflection (::class)
##### 9.3.1. Kiểm tra kiểu dữ liệu (Type Check)
    - Toán tử ::class cho phép bạn kiểm tra loại kiểu dữ liệu (type) của một đối tượng tại runtime.

```kotlin
    fun printClassInfo(obj: Any) {
        println("Class of the object: ${obj::class}")
    }

    fun main() {
        printClassInfo("Hello")  // Kết quả: Class of the object: class kotlin.String
        printClassInfo(323)      // Kết quả: Class of the object: class kotlin.Int
    }
```

##### 9.3.2. Reflection để truy xuất thông tin lớp
    - ::class được sử dụng để làm việc với Kotlin Reflection (kotlin.reflect) 
    để truy xuất thông tin như thuộc tính, phương thức, constructor của lớp.

***Ví dụ***
```kotlin
import kotlin.reflect.full.declaredMemberProperties

class Person(val name: String, val age: Int)

fun main() {
    val kClass = Person::class
    println("Properties of ${kClass.simpleName}:")
    kClass.declaredMemberProperties.forEach { println(it.name) }
}

```

##### 9.3.3. Truy cập Annotation của lớp
* Nếu một lớp có chứa annotation (chú thích), bạn có thể sử dụng ::class để truy vấn chú thích đó, rất hữu ích trong lập trình framework hoặc thư viện.

```kotlin
@Target(AnnotationTarget.CLASS)
annotation class Info(val author: String)

@Info(author = "Duc")
class Example

fun main() {
    val annotation = Example::class.annotations.find { it is Info } as? Info
    println("Author: ${annotation?.author}")  // Kết quả: Author: Duc
}

```

##### 9.3.4. Sử dụng với Dependency Injection
* Trong các framework như Koin hoặc Dagger, ::class thường được dùng để định nghĩa hoặc trỏ tới loại lớp (class type) cho việc tiêm phụ thuộc (Dependency Injection).

```
val myModule = module {
    single { MyClass() }
}

startKoin {
    modules(myModule)
}

// Khi sử dụng:
val myClassInstance = get<MyClass>()  // Nhờ `MyClass::class` để xác định lớp

```

##### 9.3.5. Lấy Constructor của Lớp
* Bạn có thể dùng ::class để tham chiếu tới constructor (hàm khởi tạo) của một lớp, điều này hữu ích trong các trường hợp tạo đối tượng linh động.
```kotlin
class Person(val name: String, val age: Int)

fun main() {
    val constructor = Person::class.constructors.first()
    val person = constructor.call("Alice", 25)
    println("${person.name}, ${person.age}")  // Kết quả: Alice, 25
}

```

##### 9.3.6. Tương thích với Reflection của Java
* Kotlin Reflection cho phép bạn làm việc dễ dàng với các lớp hoặc framework Java thông qua .java.
```kotlin
val javaClass = Person::class.java
println(javaClass.name)  // Kết quả: full package name của lớp Person

```

```
* Khi nào nên sử dụng ::class?
    * Runtime Metadata: Khi bạn cần truy xuất thông tin về một lớp (như thuộc tính, phương thức, annotation).
    * Framework hoặc Library: Trong các ứng dụng phức tạp yêu cầu Dependency Injection hoặc phân tích lớp động.

    * Validation: Kiểm tra kiểu dữ liệu hoặc phân tích thông tin cấu trúc đối tượng.
```

#### 9.4. DSL(Domain Specific Language) trong Kotlin
* **DSL (Domain-Specific Language)** là một ngôn ngữ lập trình được thiết kế đặc biệt cho một lĩnh vực cụ thể. Trong Kotlin, DSL cho phép bạn tạo ra các cú pháp dễ đọc và dễ hiểu cho các tác vụ cụ thể, giúp cải thiện khả năng sử dụng và bảo trì mã nguồn.

**1. Khái niệm DSL trong Kotlin**
   DSL là một cách để viết mã theo cách gần gũi với ngôn ngữ tự nhiên, giúp người dùng dễ dàng hiểu và sử dụng.
   Kotlin hỗ trợ việc xây dựng DSL thông qua các tính năng như extension functions, lambda expressions, và higher-order functions.
**2. Các thành phần chính của DSL**
   * **Extension Functions:** Cho phép bạn thêm các hàm mới vào các lớp hiện có mà không cần kế thừa.
   * **Lambda Expressions:** Cung cấp cách viết hàm ngắn gọn và linh hoạt.
   * **Higher-Order Functions:** Hàm có thể nhận hàm khác làm tham số hoặc trả về hàm.
**3. Cú pháp và ví dụ**
   Kotlin cho phép bạn tạo DSL với cú pháp rất tự nhiên. Dưới đây là một ví dụ đơn giản về cách tạo một DSL để xây dựng một cấu trúc HTML:

```kotlin
fun html(init: HTML.() -> Unit) {
    val html = HTML()
    html.init()
    println(html)
}

class HTML {
    private val elements = mutableListOf<String>()

    fun body(init: Body.() -> Unit) {
        val body = Body()
        body.init()
        elements.add("<body>${body.content}</body>")
    }

    override fun toString(): String {
        return elements.joinToString("\n")
    }
}

class Body {
    var content: String = ""

    fun p(text: String) {
        content += "<p>$text</p>"
    }
}

// Sử dụng DSL
fun main() {
    html {
        body {
            p("Hello, World!")
            p("Welcome to Kotlin DSL.")
        }
    }
}
```

**4. Lợi ích của việc sử dụng DSL**
   * **Dễ đọc:** Mã nguồn trở nên dễ hiểu hơn cho những người không phải lập trình viên.
   * **Tính linh hoạt:** Có thể dễ dàng mở rộng và tùy chỉnh theo nhu cầu của dự án.
   * **Giảm thiểu lỗi:** Cú pháp rõ ràng giúp giảm thiểu khả năng xảy ra lỗi.
**5. Ứng dụng của DSL trong Kotlin**
   * **Xây dựng UI:** Sử dụng DSL để tạo giao diện người dùng một cách trực quan.
   * **Cấu hình:** Tạo DSL cho việc cấu hình ứng dụng, giúp người dùng dễ dàng thiết lập các tham số.
   * **Tạo báo cáo:** Sử dụng DSL để định nghĩa cấu trúc báo cáo hoặc tài liệu.

**Kết luận**

* DSL trong Kotlin là một công cụ mạnh mẽ giúp cải thiện khả năng đọc và bảo trì mã nguồn. Bằng cách sử dụng các tính năng của Kotlin, bạn có thể tạo ra các ngôn ngữ miền cụ thể dễ sử dụng cho các tác vụ trong lĩnh vực của bạn.






