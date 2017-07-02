![Android Studio](/img/androidstudio3.png)

Việc nâng lên Android Studio 3.0 sẽ làm cho việc build các dự án với nhiều module nhanh hơn rất nhiều, nhưng nó cũng có nghĩa là API của Gradle không may đã bị thay đổi.

Bài đăng trên blog này sẽ trình bày chi tiết tất cả các lợi ích của thay đổi này và hướng dẫn bạn qua quá trình nâng cấp.

# Problem situation

Để hiểu được những hạn chế của hệ thống cũ của Gradle 2.0, hãy xem dự án sau với nhiều lớp module:

![Project](/img/project.png)

Nhìn vào module dưới cùng thì có hai thay đổi cơ bản bạn có thể thực hiện:

1. Implementation change: thay đổi nội bộ, không thay đổi các interface bên ngoài của module

2. Application binary interface (ABI) change: thay đổi các interface ở ngoài module

Lưu ý: Ở phần tiếp theo, tất cả các module được biên dịch lại sẽ được đánh dấu màu đỏ.

# Implementation change

Vì interface bên ngoài của module không thay đổi, Gradle sẽ chỉ biên dịch lại module đó. Tất cả các module dùng chính nó sẽ không bị ảnh hưởng.

![Project Implementation](/img/project_implementation.png)

Không có vấn đề gì trong tình huống này.

# ABI change

Tuy nhiên, khi interface bên ngoài của module thay đổi, các module dùng module đó cũng cần phải được biên dịch lại trực tiếp.

![Project Gradle 2.0](/img/project_gradle_2.0_step1.png)

Nhưng những module này có thể phơi bày ra các phần của module dưới cùng trực tiếp thông qua interface của riêng chúng! Vì vậy, để được an toàn, chúng cũng cần phải được biên dịch lại.

Do đó Gradle muốn hiệu quả sẽ cần phải biên dịch lại tất cả các module.

![Project Gradle 2.0 Final](/img/project_gradle_2.0_final.png)

Bây giờ chúng ta có một vấn đề lớn: một sự thay đổi code làm cho tất cả các module được biên dịch lại. Nguyên nhân gốc của điều này là Gradle không biết nếu bạn leak inteface của module thông qua một module khác hay không.

# Android Gradle plugin 3.0 để giải thoát

Android Gradle plugin mới nhất hiện yêu cầu bạn xác định rõ ràng nếu bạn leak interface của một module. Trên cơ sở đó nó có thể lựa chọn đúng về những gì nó cần biên dịch lại.

Như vậy, `compile` dependency đã bị deprecated và được thay thế bằng hai cái mới:

* `api`: Bạn leak interface của module này thông qua interface của riêng bạn, có nghĩa chính xác giống như `complie` dependency cũ.

* `implementation`: Bạn chỉ sử dụng module này trong internal và không bị leak thông qua interface của bạn.

Vì vậy, bây giờ bạn có thể nói rõ Gradle để biên dịch lại một module nếu interface của một module được sử dụng có bị thay đổi hay không.

```
dependencies {
  // Biên dịch lại module này và tất cả module bằng cách dùng cái này
  // khi legofy interface thay đổi
  api project(':legofy')

  // // chỉ biên dịch lại module này khi interface landscapevideocamera được thay đổi
  implementation project(':landscapevideocamera:1.0.0')
}
```

# Hướng dẫn migration

Về lý thuyết, bạn có thể chỉ cần thay thế tất cả `compile` dependency với `api` dependencies, nhưng điều đó vẫn sẽ làm cho mọi thứ được biên dịch lại:

![Project Gradle 2.0 Final](/img/project_gradle_2.0_final.png)

Vì vậy, cách tiếp cận tốt hơn là thay thế tất cả các `compile` dependencies với `implementation` dependencies.

Và chỉ khi bạn leak một interface của module, bạn nên sử dụng `api`. Điều đó sẽ làm cho việc biên dịch lại ít hơn nhiều.

![Project Gradle 2.0](/img/project_gradle_2.0_step1.png)

Hy vọng rằng, điều này làm rõ sự mơ hồ giữa `api` và `implementation`, vì ` migration guide ` chính thức là khá khó hiểu.

# Các cấu hình dependency khác

Vì đã có một thay đổi đột phá, nhóm nghiên cứu cũng sử dụng cơ hội để cuối cùng đưa ra các cấu hình khác tên:

* `provided` thành `compileOnly`
* `apk` thành `runtimeOnly`

Giống như trước đây bạn cũng có thể kết hợp chúng với các build variants của bạn: `debugApi`, `testImplementation`, ...

# Các migration items khác

Android Studio 3.0 pack tons của cải tiến khác cuối cùng đã được giải quyết. Các yêu thích của tôi là: 

* Tất cả Google dependencies đều có sẵn thông qua Maven repository.
* Build variant hiện tại có thể được thông qua các library của bạn, loại bỏ sự cần thiết cho `publishNonDefault true`
* ...

Để biết thêm thông tin, hãy xem [migration guide](https://developer.android.com/studio/preview/features/new-android-plugin-migration.html) hoàn chỉnh.

# Tóm lại

Để build các project đa module nhanh hơn, Android Gradle cần một sự thay đổi API đột phá. Luôn luôn cố gắng sử dụng `implementation` dependency vì điều này sẽ làm cho ít module được biên dịch lại.

# Reference
[Implementation vs API dependency](https://jeroenmols.com/blog/2017/06/14/androidstudio3/)