HyperFrames 视频生成原理学习资料
================================

版本：1.0
主题：用 Codex 编写 HTML/CSS/JS，再由 HyperFrames 渲染成视频
适用方向：MG 动画、科普动画、口播包装、动态字幕、数据图表短视频、标题片


一、先理解一句话
================

HyperFrames 不是传统时间轴剪辑软件。

它采用“代码生成视频”的工作方式：

  用户需求
    ↓
  Codex 理解需求并编写 HTML/CSS/JS
    ↓
  HyperFrames 解析时间轴和动画
    ↓
  浏览器逐帧渲染画面
    ↓
  FFmpeg 编码画面和音频
    ↓
  输出 MP4

视频的画面、文字、布局、动画和时间控制，最终都来自网页代码。


二、适合与不适合
================

适合：

  1. 极简科技 MG 动画
  2. 科普和知识解释视频
  3. 动态文字和动态字幕
  4. 口播视频的视觉包装
  5. 数据图表和数字增长动画
  6. 流程演示、标题片、片头片尾
  7. 模板化、批量参数化视频
  8. 用代码精确控制每一帧的短视频

不适合：

  1. 重度真人素材抠像
  2. 复杂多机位粗剪
  3. 依赖人工逐帧调整的传统 NLE 剪辑
  4. 大量素材浏览、筛选和拖拽排列
  5. 复杂专业调色和录音棚级混音工作流

结论：

  HyperFrames 更适合“设计驱动、代码驱动、可精确复现”的视频。
  如果需要大量真人素材剪辑，传统剪辑软件通常更合适。


三、各组件负责什么
==================

1. Codex

  负责：

  1. 理解视频主题和脚本
  2. 选择视觉方向和动画方式
  3. 编写 HTML
  4. 编写 CSS
  5. 编写 JavaScript 和 GSAP 动画
  6. 运行检查、预览、截图和渲染
  7. 根据检查结果修正布局和动画

2. HyperFrames

  负责：

  1. 定义 HTML 视频合成合同
  2. 读取 data-* 时间属性
  3. 管理 clip 出现和消失时间
  4. 管理轨道、视频、音频和子合成
  5. 控制浏览器按指定时间点跳转
  6. 捕获每一帧画面
  7. 输出无声画面帧或直接交给 FFmpeg 编码

3. GSAP

  负责：

  1. 创建动画时间线
  2. 控制淡入、位移、缩放、旋转和颜色变化
  3. 安排多个元素的先后顺序
  4. 控制缓动和节奏

4. 浏览器与 Puppeteer/Chrome

  负责：

  1. 打开编译后的 HTML
  2. 执行 CSS 和 JavaScript
  3. 按时间点 seek 到指定帧
  4. 截取画面或读取浏览器绘制结果
  5. 保证动画状态与目标时间一致

5. FFmpeg

  负责：

  1. 将连续画面编码成 H.264 MP4
  2. 合并音频
  3. 处理分辨率、帧率、像素格式和编码质量
  4. 输出 WebM、MOV 等格式

6. FFprobe

  负责：

  1. 读取视频和音频元数据
  2. 检查时长、帧率、分辨率、采样率、音轨和编码格式
  3. 为检查和渲染提供媒体信息

7. Node.js

  负责：

  1. 运行 HyperFrames CLI
  2. 运行构建和渲染脚本
  3. 启动本地静态服务器
  4. 调度浏览器与 FFmpeg


四、本地渲染和云端渲染的区别
============================

本地渲染：

  HTML
    ↓
  本机 Chrome 逐帧渲染
    ↓
  本机 FFmpeg 编码
    ↓
  本地 MP4

特点：

  1. 不需要云端账号
  2. 文件不上传
  3. 依赖本机 Chrome、FFmpeg、FFprobe 和 Node.js
  4. 渲染速度受本机 CPU、GPU、内存影响
  5. 适合个人制作和本地自动化

