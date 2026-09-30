# 《板栗汁 20 秒 MG 动画广告》学习资料

这套资料以当前项目的真实文件、时间轴和渲染结果为基础，目标不是只解释“做了什么”，
而是让你能够独立完成一支同类 HyperFrames 广告片。

## 学习目标

完成本套资料后，你应该能够：

1. 从参考广告中拆解镜头节奏、转场、配色和文字动效。
2. 把 20 秒广告拆成可执行的时间轴与场景结构。
3. 用 HTML、CSS、SVG 和 GSAP 构建可逐帧渲染的动画。
4. 正确使用 HyperFrames 的 `data-*`、`class="clip"` 和单一暂停时间轴。
5. 处理本地图片、透明抠图、背景音乐、淡入淡出和音频混合。
6. 使用 `lint`、`check`、`keyframes`、`snapshot` 和最终渲染验证成片。
7. 遇到常见错误时，能独立定位并修复。

## 阅读顺序

本文档已经合并全部学习内容，建议按以下顺序阅读：

1. 参考广告拆解
2. HyperFrames 工程搭建
3. GSAP 时间轴与镜头表
4. 素材、音频、质检与渲染
5. 练习任务与故障排查
6. 镜头表

## 项目快照

- 成片：`banliz-20s-mg-ad.mp4`
- 画幅：`1920x1080`
- 帧率：`30fps`
- 时长：`20.000s`
- 视频编码：H.264
- 音频编码：AAC，48kHz，双声道
- 主合成：[index.html](../index.html)
- 运动断言：[index.motion.json](../index.motion.json)
- 设计规范：[frame.md](../frame.md)
- 项目意图：[BRIEF.md](../BRIEF.md)

## 本片的结构

| 时间 | 场景 | 主要任务 |
| --- | --- | --- |
| 0.00-3.25s | 开场 | 快色切、产品落位、短卖点闪现 |
| 3.25-7.20s | 原料 | 板栗图形、产品展示、“纯天然板栗” |
| 7.20-11.20s | 卖点 | 三组卖点快速轮播 |
| 11.20-15.50s | 高潮 | 斜切色带、产品放大、真材实料 |
| 15.50-20.00s | 收尾 | 多产品条、品牌弹出、slogan 锁定 |

## 四层项目文件

```text
BRIEF.md       为什么做、给谁看、必须满足什么
frame.md       颜色、字体、视觉规则
index.html     真正可渲染的动画合成
assets/        图片、音乐、GSAP 等本地资源
```

## 最重要的五条结论

1. 参考广告的核心不是某种固定模板，而是“高频变化 + 产品连续运动 + 最后快速锁定品牌”。
2. HyperFrames 的动画必须能由时间值唯一决定，不能依赖当前时钟、随机数、悬停或异步回调。
3. 每个合成只注册一个暂停的 GSAP 时间轴，根节点 ID 必须和注册键一致。
4. 产品入场可以用越界运动制造速度感，但最终状态必须回到安全画布内。
5. 先用 `check` 找客观问题，再用快照和成片判断审美问题，两者不能互相替代。

## 建议学习时长

| 阶段 | 时间 | 产出 |
| --- | --- | --- |
| 阅读参考拆解 | 15 分钟 | 能复述参考节奏 |
| 阅读工程规范 | 20 分钟 | 能搭出最小可渲染合成 |
| 阅读时间轴与 GSAP | 30 分钟 | 能解释每个场景的运动逻辑 |
| 阅读媒体与质检 | 20 分钟 | 能独立渲染和验证 |
| 完成练习 | 60-120 分钟 | 生成自己的短视频 |

---

# 01 参考广告拆解

## 参考文件信息

- 文件：`../video.mp4`
- 时长：`19.7333s`
- 分辨率：`1672x944`
- 帧率：`30fps`
- 视频编码：H.264
- 音频：AAC，48kHz，双声道

参考视频只用于分析，没有嵌入最终成片。

## 参考片的五段结构

### 0-4 秒：纯色场快速切换

画面不断切换玫红、绿色、蓝色、洋红等高饱和色场。产品一直存在，但位置、角度和大小
持续变化。这个阶段依靠硬切制造能量，不依赖复杂背景。

可迁移规则：

