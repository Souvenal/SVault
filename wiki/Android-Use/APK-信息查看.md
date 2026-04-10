# APK 信息查看

分析 APK 包内容，获取元信息。

---

## Android Studio 分析

`Android Studio > Build > Analyze APK`

直接打开 APK 文件查看：
- 编译信息（Build ID、签名）
- 资源文件
- `AndroidManifest.xml`
- 依赖库

## AndroidManifest.xml

APK 的清单文件，记录关键元信息：

- **package**：应用包名
- **versionCode/versionName**：版本信息
- **permissions**：权限声明
- **components**：Activity、Service、BroadcastReceiver 等组件

```bash
# 通过 aapt 查看基本信息
aapt dump badging foo.apk
```

---

## 相关命令

- [[APK-安装]] — APK 安装方法