云端渲染：

  HTML 项目
    ↓
  打包上传
    ↓
  HeyGen 云端 Chrome 渲染
    ↓
  云端 FFmpeg 编码
    ↓
  下载 MP4

特点：

  1. 不依赖本机 FFmpeg 和 Chrome
  2. 通常需要 HeyGen 登录或 API 凭据
  3. 适合远程、CI、批量和团队协作
  4. 项目代码和素材会上传到云端

重要：

  本地 HyperFrames 和云端 HyperFrames 使用同一套 HTML 合成规则。
  区别主要在渲染执行位置，不是两套不同的视频语言。


五、一个 HyperFrames 项目的结构
==============================

典型结构：

  project/
    index.html
      主合成，也是渲染入口

    compositions/
      子合成、局部场景、可复用片段

    assets/
      图片、音频、视频、字体等本地素材

    BRIEF.md
      视频目标、脚本、时长、画幅和限制

    DESIGN.md
      色彩、字体、动画规则和禁止项

    hyperframes.json
      HyperFrames 项目配置

    package.json
      项目脚本和 CLI 版本

    meta.json
      项目编号和名称

    snapshots/
      关键帧截图

    renders/
      默认渲染输出目录

其中：

  index.html 是视频源文件。
  MP4 是渲染产物。
  修改视频内容时修改 HTML、CSS 和 JS，不直接修改 MP4。


六、主合成的基本结构
====================

最小结构示例：

  <div
    id="root"
    data-composition-id="main"
    data-start="0"
    data-duration="10"
    data-fps="30"
    data-width="1920"
    data-height="1080"
  >
    <h1
      id="title"
      class="clip"
      data-start="0"
      data-duration="10"
      data-track-index="0"
    >
      示例标题
    </h1>
  </div>

关键含义：

  data-composition-id
    合成唯一 ID。

  data-start
    元素或合成开始时间，单位秒。

  data-duration
    持续时间，单位秒。

  data-fps
    帧率。30fps 表示每秒 30 张画面。

  data-width / data-height
    画布像素尺寸。

  class="clip"
    标记为可被 HyperFrames 时间系统管理的视觉元素。

  data-track-index
    时间轴轨道编号。同一轨道不能放置重叠 clip。


七、时间轴原理
==============

视频时间可以理解为：

  总帧数 = 视频时长 × 帧率

例如：

  10 秒 × 30fps = 300 帧

一帧就是一张完整的静止画面。

当 MP4 连续播放 300 帧时，人眼就会看到 10 秒动画。

HyperFrames 的渲染不是简单录屏。

它通常会：

  1. 打开合成页面
  2. 暂停动画
  3. 把 GSAP 时间线 seek 到目标时间
  4. 等浏览器完成布局和绘制
  5. 捕获该时间点的一帧
  6. 处理下一帧
  7. 将所有帧编码成视频

因此动画必须具备 seek 安全性。

seek 安全的意思是：

  无论直接跳到 0.5 秒、3.2 秒还是 9.7 秒，
  页面都应该显示与该时间严格对应的画面。


八、GSAP 时间线合同
===================

HyperFrames 中常用：

  window.__timelines = window.__timelines || {};

  const tl = gsap.timeline({
    paused: true
  });

  tl.from(
    "#title",
    {
      opacity: 0,
      y: 26,
      duration: 0.9,
      ease: "power3.out"
    },
    0.22
  );

  window.__timelines["main"] = tl;
  tl.seek(0);

关键点：

  1. 时间线必须默认 paused。
  2. 时间线必须注册到 window.__timelines。
  3. 时间线构建必须同步完成。
  4. 不能在异步函数、Promise 或 setTimeout 中创建时间线。
  5. 不调用 video.play() 或 audio.play()。
  6. 不使用 Math.random()。
  7. 不使用 Date.now()。
  8. 不使用无限循环 repeat: -1。
  9. 不能只依赖浏览器实时播放状态。


九、推荐动画流程
================

