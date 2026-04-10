# Wiki Voice Style - 模仿作者语言风格

## 触发条件

Use this skill when writing wiki content for the LLM-Wiki knowledge base. The goal is to match the author's note-taking style and eliminate AI-generated text patterns.

---

## 核心风格准则

### 1. 简洁直接，零废话

**禁止使用的过渡词和填充词：**
- ❌ "值得注意的是"
- ❌ "需要注意的是"
- ❌ "首先、其次、最后"
- ❌ "总的来说"
- ❌ "总之"
- ❌ "综上所述"
- ❌ "值得注意的是"
- ❌ "值得一提的是"
- ❌ "显而易见"
- ❌ "不难发现"
- ❌ "我们可以看到"

**正确做法：**
- ✅ 直接陈述事实
- ✅ 用代码/公式代替长篇解释
- ✅ 一句话说完，不展开

**示例对比：**

❌ AI 味道：
```
值得注意的是，Swapchain image 没有 TRANSFER_DST 标志，因此在使用时需要注意这一点，不能直接使用 vkCmdBlitImage。
```

✅ 作者风格：
```
这是 swapchain image 的 usage 问题。Swapchain image 没有 TRANSFER_DST 标志，不能用 vkCmdBlitImage。需要改用 vkCmdCopyImage 或其他方式。
```

---

### 2. 技术术语中英混用

**规则：**
- 关键技术概念、API 名称、工具名保留英文
- 解释性描述用中文
- 首次出现可简短说明，不展开教学式解释

**示例：**
- ✅ "BLAS (Bottom Level Acceleration Structure) 存储 GPU 几何数据..."
- ✅ "journald 使用二进制格式存储日志..."
- ✅ "这是 Scratch Buffer 的用法..."
- ❌ "底层加速结构（英文缩写为 BLAS，即 Bottom Level Acceleration Structure）是一种..."

---

### 3. Q&A 风格的自问自答

**用于解释关键概念：**

```markdown
> Scratch Buffer 是什么？
> 加速结构本身的存储需要申请显存，而构建过程的中间变量也需要申请显存，中间变量的存储就在 Scratch Buffer 中，通常大小是加速结构本身的一到两倍。

> 为什么 Scratch Buffer 不由驱动隐式分配？
> Vulkan 希望 Host 对 Device 内存有绝对控制权，这样更方便优化，可以对 Scratch Buffer 进行复用和池化等。
```

**使用场景：**
- 引入新概念
- 解释"为什么"
- 澄清常见误解

---

### 4. 代码与解释紧密结合

**代码注释风格：**
```cpp
// sizeInfo.accelerationStructureSize: AS最终大小
// sizeInfo.buildScratchSize: 构建时临时内存需求
VkAccelerationStructureBuildSizesInfoKHR sizeInfo;
```

**禁止：**
- ❌ 代码块后单独成段解释
- ❌ 先写三行解释，再贴代码
- ❌ 代码块前写"下面是一个示例："

**正确：**
- ✅ 代码注释直接在行内
- ✅ 代码后紧接一句话说明用途
- ✅ 如需展开，用 Q&A 格式

---

### 5. 数学公式与解释一体

**格式：**
```markdown
$$
NDF_{GGXTR}(n, h, \alpha)=\frac{\alpha^{2}}{\pi ((n\cdot h)^{2} (\alpha^{2} - 1) + 1)^{2}},
$$

其中 $k$ 是粗糙度的函数，对于直接光照和 IBL 光照有不同定义...
```

**禁止：**
- ❌ 公式前后加"公式如下："、"其表达式为："
- ❌ 单独成段解释公式每一项

---

### 6. 踩坑风格：问题 → 原因 → 方案

**标准结构：**

```markdown
## 问题场景

Validation Error: [ VUID-VkImageMemoryBarrier-oldLayout-01213 ] 
    vkCmdPipelineBarrier(): pImageMemoryBarriers[0].newLayout (VK_IMAGE_LAYOUT_TRANSFER_DST_OPTIMAL) 
    is not compatible with VkImage usage flags...

这是 swapchain image 的 usage 问题。Swapchain image 没有 TRANSFER_DST 标志，不能用 vkCmdBlitImage。

**解决方案：**
- 改用 vkCmdCopyImage
- 或创建单独的 image 用于 blit 操作
```

**禁止：**
- ❌ "在开发过程中，我们可能会遇到这样一个问题..."
- ❌ "遇到这个问题时不要慌张，可以按照以下步骤解决..."

---

### 7. 表格和结构化信息

**优先使用表格对比：**

```markdown
| 类型 | 后缀 | 作用 | 示例 |
|------|------|------|------|
| Service | .service | 后台服务 | nginx.service |
| Target | .target | 运行级别 | multi-user.target |
```

**使用 ASCII 图表：**

```
TLAS (Top Level)
    ↓
Instances (每个instance指向一个BLAS)
    ↓
BLAS (Bottom Level) - 几何图元（三角形/AABB）
```

---

### 8. 引用外部资源

**格式：**

```markdown
ref:
https://example.com/article

[这篇文章](https://example.com/article)描述了常见的D、F、G 函数的近似实现...
```

**禁止：**
- ❌ "详细内容可以参考以下链接："
- ❌ "更多信息请参阅："

---

### 9. 列表风格

