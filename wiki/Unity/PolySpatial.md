# PolySpatial

Unity 在 Apple Vision Pro 上的混合现实开发配置与功能模块。

---

## 开发环境配置

1. 切换到 VisionOS 平台
2. 配置好 XR Plugin Management
3. 导入 Unity PolySpatial Samples
4. 下载 TMP（TextMeshPro）
5. MR 功能需要 XR Simulation
6. XR Interaction Toolkit 需要手动下载

---

## 构建平台

build 时选择发布目标：
- **Device SDK** — 真机
- **Simulator SDK** — 模拟器

---

## Play to Device

使用 Xcode 进入构建目录：
1. 选择同一局域网的设备
2. `Signing & Capabilities → All → 勾选 Automatically manage signing`
3. 配置 Team

真机串流下载 Play to Device Host：https://testflight.apple.com/join/15PwZO6I

串流到真机时需要**取消 XR Simulation**，改为 PolySpatial XR。

---

## 功能模块一览

| 模块 | 功能描述 |
|------|---------|
| **Targeted Input** | 点击触发 |
| **uGUI** | Unity UI 接口（滑动条、Toggle、Button、键盘输入） |
| **Input Debug** | 触碰 / 捏 / 注视信息获取 |
| **Object Manipulation (XRI)** | 对物体抓取 |
| **Character Navigation** | 控制航标 + 体积相机 |
| **Mixed Reality (AR Kit)** | 获取手部坐标，检测手势 + 视线碰撞点信息 |
| **Image Tracking** | 识别数字图像 |
| **Swift UI** | 调用 Swift UI 创建对象 |
| **Meshing (AR Kit)** | 环境扫描：贴图 / 剔除（遮挡）/ 网格 + 生成物体可与环境碰撞 |
| **Portal** | 蒙版实现沉浸窗口（如恐龙体验） |
| **Particles** | 粒子效果 |
| **Lights** | 动态打光渲染 |
| **Blendshapes** | 动态物体 |
| **Resizable Volumes** | 物体是否随窗体大小变化可控 |
| **Billboard Component** | 追踪视线的面板 |
