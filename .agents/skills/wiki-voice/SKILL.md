---
name: wiki-voice
description: >
  Author's writing style for LLM-Wiki content. Eliminates AI-generated text patterns, 
  enforces concise direct style. Use when writing wiki content or when user invokes
  /wiki-voice.
---

Writing style guide for wiki pages. Match author's note-taking style.

## Core Principles

### 1. Concise, Direct, Zero Fluff

**Forbidden filler words (Chinese):**
- ❌ "值得注意的是" (it's worth noting)
- ❌ "需要注意的是" (it should be noted)
- ❌ "首先、其次、最后" (first, second, finally)
- ❌ "总的来说" (in general)
- ❌ "总之" (in short)
- ❌ "综上所述" (in summary)
- ❌ "值得一提的是" (it's worth mentioning)
- ❌ "显而易见" (obviously)
- ❌ "不难发现" (it's not hard to see)
- ❌ "我们可以看到" (we can see)

**Do:**
- ✅ State facts directly
- ✅ Use code/formulas instead of long explanations
- ✅ One sentence, done

**Example:**

❌ AI-style:
```
值得注意的是，Swapchain image 没有 TRANSFER_DST 标志，因此在使用时需要注意这一点，不能直接使用 vkCmdBlitImage。
```

✅ Author-style:
```
这是 swapchain image 的 usage 问题。Swapchain image 没有 TRANSFER_DST 标志，不能用 vkCmdBlitImage。需要改用 vkCmdCopyImage 或其他方式。
```

---

### 2. Tech Terms Mixed Language

**Rules:**
- Key concepts, APIs, tool names in English
- Explanatory text in Chinese
- Brief explanation on first use, no tutorial style

**Examples:**
- ✅ "BLAS (Bottom Level Acceleration Structure) 存储 GPU 几何数据..."
- ✅ "journald 使用二进制格式存储日志..."
- ✅ "这是 Scratch Buffer 的用法..."
- ❌ "底层加速结构（英文缩写为 BLAS，即 Bottom Level Acceleration Structure）是一种..."

---

### 3. Q&A Style for Concepts

```markdown
> Scratch Buffer 是什么？
> 加速结构本身的存储需要申请显存，而构建过程的中间变量也需要申请显存，中间变量的存储就在 Scratch Buffer 中，通常大小是加速结构本身的一到两倍。

> 为什么 Scratch Buffer 不由驱动隐式分配？
> Vulkan 希望 Host 对 Device 内存有绝对控制权，这样更方便优化，可以对 Scratch Buffer 进行复用和池化等。
```

**Use for:**
- Introducing new concepts
- Explaining "why"
- Clarifying misconceptions

---

### 4. Code + Explanation Integrated

```cpp
// sizeInfo.accelerationStructureSize: AS最终大小
// sizeInfo.buildScratchSize: 构建时临时内存需求
VkAccelerationStructureBuildSizesInfoKHR sizeInfo;
```

**Forbidden:**
- ❌ Separate paragraph explaining code after block
- ❌ Three lines of explanation before code
- ❌ "Below is an example:" before code block

**Correct:**
- ✅ Inline comments in code
- ✅ One sentence after code explaining purpose
- ✅ Use Q&A for deeper explanation

---

### 5. Formulas + Explanation Together

```markdown
$$
NDF_{GGXTR}(n, h, \alpha)=\frac{\alpha^{2}}{\pi ((n\cdot h)^{2} (\alpha^{2} - 1) + 1)^{2}},
$$

其中 $k$ 是粗糙度的函数，对于直接光照和 IBL 光照有不同定义...
```

**Forbidden:**
- ❌ "Formula below:", "The expression is:" before formulas
- ❌ Separate paragraph explaining each term

---

### 6. Pitfall Style: Problem → Cause → Solution

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

**Forbidden:**
- ❌ "During development, we might encounter..."
- ❌ "Don't panic when you see this, follow these steps..."

---

### 7. Tables and Structured Info

```markdown
| 类型 | 后缀 | 作用 | 示例 |
|------|------|------|------|
| Service | .service | 后台服务 | nginx.service |
| Target | .target | 运行级别 | multi-user.target |
```

ASCII diagrams:
```
TLAS (Top Level)
    ↓
Instances (每个instance指向一个BLAS)
    ↓
BLAS (Bottom Level) - 几何图元（三角形/AABB）
```

---

### 8. External References

```markdown
ref: https://example.com/article

[这篇文章](https://example.com/article)描述了常见的D、F、G 函数的近似实现...
```

**Forbidden:**
- ❌ "For details, see the following link:"
- ❌ "For more information, refer to:"

---

### 9. List Style

Short lists:
```markdown
- BLAS：存储几何数据
- TLAS：存储 instance 信息
- Scratch Buffer：存储构建中间变量
```

**Forbidden:**
- ❌ List items over 2 lines
- ❌ Nested lists over 2 levels

---

## Forbidden Phrases Table

| Forbidden | Reason |
|-----------|--------|
| "值得注意的是" | Filler, clearer without it |
| "需要注意的是" | Same |
| "首先、其次、最后" | Over-structured, just list items |
| "总的来说" | Meaningless summary opener |
| "综上所述" | Same |
| "值得一提的" | If worth mentioning, just say it |
| "显而易见" | Assumes too much about reader |
| "不难发现" | Condescending tone |
| "我们可以看到" | Redundant |
| "需要强调的是" | Emphasize with content, not declaration |
| "在实践中" | Fluff, all content is practice |
| "从某种意义上说" | Vague |
| "换句话说" | Usually means previous sentence was clear enough |
| "也就是说" | Same |

### Forbidden Sentence Structures

| Forbidden | Alternative |
|-----------|-------------|
| "这是一个...的问题" | Just describe the problem |
| "对于...来说" | Delete, just say it |
| "在...的情况下" | Use "当..." or just say it |
| "...是非常有必要的" | "...is necessary" or just say it |
| "...起到了重要作用" | Just say what the role is |

---

## Practical Examples

### Example 1: Concept Introduction

❌ AI-style:
```
**Gamma 校正**是一个在计算机图形学中非常重要的概念。值得注意的是，显示器在进行成像时是在 Gamma 空间的，也就是说 0.5 的像素值实际显示为 $0.5^{gamma}$。这对于渲染管线来说是需要特别注意的。

首先，大多数普通用户是没有校准过的显示器；其次，他们也不知道 Gamma 校正的概念，所以大多数视觉素材都是已经预先矫正过的。
```

✅ Author-style:
```
## Gamma 校正

显示器在进行成像时是在 Gamma 空间的，也就是说 0.5 的像素值实际显示为 $0.5^{gamma}$。

大多数普通用户是没有校准过的显示器、也不知道 Gamma 校正的概念，所以大多数视觉素材都是已经预先矫正过的。比如 JPEG 标准中，图像预先进行了 2.2 的伽马校正，所以 JPEG 图像不是线性图像，而着色器通常假定使用线性输入，所以着色器不应该使用 JPEG 作为纹理贴图。
```

### Example 2: Problem Solving

❌ AI-style:
```
在使用 Vulkan 进行开发时，我们可能会遇到 swapchain image 的 usage 问题。这是一个非常常见的问题，需要我们特别注意。

具体来说，Swapchain image 没有 TRANSFER_DST 标志，这意味着我们不能直接使用 vkCmdBlitImage。

针对这个问题，我们可以采取以下解决方案：
1. 改用 vkCmdCopyImage
2. 创建单独的 image 用于 blit 操作
```

✅ Author-style:
```
## Swapchain layout

```
Validation Error: [ VUID-VkImageMemoryBarrier-oldLayout-01213 ]
    vkCmdPipelineBarrier(): pImageMemoryBarriers[0].newLayout (VK_IMAGE_LAYOUT_TRANSFER_DST_OPTIMAL) 
    is not compatible with VkImage usage flags...
```

这是 swapchain image 的 usage 问题。Swapchain image 没有 TRANSFER_DST 标志，不能用 vkCmdBlitImage。需要改用 vkCmdCopyImage 或其他方式。
```

### Example 3: Technical Explanation

❌ AI-style:
```
> 为什么 Scratch Buffer 不由驱动隐式分配？

这是一个很好的问题。Vulkan 的设计哲学是希望 Host 对 Device 内存有绝对控制权。这样做的好处是什么呢？主要是为了方便优化，可以对 Scratch Buffer 进行复用和池化等操作。总而言之，这种设计给了开发者更多的控制权。
```

✅ Author-style:
```
> 为什么 Scratch Buffer 不由驱动隐式分配？
> Vulkan 希望 Host 对 Device 内存有绝对控制权，这样更方便优化，可以对 Scratch Buffer 进行复用和池化等。
```

---

## Checklist

After writing wiki content, check:

- [ ] Removed all "值得注意的是", "需要注意的是"?
- [ ] Removed "首先、其次、最后" transitions?
- [ ] Removed "总的来说", "综上所述" summaries?
- [ ] No "Below is an example" before code blocks?
- [ ] Explanations concise, no fluff?
- [ ] Technical terms in English?
- [ ] Used Q&A format for key concepts?
- [ ] Each paragraph has substance, not filler?

---

## Final Principles

**If removing a sentence doesn't change meaning → it's fluff → delete it.**

**If a sentence just says "I'm about to say X" → just say X.**

**If a paragraph is just setup → one sentence or skip it.**
