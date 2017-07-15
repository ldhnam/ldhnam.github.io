>Room được Google giới thiệu tại Google I/O 2017. Room cung cấp một lớp abstraction cho SQLite cho phép truy cập database một cách trôi chảy trong khi tận dụng hết sức mạnh của SQLite.

Core famework của `Room` cung cấp một sự hỗ trợ built-in để làm việc với SQL thô. Mặc dù các API này rất mạnh nhưng chúng khá low-level và đòi hỏi rất nhiều thời gian và công sức để sử dụng:

+ Không có kiểm tra thời gian compile các truy vấn SQL thô. Khi data của bạn bị thay đổi, bạn cần cập nhật các truy vấn SQL bị ảnh hưởng theo cách thủ công. Quá trình này có thể mất nhiều thời gian và có thể bị lỗi.

+ Bạn cần sử dụng rất nhiều boilerplate code để convert giữa các truy vấn SQL và các objects của Java.

`Room` sẽ take care những mối quan tâm này cho bạn trong khi cung cấp một lớp abstraction hơn SQLite.

Đây là 3 thành phần chính trong `Room`:

+ `Database:` Bạn có thể sử dụng component nà để khởi tạo database. Annotation định nghĩa một danh sách các entities, và nội dung của class định nghĩa danh sách các objects truy cập dữ liệu (DAO) trong database. Đây là điểm chính để truy cập cho các connection cơ bản.

    Lớp annotated nên là một lớp abstraction extends (kế thừa) từ `RoomDatabase`. Tại thời điểm runtime, bạn có thể có một instance của nó bằng cách gọi `Room.databaseBuilder()` hoặc `Room.inMemoryDatabaseBuilder()`

+ `Entity:` Component này đại diện cho một class chứa một row database. Đối với mỗi entity, một table database được tạo ra để chứa các items. Bạn phải reference tới entity class thông qua mảng `entities` trong lớp `Database`. Với mỗi field của entity được duy trì trong database, trừ khi bạn annotate nó với annotation `@Ignore`.

    > `Note:` Entites có thể có một constructor trống (nếu lớp DAO có thể access vào từng field tồn tại) hoặc một constructor có các parameters chứa các type và tên khớp với các field trong entity. `Room` có thể sử dụng các constructor đầy đủ hoặc một phần, chẳng hạn như một constructor chỉ nhận một số field.

+ `DAO:` Component này đại diện cho một lớp hoặc một interface như là một Data Access Object (DAO). DAO là thành phần chính của `Room` và có trách nhiệm định nghĩa các methods truy cập database. Lớp phải được annotated với `@Database` phải chứa một abstract method có 0 đối số và trả về lớp với annotated `@Dao`. Khi generate code tại thời gian compile, `Room` tạo ra một implementation với lớp này

    > `Ghi chú:` Bằng cách access database sử dụng một lớp DAO thay vì xây dựng query hoặc query trực tiếp, bạn có thể tách các component khác nhau của kiến trúc database của bạn. Hơn nữa, các DAO cho phép bạn dễ dàng mock database khi bạn test ứng dụng của bạn.

Các components này, cùng với những mối quan hệ của chúng với phần còn lại của application trong hình 1:

![Room Architecture](/img/room_architecture.png)

**Hình 1.** Sơ đồ kiến trúc Room

**Apply Room vào project**

```
implementation 'android.arch.persistence.room:runtime:1.0.0-alpha4'
kapt 'android.arch.persistence.room:compiler:1.0.0-alpha4'
//annotationProcessor 'android.arch.persistence.room:compiler:1.0.0-alpha4'
nếu như bạn sử dụng Java
```

Tiếp theo bạn tạo model:

*User.kt*
```
@Entity
data class User(
        @PrimaryKey(autoGenerate = true)
        var id: Int = 0,
        var name: String = "",
        var gender: String = "",
        var age: Int = 0,
        var phone: String = "",
        var email: String = "",
        var photo: String = "",
        var created_at: Long = System.currentTimeMillis())
```

`@Entity` sẽ tạo ra table name trùng với tên class và được viết `lowercase`, nếu bạn muốn đổi table name bạn dùng `@Entity(tableName = "table_name")`. `@PrimaryKey(autoGenerate = true)` sẽ dùng cho primary key table của bạn và tự động auto generate id nếu `autoGenerate = true`, bạn có thể tắt generate bằng cách cho `autoGenerate = false`. Nếu bạn không sử dụng `@ColumnInfo(name = table_field)` thì mặc định `Room` sẽ lấy tên field như là tên column trong database. Nếu bạn muốn column có tên khác, bạn sẽ thêm `@ColumnInfo(name = table_field)` cho field. Dưới đây là ví dụ:

*User.kt*
```
@Entity(tableName = "users")
data class User(
        @PrimaryKey(autoGenerate = true)
        var id: Int = 0,
        var name: String = "",
        var gender: String = "",
        var age: Int = 0,
        var phone: String = "",
        var email: String = "",
        var photo: String = "",
        @ColumnInfo(name = "created_at")
        var createdAt: Long = System.currentTimeMillis())
```

