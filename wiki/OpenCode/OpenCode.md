# OpenCode

终端 AI 助手工具。

---

## 安装

```shell
npm i -g opencode-ai
```

## 添加 API

```shell
# 直接选择
opencode auth login

# 进入 opencode 后选择
opencode
/connect
```

交互界面中选择 provider 和 api-key。

## 键位绑定

参照：https://opencode.ai/docs/keybinds

### shift+enter

Windows Terminal 改绑 shift+enter 有特殊设置。

VSCode 需要添加：

```text
# settings.json
    "terminal.integrated.sendKeybindingsToShell": false,

# keybindings.json
    {
        "key": "shift+enter",
        "command": "workbench.action.terminal.sendSequence",
        "args": {
            "text": "\u001B\u000A"
        },
        "when": "terminalFocus"
    },
```
