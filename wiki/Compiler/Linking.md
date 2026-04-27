# 链接方式

程序构建过程中，将编译生成的目标文件与库文件组合成可执行文件或共享库的机制。

## 编译时链接

```bash
gcc test.c -o test -lsomelib -L/path/to/lib
```

- `-l<lib>`：指定需要链接的库。默认优先搜索并使用**动态库**（`.so`/`.dylib`/`.dll`），然后回退到**静态库**（`.a`/`.lib`）
- `-L<path>`：指示编译器/链接器在指定路径下搜索库文件

### 链接顺序注意事项

链接器通常按命令行顺序解析符号。被依赖的库应放在依赖它的目标文件/库之后。例如，若 `main.o` 依赖 `libfoo`，则：

```bash
gcc main.o -lfoo -o program   # 正确
gcc -lfoo main.o -o program   # 可能报错：未定义引用
```

## 相关主题

- [[C++/_index|C++]] — C++ 语言相关编译特性
- [[cmake/_index|cmake]] — CMake 构建系统
- [[Make/_index|Make]] — GNU Make 构建工具
- [[xmake/_index|xmake]] — xmake 构建工具