正确做法是先确定最终画面，再添加动画。

步骤：

  1. 先确定最完整、最清晰的那一帧
  2. 用静态 HTML 和 CSS 把最终布局排好
  3. 再使用 gsap.from() 添加入场动画
  4. 最后使用 gsap.to() 添加必要的退场动画

不要先在 CSS 中把元素放到屏幕外，再靠动画“猜”最终位置。

推荐：

  最终位置写在 CSS 中
  GSAP 只描述从什么状态移动到最终位置

例如：

  CSS：
  .title {
    transform: none;
  }

  GSAP：
  tl.from(".title", {
    opacity: 0,
    y: 30,
    duration: 0.6
  });

动画常见属性：

  opacity      透明度
  x / y        水平和垂直位移
  scale        缩放
  rotation     旋转
  color        文字颜色
  backgroundColor 背景颜色

优先使用 transform 和 opacity，不要频繁动画 width、height、top、left。


十、缓动和节奏
==============

缓动决定动画的情绪。

常用：

  power2.out
    平稳、专业、常用入场

  power3.out
    更快进入，缓慢停住

  expo.out
    快速、自信、技术感强

  sine.inOut
    柔和、缓慢、环境运动

  power2.in
    常用于退场，先慢后快

常用节奏：

  0.15 至 0.30 秒
    快速、有能量

  0.30 至 0.50 秒
    专业、常规

  0.50 至 0.80 秒
    稳重、有重量

  0.80 至 2.00 秒
    电影感、氛围感

入场通常使用 .out。
退场通常使用 .in。
环境性持续运动通常使用 .inOut。


十一、初帧布局原则
==================

视频不是网页。

不要只做一个居中的小文本块。

每个重要场景最好包含：

  1. 背景层
  2. 主体信息层
  3. 修饰和结构层
  4. 两个或以上视觉焦点
  5. 明确的对齐和留白

常见背景层：

  1. 径向光影
  2. 网格
  3. 细线
  4. 边框
  5. 超大低透明度文字
  6. 缓慢移动的几何结构

注意：

  深色视频不要大量使用全屏线性渐变，H.264 容易产生色带。
  优先使用纯色底、径向光和局部装饰。


十二、文字和字体
================

视频字号不能直接沿用网页小字号。

建议：

  主标题：60px 以上
  正文：20px 以上
  数据标签：16px 以上
  技术元数据：16px 以上

深色背景上的浅色文字会显得更粗、更紧。

可采取：

  1. 适当降低正文粗细
  2. 增加行高
  3. 大标题使用负字距
  4. 中文和英文混合时确认字体覆盖
  5. 不要依赖本机独有字体

字体处理原则：

  1. 使用 HyperFrames 支持自动嵌入的字体
  2. 或明确提供 @font-face 和 WOFF2 文件
  3. 或使用 local() 指向确定存在的系统字体
  4. 不要使用未经声明的外部字体名称


十三、视频和音频的关系
======================

带声音的视频通常不能直接把视频元素当作音源。

推荐结构：

  <video
    id="main-video"
    data-start="0"
    data-duration="10"
    data-track-index="0"
    src="assets/video.mp4"
    muted
    playsinline
  ></video>

  <audio
    id="main-audio"
    data-start="0"
    data-duration="10"
    data-track-index="5"
    data-volume="1"
    src="assets/video.mp4"
  ></audio>

原因：

  1. HyperFrames 统一管理媒体播放
  2. 音画可以独立裁剪和偏移
  3. 画面可以静音，音频单独混音
  4. 更符合确定性渲染要求


十四、加入音频需要什么
======================

最少需要：

  1. 一个音频文件
  2. 一个独立的 audio 元素
  3. 正确的 data-start
  4. 正确的 data-duration
  5. 正确的 data-track-index
  6. 合适的 data-volume

常用音频属性：

  data-start
    音频开始时间

  data-duration
    音频播放持续时间

  data-media-start
    从音频素材内部第几秒开始播放

  data-volume
    音量，通常为 0 到 1

