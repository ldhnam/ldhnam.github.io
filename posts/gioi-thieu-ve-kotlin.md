# Giới thiệu về Kotlin

### Kotlin Language là gì ?

`Kotlin` là một ngôn ngữ ngữ kiểu tĩnh dành cho `Java Virtual Machine`. Nó được tạo ra bởi `JetBrains`, `Kotlin` sẽ chạy trên JVM và sử dụng các công cụ và thư viện hiện có của `Java`. Và ngược lại `Java` cũng có thể sử dụng các item được xây dựng trong `Kotlin`.

`Statically-typed` không có nghĩa chúng ta phải khai báo tất cả các biến lúc đầu trước khi sử dụng chúng. Các biến có thể được khởi tạo bất cứ nơi nào trong chương trình và chúng ta có thể sử dụng chúng bất cứ nơi nào khi cần.

### Tổng quan về Kotlin

#### Định nghĩa function (Defining function)
Function có 2 tham số `Int` với kết quả trả về là `Int`:
```
fun sum(a: Int, b: Int): Int {
    return a + b
}
```
Function có kiểu kết quả trả về được suy ra từ thân của biểu thức:
```
    fun sum(a: Int, b: Int) = a + b
```
Function trả về kết quả vô nghĩa:
```
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
```
Kiểu trả về `Unit` có thể bỏ qua:
```
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
```

#### Định nghĩa biến cục bộ
Biến cục bộ chỉ gán giá trị một lần (read-only):
```
val a: Int = 1  // immediate assignment
val b = 2   // `Int` type is inferred
val c: Int  // Type required when no initializer is provided
c = 3       // deferred assignment
```

Biến có thể thay đổi:
```
var x = 5 // `Int` type is inferred
x += 1
```

#### Comments
Giống như `Java` và `JavaScript`, `Kotlin` hỗ trợ end-of-line và block comments.
```
// This is an end-of-line comment

/* This is a block comment
on multiple lines. */
```
Không giống `Java`, block comments trong `Kotlin` có thể lồng nhau.

#### Sử dụng String templates
```
var a = 1
// simple name in template:
val s1 = "a is $a" 

a = 2
// arbitrary expression in template:
val s2 = "${s1.replace("is", "was")}, but now is $a"
```

#### Sử dụng các biểu thức có điều kiện
```
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```
Sử dụng `if` như một biểu thức:
```
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

#### Sử dụng các giá trị nullable và check null
Một tham chiếu phải được đánh dấu rõ ràng là `nullable` khi giá trị `null` là có thể.
Trả về `null` nếu `str` không giữ một số nguyên:
```
fun parseInt(str: String): Int? {
    // ...
}
```
Sử dụng function trả về giá trị `nullable`:
```
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // Using `x * y` yields error because they may hold nulls.
    if (x != null && y != null) {
        // x and y are automatically cast to non-nullable after null check
        println(x * y)
    }
    else {
        println("either '$arg1' or '$arg2' is not a number")
    }    
}
```
hoặc
```
// ...
if (x == null) {
    println("Wrong number format in arg1: '${arg1}'")
    return
}
if (y == null) {
    println("Wrong number format in arg2: '${arg2}'")
    return
}

