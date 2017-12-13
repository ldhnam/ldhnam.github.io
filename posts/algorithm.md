## Given an array of integer, every element appears twice except for one. Find that single one.

**Example**
```
Input: [1 2 2 3 1]
Output: 3
```

Solution:
Trick: Use bitwise `XOR`

## Cho 2N + 3 điểm phân biệt trên mặt phẳng, không có 4 điểm nào cùng nằm trên một đường tròn, không có 3 điểm nào thẳng hàng. Tìm một đường tròn sao cho có đúng N điểm nằm trong, N điểm nằm ngoài đường tròn

```
yêu cầu đặt ra là chọn ra 3 điểm, vẽ đường tròn qua đó, chia mặt phẳng làm 2 miền, n điểm nằm trong, n điểm nằm ngoài đường tròn
Solution rất đơn giản: hãy chứng minh nếu ta chọn 1 cạnh bất kì của đa giác lồi bao 2n+3 điểm này
1 cạnh bất kì
thì đường tròn cần tìm luôn đi qua cạnh đó
hoặc ta luôn có thể tìm một đường tròn đi qua cạnh đó
do cạnh của bao lồi tạo thành bởi 2 điểm, nên số điểm còn lại là 2n+1, và do đặc tính của bao lồi nên 2n+1 điểm này sẽ nằm cùng phía so với đường thẳng đi qua cạnh trên
đặc tính tiếp theo của đường tròn là cung và số đo cung (tính bằng góc)
Nếu đương tròn O nào đó đi qua 3 điểm ABC
thì cung BC sẽ có số đo tính bằng số đo góc BOC = góc BAC * 2
số đo góc càng lớn thì tâm của đường tròn càng gần với cạnh BC
từ nhận xét trên, ta làm một động tác nhỏ như sau
2N+1 điểm cho 2N + 1 số đo góc so với cạnh được chọn
sắp xếp số đo góc này tăng dần (edited)
chọn ra điểm có số đo góc ở vị trí thứ N + 1 sau khi sắp xếp như trên
đây chính là điểm còn lại cần tìm
những điểm có số đo góc nhỏ hơn điểm N+1 này sẽ nằm ngoài đường tròn
những điểm có số đo góc lớn hơn điểm N+1 này sẽ nằm trong đường tròn
và có đúng N điểm như thế cho mỗi trường hợp
```

![](https://imgur.com/a/0eSw2)

## Cho một thành phố là hình vuông kích cỡ 10x10 đơn vị. Có 20 quả bom rơi, mỗi quả tạo ra thiệt hại trong bán kính R. Hỏi tổng diện tích không bị thiệt hại là bao nhiêu, độ chính xác 3 chữ số sau dấu phẩy.

```
dễ dàng chứng minh là chẳng có công thức toán học nào  cho bài này cả, nó vô cùng phức tạp nếu số quả thiên thạch tăng lên
vậy có một cách tiếp cận thế này:
giả lập vi phân
cách tiếp cận của vi phân là chia nhỏ epsilon đủ nhỏ, khi đó diện tích tại một vùng sẽ tính bằng đạo hàm tại vùng đó
nhân với epsilon
cách tiếp cận như vậy, tiếp theo là xử lý
1. Làm sao để chọn ra epsilon đủ nhỏ
2. Làm sao để chạy ra kết quả nhanh khi epsilon đủ nhỏ
Từ hai luận điểm trên, nếu epsilon quá nhỏ thì thời gian chạy sẽ lâu
và ngược lại
do đó cần một hướng tiếp cận khác
đó là chặt nhị phân
chặt nhị phân 2D
chia hình vuông làm 4 hình vuông nhỏ
nếu có bất kì hình vuông nhỏ nào nằm trọn vẹn trong một đường tròn
thì bỏ qua không chia tiếp
nếu không nằm trọn vẹn trong bất kì nhưng vẫn cắt một đường tròn nào đó thì chia tiếp
Tóm lại:
a. Nếu hình vuông nào đó không giao với bất kì đường tròn nào thì tính vào tổng, không chia nữa
b. Nếu hình vuông đó giao cắt thì chia tiếp làm 4 hình vuông con và tính với 4 hình vuông con đó
khi hình vuông con đủ nhỏ, ví dụ chiều dài rộng chỉ nhỏ hơn 1e-9
stop không chia nữa
```

## Cho N số nguyên tìm tập con không rỗng của N số nguyên này có tổng chia hết cho N

```
Gọi S(i) = SUM(A1 -> Ai)
Nếu tồn tại i nào đó mà S(i) % N == 0 --> đây là một đáp án
Nếu không tồn tại suy ra toàn bộ các S(i) chia cho N đều dư từ 1 đến N-1
lại có N số S như vậy --> có 2 cái S nào đó đồng dư khi chia cho N
S(i1) và S(i2) nào đó chẳng hạn
lấy S(i2) - S(i1) = SUM(i1+1 -> i2) % N
vậy tổng các số từ vị trí i1+1 tới i2 là chia hết cho N
```
