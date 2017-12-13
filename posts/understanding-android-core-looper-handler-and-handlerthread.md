## English
The main thread in Android is built with a `Looper` and `Handlers`.
**What is the problem with Java thread?**
Java threads are one-time use only and die after executing its run method.
**Can we improve upon it?**
The Thread is a double edged sword. We can speed up the execution by distributing the tasks among threads of execution, but can also slow it down when threads are in excess. Thread creation in itself is an overhead. So, the best option is to have an optimum number of threads and reuse them for tasks execution.
**Model for thread reusability:**
1. The thread is kept alive, in a loop via it’s `run()` method.
2. The task is executed serially by that thread and is maintained in a queue (MessageQueue).
3. The thread must be terminated when done.
**What is the Android’s way of doing it?**
The above model is implemented in the Android via `Looper`, `Handler`, and `HandlerThread`. The System can be visualized to be a vehicle as in the article’s cover.
1. `MessageQueue` is a queue that has tasks called messages which should be processed.
2. `Handler` enqueues task in the `MessageQueue` using `Looper` and also executes them when the task comes out of the MessageQueue.
3. `Looper` is a worker that keeps a thread alive, loops through MessageQueue and sends messages to the corresponding handler to process.
4. Finally `Thread` gets terminated by calling Looper’s `quit()` method.
**Creating Looper and MessageQueue for a Thread:**
A thread gets a `Looper` and `MessageQueue` by calling `Looper.prepare()` after its running. `Looper.prepare()` identifies the calling thread, creates a `Looper` and `MessageQueue` object and associate the `thread` with them in `ThreadLocal` storage class. `Looper.loop()` must be called to start the associated looper. Similarly, the `looper` must be terminated explicitly through `looper.quit()`.
```
class LooperThread extends Thread {
      public Handler mHandler; 
 
      public void run() { 
          Looper.prepare();
 
          mHandler = new Handler() { 
              public void handleMessage(Message msg) { 
                 // process incoming messages here
                 // this will run in non-ui/background thread
              } 
          }; 
 
          Looper.loop();
      } 
  }
```
**Creating Handler for a Thread:**
A `Handler` gets implicitly associated with the thread that instantiates it via thread’s `Looper`, but we can explicitly tie it to a thread by passing the thread’s `looper` in the constructor of the `Handler`.
```
handler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        // process incoming messages here
        // this will run in the thread, which instantiates it
    }
};
```
Sending messages to the `MessageQueue` via `Handler` can be done by two modes:
1. `Message`: It is a class that defines various useful methods to deal with message data. To send an object we set the **obj** variable.
```
Message msg = new Message();
msg.obj = "Ali send message";
handler.sendMessage(msg);
```
2. `Runnable`: A runnable can also be posted in the `MessageQueue`. Ex: posting and running a task in the main thread.
```
new Handler(Looper.getMainLooper()).post(new Runnable() {
    @Override
    public void run() {
        // this will run in the main thread
    }
});
```

Send message đến `MessageQueue` thông qua `Handler` có thể được thực hiện bằng 2 cách:
1. `Message`: Đây là một class dùng để định nghĩa các method hữu ích khác nhau để xử lý với dữ liệu message. Để send một object chúng ta có thể set biến **obj**.
```
Message msg = new Message();
msg.obj = "Ali send message";
handler.sendMessage(msg);
```
2. `Runnable`: Một runnable có thể được post trong `MessageQueue`. Ví dụ: posting và running một task trên main thread.
```
new Handler(Looper.getMainLooper()).post(new Runnable() {
    @Override
    public void run() {
        // this will run in the main thread
    }
});
```


## Vietnamese
Main thread trong Android được build bằng Looper và Handlers.
**Vấn đề với Java thread là gì?**
Java threads chỉ được sử dụng một lần và die sau khi thực hiện xong method của nó.
**Chúng ta có thể improve chúng?**
Thread là một con dao hai mặt. Chúng ta có thể tăng tốc thực hiện bằng cách cách phân phối các task trong các thread thực hiện, nhưng cũng có thể làm chậm nó khi threads dư thừa. Tạo thread chính nó là một overhead. Vì vậy, lựa chọn tốt nhất là có một số lượng tối ưu của các thread và sử dụng lại chúng để thực hiện tasks.
**Model cho việc sử dụng lại thread**
1. Thread được kept alive (giữ), trong một loop thông qua method run() của nó.
2. Task được thực hiện liên tục bởi thread và được maintain trong một queue (MessageQueue).
3. Thread nên được terminate khi hoàn thành.
**Cách làm của Android là gì?**
Model trên được implement trong Android thông qua `Looper`, `Handler` và `HandlerThread`. Hệ thống được hình dung là một chiếc xe trong cover của bài viết.
1. `MessageQueue` là queue có nhiệm vụ gọi các message cần được xử lý.
2. `Handler` enqueue task trong `MessageQueue` sử dụng `Looper` và cũng thực hiện chúng khi task được đưa ra khỏi `MessageQueue`.
3. `Looper` là một worker có nhiệm vụ giữ thread luôn sống, loop qua `MessageQueue` và send các message đến `handler` tương ứng để thực hiện.
4. Cuối cùng, `Thread` được chấm dứt (terminated) bằng cách gọi method `quit()` của Looper.
**Tạo Looper và MessageQueue cho một Thread:**
Một thread sẽ nhận được một `Looper` và `MessageQueue` bằng cách gọi `Looper.prepare()` sau khi nó chạy. `Looper.prepare()` xác định thread gọi, tạo một `Looper` và `MessageQueue` object và liên kết `thread` với chúng, trong class lưu trữ `ThreadLocal`. `Looper.loop()` phải được gọi để start các `looper` liên quan. Tương tự, `looper` nên được terminated rõ ràng thông qua `looper.quit()`
```
class LooperThread extends Thread {
      public Handler mHandler; 
 
      public void run() { 
          Looper.prepare();
 
          mHandler = new Handler() { 
              public void handleMessage(Message msg) { 
                 // process incoming messages here
                 // this will run in non-ui/background thread
              } 
          }; 
 
          Looper.loop();
      } 
  }
```
**Tạo Handler cho một Thread:**
Một `Handler` nhận được một liên kết ngầm với thread đại diện nó thông qua `Looper` của thread, nhưng chúng ta có thể buộc nó một cách rõ ràng một thread bằng cách passing `looper` của thread trong constructor của `Handler`.
```
handler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        // process incoming messages here
        // this will run in the thread, which instantiates it
    }
};
```

