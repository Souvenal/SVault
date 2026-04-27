# Mach-O

Mach-O（Mach Object File Format）是苹果生态（macOS/iOS/watchOS/tvOS）的原生可执行文件格式，用于目标文件、可执行程序、动态库、插件和内核扩展。

> Mach-O 与 ELF 最本质的区别是什么？
> ELF 是"表驱动"的（程序头表 + 节头表），Mach-O 是"命令驱动"的（统一的 Load Commands 列表）。Mach-O 添加新特性只需定义新的 `LC_*` 命令类型，旧加载器自动跳过不认识命令，扩展性更好。

---

## 整体结构

三部分结构：

```
┌─────────────────┐
│  Mach-O Header  │  ← 文件标识、目标架构、文件类型
├─────────────────┤
│ Load Commands   │  ← 元数据：内存布局、依赖关系、入口点
├─────────────────┤
│ Data            │  ← Segments + Sections 实际内容
└─────────────────┘
```

| ELF 结构 | Mach-O 对应 | 核心区别 |
|----------|------------|----------|
| ELF Header | Mach-O Header | 功能类似，Mach-O 更简洁 |
| Program Header Table | `LC_SEGMENT` 加载命令 | ELF 独立表，Mach-O 是命令的一种 |
| Section Header Table | 嵌入在 `LC_SEGMENT` 命令中 | ELF 独立表，Mach-O 节信息跟在段描述后 |
| Sections | Sections | 概念一致，命名规则不同 |
| 无原生支持 | Fat Binary | Mach-O 原生支持多架构打包 |

---

## Mach-O Header

位于文件最开头，大小固定：
- 32 位：28 字节
- 64 位：32 字节

```c
// 64 位头部结构（mach_header_64）
struct mach_header_64 {
    uint32_t magic;      // 0xfeedfacf = 64位, 0xfeedface = 32位, 0xcafebabe = Fat Binary
    cpu_type_t cputype;  // CPU 类型（如 CPU_TYPE_X86_64, CPU_TYPE_ARM64）
    cpu_subtype_t cpusubtype;
    uint32_t filetype;   // MH_OBJECT / MH_EXECUTE / MH_DYLIB / MH_BUNDLE
    uint32_t ncmds;      // Load Commands 数量
    uint32_t sizeofcmds; // Load Commands 总大小
    uint32_t flags;      // PIE、代码签名等标志
    uint32_t reserved;
};
```

**filetype 常见值：**
- `MH_OBJECT`：目标文件（.o）
- `MH_EXECUTE`：可执行文件
- `MH_DYLIB`：动态库（.dylib）
- `MH_BUNDLE`：插件（.bundle）
- `MH_DYLINKER`：动态链接器（dyld）

---

## Load Commands

Mach-O 与 ELF 最大的设计差异。一个统一的命令列表描述所有元数据。

每个命令以相同头部开头：

```c
struct load_command {
    uint32_t cmd;     // 命令类型（LC_SEGMENT_64, LC_MAIN 等）
    uint32_t cmdsize; // 命令大小（含头部）
};
```

加载器的工作方式：
1. 读取 Mach-O Header，获取 `ncmds`
2. 按 `ncmds` 数量逐个读取命令
3. 认识的命令 → 执行对应操作
4. 不认识的命令 → 按 `cmdsize` 跳过

> 为什么这种设计扩展性好？
> ELF 添加代码签名需要在 ELF Header 加新字段，旧加载器不认识新文件。Mach-O 只需定义 `LC_CODE_SIGNATURE` 命令，旧加载器自动跳过，不影响加载。

### 核心命令类型

#### `LC_SEGMENT_64`（段描述）

对应 ELF 的 `PT_LOAD` 程序头。描述文件中的段如何映射到虚拟内存。

