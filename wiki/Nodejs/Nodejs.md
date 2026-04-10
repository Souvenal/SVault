# Node.js

JavaScript 运行时，主要运行 JS 代码。对非 JS 程序的调用方式：

| 方式 | 说明 | 示例 |
|------|------|------|
| 原生模块 (.node) | C/C++ 编译的二进制文件 | `require('bcrypt')` |
| WASM | WebAssembly 模块 | `WebAssembly.instantiate()` |
| JSON | 直接当模块加载 | `require('./config.json')` |
| child_process | 调用其他程序 | `exec('python script.py')` |

## 原生模块示例

```js
const bcrypt = require('bcrypt'); // 内部包含编译后的 C++ 代码
```

## WASM 示例

```js
const wasmModule = await WebAssembly.instantiate(fs.readFileSync('./file.wasm'));
```

## child_process 示例

```js
const { exec } = require('child_process');
exec('python script.py');
```

## npm 包的底层实现

很多 npm 包安装时会执行 `node-gyp rebuild` 编译 C++ 代码：

- **sharp** — JS 封装 + C++ libvips
- **sqlite3** — JS + C 语言 SQLite 引擎
- **esbuild** — Go 编译成二进制，JS 只是 CLI 包装