- 每 `0.45-0.7s` 至少发生一次视觉变化。
- 背景可以硬切，主体不要也跟着彻底重置。
- 产品始终保持可识别性。

### 4-10 秒：原料和产品交替出现

水果成组进入，左右分布，中间留出产品或文字。部分画面使用波浪线分隔上下区域。

可迁移规则：

- 原料不仅是装饰，还要承担“产品来自什么”的叙事任务。
- 同一时间只保留一个视觉主角，其他元素做节奏和陪衬。
- 原料可以左右对称，但运动速度不能完全一致。

### 10-14 秒：斜切色带和加速转场

画面从水平分区转为斜切色带，产品沿对角线进入或滑过。速度感来自大面积形状移动，
而不是单纯提高产品缩放速度。

可迁移规则：

- 大面积色带负责速度，产品负责识别。
- 产品位移和背景位移方向可以不同，产生空间层次。
- 斜切色带允许越出画布，但设计上要明确这是全屏背景层。

### 14-16.8 秒：产品阵列和品牌弹出

多个产品形成底部阵列，彩色斜带快速扫过，最后的品牌字从小尺寸快速弹出。

可迁移规则：

- 品牌字出场前要先有视觉蓄力。
- 品牌字不要逐字慢慢淡入，要缩短时间并制造尺寸反差。
- 品牌出现后需要明确停顿，让观众看清。

### 16.8-19.7 秒：收尾锁定

背景突然切成珊瑚红，只保留品牌字和产品。最后约 3 秒几乎不做新叙事，只保留呼吸和
轻微位置变化。

可迁移规则：

- 结尾不要持续加新信息。
- 产品在结尾仍然要活，但运动幅度小于高潮段。
- 留出品牌识别和 slogan 的阅读时间。

## 转场分类

| 类型 | 参考片中的位置 | 作用 | GSAP 实现思路 |
| --- | --- | --- | --- |
| 硬切 | 0-4s、4-10s | 制造节奏和“醒来”感 | `tl.set()` 切换色块透明度 |
| 斜切进入 | 10-16s | 加速、推出下一场景 | 全宽色带 `xPercent` 从画外进入 |
| 遮罩式文字 | 结尾 slogan | 让文字像被容器“抬出来” | `clipPath` + `y` |
| 品牌弹出 | 16s 左右 | 强制建立品牌记忆 | `scale 0.2 -> 1` + `back.out` |
| 最终硬切 | 16.8s | 把高潮压成稳定收尾 | 色层透明度切换，不做慢渐变 |

## 文字动效拆解

参考片的文字策略很克制：

- 前 14 秒几乎不让文字承担叙事，主要靠画面运动。
- 品牌字最后以极小状态出现，然后在约 `0.3-0.4s` 内放大。
- 品牌字稳定后，不再持续做大幅运动。
- 结尾阅读优先于装饰。

板栗汁广告采用了相同的逻辑，但将“MECO”替换为“板栗汁”，并增加三段短卖点。

## 配色逻辑

参考片使用高饱和平涂色场：

- 玫红
- 苹果绿
- 亮蓝
- 洋红
- 浅粉
- 珊瑚红

板栗汁不能直接照搬这些颜色，因为产品包装本身是栗棕色。项目采用：

- 栗棕：产品主色和深色底板
- 奶油色：主背景
- 叶绿：天然、植物感
- 琥珀黄：食品能量和亮度
- 珊瑚红：结尾品牌锁定
- 天蓝：少量冷色对比

## 运动速度拆解

| 阶段 | 运动速度 | 主要原因 |
| --- | --- | --- |
| 开场 | 最快 | 需要立即抓住注意力 |
| 原料 | 中速 | 要看清板栗和产品 |
| 卖点 | 快 | 连续输出信息 |
| 斜切高潮 | 最快 | 建立视觉峰值 |
| 收尾 | 慢 | 品牌和 slogan 需要阅读 |

## 复刻与原创的边界

应该复刻：

- 节奏密度
- 转场快慢
- 产品持续运动
- 品牌弹出的时间比例
- 结尾锁定方式

不应该复刻：

- 参考品牌
- 参考产品包装
- 参考水果素材
- 参考文案
- 参考视频画面本身

## 拆解练习

