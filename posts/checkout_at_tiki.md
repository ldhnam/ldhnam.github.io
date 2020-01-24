# Checkout at Tiki

Vậy là sau hơn 1 năm gắn bó và làm việc ở Tiki ở vị trí Software Engineer (SE) cụ thể hơn là Back-End Engineer thuộc Checkout and Payment team. Đây là bài blog đầu tiên của mình sẽ chia sẻ business và technical của một hệ thống lớn E-Commerce ở Việt Nam về các vấn đề Cart Processing, Promotion, Payment, Deal Processing, Order Routing, Order Processing.

## 1. Checkout

 Có thể hiểu đơn giản Checkout & Payment là một flow đi từ lúc bạn add sản phẩm vào giỏ hàng và thanh toán, đây cũng chính là thành phần quan trọng nhất trong một hệ thống E-Commerce. Dưới đây là checkout flow ở Tiki:

 ![Checkout Flow Detail at Tiki](../img/checkout-flow.png)

 Chính vì vậy Checkout phải đáp ứng các requirement sau:
  * Single responsibility, high availability, low latency.
  * Scaleable, reuseable, extendable, maintainable.
  * Support non-blocking, asynchronous, muli-threaded when needed.
  * Well-tested.
  * Consistency

#### Cart Processing

Cart Processing là một component cực kỳ phức tạp bao gồm: insert/update/delete sản phẩm, calculate, apply promotion, routing order, iventory. Sau đây mình sẽ giải thích chi tiết từng component:

#### 1.1 Add Product to Cart

Khi user đã add product to cart thì hệ thống sẽ gọi sang **Pegasus Service** để retrive all info. **Pegasus Service** là một service high permomance get product API và phải đáp ứng các requirement sau:
  * Zero downtime
  * TP99 < 10ms

Sau khi đã get được info của product thì hệ thống sẽ đi query xem sản phẩm vừa add có được add free gift nào không (**Catalog Free Gift**) và được add vào **Quote** object, sau đó hệ thống sẽ tìm **Quote** đang active trên **MongoDB** sau đó sẽ upsert **Quote** vào **MongoDB**.

Sau khi user đã có **Quote** thì tiếp theo sẽ apply promotion.

#### 1.2 Apply Promotion

Promotion ở Tiki rất là complex vì không chỉ đơn giản là apply coupon xong nhận được discount mà có thể nhập coupon vừa được discount vừa được free gift, hoặc sản phẩm đó được tặng gift (**Promotion Gift**) nào.

Sau khi user đã có **Quote** thì hệ thống get ra toàn bộ promotion đang active và đi qua bộ validator để check và apply promotion như: reward Tiki Xu, apply discount (fixed price, percent, free gift...).

#### 1.3 Select Shipping Address

Bước này chỉ là get toàn bộ thông tin shipping address của 1 user lên, user có thể update/delete shipping đó trước khi tới payment.

#### 1.4 Payment

Đây là step cuối cùng của checkout, ở step này hệ thống sẽ gọi sang **Order Routing Service** để distribute các items trong **Quote** về đúng kho và phân chia package ví dự như: Normal, TikiNOW, Normal and TikiNOW thành những shipping plan.

#### Payment

Có thể nói đây chính là phần quan trọng của Checkout & Payment vì liên quan đến tiền bạc, mà đã dính đến tiền bạc thì có rất nhiều vấn đề xảy ra nếu như hệ thống xử lý không tốt. Vì thế phải đáp ứng đủ các yêu cầu sau:

 * Extendable
 * Consistency