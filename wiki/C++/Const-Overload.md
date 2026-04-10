# const 重载

## 形参 const 修饰的重载规则

```c++
void funcInt(int i) {}
void funcInt(const int i) {}
// 编译失败：int 可绑定到 int 和 const int，重载决议歧义
```

```c++
void funcChar(char* s) {}
void funcChar(const char* s) {}
// 编译成功：const char* 无法转换为 char*
```

```c++
void funcInt(int& i) {}
void funcInt(const int& i) {}
// 编译成功：引用本质上是指针，规则同上
```

## 类成员函数的 const 重载

```c++
class A {
public:
    auto foo() -> int { return m_value; };
    auto foo() const -> const int { return m_value; }

private:
    int m_value;
};
```

- 常量对象只能调用 const 版本
- 非常量对象优先使用非 const 版本
- 无非 const 版本时，const 对象也能调用 const 版本