任选一支 15-30 秒广告，只记录以下数据：

1. 每个镜头开始和结束时间。
2. 每段的主色和背景变化数量。
3. 产品在哪些镜头持续存在。
4. 文字首次出现的准确时间。
5. 最终品牌停留多久。

如果这份表无法解释“为什么快、为什么慢、注意力去了哪里”，拆解还不够。

---

# 02 HyperFrames 工程搭建

## HyperFrames 是什么

HyperFrames 把 HTML、CSS、SVG、Canvas、WebGL 和 GSAP 组合成一个可逐帧寻址的
视频合成。渲染器按照时间值请求每一帧，所以动画状态必须能从时间值稳定重算。

这不是普通网页。不要把以下网页习惯直接带进来：

- 悬停、点击、滚动触发
- `Date.now()` 或 `performance.now()`
- 未固定种子的 `Math.random()`
- 无限循环 CSS/GSAP 动画
- 异步完成后才注册时间轴

## 四层文件

```text
BRIEF.md       为什么做
frame.md       长什么样
index.html     怎么运动
assets/        使用什么素材
```

当前项目的关键文件：

- [index.html](../index.html)
- [frame.md](../frame.md)
- [BRIEF.md](../BRIEF.md)
- [index.motion.json](../index.motion.json)
- [package.json](../package.json)
- [hyperframes.json](../hyperframes.json)

## 最小可渲染结构

```html
<div
  id="root"
  data-composition-id="main"
  data-width="1920"
  data-height="1080"
  data-duration="20"
>
  <section class="clip" data-start="0" data-duration="3">
    画面内容
  </section>
</div>

<script>
  const tl = gsap.timeline({ paused: true });
  window.__timelines["main"] = tl;
</script>
```

关键点：

- 顶层根节点不能包在 `<template>` 中。
- 根节点必须有 `data-composition-id`。
- `window.__timelines` 的键必须和根 ID 一致。
- 时间轴必须是 `paused: true`。
- 渲染长度由根 `data-duration` 决定。

## 场景即时间片段

本项目使用 5 个顶层 `.clip` 场景：

| 场景 | `data-start` | `data-duration` |
| --- | ---: | ---: |
| `#intro` | 0 | 3.25 |
| `#ingredients` | 3.25 | 3.95 |
| `#points` | 7.20 | 4.00 |
| `#diagonal` | 11.20 | 4.30 |
| `#final` | 15.50 | 4.50 |

场景在时间上可以紧邻，也可以重叠。重叠时需要明确哪一层应该覆盖哪一层。

## 为什么没有把场景拆成子合成

`check` 会建议把复杂场景拆成 `data-composition-src` 子合成，这是工程可维护性建议。
当前版本为了保持一份完整的 20 秒 GSAP 主时间轴，采用了单文件方案。

量产项目建议进一步拆分：

```html
<div
  data-composition-id="scene-intro"
  data-composition-src="compositions/intro.html"
  data-start="0"
  data-duration="3.25"
></div>
```

拆分后，每个场景在 Studio 时间线上会成为一行，便于修改。

## 媒体规范

### 图片

首屏和产品展示推荐使用项目内相对路径：

```html
<img src="assets/product-cutout.png" alt="板栗汁产品" />
```

不要依赖本地绝对 `file:///` 路径。HyperFrames 运行在浏览器环境下，相对路径更稳定，
也便于复制和发布。

### 音频

当前项目使用：

```html
<audio
  id="bgm"
  class="clip"
  src="assets/bgm.mp3"
  data-start="0"
  data-duration="20"
  data-volume="0.35"
  data-fade-in="0.6"
  data-fade-out="1.5"
  data-track-index="20"
></audio>
```

规则：

- 每个 `<audio>` 必须有 `id`。
- 音频必须独立于视频元素。
- 不要在 `<audio>` 上写 `crossorigin`。
- 淡入淡出用 `data-fade-in` 和 `data-fade-out`。
- 音频可以用 `data-volume` 设静态增益。

## 本地素材

最终项目中：

```text
assets/
  product.png          原始产品图
  product-cutout.png   透明抠图版本
  bgm.mp3              背景音乐
  vendor/
    gsap.min.js        固定版本 GSAP
```

将 GSAP 放在本地可以避免渲染依赖 CDN 网络。

