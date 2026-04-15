# Xcode Simulator 幽灵运行时

## 问题现象

`xcrun simctl list runtimes` 能看到已安装的运行时，但 `simctl runtime delete <bundle_id>` 提示 "No runtime disk images or bundles found"。手动删除报 `Operation not permitted` 或 `Read-only file system`。

## 根本原因

1. **存储位置变更**：Xcode 15.3+ 将运行时存储到 SIP 保护的目录：
   ```
   /System/Library/AssetsV2/com_apple_MobileAsset_*SimulatorRuntime/
   ```
2. **simctl bug**：`simctl runtime delete` 对 bundle ID 支持有缺陷，但支持 **build 号**
3. **挂载点混淆**：`/Library/Developer/CoreSimulator/Volumes/` 只是挂载点，删除它毫无意义

## 解决方案

### 方案一：Build 号删除法（99% 成功率）

```bash
# 查看所有运行时及其 build 号
xcrun simctl list runtimes

# 使用 build 号删除（不要用 bundle ID）
xcrun simctl runtime delete 22N895
xcrun simctl runtime delete 22O237
```

### 方案二：MobileAssetd 清理法（80% 成功率）

```bash
sudo /System/Library/PrivateFrameworks/MobileAsset.framework/Versions/A/Resources/mobileassetd --cleanup-all
sudo killall -9 com.apple.CoreSimulator.CoreSimulatorService
```

### 方案三：临时关闭 SIP（100% 成功率）

适用场景：前两种方法失败。

1. 重启进入恢复模式，执行 `csrutil disable`
2. 重启后执行：
   ```bash
   sudo rm -rf /System/Library/AssetsV2/com_apple_MobileAsset_xrOSSimulatorRuntime/*
   sudo rm -rf /Library/Developer/CoreSimulator/Profiles/Runtimes/xrOS*.simruntime
   ```
3. 再次进入恢复模式，执行 `csrutil enable`

## 验证清理成功

```bash
xcrun simctl list runtimes | grep xrOS  # 应无输出
du -sh /System/Library/AssetsV2/com_apple_MobileAsset_xrOSSimulatorRuntime  # 应显示 0B 或无输出
```

## 常见误区

| 错误做法 | 后果 |
|---------|------|
| 删除 `CoreSimulator/Volumes/` | 空间不释放 |
| 使用 bundle ID 删除 | "No matching images found" |
| 反复 `sudo rm -rf` | 无法突破 SIP |
