# Mole

macOS 磁盘清理工具。

ref: https://github.com/tw93/Mole

```bash
brew install mole
```

---

## 命令

| 命令 | 功能 |
|------|------|
| `mo` | Interactive menu |
| `mo clean` | Deep cleanup |
| `mo uninstall` | Remove apps + leftovers |
| `mo optimize` | Refresh caches & services |
| `mo analyze` | Visual disk explorer |
| `mo status` | Live system health dashboard |
| `mo purge` | Clean project build artifacts |

### Touch ID 配置

```bash
mo touchid  # Configure Touch ID for sudo
```

### 其他

```bash
mo completion   # Setup shell tab completion
mo update       # Update Mole
mo remove       # Remove Mole from system
mo --help       # Show help
mo --version    # Show installed version
```

### 选项

```bash
mo clean --dry-run           # Preview the cleanup plan
mo clean --whitelist          # Manage protected caches
mo optimize --dry-run        # Preview optimization actions
mo optimize --whitelist      # Manage protected optimization rules
mo purge --paths             # Configure project scan directories
```