## 为什么使用 `product-cutout.png`

原始 `product.png` 虽然带 RGBA 通道，但实际 Alpha 全是 `255`，白色背景完全不透明。
直接放在彩色背景上会出现白框。

处理方式：

```bash
npx hyperframes remove-background assets/product.png \
  --output assets/product-cutout.png \
  --device cpu
```

处理后 Alpha 范围确认包含透明值，可以安全叠在彩色场景上。

## 检查命令的含义

```bash
npx hyperframes lint
```

快速静态检查 HTML 和 GSAP 结构。适合每完成一次结构修改就运行。

```bash
npx hyperframes check --snapshots --samples 15
```

浏览器级完整检查，包括：

- 运行时错误
- 资源加载
- 布局溢出
- 文字重叠
- 对比度
- motion sidecar 断言

```bash
npx hyperframes keyframes . --json
```

列出时间轴上的关键帧、路径和组合运动，用于排查逐帧运动是否可寻址。

```bash
npx hyperframes render . --quality delivery --output banliz-20s-mg-ad.mp4
```

输出最终 MP4。

## 工程搭建步骤

1. 创建项目目录。
2. 写 `BRIEF.md` 和 `frame.md`。
3. 把图片和音频复制到 `assets/`。
4. 生成透明产品图。
5. 写 `index.html`。
6. 运行 `lint`。
7. 修复错误。
8. 运行 `check --snapshots`。
9. 看快照，修复审美问题。
10. 渲染 draft。
11. 检查成片帧和音频。
12. 渲染 delivery。
13. 用 FFprobe 验证时长、分辨率、帧率和音频流。

---

# 03 GSAP 时间轴与 20 秒镜头表

## 时间轴架构

整个视频只有一个暂停时间轴：

```js
const tl = gsap.timeline({ paused: true });
window.__timelines["main"] = tl;
```

所有场景动画、文字、产品、色带和装饰运动都添加到这个 `tl`。

这种结构的好处：

- 每一帧状态只由 `tl` 的时间决定。
- 浏览器预览和最终渲染使用同一套动画。
- 可以用 `check` 和 `keyframes` 自动扫描。

## 20 秒镜头表

| 时间 | 场景 | 画面 | 动画 | 文字 |
| --- | --- | --- | --- | --- |
| 0.00-3.25s | 开场 | 6 个高饱和背景色块 | 产品入场、旋转漂移、短标签弹出 | 板栗汽水、板栗原香等 |
| 3.25-7.20s | 原料 | 奶油底、幽灵字“栗” | 板栗图形错峰进入，产品从右侧进入 | 纯天然板栗 |
| 7.20-11.20s | 卖点 | 栗棕、叶绿、琥珀三色带 | 产品左右穿梭，三条卖点轮播 | 栗香浓郁、细腻顺滑、清爽不腻 |
| 11.20-15.50s | 高潮 | 多条斜切色带 | 色带横向扫入，产品 3D 倾斜进入 | 真材实料 |
| 15.50-16.78s | 品牌爆发 | 多色竖条和产品阵列 | 产品阵列弹入，品牌字快速放大 | 板栗汁 |
| 16.78-20.00s | 收尾 | 珊瑚红纯色 | 产品轻浮、光晕呼吸 | 板栗汁、纯天然板栗、一口清爽栗香 |

更细的机读版本见 [shot-list.csv](./shot-list.csv)。

## 模式一：硬切色场

开场不是淡入切换，而是直接切色：

```js
tl.set("#intro-bg-a", { opacity: 0 }, 0.48);
tl.set("#intro-bg-b", { opacity: 1 }, 0.48);
tl.set("#intro-bg-b", { opacity: 0 }, 0.94);
tl.set("#intro-bg-c", { opacity: 1 }, 0.94);
```

为什么用 `set`：

- 持续时间是 0，形成硬切。
- 时间值明确，可逆、可寻址。
- 不依赖 CSS 动画或页面加载状态。

注意：公开给 HyperFrames 的 `.clip` 场景不要直接做透明度动画。这里的 `#intro-bg-*`
是场景内部的背景层，所以安全。

## 模式二：父级负责入场，子级负责呼吸

产品入场和持续运动分成两层：