常见音频轨：

  Track 4：旁白
  Track 5：背景音乐
  Track 6：音效

同一轨道不能有重叠音频。

音频格式建议：

  WAV
    适合旁白和高质量音效

  MP3
    适合背景音乐和体积敏感场景

  M4A / AAC
    适合已有压缩音频

最终响度建议：

  网络视频整体参考：约 -14 至 -16 LUFS
  真峰值控制：约 -1 dBTP

如果同时有旁白和 BGM：

  1. 旁白作为主音量基准
  2. BGM 音量通常降低
  3. 有人声时对 BGM 做 ducking
  4. 无人声时 BGM 自动恢复
  5. 结尾可加入 0.3 至 1 秒淡出

如需要动态字幕：

  音频
    ↓
  转写
    ↓
  词级或句级时间戳
    ↓
  生成字幕时间线


十五、HyperFrames 的检查和渲染流程
==================================

推荐顺序：

  1. hyperframes doctor
     检查 Node.js、FFmpeg、FFprobe、Chrome、内存和环境

  2. hyperframes lint
     检查 HTML 合同、时间属性、轨道和 CSS

  3. hyperframes validate
     在浏览器中运行，检查 JavaScript 错误、资源缺失和对比度

  4. hyperframes inspect
     按时间采样，检查文字溢出、容器溢出和越界

  5. hyperframes snapshot
     抓取指定时间点的关键帧图片

  6. hyperframes keyframes
     检查 GSAP 关键帧、位移路径和动画节奏

  7. hyperframes render
     正式渲染 MP4

  8. FFprobe
     检查最终时长、分辨率、帧率、帧数和编码格式

更完整的一体化命令：

  hyperframes check

它通常组合检查：

  1. Lint
  2. Runtime
  3. Layout
  4. Motion
  5. Contrast


十六、渲染命令示例
==================

本地标准渲染：

  hyperframes render

指定输出：

  hyperframes render --output final.mp4

指定帧率：

  hyperframes render --fps 30

指定质量：

  hyperframes render --quality high

严格检查后渲染：

  hyperframes render --strict-all

透明 WebM：

  hyperframes render --format webm

高质量最终交付：

  hyperframes render --quality high --fps 30 --strict-all


十七、怎样验证最终视频
======================

1. 看时长

  FFprobe 应显示目标时长，例如 10.000 秒。

2. 看分辨率

  横屏应为 1920x1080。

3. 看帧率

  应为 30/1 或项目指定帧率。

4. 看帧数

  10 秒 × 30fps = 300 帧。

5. 看编码

  常用为 H.264。

6. 看像素格式

  常用于 SDR 视频的是 yuv420p。

7. 抽帧检查

  检查：

  - 标题是否正确
  - 中文是否缺字
  - 动画是否正常
  - 文字是否溢出
  - 结尾是否完整淡出
  - 音频是否同步

FFprobe 示例：

  ffprobe -v error \
    -show_entries format=duration,size,bit_rate \
    -show_entries stream=codec_name,width,height,r_frame_rate,nb_frames \
    -of json output.mp4


十八、当前 10 秒示例视频的原理
==============================

规格：

  画幅：1920x1080
  比例：16:9
  帧率：30fps
  时长：10 秒
  总帧数：300
  输出：H.264 MP4
  外部素材：无
  音频：无

脚本：

  0 至 3 秒
    “HyperFrames 本地渲染”淡入居中

  3 至 7 秒
    “HTML+GSAP｜Puppeteer｜FFmpeg”滑入

  7 至 10 秒
    “本地部署 · 不上云”浮现
    结尾整体淡出

主要画面层：

  1. 深石墨背景
  2. 低透明度网格
  3. 径向绿色光场
  4. 细边框和四角支架
  5. 顶部技术标签
  6. 居中主标题
  7. 技术链路文字
  8. 底部本地部署说明
  9. 最后覆盖黑场实现收尾淡出

