### 关闭连接

---

#### 1、关闭连接

- 显示地关闭Channel是个好习惯，但不是必须的，在Connection关闭的时候，channel也会自动关闭
- Connection和Channel所具备的生命周期如下：
  - Open: 开启状态，代表当前对象可以使用
  - Closing：正在关闭状态。当前对象被显式地通知调用关闭方法（shutdown）,这样就会产生了一个关闭请求让其内部对象进行相应的操作，并等待这些关闭操作的完成
  - Closed：已经关闭状态。当前对象已经接收到所有的内部对象已经完成关闭动作的通知，并且其也关闭了自身
- getCloseReason方法可以让你知道对象关闭的原因；isOpen方法检测对象当前是否处于开启状态；close方法显式地通知当前对象执行关闭操作
- ShutdownSignalException提供了多个方法来分析关闭的原因。isHardError方法可以知道是Connection还是Channel的错误；getReason方法可以获取cause相关的信息