```html
<div id="intro-product-wrap">
  <img id="intro-product" src="assets/product-cutout.png" />
</div>
```

父级：

```js
tl.fromTo(
  "#intro-product-wrap",
  { opacity: 0, y: -320, scale: 0.72, rotation: -18 },
  { opacity: 1, y: 0, scale: 1, rotation: -8, duration: 0.64 },
  0.12,
);
```

子级：

```js
tl.fromTo(
  "#intro-product",
  { scale: 0.96, rotation: -4 },
  { scale: 1.045, rotation: 4, duration: 2.72 },
  0.2,
);
```

为什么要拆开：

- 避免同一个元素上同时出现互相覆盖的 transform 动画。
- 父级完成位移后，子级可以继续做呼吸。
- 运动层次更清楚。

## 模式三：多状态移动用有限 keyframes

产品不能只从 A 到 B，需要在多个位置之间持续运动：

```js
tl.to(
  ".intro-product-wrap",
  {
    keyframes: [
      { x: 88, y: 22, rotation: 7, duration: 0.46 },
      { x: -24, y: -8, rotation: -4, duration: 0.42 },
      { x: 122, y: 26, rotation: 9, duration: 0.44 },
    ],
    ease: "none",
  },
  0.78,
);
```

每个 keyframe 可以有自己的缓动。外层 `ease: "none"` 防止段与段之间重复缓动。

不要使用 `repeat: -1`。有限循环可以写成：

```js
repeat: Math.max(0, Math.floor(duration / cycleDuration) - 1)
```

## 模式四：文字遮罩升起

结尾 slogan：

```js
tl.fromTo(
  "#final-slogan",
  { opacity: 0, clipPath: "inset(0 0 100% 0)", y: 38 },
  {
    opacity: 1,
    clipPath: "inset(0 0 0% 0)",
    y: 0,
    duration: 0.48,
    ease: "power4.out",
  },
  17.18,
);
```

`clipPath` 负责“从文字框内露出”，`y` 负责轻微上移。两者组合比单纯淡入更有设计感。

## 模式五：品牌快速弹出

```js
tl.fromTo(
  "#final-brand-big",
  { opacity: 0, scale: 0.22, y: 150, rotation: -4 },
  {
    opacity: 1,
    scale: 1,
    y: 0,
    rotation: 0,
    duration: 0.42,
    ease: "back.out(2.15)",
  },
  16.04,
);
```

这段长度只有 `0.42s`，因为品牌识别需要冲击力，而不是缓慢淡入。

## 模式六：斜切色带扫入

```js
tl.fromTo(
  ".diag-band",
  { xPercent: -112, rotation: -18 },
  {
    xPercent: 0,
    rotation: -18,
    duration: 0.62,
    stagger: 0.09,
    ease: "expo.out",
  },
  11.24,
);
```

色带使用 `xPercent`，这样不同宽度都能按自身比例从画外进入。

## 缓动选择

| 缓动 | 使用场景 |
| --- | --- |
| `expo.out` | 产品落位、色带快速进入 |
| `power4.out` | 文本和图形快速减速 |
| `back.out(1.8)` | 标签、圆片、品牌字弹性弹出 |
| `sine.inOut` | 呼吸、漂浮、光晕 |
| `power2.in` | 元素退出 |

不要所有元素都用 `power2.out`。如果入场、退场、呼吸都相同，画面会像模板。

## 产品运动的设计原则

1. 进入要有明确方向，不要原地淡入。
2. 入场结束后进入第二段运动，而不是完全静止。
3. 旋转幅度控制在可识别范围。
4. 产品越界只能发生在入场和出场。
5. 结尾产品仍然有轻微呼吸，避免黑帧或死帧。

## motion sidecar

[index.motion.json](../index.motion.json) 用机器可读方式验证：

- 产品是否按时出现
- 重要文案是否按时出现
- 整个画面是否长时间冻结

```json
{
  "kind": "keepsMoving",
  "withinSelector": "#root",
  "maxStaticSec": 2
}
```

`check` 会在多个时间点采样真正的时间轴。如果某个元素入场晚于预期，或者整段视频超过
2 秒没有变化，会直接报错。

## 给新项目的镜头脚本模板

