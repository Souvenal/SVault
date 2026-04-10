# Formula 和 Cask

Homebrew 两种包类型。

---

## Formula

下载源码解压，`./configure.sh && make install`，处理依赖，自动配置环境变量。用来安装命令行工具和第三方库。

## Cask

下载已编译好的应用包（.dmg/.pkg）并解压，放在 Caskroom 目录。省去步骤，用来安装带界面的应用。

## 查找优先级

`brew install` 优先查找 formula 版本，如果找不到再去寻找 cask 版本。

---

## 命令示例

```bash
brew install --formula some-tool    # 仅安装 formula
brew install --cask some-app       # 仅安装 cask
```