// x and y are automatically cast to non-nullable after null check
println(x * y)
```

#### Kiểm tra và tự động ép kiểu
Toán tử `is` kiểm tra nếu một biểu thức là một thể hiện của một kiểu. Nếu một biến cục bộ `immutable ` hoặc một thuộc tính được kiểm tra cho một kiểu cụ thể thì không cần ép kiểu một cách rõ ràng:
```
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // `obj` is automatically cast to `String` in this branch
        return obj.length
    }

    // `obj` is still of type `Any` outside of the type-checked branch
    return null
}
```
hoặc
```
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // `obj` is automatically cast to `String` in this branch
    return obj.length
}
```
thậm chí hoặc
```
fun getStringLength(obj: Any): Int? {
    // `obj` is automatically cast to `String` on the right-hand side of `&&`
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
```

#### Sử dụng vòng lặp for
```
val items = listOf("apple", "banana", "kiwi")
for (item in items) {
    println(item)
}
```
hoặc
```
val items = listOf("apple", "banana", "kiwi")
for (index in items.indices) {
    println("item at $index is ${items[index]}")
}
```

#### Sử dụng vòng lặp white
```
val items = listOf("apple", "banana", "kiwi")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

#### Sử dụng biểu thức when
```
fun describe(obj: Any): String =
when (obj) {
    1          -> "One"
    "Hello"    -> "Greeting"
    is Long    -> "Long"
    !is String -> "Not a string"
    else       -> "Unknown"
}
```

#### Sử dụng dãy (ranges)
Để kiểm tra một số có thuộc một dãy, sử dụng toán tử `in`:
```
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```
Kiểm tra một số nằm ngoài dãy:
```
val list = listOf("a", "b", "c")

if (-1 !in 0..list.lastIndex) {
    println("-1 is out of range")
}
if (list.size !in list.indices) {
    println("list size is out of valid list indices range too")
}
```
Lặp trong một dãy:
```
fun main(args: Array<String>) {
    for (x in 1..5) {
        print(x)
    }
}
```
hoặc với `step`
```
for (x in 1..10 step 2) {
    print(x)
}
for (x in 9 downTo 0 step 3) {
    print(x)
}
```
#### Sử dụng collections
Lặp trên một collection
```
for (item in items) {
    println(item)
}
```
Kiểm tra nếu một collection có chứa một đối tượng, sử dụng toán tử `in`:
```
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```
Sử dụng `lambda expressions` để `filter` và `map` collection:
```
fruits
.filter { it.startsWith("a") }
.sortedBy { it }
.map { it.toUpperCase() }
.forEach { println(it) }
```

#### Null Safety
Một trong những cạm bẫy phổ biến trong các ngôn ngữ lập trình, bao gồm cả `Java` là cho phép 1 thành phần nào đó được `null`. Nếu không chắc chắn thành phần đó được phép `null` hay không sẽ rất dễ xảy ra lỗi không lường trước được, gây nguy hiểm cho hệ thống của bạn. Cụ thể, trong `Java` sẽ gây ra 1 `exception` là `NullPoiterException`.
`Kotlin` nhằm mục đích xóa bỏ `NullPoiterException`. `Kotlin` cung cấp một cơ chế ngay khi khai báo biến, bạn phải chỉ rõ biến đó được phép `null` hay là không. Có 2 trường hợp được phép đó là : không thể `null` và có thể `null`
Nếu bạn muốn nó là `null`, hãy sử dụng dấu `?` vào sau kiểu.
Khai báo 1 biến cho phép `null`:
```
var b: String? = "abc"
b = null // compilation ok
val l = b.length // not safe
```
Khai báo 1 biến không được phép `null`:
```
var a: String = "abc"
a = null // compilation error
val l = a.length // safe
```
##### Safe call
Để tương tác với một object có thể `null` 1 cách dễ dàng, sử dụng dấu `?` cho phép bạn lấy giá trị của object chỉ khi nó tồn tại, nếu không nó sẽ bỏ qua và chương trình sẽ vẫn chạy như thường:
```
val len = b?.length
```

#### Kế thừa và override
Mặc định class trong `Kotlin` đều là `final`(tức là không được phép kế thừa):
```
open class Base {
    open fun v() {}
    fun nv() {}
}
```
```
class Derived() : Base() {
    override fun v() {}
}
```
Trong class `final`(tức là không có từ khóa `“open”`) thì việc khai báo các hàm, thuộc tính `open` bị cấm.
Ở 1 lớp con khác, hàm được đánh dấu là `override` thì chính nó là `open`(lại được kế thừa từ lớp khác), nếu muốn không cho `override` thì lại sử dụng từ khóa `final`:
```
open class AnotherDerived() : Base() {
    final override fun v() {}
}
```
Trong `interface`, các member của `interface` mặc định là `“open”`:
```
interface B {
    fun f() { print("B") } // interface members are 'open' by default
    fun b() { print("b") }
}
```
`Kotlin` implement `interface`:
```
class C() : A(), B {
    // The compiler requires f() to be overridden:
    override fun f() {
    super<A>.f() // call to A.f()
    super<B>.f() // call to B.f()
    }
}
```
#### Data class
Trong `Java`, chúng ta thường tạo ra các class chỉ để chứa data (JavaBean). Những class này được `Kotlin `thể hiện bằng data class:
```
data class Money(val amount: Int, val currency: String)
```
Chỉ với 1 dòng code như trên sẽ tương đương với 1 class trong Java như sau:
```
public class JavaMoney {
    private int amount;
    private String currency;

    public JavaMoney(int amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    public int getAmount() {
        return amount;
    }

    public void setAmount(int amount) {
        this.amount = amount;
    }

    public String getCurrency() {
        return currency;
    }

    public void setCurrency(String currency) {
        this.currency = currency;
    }

    @Override
    public boolean equals(Object obj) {
        return super.equals(obj);
    }

    @Override
    public int hashCode() {
        return super.hashCode();
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    @Override
    public String toString() {
        return "JavaMoney{" +
                "amount=" + amount +
                ", currency='" + currency + '\'' +
                '}';
    }
}
```
`Kotlin` sẽ tự động thêm cho chúng ta các hàm utility như `equals()`, `hashCode()`, `toString()` và `copy()`.
#### Functional support (Lambda Expression)
Hiện tại thì `Android` cũng đã support `Java 8` với biểu thức `Lambda`, một điều rất tuyệt vời `more lambdas, more beautiful`.

### References
* [https://kotlinlang.org/docs/reference/faq.html](https://kotlinlang.org/docs/reference/faq.html)
* [https://kotlinlang.org/docs/reference/basic-syntax.html](https://kotlinlang.org/docs/reference/basic-syntax.html)
* [https://medium.com/@octskyward/why-kotlin-is-my-next-programming-language-c25c001e26e3](https://medium.com/@octskyward/why-kotlin-is-my-next-programming-language-c25c001e26e3)