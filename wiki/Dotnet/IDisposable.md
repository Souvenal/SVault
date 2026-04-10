# IDisposable

.NET 提供的接口，用于显式释放非托管资源。只需实现 `Dispose()` 方法。

---

> 为什么要显式释放？
> 非托管资源（文件句柄、数据库连接、网络连接等）不受 GC 管理，需要手动释放。

---

ref: https://zhuanlan.zhihu.com/p/244894004

[这篇文章](https://zhuanlan.zhihu.com/p/244894004)详细介绍了 IDisposable 的完整实现模式，包括终结器、Dispose 模式、异常处理等。