```c
struct segment_command_64 {
    uint32_t cmd;       // LC_SEGMENT_64
    uint32_t cmdsize;   // 包含所有节头的大小
    char segname[16];   // 段名，如 "__TEXT", "__DATA"
    uint64_t vmaddr;    // 虚拟内存起始地址
    uint64_t vmsize;    // 虚拟内存大小
    uint64_t fileoff;   // 文件中偏移
    uint64_t filesize;  // 文件中大小
    vm_prot_t maxprot;  // 最大内存保护权限
    vm_prot_t initprot; // 初始内存保护权限
    uint32_t nsects;    // 该段包含的节数量
    uint32_t flags;
};
```

内存保护权限：
- `VM_PROT_NONE` (0x00)：无权限
- `VM_PROT_READ` (0x01)：可读
- `VM_PROT_WRITE` (0x02)：可写
- `VM_PROT_EXECUTE` (0x04)：可执行

#### `LC_MAIN`（入口点）

对应 ELF 的 `e_entry`。

```c
struct entry_point_command {
    uint32_t cmd;       // LC_MAIN
    uint32_t cmdsize;
    uint64_t entryoff;  // 入口函数相对文件偏移
    uint64_t stacksize; // 主线程栈大小
};
```

#### `LC_LOAD_DYLIB`（动态库依赖）

对应 ELF `.dynamic` 节中的 `DT_NEEDED`。

```c
struct dylib_command {
    uint32_t cmd;       // LC_LOAD_DYLIB
    uint32_t cmdsize;
    struct dylib dylib; // 库名（offset 指向字符串）、时间戳、版本
};
```

#### 其他重要命令

| 命令 | 作用 | ELF 对应 |
|------|------|----------|
| `LC_SYMTAB` | 符号表和字符串表位置 | `.symtab`, `.strtab` |
| `LC_DYSYMTAB` | 动态符号表信息 | `.dynsym` |
| `LC_RPATH` | 动态库搜索路径（`@rpath`） | `RPATH` |
| `LC_LOAD_DYLINKER` | 指定动态链接器（`/usr/lib/dyld`） | `ld.so` |
| `LC_CODE_SIGNATURE` | 代码签名数据位置 | 无原生支持 |
| `LC_FUNCTION_STARTS` | 函数起始地址表 | 无原生支持 |

---

## Segments 和 Sections

**Segment（段）**：运行时加载单位，直接映射到虚拟内存。页对齐（macOS 4KB，iOS 16KB）。

**Section（节）**：链接时和调试时单位，段内细分。不需要页对齐。

命名规则：
- 段名：`__` 开头 + 全大写（`__TEXT`）
- 节名：`__` 开头 + 全小写（`__text`）
- 完整表示：`段名,节名`（`__TEXT,__text`）

### 标准段

#### `__PAGEZERO`

| 属性 | 值 |
|------|-----|
| 虚拟地址 | 0x00000000 |
| 大小 | 4GB（64位） |
| 文件大小 | 0（不占磁盘） |
| 权限 | 无权限（---） |

作用：捕获空指针解引用。访问地址 0 附近触发段错误。

> ELF 如何实现空指针保护？
> ELF 无原生支持，依赖操作系统在地址 0 处不映射页面。

#### `__TEXT`

权限：R-X（可读可执行）

| 节名 | 内容 | ELF 对应 |
|------|------|----------|
| `__text` | 机器代码 | `.text` |
| `__cstring` | C 字符串常量 | `.rodata` |
| `__stubs` | 动态链接桩（调用外部函数） | `.plt` |
| `__objc_methname` | Objective-C 方法名 | — |
| `__gcc_except_tab` | C++ 异常处理信息 | `.eh_frame` |

#### `__DATA`

权限：RW-（可读可写）

| 节名 | 内容 | ELF 对应 |
|------|------|----------|
| `__data` | 已初始化全局/静态变量 | `.data` |
| `__bss` | 未初始化全局/静态变量 | `.bss`（文件大小为 0） |
| `__la_symbol_ptr` | 懒加载符号指针表 | `.plt.got` |
| `__objc_classlist` | Objective-C 类列表 | — |
| `__mod_init_func` | C++ 静态构造函数 | `.init_array` |
| `__mod_term_func` | C++ 静态析构函数 | `.fini_array` |

