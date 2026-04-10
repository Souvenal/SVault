# Nix-language

Nix 语言是纯函数式、惰性求值、动态类型的语言。

---

## 基础

- Nix 代码即 Nix 表达式
- 对表达式求值得到 Nix 值
- `.nix` 文件内容是一个表达式

## 命令

```shell
nix repl                           # 交互 REPL
nix-instantiate --eval default.nix  # 计算 nix 文件
```

## Nix 表达式

形式与 JSON 类似，区别是属性名无需引号、用分号分隔：

```nix
{
  string = "hello";
  integer = 1;
  float = 3.141;
  bool = true;
  null = null;
  list = [ 1 "two" false ];
  attribute-set = {
    a = "hello";
    b = 2;
  };
}
```

语法细节见 [官方文档](https://nix.dev/tutorials/nix-language)。

## Lookup Paths

`<nixpkgs>` 由尖括号包裹，返回文件路径，取决于 `builtins.nixPath`。

## 函数

函数值都是 `<LAMBDA>` 匿名函数，`x: x + 1` 冒号前是参数：

```nix
let
  f = x: x + 1;
in f 1;
```

Nix 函数只接受一个参数，多参数用柯里化处理。

## 函数库

### builtins

- `builtins.toString`
- `import`：接受文件路径，计算其中 Nix 表达式并返回值

### pkgs.lib

```nix
let
  pkgs = import <nixpkgs> {};
in
pkgs.lib.strings.toUpper "lookup paths considered harmful"
```

## 非纯性

通过非纯函数引入副作用进行构建。

### Paths

`"${./name}"` 字符串插值使用文件路径时，文件内容被复制到 Nix store：

```
/nix/store/<hash>-<name>
```

目录同样被复制，字符串成为 Nix 存储路径。

### Fetchers

求值时通过网络获取文件：

- `builtins.fetchurl`
- `builtins.fetchTarball`：自动解压缩
- `builtins.fetchGit`
- `builtins.fetchClosure`

```nix
builtins.fetchurl "https://github.com/NixOS/nix/archive/7c3ab5751568a0bc63430b33a5169c5e4784a0ff.tar.gz"
```

## 派生

派生（derivation）是 Nix 核心特性，使 Nix 能在文件系统上进行函数式编程。

`stdenv.mkDerivation` 封装了 `derivation` 非纯函数，隐藏构建过程。

## Shell 环境示例

`shell.nix`:

```nix
{ pkgs ? import <nixpkgs> {} }:
let
  message = "hello world";
in
pkgs.mkShellNoCC {
  packages = with pkgs; [ cowsay ];
  shellHook = ''
    cowsay ${message}
  '';
}
```

启动 nix-shell 时自动运行。

---

相关：[[Nix]] — 安装入门、nix-shell、垃圾清理
