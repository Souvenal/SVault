# Tag Dispatch

用空类型作为标签指导编译器选择不同重载，可在编译期完成分发。

## 应用场景

`std::unique_lock` 初始化使用 tag dispatch 而非枚举值实现不同行为。

## 原理

定义空类型标签重载不同函数：

```cpp
struct tag1 {};
struct tag2 {};

// 重载函数
void process(tag1) { /* 路径1 */ }
void process(tag2) { /* 路径2 */ }

// 调用时隐式创建标签
process(tag1{});
```

## 优势

- 编译期分发，无运行时开销
- 扩展性好，可添加新标签对应新行为

ref: https://iamsorush.com/posts/cpp-tag-dispatch/