动画：

  标题：
    从下方 26px 淡入，并从小缩放恢复

  技术链路：
    从左侧 32px 淡入

  强调线：
    从中间向两侧展开

  底部说明：
    从下方 18px 淡入

  背景：
    缓慢放大和轻微移动

  结尾：
    8.55 秒开始渐入黑色覆盖层
    约 9.77 秒完全进入黑场

这类视频没有任何外部图片和音视频素材，所有画面都由 HTML、CSS 和 GSAP 原生绘制。


十九、从需求到视频的标准工作流
==============================

步骤 1：定义需求

  至少明确：

  1. 视频主题
  2. 视频目标
  3. 目标观众
  4. 时长
  5. 画幅
  6. 帧率
  7. 脚本或关键文字
  8. 视觉风格
  9. 是否有音频
  10. 输出位置

步骤 2：定义视觉规范

  在 DESIGN.md 中确定：

  1. 背景色
  2. 主文字色
  3. 次文字色
  4. 强调色
  5. 字体
  6. 动画规律
  7. 禁止出现的效果

步骤 3：先写最终布局

  不使用动画，先确认文字、图形、间距和层级正确。

步骤 4：再写 GSAP 动画

  添加：

  1. 入场
  2. 环境运动
  3. 节奏停顿
  4. 需要的退场

步骤 5：运行检查

  hyperframes check

步骤 6：抓取关键帧

  hyperframes snapshot

步骤 7：渲染

  hyperframes render

步骤 8：最终验证

  检查视频编码、时长、音画同步和结尾。


二十、几个最容易出错的地方
==========================

1. 把动画写成实时逻辑

  错误：
  使用 Date.now()、setInterval() 或无限循环。

  正确：
  使用 paused GSAP 时间线和明确时间点。

2. 在异步代码中创建时间线

  错误：
  在 Promise、async/await 或 setTimeout 中创建。

  正确：
  页面加载时同步创建。

3. 忘记注册时间线

  错误：

  const tl = gsap.timeline({ paused: true });

  但没有：

  window.__timelines["main"] = tl;

4. 先放屏幕外，再凭感觉回来

  正确：
  先确定最终位置，再用 gsap.from() 描述从哪里进入。

5. 同一属性被多个时间线同时控制

  会造成动画冲突。

6. 同一轨道音频重叠

  应使用不同轨道。

7. 字幕和字体没有嵌入

  最终渲染可能出现回退字体。

8. 只检查静态页面

  必须按多个时间点检查布局和动画。

9. 只看浏览器播放

  最终 MP4 必须经过逐帧渲染和编码验证。


二十一、常用命令速查
====================

环境检查：

  hyperframes doctor

项目信息：

  hyperframes info

列出合成：

  hyperframes compositions

列出时间轴：

  hyperframes timeline

静态检查：

  hyperframes lint

运行时检查：

  hyperframes validate

布局检查：

  hyperframes inspect

一体化检查：

  hyperframes check

关键帧截图：

  hyperframes snapshot

动画关键帧分析：

  hyperframes keyframes

本地预览：

  hyperframes preview

正式渲染：

  hyperframes render

高质量渲染：

  hyperframes render --quality high

严格渲染：

  hyperframes render --strict-all


二十二、最终记忆框架
====================

记住七个词：

  1. Brief
     要做什么视频

  2. Design
     长什么样

  3. HTML
     视频中有哪些元素

  4. Timeline
     每个元素何时出现、何时结束

  5. GSAP
     元素如何运动

  6. Render
     Chrome 逐帧生成画面，FFmpeg 编码 MP4

  7. Verify
     检查编码、时长、帧率、布局、动画和音频

一句话总结：

  HyperFrames 用 HTML 描述视频内容和时间，
  用 CSS 描述视觉样式，
  用 GSAP 描述运动，
  用浏览器逐帧生成画面，
  最后用 FFmpeg 编码成 MP4。


完