Tiếp theo, bạn sẽ tạo ra một interface `Dao` để query với table `user` của bạn

*UserDao.kt*
```
@Dao
interface UserDao {

    @Query("SELECT * FROM user ORDER BY created_at DESC")
    fun getAllUser(): List<User>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    fun insert(user: User)
}
```

Bạn sẽ sử dụng `@Dao` annotation để chỉ rõ đây là interface `Dao`. `@Query` đây là annotation để chỉ rõ đây là câu `select` và bạn có thể viết custom query tùy theo ý muốn của bạn. `@Insert` annotation dùng để insert tất cả các parameters class của bạn vào database trong một transaction đơn.

Nếu `@Insert` chỉ nhận một parameter, nó có thể return về `long`, đó là `rowId` mới được insert. Nếu parameter là một array hoặc một collection, nó sẽ return về `long[]` hoặc `List<Long>` thay thế.

> **Ghi chú:** SQLite sẽ handle `@Insert(OnConflict = REPLACE)` như một tập các thao tác `REMOVE` và `REPLACE` thay vì một thao tác `UPDATE` duy nhất. Method thay đổi giá trị conflict này có thể ảnh hưởng đến các ràng buộc foreign key. Để biết thêm, bạn hãy xem [tài liệu SQLite](https://sqlite.org/lang_conflict.html) cho mệnh đề `ON_ONFLICT`.

Tiếp theo, bạn sẽ tạo ra một abstract database của bạn extends từ `RoomDatabase`.

*AppDatabase.kt*
```
@Database(entities = arrayOf(User::class), version = 1, exportSchema = false)
abstract class AppDatabase: RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

Bạn cần phải chỉ định các `entities` của bạn, `version` database của bạn và `exportSchema`. Nếu bạn không chỉ định `exportSchema = false` bạn cần phải thêm dòng này ở `build.gradle` để export thông tin schema database của bạn dưới JSON file.

*build.gradle*
```
android {
    ...
    defaultConfig {
        ...
        javaCompileOptions {
            annotationProcessorOptions {
                arguments = ["room.schemaLocation":
                             "$projectDir/schemas".toString()]
            }
        }
    }
}
```   

Tiếp đến bạn cần phải tạo các method trong class `AppDatabase` để chỉ rõ các `Dao` của bạn.

Cuối cùng bạn cần phải tạo database của bạn bằng cách sử dụng method `Room.databaseBuilder`, ở đây mình sẽ sử dụng DI (Dependency Injection) của `Dagger`. `Room.databaseBuilder` sẽ nhận 3 tham số: tham số thứ 1 là `Context`, thứ 2 là lớp database của bạn extends từ `RoomDatabase` và tham số cuối cùng là tên database.
```
@Provides
    @Singleton
    fun provideDatabase(): AppDatabase {
        return Room.databaseBuilder(mApplication, AppDatabase::class.java, "app-database").allowMainThreadQueries().build()
    }
```
Nếu bạn không gọi method `allowMainThreadQueries()` thì bạn sẽ bị một exception là không access được database trên main thread.
>Caused by: java.lang.IllegalStateException: Cannot access database on the main thread since it may potentially lock the UI for a long periods of time.

### Sử dụng Room với RxJava/RxAndroid và Kotlin

Thêm `RxJava/RxAndroid` vào `build.gradle:`

```
implementation 'io.reactivex.rxjava2:rxjava:2.1.1'
implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
```
và `implementation 'android.arch.persistence.room:rxjava2:1.0.0-alpha4'` để sử dụng `Room` cùng với `RxJava`.

Chúng ta sẽ sửa lại lớp `UserDao` hồi nãy:
*UserDao.kt*
```
@Dao
interface UserDao {

    @Query("SELECT * FROM user ORDER BY created_at DESC")
    fun getAllUser(): Flowable<List<User>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    fun insert(user: User)
}
```
và lớp `AppModule:`
*AppModule.kt*
```
@Provides
    @Singleton
    fun provideDatabase(): AppDatabase {
        return Room.databaseBuilder(mApplication, AppDatabase::class.java, "app-database").build()
    }
```

và bây giờ sẽ sử dụng `Room` trong presenter:
```
fun getUsersFromNetwork() {
        addDisposable(mRestService.getUsers()
                .map {
                    for (user in it) {
                        mAppDatabase.userDao().insert(user)
                    }
                    it
                }
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe({
                    users -> mvpView?.showUsers(users)
                }, { throwable -> throwable.printStackTrace() }))
    }
```

Bạn sẽ lấy list user từ `API` và sử dụng operator map để thêm từng `User` vào database.
Nếu bạn không muốn sử dụng `for` trong map thì bạn sẽ tạo thêm 1 method trong `UserDao:`

```
    @Insert
    fun insertAll(users: ArrayList<User>)
```

Vậy là mình đã giới thiệu xong `Room`, và cách sử dụng `Room` trong project cùng với `RxJava/RxAndroid`.

Bạn có thể xem thêm project tại [Github](https://github.com/ldhnam/RoomRxJava2Example).

### Reference

[Room Persistence Library](https://developer.android.com/topic/libraries/architecture/room.html)