```text
0.0-3.0s  Hook      一个强视觉 + 主体进入
3.0-6.5s  Origin    原料/来源/工艺
6.5-10.5s Benefits  2-3 个卖点快速轮播
10.5-15.0s Peak     最大运动幅度和对比
15.0-20.0s Lockup   品牌 + 产品 + slogan
```

先写这张表，再写 GSAP。不要一边写代码一边决定故事。

---

# 04 素材、音频、质检与渲染

## 素材处理

### 产品图

原始产品图：

- 路径：`assets/product.png`
- 尺寸：`928x1104`
- 格式：PNG RGBA
- 问题：Alpha 通道全部为 `255`，背景不透明

处理结果：

- 路径：`assets/product-cutout.png`
- Alpha 范围：包含 `0`
- 用途：叠加到彩色背景

保留原图的意义：

- 方便重新处理
- 不破坏用户提供的源文件
- 动画只使用派生文件

### 背景音乐

- 路径：`assets/bgm.mp3`
- 时长：`24.192s`
- 采样率：`48kHz`
- 声道：双声道

背景音乐长于 20 秒，因此可以完整覆盖成片。

## HyperFrames 音频写法

```html
<audio
  id="bgm"
  class="clip"
  src="assets/bgm.mp3"
  data-start="0"
  data-duration="20"
  data-volume="0.35"
  data-fade-in="0.6"
  data-fade-out="1.5"
  data-track-index="20"
></audio>
```

含义：

| 属性 | 作用 |
| --- | --- |
| `data-start="0"` | 从成片 0 秒开始 |
| `data-duration="20"` | 播放 20 秒 |
| `data-volume="0.35"` | 静态音量 |
| `data-fade-in="0.6"` | 前 0.6 秒淡入 |
| `data-fade-out="1.5"` | 最后 1.5 秒淡出 |
| `class="clip"` | 作为 HyperFrames 时间片段管理 |

不要用 GSAP 直接改 `<audio>.volume`。HyperFrames 的音频属性由框架读取和混合。

## 背景移除

命令：

```bash
npx hyperframes remove-background assets/product.png \
  --output assets/product-cutout.png \
  --device cpu
```

本次遇到的故障：

```text
Download timed out after 30000ms
```

原因：

- 需要下载约 168MB 的 U2Net 模型
- CLI 默认下载超时为 30 秒
- GitHub 连接速度不足

解决：

1. 先确认 `onnxruntime-node` 已安装。
2. 使用支持重试和长超时的 `curl` 直接把模型下载到缓存。
3. 重新运行 `remove-background`。

模型缓存路径：

```text
~/.cache/hyperframes/background-removal/models/u2net_human_seg.onnx
```

## 质检工具

### lint

```bash
npx hyperframes lint --verbose
```

快速检查：

- 缺失的时间轴
- `.clip` 上不合法的动画
- CSS 与 GSAP 的 transform 冲突
- 媒体重复发现
- 全屏层首帧可见

### check

```bash
npx hyperframes check --snapshots --samples 15
```

检查内容：

- 运行时 JS 错误
- 资源加载失败
- 文本溢出
- 文本重叠
- 画布越界
- 对比度
- motion sidecar

本次最终结果：

- 运行时：0 错误
- 布局：0 错误
- 运动：0 错误
- 对比度：0 错误

### keyframes

```bash
npx hyperframes keyframes . --json
```

用于查看：

- 每个 tween 的开始和结束
- 关键帧路径
- transform 组合关系
- 父级和子级运动

### snapshot

```bash
npx hyperframes snapshot --frames 10
```

用于生成关键帧图片，适合人工检查：

- 文字是否可读
- 产品是否超出画布
- 图层遮挡是否正确
- 最后定格是否完整

## Doctor

```bash
npx hyperframes doctor --json
```

本机结果：

- Node.js：通过
- CPU：通过
- 内存：通过
- 磁盘：通过
- FFmpeg：通过
- FFprobe：通过
- Chrome：通过
- onnxruntime-node：通过

`ok: false` 的原因来自可选组件：

- Whisper
- Kokoro TTS
- MusicGen
- Docker Desktop 未运行

这些组件不影响本项目的本地 MP4 渲染。

## 渲染

快速预览：