**使用短列表：**

```markdown
- BLAS：存储几何数据
- TLAS：存储 instance 信息
- Scratch Buffer：存储构建中间变量
```

**禁止：**
- ❌ 每个列表项超过 2 行
- ❌ 嵌套列表超过 2 层

---

## 语言禁忌清单

### 绝对禁止的 AI 味道词汇

| 禁止 | 原因 |
|------|------|
| "值得注意的是" | 填充词，删掉后句子更清晰 |
| "需要注意的是" | 同上 |
| "首先、其次、最后" | 过度结构化，直接列举即可 |
| "总的来说" | 无意义的总结开场 |
| "综上所述" | 同上 |
| "值得一提的是" | 凡是值得一提的都值得直接说 |
| "显而易见" | 对读者假设太多 |
| "不难发现" | 居高临下的语气 |
| "我们可以看到" | 画蛇添足 |
| "需要强调的是" | 强调用内容本身，不需要声明 |
| "值得注意的是" | 重复警告 |
| "在实践中" | 废话，所有内容都是实践 |
| "从某种意义上说" | 模糊不清 |
| "换句话说" | 通常意味着上一句已经够清楚了 |
| "也就是说" | 同上 |

### 禁止的句式结构

| 禁止 | 替代方案 |
|------|---------|
| "这是一个...的问题" | 直接描述问题 |
| "对于...来说" | 删掉，直接说 |
| "在...的情况下" | 用"当..."或直接说 |
| "...是非常有必要的" | "...是必要的"或直接说 |
| "...起到了重要作用" | 直接说作用是什么 |

---

## 实战示例对比

### 示例 1：概念介绍

❌ AI 味道：
```
**Gamma 校正**是一个在计算机图形学中非常重要的概念。值得注意的是，显示器在进行成像时是在 Gamma 空间的，也就是说 0.5 的像素值实际显示为 $0.5^{gamma}$。这对于渲染管线来说是需要特别注意的。

首先，大多数普通用户是没有校准过的显示器；其次，他们也不知道 Gamma 校正的概念，所以大多数视觉素材都是已经预先矫正过的。
```

✅ 作者风格：
```
## Gamma 校正

显示器在进行成像时是在 Gamma 空间的，也就是说 0.5 的像素值实际显示为 $0.5^{gamma}$。

大多数普通用户是没有校准过的显示器、也不知道 Gamma 校正的概念，所以大多数视觉素材都是已经预先矫正过的。比如 JPEG 标准中，图像预先进行了 2.2 的伽马校正，所以 JPEG 图像不是线性图像，而着色器通常假定使用线性输入，所以着色器不应该使用 JPEG 作为纹理贴图。
```

### 示例 2：问题解决

❌ AI 味道：
```
在使用 Vulkan 进行开发时，我们可能会遇到 swapchain image 的 usage 问题。这是一个非常常见的问题，需要我们特别注意。

具体来说，Swapchain image 没有 TRANSFER_DST 标志，这意味着我们不能直接使用 vkCmdBlitImage。

针对这个问题，我们可以采取以下解决方案：
1. 改用 vkCmdCopyImage
2. 创建单独的 image 用于 blit 操作
```

✅ 作者风格：
```
## Swapchain layout

```
Validation Error: [ VUID-VkImageMemoryBarrier-oldLayout-01213 ]
    vkCmdPipelineBarrier(): pImageMemoryBarriers[0].newLayout (VK_IMAGE_LAYOUT_TRANSFER_DST_OPTIMAL) 
    is not compatible with VkImage usage flags...
```

这是 swapchain image 的 usage 问题。Swapchain image 没有 TRANSFER_DST 标志，不能用 vkCmdBlitImage。需要改用 vkCmdCopyImage 或其他方式。
```

### 示例 3：技术解释

❌ AI 味道：
```
> 为什么 Scratch Buffer 不由驱动隐式分配？

这是一个很好的问题。Vulkan 的设计哲学是希望 Host 对 Device 内存有绝对控制权。这样做的好处是什么呢？主要是为了方便优化，可以对 Scratch Buffer 进行复用和池化等操作。总而言之，这种设计给了开发者更多的控制权。
```

✅ 作者风格：
```
> 为什么 Scratch Buffer 不由驱动隐式分配？
> Vulkan 希望 Host 对 Device 内存有绝对控制权，这样更方便优化，可以对 Scratch Buffer 进行复用和池化等。
```

---

## 检查清单

写完每段 wiki 内容后，检查：

- [ ] 是否删掉了所有"值得注意的是"、"需要注意的是"？
- [ ] 是否删掉了"首先、其次、最后"等过渡词？
- [ ] 是否删掉了"总的来说"、"综上所述"等总结句？
- [ ] 代码块前后是否没有"下面是一个示例"之类的引导？
- [ ] 解释是否简洁直接，没有废话铺垫？
- [ ] 技术术语是否保留英文？
- [ ] 是否使用了 Q&A 格式解释关键概念？
- [ ] 每个段落是否都有实质内容，而不是过渡/填充？

---

## 最终原则

**如果删掉某句话后，意思没有变化 → 这句话就是废话 → 删掉它。**

**如果一句话只是在说"接下来我要说什么" → 直接说那件事。**

**如果一段话只是在铺垫 → 用一句话说完，或直接进入主题。**
