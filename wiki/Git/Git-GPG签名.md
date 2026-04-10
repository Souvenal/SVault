# Git GPG 签名

使用 GPG 对 git commit 进行签名，验证提交者身份。

---

## 配置步骤

### 1. 配置 GPG 程序

```shell
git config --global gpg.program gpg
```

### 2. 设置签名密钥

```shell
# 查看可用密钥
gpg --list-secret-keys --keyid-format=long

# 设置签名密钥（使用密钥 ID）
git config --global user.signingKey <KEY_ID>

# 可选：自动签名所有提交
git config --global commit.gpgSign true
```

### 3. 配置 GPG TTY（macOS/Linux）

```shell
# 添加到 ~/.zshrc 或 ~/.bashrc
export GPG_TTY=$(tty)
```

---

## GitHub 配置

### 添加 GPG Key

1. 导出公钥：
   ```shell
   gpg --armor --export <KEY_ID>
   ```

2. GitHub → Settings → SSH and GPG keys → New GPG key

3. 粘贴公钥内容，保存

### 验证签名

提交后，GitHub 会显示 "Verified" 标签，表示签名验证通过。

---

## 常见问题

> 签名失败：`gpg failed to sign the data`

检查 pinentry 配置。macOS 需要安装 pinentry-mac：

```shell
brew install pinentry-mac

# 配置 gpg-agent 使用 pinentry-mac
echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
gpgconf --kill gpg-agent
```

> 如何跳过某次签名？

```shell
git commit -S=false -m "message"
```

---

## 相关

- [[GPG/pinentry]] — GPG 密码输入程序
