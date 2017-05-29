Mặc dù nó có gồ ghề, tôi thực sự thích JSON. Thật dễ dàng để đọc, khá nhanh để phân tích cú pháp và đơn giản một cách mới mẻ. Dưới đây là một API mẫu của GitHub:
```
{
  "url": "https://api.github.com/repos/square/okio/issues/156",
  "id": 91393390,
  "number": 156,
  "title": "ByteString CharSequence idea",
  "state": "open",
  "created_at": "2015-06-27T00:49:40.000Z",
  "body": "Let's make CharSequence that's backed by bytes.\n"
}
```
Các lớp dữ liệu không thay đổi của `Kotlin` làm cho nó dễ dàng để xây dựng một model cơ bản cho JSON này.
```
data class Issue(
    val url: String,
    val id: Long,
    val number: Long,
    val title: String,
    val state: String,
    val created_at: String,
    val body: String)
```
Chính là nó. Không có `equals()`, `hashCode()`, hoặc `toString()` boilerplate (bản mẫu). Chúng ta thậm chí không cần một `builder`! Hãy mở rộng model để tận dụng các giá trị mặc định của `Kotlin` và `nulls` rõ ràng:
```
data class Issue(
    val url: String,
    val id: Long,
    val number: Long,
    val title: String,
    val comments: Long = 0L,
    val created_at: String,
    val closed_at: String?,
    val body: String = "")
```
Các giá trị mặc định được điền vào khi decode `JSON` từ network. Tôi thích rằng tôi có thể để chúng tạo ra khi tạo sample data trong các test cases của tôi. Các loại `nullable` rõ ràng để ngăn ngừa các vấn đề về data.
Hôm nay chúng tôi sẽ release __Moshi 1.5__ với sự hỗ trợ mạnh mẽ của `Kotlin` thông qua module `moshi-kotlin`. Moshi kiểu adapters và annotation bind JSON thành một idiomatic data model.
```
data class Issue(
    val url: String,
    val id: Long,
    val number: Long,
    val title: String,
    val state: IssueState,
    val comments: Long = 0L,
    @Json(name = "created_at") val createdAt: Date,
    @Json(name = "closed_at") val closedAt: Date?,
    val body: String = "")
```
Class này sử dụng các kiểu phù hợp thay vì chuỗi cho state và timestamps. Annotation `@Json` maps với tên `snake_case`  trong JSON thành thuộc tên thuộc tính `camelCase` trong `Kotlin`.
Để thiết lập điều này chúng ta cần một `Moshi.Builder` và một `JsonAdapter`. Tôi có thể sử dụng chuỗi của `Kotlin` để nhúng một sample message ngay trong code.
```
val issueJson = """
{
  "url": "https://api.github.com/repos/square/okio/issues/156",
  "id": 91393390,
  "number": 156,
  "title": "ByteString CharSequence idea",
  "state": "open",
  "created_at": "2015-06-27T00:49:40.000Z"
}
"""
val moshi = Moshi.Builder()
    .add(KotlinJsonAdapterFactory())
    .add(Date::class.java, Rfc3339DateJsonAdapter().nullSafe())
    .build()
val issueAdapter = moshi.adapter(Issue::class.java)
val issue = issueAdapter.fromJson(issueJson)
```
Nếu bạn sử dụng JSON, __Moshi__ và __Kotlin__ giúp bạn build model tốt hơn với ít code hơn. Ghi chú rằng `moshi-kotlin` sử dụng `kotlin-reflect` cho việc binding property. Dependency lớn theo tiêu chuẩn của Android (1.7 MiB / 11,500 methods). Chúng tôi đang nghĩ đến cách sáng tạo để giải quyết điều đó!

Đây đăng này là một phần của loạt bài "[Square Open Source ♥s Kotlin](https://medium.com/square-corner-blog/square-open-source-loves-kotlin-c57c21710a17)".

### Reference
[https://medium.com/square-corner-blog/kotlins-a-great-language-for-json-fcd6ef99256b](https://medium.com/square-corner-blog/kotlins-a-great-language-for-json-fcd6ef99256b)