```bash
npx hyperframes render . --quality draft --output renders/banliz-draft.mp4
```

最终交付：

```bash
npx hyperframes render . --quality delivery --output banliz-20s-mg-ad.mp4
```

本次渲染数据：

| 项目 | draft | delivery |
| --- | ---: | ---: |
| 质量 | draft | delivery/high |
| 帧数 | 600 | 600 |
| 画面 | 1920x1080 | 1920x1080 |
| 帧率 | 30fps | 30fps |
| 时长 | 20.0s | 20.0s |
| 文件大小 | 5.6MB | 11.3MB |
| 总耗时 | 53.9s | 54.1s |

## FFprobe 验证

```bash
ffprobe -v error \
  -show_entries format=duration,size,bit_rate \
  -show_entries stream=index,codec_name,codec_type,width,height,r_frame_rate,channels,sample_rate \
  -of json banliz-20s-mg-ad.mp4
```

最终结果：

- 时长：`20.000000s`
- 视频：H.264，1920x1080，30fps
- 音频：AAC，48kHz，双声道
- 完整解码无错误

## 成片质检清单

- [ ] 第 0 帧不是黑帧
- [ ] 最后一帧不是黑帧
- [ ] 产品没有在正常展示阶段意外越界
- [ ] 品牌字停留时间足够阅读
- [ ] 音频不是静音
- [ ] 音频开头和结尾有淡入淡出
- [ ] 视频时长等于根 `data-duration`
- [ ] 分辨率与客户要求一致
- [ ] 帧率与客户要求一致
- [ ] 参考视频没有出现在成片素材中

---

# 05 练习任务与故障排查

## 练习一：把 20 秒压缩到 15 秒

目标：保持叙事完整，但删除重复视觉节拍。

建议方向：

- 开场从 3.25 秒压缩到 2.2 秒。
- 原料段从 3.95 秒压缩到 2.8 秒。
- 三个卖点压缩为两个。
- 斜切高潮从 4.3 秒压缩到 3 秒。
- 收尾至少保留 3 秒。

练习重点：

- 改根 `data-duration`。
- 改每个场景的 `data-start` 和 `data-duration`。
- 改 GSAP 中所有绝对时间。
- 需要同步修改 `index.motion.json`。

## 练习二：制作 9:16 竖屏版

目标：不是简单裁切，而是重新安排构图。

建议：

- 产品放上方或中央。
- 卖点文字改为上下分层。
- 斜切色带旋转角度减小。
- 最终品牌字改成两行思路，但避免机械断句。
- 安全区上下各留至少 120px。

需要调整：

- `data-width="1080"`
- `data-height="1920"`
- 所有绝对定位坐标
- 产品尺寸
- 字体大小

## 练习三：替换配色

目标：保持同一套运动，只替换设计语言。

可尝试：

- 抹茶版：深绿 + 奶油 + 浅黄
- 黑金版：深棕 + 金色 + 暖白
- 夏日版：天蓝 + 柠檬黄 + 奶油

修改位置：

- `frame.md`
- `index.html` 顶部的 CSS 变量
- 背景层和色带的具体颜色

不要只改一个按钮或一个标题颜色。配色系统必须整体变化。

## 练习四：加入第二种产品

目标：让多产品展示不是简单复制。

建议：

- 每款产品有独立颜色场。
- 使用错峰进入，不做完全同步。
- 最终阵列中保留一个主产品。
- 不要让多个产品同时争抢视觉中心。

## 练习五：重写文字出场

当前文字策略：

- 卖点用短促入场
- 品牌用快速放大
- slogan 用遮罩升起

可替换方案：

- 字符级错峰放大
- 横向整行擦除
- 文字沿路径进入
- 文字背后先出现色块，再显示文字

每次只改变一种文字机制，不要同时重写所有文字运动。

## 练习六：加入旁白或字幕

如果要加入旁白：

1. 用 `/media-use` 生成或导入音频。
2. 添加独立 `<audio id="voiceover">`。
3. 背景音乐需要避免抢占人声频段。
4. 有旁白时，背景音乐通常需要 ducking 或 carve。
5. 用 `check` 验证音频元素有 id 且没有 `crossorigin`。

如果只加字幕：

