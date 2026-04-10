# Keg-Only

macOS 自带某些软件，使用 brew 下载时通常不会放在 `/opt/homebrew/bin` 下，而是符号链接到 `/opt/homebrew/opt/xxx` 下，避免影响系统环境。

此时需要手动修改 PATH，或创建软链接。

---

## 示例

```
bison is keg-only, which means it was not symlinked into /opt/homebrew,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have bison first in your PATH, run:
  echo 'export PATH="/opt/homebrew/opt/bison/bin:$PATH"' >> ~/.zshrc
```

## 解决方法

1. 手动添加到 PATH
2. 创建软链接到 `/opt/homebrew/bin`