#### `__LINKEDIT`

权限：R--（只读）

内容：链接器和动态链接器使用的元数据——符号表、字符串表、重定位信息、代码签名、函数起始地址表。

---

## Fat Binary（胖二进制）

Mach-O 原生支持多架构打包。同一个文件可在多种架构上运行。

```
┌──────────────────┐
│   Fat Header     │  ← magic = 0xcafebabe, nfat_arch = 架构数量
├──────────────────┤
│  Fat Arch 1      │  ← cputype, offset, size
├──────────────────┤
│  Fat Arch 2      │
├──────────────────┤
│  ...             │
├──────────────────┤
│  Mach-O for A    │  ← 完整 Mach-O 文件
├──────────────────┤
│  Mach-O for B    │
└──────────────────┘
```

```c
struct fat_header {
    uint32_t magic;      // FAT_MAGIC (0xcafebabe) 或 FAT_MAGIC_64
    uint32_t nfat_arch;  // 包含的架构数量
};

struct fat_arch {
    cpu_type_t cputype;
    cpu_subtype_t cpusubtype;
    uint32_t offset;     // 该架构 Mach-O 在文件中的偏移
    uint32_t size;       // 该架构 Mach-O 的大小
    uint32_t align;      // 对齐要求
};
```

---

## 加载过程

分两阶段，由两个加载器分工处理不同类型的命令。

### 阶段一：内核加载器

目标：创建进程上下文，建立地址空间。

处理的命令：
- `LC_SEGMENT_64`：将文件数据映射到虚拟地址
- `LC_MAIN`：设置入口点
- `LC_LOAD_DYLINKER`：加载 dyld
- `LC_CODE_SIGNATURE`：验证代码签名

### 阶段二：dyld（动态链接器）

目标：解析外部符号，完成动态链接。

处理的命令：
- `LC_LOAD_DYLIB`：加载依赖的动态库
- `LC_RPATH`：设置库搜索路径
- `LC_SYMTAB` / `LC_DYSYMTAB`：解析符号
- `LC_LAZY_LOAD_DYLIB`：延迟加载库
- `LC_FUNCTION_STARTS`：函数起始地址表（异常处理、性能分析）

dyld 完成所有重定位后，调用 `main()`。

---

## ELF vs Mach-O 核心区别

| 特性 | ELF | Mach-O |
|------|-----|--------|
| 元数据组织 | 两个独立表（程序头表 + 节头表） | 统一的加载命令列表 |
| 扩展性 | 修改表结构，破坏兼容 | 定义新命令类型，旧加载器跳过 |
| 入口点 | `e_entry` 字段 | `LC_MAIN` 命令 |
| 多架构 | 无原生支持 | Fat Binary 原生支持 |
| 动态链接器 | `ld.so` | `dyld` |
| 符号命名空间 | 扁平 | 两级（库名 + 符号名） |
| 空指针保护 | 无原生支持 | `__PAGEZERO` 段原生支持 |

---

## 常用分析工具

| 工具 | 作用 | ELF 对应 |
|------|------|----------|
| `otool -h` | 查看 Mach-O Header | `readelf -h` |
| `otool -l` | 查看所有 Load Commands | `readelf -l` |
| `otool -tV` | 反汇编 `__text` 节 | `objdump -d` |
| `otool -L` | 查看依赖的动态库 | `ldd` |
| **MachOView** | 图形化查看完整结构 | — |
| **lipo** | 提取/合并 Fat Binary 架构 | — |

---

## 双向链接

- [[Compiler/Linking]] — 链接方式：静态链接、动态链接
- [[Linux-Dev/动态链接库]] — Linux 动态链接库管理
- [[Mac-Use/_index]] — macOS 系统使用