1. 一个字幕位置只放一个短线。
2. 字幕进入时间必须服从旁白时间。
3. 字幕不能遮挡品牌字。
4. 字幕结束后保留适当空档。

## 常见错误

| 错误 | 原因 | 修复 |
| --- | --- | --- |
| 首帧全白/全黑 | 全屏层在时间轴 0 点前可见 | CSS 初始 `opacity: 0`，在 `tl.set` 中显示 |
| 时间轴不执行 | 注册键和根 ID 不一致 | 统一为 `window.__timelines["main"]` |
| 页面预览正常，渲染无动画 | 动画不在暂停时间轴上 | 删除裸 `gsap.to()`，统一写入 `tl` |
| 产品图有白框 | 原图 Alpha 不透明 | 生成 cutout 或使用浅色容器 |
| 产品运动突然跳变 | 同一元素上并行 transform | 拆成父级入场、子级呼吸 |
| 文字重叠 | 字形超出 line box | 增加间距或容器分区 |
| 对比度失败 | 白字放在亮色或绿底 | 加深色底板或改字色 |
| 音频无声 | 缺少 `<audio id>` | 加唯一 id |
| 参考组件无法下载 | GitHub 网络超时 | 使用长超时重试或手动缓存模型 |
| 渲染耗时过长 | 软件 GPU 或截图路径 | 检查 render summary 的 GPU 行 |

## lint 警告的含义

`composition_file_too_large`：

- 文件过大，不是渲染错误。
- 量产项目建议拆成子合成。

`nested_structure_needs_subcomposition`：

- 顶层时间片段内部仍有复杂嵌套。
- Studio 可维护性下降，但不阻塞渲染。

如果要彻底消除这些警告，可以把 5 个场景分别迁移到 `compositions/*.html`。

## 新项目执行顺序

```text
1. 写 BRIEF.md
2. 写 frame.md
3. 收集素材
4. 处理素材
5. 写镜头表
6. 搭最小 HTML
7. 运行 lint
8. 写第一场景
9. 每完成一个场景运行 lint
10. 全部完成后运行 check --snapshots
11. 看快照
12. 修布局和对比度
13. 渲染 draft
14. 看 contact sheet 和音频
15. 渲染 delivery
16. FFprobe 验证
```

## 最终自查问题

1. 第一个视觉钩子是否在 1 秒内出现？
2. 产品是否在每 2 秒内至少发生一次可感知运动？
3. 卖点是否能在停留时间内读完？
4. 品牌字是否有足够停留时间？
5. 结尾是否比高潮更安静？
6. 画面是否仍然像网页而不像视频？
7. 有没有参考视频的素材或品牌元素被误带进成片？
8. 关掉声音后，画面是否仍然能表达核心信息？
9. 关掉文字后，产品是否仍然是主角？
10. 成片最后一帧是否能作为单独海报使用？

---

# 附：镜头表

| scene | start_sec | end_sec | duration_sec | purpose | background | product_motion | text | transition |
| --- | ---: | ---: | ---: | --- | --- | --- | --- | --- |
| intro | 0.000 | 3.250 | 3.250 | Hook and product reveal | 6 flat saturated color fields | falls in then drifts and rotates | 板栗汽水 / short benefit chips | hard color cuts every 0.45-0.55s |
| ingredients | 3.250 | 7.200 | 3.950 | Ingredient origin | cream paper with ghost 栗 | enters from right with 3D tilt then floats | 纯天然板栗 | hard cut into cream scene |
| points | 7.200 | 11.200 | 4.000 | Three benefit beats | leaf green amber chestnut horizontal bands | moves left-right across bands | 栗香浓郁 / 细腻顺滑 / 清爽不腻 | phrase swaps with fast exits |
| diagonal | 11.200 | 15.500 | 4.300 | Visual peak and proof | large diagonal color bands | sweeps in with rotation and scale | 真材实料 | diagonal band sweep |
| brand-pop | 15.500 | 16.780 | 1.280 | Brand burst | vertical multicolor stripes | multiple cans enter as a row | 板栗汁 | hard cut to final coral |
| final | 16.780 | 20.000 | 3.220 | Lockup and slogan | coral solid with radial aura | hero can bobs and breathes | 板栗汁 / 纯天然板栗 / 一口清爽栗香 | masked slogan entrance |
