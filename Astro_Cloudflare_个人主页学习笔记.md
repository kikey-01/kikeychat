# Astro + Cloudflare 个人主页学习笔记

> 项目：`my-homepage`\
> 技术栈：Astro + HTML + CSS + JavaScript + Node.js + npm + Wrangler +
> Cloudflare Workers\
> 本文整理本次从创建、设计、调试、部署到更新网页的完整学习过程，并系统解释
> Astro 的语法、目录、命令和常用开发方式。

------------------------------------------------------------------------

## 1. 本次项目最终完成了什么？

这次完成的是一个黑色 / 紫色 Neon / Cyber HUD 风格的个人主页。

主要效果：

-   超大标题
-   Anime Hero 图片
-   黑紫色视觉风格
-   Neon 发光边框
-   HUD 四角
-   图片信息层
-   扫描线
-   动态扫描光束
-   鼠标跟随光晕
-   鼠标控制图片 3D 倾斜
-   `SYS ONLINE`
-   `FPS 60`
-   动态 `LOAD xx%`
-   Glitch CSS 效果
-   项目展示
-   About 区域
-   Contact 区域
-   Footer

最终部署到：

``` text
https://my-homepage.twomz.workers.dev
```

项目目录：

``` text
C:\Users\27215\my-homepage
```

------------------------------------------------------------------------

# 2. 最重要的技术关系

把整个项目理解成这条链：

``` text
HTML
  ↓
CSS
  ↓
JavaScript
  ↓
Astro
  ↓
Node.js / npm
  ↓
Astro Build
  ↓
dist/
  ↓
Wrangler
  ↓
Cloudflare Workers
  ↓
互联网
```

每一层职责不同：

  技术                 主要职责
  -------------------- -------------------------------
  HTML                 页面结构
  CSS                  页面外观、布局、动画
  JavaScript           页面行为和交互
  Astro                页面/组件组织、构建、路由
  Node.js              JavaScript 开发运行环境
  npm                  包管理和脚本
  Wrangler             Cloudflare Workers 命令行工具
  Cloudflare Workers   部署和运行网站

------------------------------------------------------------------------

# 3. HTML：网页的骨架

HTML 决定网页有什么。

例如：

``` html
<h1>Hello World</h1>

<p>这是我的个人主页</p>

<a href="/about">About</a>

<img src="/hero.jpg" alt="Hero">
```

常见标签：

``` html
<h1>一级标题</h1>
<h2>二级标题</h2>
<p>段落</p>
<div>普通容器</div>
<section>区域</section>
<header>页头</header>
<nav>导航</nav>
<footer>页脚</footer>
<a>链接</a>
<button>按钮</button>
<img>图片</img>
```

HTML 可以理解为：

> 决定网页"有什么"。

------------------------------------------------------------------------

# 4. CSS：网页的外观

CSS 决定网页长什么样。

基本语法：

``` css
选择器 {
  属性: 值;
}
```

例如：

``` css
body {
  background: black;
  color: white;
}

h1 {
  font-size: 80px;
}
```

常用属性：

``` css
color: white;
background: black;

font-size: 20px;
font-weight: 700;

margin: 20px;
padding: 20px;

width: 100%;
height: 100vh;

border: 1px solid white;
border-radius: 10px;

opacity: 0.8;
```

------------------------------------------------------------------------

# 5. class

HTML：

``` html
<div class="hero"></div>
```

CSS：

``` css
.hero {
  min-height: 100vh;
}
```

多个 class：

``` html
<div class="hero neon-card large"></div>
```

CSS：

``` css
.hero {}
.neon-card {}
.large {}
```

这是前端最常用的样式组织方式之一。

------------------------------------------------------------------------

# 6. Flex 布局

Flex 非常重要。

``` css
.container {
  display: flex;
}
```

常见：

``` css
.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

理解：

``` text
display: flex
    ↓
启用 Flex

justify-content
    ↓
主轴排列

align-items
    ↓
交叉轴对齐
```

例如：

``` css
nav {
  display: flex;
  gap: 20px;
}
```

表示导航项目横向排列并保持间距。

------------------------------------------------------------------------

# 7. Grid 布局

例如：

``` css
.projects {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}
```

表示：

``` text
┌──────┐ ┌──────┐ ┌──────┐
│      │ │      │ │      │
└──────┘ └──────┘ └──────┘
```

适合项目卡片、图库、产品列表等。

------------------------------------------------------------------------

# 8. position

这次 Cyber HUD 大量使用：

``` css
position: relative;
position: absolute;
```

典型结构：

``` css
.image-box {
  position: relative;
}

.corner {
  position: absolute;
  top: 0;
  left: 0;
}
```

理解：

``` text
父元素 relative
       ↓
子元素 absolute
       ↓
子元素可以定位在父元素内部
```

常配合：

``` css
top
right
bottom
left
z-index
```

------------------------------------------------------------------------

# 9. z-index

控制前后层级：

``` css
z-index: 10;
```

例如：

``` css
.scan-beam {
  z-index: 7;
}

.system-status {
  z-index: 10;
}
```

一般数字更大的元素会显示在更前面。

------------------------------------------------------------------------

# 10. CSS 动画

使用：

``` css
@keyframes
```

定义动画。

例如：

``` css
@keyframes scanBeam {
  0% {
    top: -5%;
    opacity: 0;
  }

  10% {
    opacity: 1;
  }

  90% {
    opacity: 1;
  }

  100% {
    top: 105%;
    opacity: 0;
  }
}
```

调用：

``` css
.scan-beam {
  animation: scanBeam 4s linear infinite;
}
```

含义：

``` text
scanBeam
↓
动画名称

4s
↓
持续 4 秒

linear
↓
匀速

infinite
↓
无限循环
```

------------------------------------------------------------------------

# 11. 这次的扫描光束

核心代码：

``` css
.scan-beam {
  position: absolute;
  left: 0;
  top: -20%;
  width: 100%;
  height: 2px;
  z-index: 7;
  pointer-events: none;

  background: linear-gradient(
    90deg,
    transparent,
    rgba(190, 90, 255, 0.15),
    rgba(220, 150, 255, 0.95),
    rgba(190, 90, 255, 0.15),
    transparent
  );

  box-shadow:
    0 0 8px rgba(190, 90, 255, 0.9),
    0 0 25px rgba(150, 50, 255, 0.65),
    0 0 60px rgba(120, 30, 255, 0.35);

  animation: scanBeam 4s linear infinite;
}
```

这里同时学习了：

``` text
position
z-index
pointer-events
linear-gradient
box-shadow
@keyframes
animation
```

------------------------------------------------------------------------

# 12. JavaScript：让网页动起来

JavaScript 负责网页行为。

例如：

``` js
document.addEventListener("mousemove", () => {
  console.log("鼠标移动");
});
```

可以实现：

-   点击
-   鼠标交互
-   动画控制
-   DOM 修改
-   网络请求
-   动态数据
-   状态变化

------------------------------------------------------------------------

# 13. DOM

DOM 可以理解成：

> JavaScript 看到的网页结构。

例如：

``` html
<h1 id="title">Hello</h1>
```

JavaScript：

``` js
const title = document.querySelector("#title");

title.textContent = "你好";
```

页面文字就会变成：

``` text
你好
```

------------------------------------------------------------------------

# 14. querySelector

最常见：

``` js
document.querySelector(".hero");
```

寻找：

``` html
<div class="hero"></div>
```

ID：

``` js
document.querySelector("#title");
```

标签：

``` js
document.querySelector("h1");
```

------------------------------------------------------------------------

# 15. 事件

例如：

``` js
button.addEventListener("click", () => {
  console.log("点击了");
});
```

常见事件：

``` text
click
mousemove
mouseenter
mouseleave
keydown
keyup
scroll
submit
```

------------------------------------------------------------------------

# 16. 本次鼠标 3D 跟随

项目使用了：

``` js
const light = document.querySelector(".mouse-light");
const visual = document.querySelector(".hero-visual");
const imageBox = document.querySelector(".image-box");

document.addEventListener("mousemove", (event) => {
  if (light) {
    light.style.left = `${event.clientX}px`;
    light.style.top = `${event.clientY}px`;
  }

  if (!visual || !imageBox || window.innerWidth <= 800) return;

  const rect = visual.getBoundingClientRect();

  const x = event.clientX - (rect.left + rect.width / 2);
  const y = event.clientY - (rect.top + rect.height / 2);

  const rotateY = (x / rect.width) * 8;
  const rotateX = -(y / rect.height) * 8;

  imageBox.style.transform = `
    perspective(1000px)
    rotateX(${rotateX}deg)
    rotateY(${rotateY}deg)
  `;
});

if (visual && imageBox) {
  visual.addEventListener("mouseleave", () => {
    imageBox.style.transform = `
      perspective(1000px)
      rotateX(0deg)
      rotateY(0deg)
    `;
  });
}
```

这里学习：

``` text
document
querySelector
addEventListener
mousemove
mouseleave
window.innerWidth
getBoundingClientRect
style
transform
perspective
rotateX
rotateY
```

------------------------------------------------------------------------

# 17. 为什么手机端关闭 3D？

代码：

``` js
if (!visual || !imageBox || window.innerWidth <= 800) return;
```

意思：

``` text
屏幕宽度 <= 800
→ 不执行鼠标 3D
```

因为手机没有传统鼠标。

这是一个基本的响应式设计思想：

> 不同设备使用不同交互方式。

------------------------------------------------------------------------

# 18. 动态 System HUD

HTML：

``` html
<div class="system-status">
  <div>
    <span>SYS</span>
    <b>ONLINE</b>
  </div>

  <div>
    <span>FPS</span>
    <b id="fps-value">60</b>
  </div>

  <div>
    <span>LOAD</span>
    <b id="load-value">24%</b>
  </div>
</div>
```

JavaScript：

``` js
const loadValue = document.querySelector("#load-value");

function updateSystemLoad() {
  if (!loadValue) return;

  const load = Math.floor(18 + Math.random() * 25);

  loadValue.textContent = `${load}%`;
}

setInterval(updateSystemLoad, 1200);
```

这里学习：

``` text
function
Math.random()
Math.floor()
textContent
setInterval()
```

------------------------------------------------------------------------

# 19. 一个重要设计经验：不要滥用随机 Glitch

这次项目曾经增加过自动随机 Glitch。

后来按照要求取消。

原因：

-   闪烁太多会影响阅读
-   页面容易杂乱
-   动效应该服务设计
-   Cyberpunk 不等于所有东西都不停闪烁

当前原则：

``` text
Glitch CSS 可以存在
自动随机触发取消
```

------------------------------------------------------------------------

# 20. Astro 是什么？

Astro 是一个现代 Web 框架。

它不是替代 HTML/CSS/JavaScript 的"第四种语言"。

更准确：

> Astro 是用来组织网页、组件、路由和构建过程的框架。

Astro 文件扩展名：

``` text
.astro
```

例如：

``` text
index.astro
```

一个 Astro 文件可以包含：

``` text
JavaScript / TypeScript
HTML
CSS
```

------------------------------------------------------------------------

# 21. 最基本的 Astro 文件结构

``` astro
---
const name = "天才小十四";
---

<h1>{name}</h1>

<style>
  h1 {
    color: purple;
  }
</style>
```

结构：

``` text
---
Astro / JavaScript 逻辑
---

HTML

<style>
CSS
</style>
```

------------------------------------------------------------------------

# 22. Frontmatter

Astro 最重要的语法之一：

``` astro
---
const name = "天才小十四";
const age = 24;
---
```

两个：

``` text
---
```

之间叫 Frontmatter。

可以放：

-   import
-   变量
-   函数
-   数据
-   JavaScript
-   TypeScript

------------------------------------------------------------------------

# 23. Astro 变量

定义：

``` astro
---
const name = "天才小十四";
---
```

使用：

``` astro
<h1>{name}</h1>
```

核心：

``` text
{表达式}
```

例如：

``` astro
<p>{name}</p>
<p>{age}</p>
<p>{1 + 2}</p>
```

------------------------------------------------------------------------

# 24. Astro 中使用 JavaScript

``` astro
---
const a = 10;
const b = 20;
const result = a + b;
---

<h1>{result}</h1>
```

页面：

``` text
30
```

函数：

``` astro
---
function hello(name) {
  return `Hello ${name}`;
}
---

<p>{hello("Astro")}</p>
```

------------------------------------------------------------------------

# 25. Astro 数组和 map

本项目有项目数据：

``` astro
---
const projects = [
  {
    title: "个人主页",
    text: "基于 Astro 与 Cloudflare 构建的个人数字空间。",
    tags: ["Astro", "Cloudflare", "Web"],
  },
  {
    title: "未来项目",
    text: "未来制作的项目与实验。",
    tags: ["AI", "Java", "Experiment"],
  },
];
---
```

可以：

``` astro
{projects.map((project) => (
  <article>
    <h3>{project.title}</h3>
    <p>{project.text}</p>
  </article>
))}
```

这就是：

> JavaScript 数据 → 批量生成 HTML。

------------------------------------------------------------------------

# 26. Astro 条件渲染

``` astro
---
const online = true;
---

{online && <p>ONLINE</p>}
```

或者：

``` astro
{online ? <p>ONLINE</p> : <p>OFFLINE</p>}
```

这也是 JavaScript 表达式。

------------------------------------------------------------------------

# 27. Astro 文件路由

Astro 的：

``` text
src/pages/
```

具有特殊意义。

例如：

``` text
src/pages/index.astro
```

对应：

``` text
/
```

所以：

``` text
https://example.com/
```

如果创建：

``` text
src/pages/about.astro
```

对应：

``` text
/about
```

如果：

``` text
src/pages/projects.astro
```

对应：

``` text
/projects
```

这叫：

> 文件系统路由。

------------------------------------------------------------------------

# 28. components 组件目录

页面变大以后，不应该所有代码都放进：

``` text
index.astro
```

可以拆：

``` text
src/components/
├─ Header.astro
├─ Hero.astro
├─ About.astro
├─ Projects.astro
└─ Footer.astro
```

导入：

``` astro
---
import Header from "../components/Header.astro";
---

<Header />
```

组件化的核心：

> 把一个大页面拆成可复用的小模块。

------------------------------------------------------------------------

# 29. layouts

多个页面通常共享：

-   Header
-   Footer
-   `<html>`
-   `<head>`
-   全局结构

可以：

``` text
src/layouts/
└─ Layout.astro
```

然后：

``` astro
---
import Layout from "../layouts/Layout.astro";
---

<Layout title="About">
  <h1>About</h1>
</Layout>
```

------------------------------------------------------------------------

# 30. public 目录

``` text
public/
```

用于放可以直接访问的静态资源。

本项目：

``` text
public/
└─ hero.jpg
```

页面：

``` html
<img src="/hero.jpg">
```

对应：

``` text
public/hero.jpg
```

以后换主页图片，可以直接替换：

``` text
public/hero.jpg
```

如果文件名不变，通常不需要改 HTML。

------------------------------------------------------------------------

# 31. src 目录

源码一般放在：

``` text
src/
```

例如：

``` text
src/
├─ pages/
├─ components/
├─ layouts/
└─ styles/
```

本项目最核心：

``` text
src/pages/index.astro
```

------------------------------------------------------------------------

# 32. 推荐的 Astro 项目结构

当前项目：

``` text
my-homepage/
├─ public/
│  └─ hero.jpg
│
├─ src/
│  └─ pages/
│     └─ index.astro
│
├─ astro.config.mjs
├─ wrangler.jsonc
├─ package.json
└─ package-lock.json
```

以后可以发展成：

``` text
my-homepage/
├─ public/
│  ├─ hero.jpg
│  ├─ favicon.svg
│  └─ images/
│
├─ src/
│  ├─ components/
│  │  ├─ Header.astro
│  │  ├─ Hero.astro
│  │  ├─ About.astro
│  │  ├─ Projects.astro
│  │  └─ Footer.astro
│  │
│  ├─ layouts/
│  │  └─ Layout.astro
│  │
│  ├─ pages/
│  │  ├─ index.astro
│  │  ├─ about.astro
│  │  └─ projects.astro
│  │
│  └─ styles/
│     └─ global.css
│
├─ astro.config.mjs
├─ wrangler.jsonc
├─ package.json
└─ package-lock.json
```

------------------------------------------------------------------------

# 33. astro.config.mjs

这是 Astro 配置文件。

当前项目最终采用：

``` js
// @ts-check

import { defineConfig } from 'astro/config';

export default defineConfig({});
```

之所以最终这么简单，是因为当前网站采用：

``` text
静态 Astro
↓
生成 dist
↓
Cloudflare Worker Assets
```

而不是使用 Astro Cloudflare adapter 生成另一套运行方式。

------------------------------------------------------------------------

# 34. 这次为什么移除了 Cloudflare Adapter？

最初配置：

``` js
import cloudflare from '@astrojs/cloudflare';

export default defineConfig({
  adapter: cloudflare({
    platformProxy: { enabled: true },
    imageService: "cloudflare"
  })
});
```

部署过程中出现：

``` text
The name 'ASSETS' is reserved in Pages projects.
```

之后发现生成的配置仍然包含 Pages 相关配置。

因此重新整理为：

``` text
Astro
 ↓
静态构建
 ↓
dist/
 ↓
Cloudflare Worker Assets
```

最终成功。

这个过程的重要经验：

> 遇到部署错误时，先判断项目到底应该采用哪一种部署模型，而不是不断叠加配置。

------------------------------------------------------------------------

# 35. wrangler.jsonc

这是 Cloudflare Wrangler 的配置文件。

当前：

``` json
{
  "$schema": "./node_modules/wrangler/config-schema.json",
  "name": "my-homepage",
  "compatibility_date": "2026-09-30",
  "assets": {
    "directory": "./dist"
  }
}
```

解释：

``` text
name
↓
Worker 名称

compatibility_date
↓
Cloudflare 运行时兼容日期

assets.directory
↓
静态网站文件目录
```

最关键：

``` text
./dist
```

------------------------------------------------------------------------

# 36. dist

执行：

``` powershell
npm run build
```

Astro 会生成：

``` text
dist/
```

可以理解为：

``` text
src + public + 配置
        ↓
    Astro Build
        ↓
       dist
```

`dist` 是最终构建结果。

一般不要直接修改 `dist`。

应该修改：

``` text
src/
public/
```

然后重新构建。

------------------------------------------------------------------------

# 37. node_modules

``` text
node_modules/
```

保存项目依赖。

例如：

``` text
astro
wrangler
@astrojs/...
```

一般不要手动修改。

如果依赖损坏，可以删除后重新：

``` powershell
npm install
```

------------------------------------------------------------------------

# 38. package.json

Node.js 项目的核心配置之一。

本项目重要 scripts：

``` json
{
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro build && wrangler dev",
    "deploy": "astro build && wrangler deploy"
  }
}
```

------------------------------------------------------------------------

# 39. npm run dev

``` powershell
npm run dev
```

实际上执行：

``` text
astro dev
```

作用：

> 启动本地开发服务器。

通常：

``` text
http://localhost:4321
```

修改代码后，开发服务器通常会自动刷新。

------------------------------------------------------------------------

# 40. npm run build

``` powershell
npm run build
```

实际：

``` text
astro build
```

作用：

> 把 Astro 源代码构建成最终网站文件。

生成：

``` text
dist/
```

------------------------------------------------------------------------

# 41. npm run deploy

本项目：

``` json
"deploy": "astro build && wrangler deploy"
```

所以：

``` powershell
npm run deploy
```

等价于：

``` text
astro build
↓
wrangler deploy
```

以后更新网页基本只需要：

``` powershell
npm run deploy
```

------------------------------------------------------------------------

# 42. Node.js

Node.js 是 JavaScript 运行环境。

浏览器：

``` text
Chrome
 ↓
JavaScript
```

Node：

``` text
Windows
 ↓
Node.js
 ↓
JavaScript
```

Astro、npm、Wrangler 等开发工具依赖 Node.js 生态。

------------------------------------------------------------------------

# 43. npm

npm 是 Node.js 生态常用的包管理工具。

例如：

``` powershell
npm install astro
```

安装 Astro。

``` powershell
npm install
```

根据：

``` text
package.json
package-lock.json
```

安装项目依赖。

------------------------------------------------------------------------

# 44. npx

例如：

``` powershell
npx wrangler deploy
```

`npx` 可以执行项目中的命令行工具。

因此：

``` text
npx wrangler
```

可以调用 Wrangler。

------------------------------------------------------------------------

# 45. Wrangler

Wrangler 是 Cloudflare Workers 的 CLI。

常用：

``` powershell
npx wrangler deploy
```

部署。

``` powershell
npx wrangler whoami
```

查看登录账号。

``` powershell
npx wrangler dev
```

本地运行 Worker 环境。

------------------------------------------------------------------------

# 46. 这次 Cloudflare 登录

最初使用：

``` powershell
npx wrangler login --browser=false
```

完成登录。

然后：

``` powershell
npx wrangler whoami
```

确认账号和权限。

------------------------------------------------------------------------

# 47. 这次遇到的邮箱验证问题

第一次部署时：

``` text
You need to verify your email address to use Workers
code: 10034
```

解决：

> 完成 Cloudflare 账号邮箱验证。

验证之后重新：

``` powershell
npx wrangler deploy
```

最终成功。

所以这个错误不是 Astro 代码错误。

------------------------------------------------------------------------

# 48. 最终部署

成功输出类似：

``` text
Uploaded my-homepage
Deployed my-homepage triggers

https://my-homepage.twomz.workers.dev
```

这证明：

``` text
Astro Build
↓
Wrangler
↓
Cloudflare Worker
↓
部署成功
```

------------------------------------------------------------------------

# 49. 本次网络排查

网站部署成功以后，在没有 VPN 的网络环境下：

``` text
Cloudflare 官网
→ 可以打开

twomz.workers.dev
→ 打不开

my-homepage.twomz.workers.dev
→ 打不开

/cdn-cgi/trace
→ 打不开
```

电脑：

``` powershell
nslookup twomz.workers.dev
```

能够正常解析。

随后测试：

``` powershell
Test-NetConnection twomz.workers.dev -Port 443
```

IPv6 TCP 443 连接失败。

进一步直接测试 IPv4：

``` powershell
Test-NetConnection 104.244.43.231 -Port 443
```

同样等待响应。

测试：

``` powershell
Test-NetConnection 1.1.1.1 -Port 443
```

结果：

``` text
PingSucceeded       : True
TcpTestSucceeded    : False
```

同时：

``` powershell
netsh winhttp show proxy
```

显示：

``` text
直接访问(没有代理服务器)
```

因此最终可以确认：

> 这个问题不是 Astro 没部署成功，也不是简单的 WinHTTP
> 代理配置问题。当前网络环境下，相关 HTTPS TCP 路径存在连接问题；VPN
> 可以绕过该问题。

用户决定暂时使用 VPN，不继续处理。

------------------------------------------------------------------------

# 50. 以后修改网页的标准流程

项目目录：

``` powershell
cd C:\Users\27215\my-homepage
```

修改：

``` text
src/pages/index.astro
```

或者：

``` text
public/hero.jpg
```

本地检查：

``` powershell
npm run dev
```

浏览器：

``` text
http://localhost:4321
```

确认后：

``` powershell
npm run deploy
```

完成更新。

------------------------------------------------------------------------

# 51. 修改文字

打开：

``` text
src/pages/index.astro
```

搜索：

``` text
天才小十四
```

或者：

``` text
拥抱AI即拥抱未来
```

直接修改。

------------------------------------------------------------------------

# 52. 修改主页图片

替换：

``` text
public/hero.jpg
```

如果仍然叫：

``` text
hero.jpg
```

通常不需要修改：

``` html
<img src="/hero.jpg">
```

重新：

``` powershell
npm run deploy
```

即可。

------------------------------------------------------------------------

# 53. 增加页面

创建：

``` text
src/pages/about.astro
```

例如：

``` astro
---
---

<html>
  <body>
    <h1>About Me</h1>
  </body>
</html>
```

开发时：

``` text
http://localhost:4321/about
```

部署后：

``` text
https://my-homepage.twomz.workers.dev/about
```

------------------------------------------------------------------------

# 54. VS Code 常用快捷键

保存：

``` text
Ctrl + S
```

全选：

``` text
Ctrl + A
```

复制：

``` text
Ctrl + C
```

粘贴：

``` text
Ctrl + V
```

撤销：

``` text
Ctrl + Z
```

查找：

``` text
Ctrl + F
```

替换：

``` text
Ctrl + H
```

打开终端：

``` text
Ctrl + `
```

命令面板：

``` text
Ctrl + Shift + P
```

------------------------------------------------------------------------

# 55. Emmet 快捷写 HTML

输入：

``` text
!
```

按：

``` text
Tab
```

可以快速生成 HTML 基础结构。

------------------------------------------------------------------------

输入：

``` text
div.container
```

按 Tab：

``` html
<div class="container"></div>
```

------------------------------------------------------------------------

输入：

``` text
ul>li*5
```

可以生成：

``` html
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>
```

------------------------------------------------------------------------

输入：

``` text
section.hero>h1+p
```

可以快速生成：

``` html
<section class="hero">
  <h1></h1>
  <p></p>
</section>
```

------------------------------------------------------------------------

# 56. PowerShell 常用命令

当前目录：

``` powershell
pwd
```

查看文件：

``` powershell
dir
```

进入目录：

``` powershell
cd C:\Users\27215\my-homepage
```

返回上级：

``` powershell
cd ..
```

删除构建结果：

``` powershell
Remove-Item -Recurse -Force .\dist
```

必要时清理：

``` powershell
Remove-Item -Recurse -Force .\dist, .\.wrangler -ErrorAction SilentlyContinue
```

------------------------------------------------------------------------

# 57. 遇到网页打不开怎么排查？

推荐：

``` text
1. 本地页面能否打开？
       ↓
2. npm run build 是否成功？
       ↓
3. wrangler deploy 是否成功？
       ↓
4. Worker URL 是否能访问？
       ↓
5. DNS 是否正常？
       ↓
6. TCP 443 是否正常？
       ↓
7. 是否只有某一种网络无法访问？
```

不要一看到网页打不开就认为：

``` text
代码错了
```

网络问题可能发生在：

``` text
DNS
TCP
TLS
HTTP
代理
防火墙
路由
运营商网络
Cloudflare 边缘路径
```

------------------------------------------------------------------------

# 58. 项目文件速查表

  文件/目录                 作用
  ------------------------- ----------------------------
  `src/pages/index.astro`   首页源代码
  `src/pages/`              页面和路由
  `src/components/`         Astro 组件
  `src/layouts/`            页面布局
  `public/`                 静态资源
  `public/hero.jpg`         当前主页图片
  `astro.config.mjs`        Astro 配置
  `wrangler.jsonc`          Cloudflare Worker 配置
  `package.json`            npm 配置、脚本、依赖
  `package-lock.json`       锁定依赖版本
  `node_modules/`           npm 安装的依赖
  `dist/`                   Astro 构建结果
  `.wrangler/`              Wrangler 本地运行/开发状态

------------------------------------------------------------------------

# 59. 不要随便修改的内容

项目正常以后，不要随便删除：

``` text
package.json
astro.config.mjs
wrangler.jsonc
src/
public/
```

尤其：

``` text
wrangler.jsonc
```

目前它负责：

``` text
Cloudflare Worker
+
dist 静态资源
```

的部署配置。

------------------------------------------------------------------------

# 60. 可以重新生成的目录

一般情况下：

``` text
node_modules/
dist/
.wrangler/
```

可以重新生成。

例如依赖出现问题：

``` powershell
Remove-Item -Recurse -Force .\node_modules
npm install
```

然后：

``` powershell
npm run build
```

------------------------------------------------------------------------

# 61. 最终工作流

以后做网站，形成这个习惯：

``` text
打开项目
  ↓
cd C:\Users\27215\my-homepage
  ↓
修改源码
  ↓
npm run dev
  ↓
浏览器检查
  ↓
满意
  ↓
npm run deploy
  ↓
Cloudflare 更新
```

------------------------------------------------------------------------

# 62. 最重要的记忆图

``` text
src/pages/index.astro
        │
        ▼
    Astro Build
        │
        ▼
      dist/
        │
        ▼
 wrangler deploy
        │
        ▼
Cloudflare Workers
        │
        ▼
my-homepage.twomz.workers.dev
```

更新网页：

``` text
改源码
  ↓
npm run dev
  ↓
本地检查
  ↓
npm run deploy
```

------------------------------------------------------------------------

# 63. 学习 Astro 的推荐路线

不要一开始同时学习大量框架。

建议：

``` text
HTML
 ↓
CSS
 ↓
JavaScript
 ↓
Node.js
 ↓
npm
 ↓
Astro
 ↓
Git / GitHub
 ↓
HTTP
 ↓
DNS
 ↓
Cloudflare
 ↓
TypeScript
 ↓
后端
 ↓
数据库
 ↓
AI API
```

其中最值得打牢：

``` text
HTML
CSS
JavaScript
```

因为 Astro、React、Vue 等框架最终仍然建立在 Web 基础之上。

------------------------------------------------------------------------

# 64. Astro 进一步应该学习什么？

基础：

``` text
.astro
Frontmatter
表达式 {}
组件
props
文件路由
```

然后：

``` text
Layouts
Slots
组件通信
TypeScript
Markdown
Content Collections
动态路由
数据获取
API
部署
```

------------------------------------------------------------------------

# 65. 前端学习的一个重要思想

不要只背：

``` text
display: flex
```

而要理解：

> 为什么这里需要 Flex？

不要只背：

``` text
position: absolute
```

而要理解：

> 为什么这个 HUD 元素需要脱离普通文档流？

不要只背：

``` text
npm run deploy
```

而要理解：

``` text
源码
↓
构建
↓
产物
↓
部署
```

真正理解这些关系，才算开始掌握前端。

------------------------------------------------------------------------

# 66. 本次学习最终总结

这次实际走完了一条完整的现代 Web 开发链：

``` text
创建项目
 ↓
理解目录
 ↓
编写 Astro
 ↓
HTML
 ↓
CSS
 ↓
JavaScript
 ↓
加入 Cyber HUD
 ↓
本地运行
 ↓
构建
 ↓
处理 Cloudflare 配置
 ↓
Wrangler 登录
 ↓
Cloudflare 邮箱验证
 ↓
Worker 部署
 ↓
网络排查
 ↓
重新部署
 ↓
形成以后的网站更新流程
```

最终项目已经可以正常部署。

------------------------------------------------------------------------

# 67. 最后只记住这几条

### 本地开发

``` powershell
npm run dev
```

### 构建

``` powershell
npm run build
```

### 部署

``` powershell
npm run deploy
```

### 项目目录

``` text
C:\Users\27215\my-homepage
```

### 首页

``` text
src/pages/index.astro
```

### 图片

``` text
public/hero.jpg
```

### Cloudflare 配置

``` text
wrangler.jsonc
```

### 网站

``` text
https://my-homepage.twomz.workers.dev
```

------------------------------------------------------------------------

# 68. 一句话理解整个项目

> **Astro 负责把你写的网页源码组织、构建成网站；npm
> 负责管理开发工具；Wrangler 负责把构建结果交给 Cloudflare；Cloudflare
> Workers 负责让这个网站在互联网上运行。**

而你以后修改网页：

## \> **改 `src` / `public` → `npm run dev` 检查 → `npm run deploy` 更新。**

# 69. 本文出现的名词、软件与工具：解释和作用

这一章专门作为"术语词典"。

前面的章节主要讲"怎么做"，这一章解决另一个问题：

> **我看到这个名词时，它到底是什么？为什么需要它？**

为了方便查阅，分为：

1.  Web / 前端基础名词
2.  HTML 名词
3.  CSS 名词
4.  JavaScript 名词
5.  Astro 名词
6.  Node.js / npm 名词
7.  Cloudflare 名词
8.  网络相关名词
9.  开发软件与工具
10. 项目文件名词
11. 命令名词
12. 本项目中的视觉效果名词

------------------------------------------------------------------------

## 69.1 Web / 前端基础名词

  ------------------------------------------------------------------------------------------
  名词                    全称 / 含义             作用
  ----------------------- ----------------------- ------------------------------------------
  Web                     World Wide Web，万维网  通过浏览器访问网页和 Web 应用

  前端                    Front-end               用户在浏览器中看到和操作的部分

  后端                    Back-end                服务器端程序、业务逻辑、数据库等

  浏览器                  Browser                 读取并显示网页的软件

  网页                    Web Page                一个可以通过浏览器访问的页面

  网站                    Website                 多个网页和相关资源组成的整体

  Web 应用                Web Application         在浏览器中运行、具有较强交互能力的应用

  HTML                    HyperText Markup        定义网页结构和内容
                          Language                

  CSS                     Cascading Style Sheets  定义网页样式、布局和动画

  JavaScript              JavaScript              实现网页动态行为和交互

  DOM                     Document Object Model   浏览器对 HTML
                                                  页面形成的对象结构，JavaScript 可以操作它

  URL                     Uniform Resource        网络资源的地址
                          Locator                 

  HTTP                    HyperText Transfer      浏览器与服务器交换 Web 数据的协议
                          Protocol                

  HTTPS                   HTTP Secure             加密的 HTTP 通信

  DNS                     Domain Name System      把域名转换成 IP 地址

  IP                      Internet Protocol       网络设备和服务器进行寻址的基础协议

  TCP                     Transmission Control    建立可靠连接并传输网络数据
                          Protocol                

  TLS                     Transport Layer         为 HTTPS 等通信提供加密和身份验证
                          Security                

  SSL                     Secure Sockets Layer    TLS 的前身，现代 HTTPS 实际主要使用 TLS

  IP 地址                 Internet Protocol       网络设备或服务器的地址
                          Address                 

  IPv4                    Internet Protocol       常见的 32 位 IP 地址体系，例如
                          version 4               `104.244.43.231`

  IPv6                    Internet Protocol       新一代 IP 地址体系，地址空间远大于 IPv4
                          version 6               

  端口                    Port                    一台设备上区分不同网络服务的逻辑入口

  443                     HTTPS 默认端口          浏览器通常通过它建立 HTTPS 连接

  localhost               本机地址                表示当前电脑自己

  `127.0.0.1`             IPv4 本机回环地址       访问当前电脑自身

  `::1`                   IPv6 本机回环地址       IPv6 版本的本机地址

  CDN                     Content Delivery        把内容部署到多个网络节点，让用户更快访问
                          Network                 

  Edge / 边缘节点         离用户较近的网络节点    在靠近用户的位置处理请求

  网络路径                Network Path            数据从客户端到服务器经过的网络路线
  ------------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.2 HTML 名词

  -----------------------------------------------------------------------------------------------------
  名词                    含义                     作用
  ----------------------- ------------------------ ----------------------------------------------------
  Tag / 标签              如                       描述 HTML 内容类型
                          `<h1>`、`<p>`、`<div>`   

  Element / 元素          一个完整 HTML 结构       组成网页内容

  Attribute / 属性        如                       给 HTML 元素提供额外信息
                          `href`、`src`、`class`   

  `class`                 CSS 类名                 让 CSS / JavaScript 定位一组元素

  `id`                    元素标识                 通常用于定位某个特定元素

  `href`                  Hypertext Reference      指定链接目标

  `src`                   Source                   指定图片、脚本等资源的位置

  `alt`                   Alternative Text         图片无法显示时的替代文字，也有助于无障碍访问

  `<h1>`                  一级标题                 页面最主要的标题

  `<p>`                   Paragraph                段落

  `<div>`                 Division                 通用容器

  `<section>`             页面区域                 表示一个相对独立的内容区域

  `<header>`              页头                     页面或区域顶部内容

  `<footer>`              页脚                     页面或区域底部内容

  `<nav>`                 Navigation               导航区域

  `<a>`                   Anchor                   超链接

  `<img>`                 Image                    图片

  `<button>`              Button                   可交互按钮

  语义化 HTML             Semantic HTML            使用有意义的标签描述内容结构，提高可读性和可访问性
  -----------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.3 CSS 名词

  ----------------------------------------------------------------------------------------
  名词                      含义                    作用
  ------------------------- ----------------------- --------------------------------------
  CSS                       Cascading Style Sheets  网页样式系统

  Selector / 选择器         CSS 用来选择元素的规则  指定哪些元素使用某种样式

  Property / 属性           如 `color`、`width`     指定要修改的样式项目

  Value / 值                如 `white`、`100px`     指定属性具体取什么值

  `margin`                  外边距                  控制元素与外部元素的距离

  `padding`                 内边距                  控制元素内容与边框之间的距离

  `width`                   宽度                    控制元素宽度

  `height`                  高度                    控制元素高度

  `color`                   文字颜色                设置文字颜色

  `background`              背景                    设置背景

  `font-size`               字体大小                控制文字大小

  `font-weight`             字体粗细                控制文字粗细

  `border`                  边框                    给元素添加边框

  `border-radius`           圆角                    设置元素角落的圆角程度

  `opacity`                 透明度                  控制元素透明程度

  `box-shadow`              盒子阴影                创建阴影和发光效果

  `text-shadow`             文字阴影                创建文字阴影/发光

  `display`                 显示方式                决定元素如何参与布局

  Flex                      Flexible Box Layout     一维布局系统，适合横向或纵向排列

  Grid                      CSS Grid Layout         二维布局系统，适合网格布局

  `gap`                     间距                    设置 Flex/Grid 子元素之间的间距

  `position`                定位方式                控制元素定位模式

  `relative`                相对定位                常作为绝对定位子元素的参考容器

  `absolute`                绝对定位                让元素脱离普通文档流并精确定位

  `fixed`                   固定定位                相对于视口固定

  `sticky`                  粘性定位                在滚动过程中根据条件保持位置

  `top/right/bottom/left`   定位偏移                配合 position 控制位置

  `z-index`                 层级                    控制元素前后覆盖关系

  `aspect-ratio`            宽高比                  保持元素比例

  `overflow`                溢出控制                控制内容超出元素边界时如何处理

  `pointer-events`          指针事件控制            可以让元素忽略鼠标/触摸事件

  `transform`               变换                    平移、旋转、缩放、倾斜等

  `perspective`             透视                    给 3D 变换提供空间透视效果

  `rotateX`                 X 轴旋转                3D 旋转

  `rotateY`                 Y 轴旋转                3D 旋转

  `linear-gradient`         线性渐变                创建渐变颜色

  `@keyframes`              关键帧                  定义 CSS 动画过程

  `animation`               动画属性                调用并控制 CSS 动画

  `transition`              过渡                    让样式变化更加平滑

  响应式设计                Responsive Design       让网页适配电脑、手机、平板等不同屏幕

  媒体查询                  Media Query             根据屏幕尺寸等条件应用不同 CSS
  ----------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.4 JavaScript 名词

  名词                        含义               作用
  --------------------------- ------------------ ----------------------------------
  JavaScript                  Web 常用编程语言   实现网页交互和动态逻辑
  Variable / 变量             保存数据的名称     保存字符串、数字、对象等
  `const`                     常量声明           声明通常不重新赋值的变量
  `let`                       变量声明           声明可以重新赋值的变量
  `function`                  函数               封装一段可以重复调用的逻辑
  Array / 数组                一组有顺序的数据   保存多个值
  Object / 对象               键值结构的数据     表示一个具有多个属性的数据实体
  String / 字符串             文本数据           保存文字
  Number / 数字               数值数据           保存数字
  Boolean / 布尔值            `true` / `false`   表示真假状态
  Template Literal            模板字符串         使用反引号和 `${}` 插入变量
  `document`                  当前网页文档对象   JavaScript 操作 DOM 的入口
  `window`                    浏览器窗口对象     获取窗口尺寸等浏览器信息
  `querySelector()`           DOM 方法           根据 CSS 选择器查找第一个元素
  `addEventListener()`        DOM 方法           给元素添加事件监听
  `mousemove`                 鼠标移动事件       鼠标移动时触发
  `mouseleave`                鼠标离开事件       鼠标离开元素时触发
  `click`                     点击事件           点击元素时触发
  `textContent`               DOM 属性           读取/修改元素文本
  `style`                     元素样式对象       用 JavaScript 修改 CSS
  `getBoundingClientRect()`   DOM 方法           获取元素在视口中的位置和尺寸
  `Math.random()`             JavaScript 方法    生成随机数
  `Math.floor()`              JavaScript 方法    向下取整
  `setInterval()`             定时器             按固定时间间隔重复执行代码
  `map()`                     数组方法           对数组每一项执行处理并生成新数组
  `return`                    返回值             从函数返回结果
  `event`                     事件对象           保存当前事件相关信息
  `clientX` / `clientY`       鼠标坐标           获取鼠标在浏览器视口中的坐标
  DOM 操作                    Manipulating DOM   使用 JavaScript 动态修改网页
  事件监听                    Event Listener     让程序对用户操作作出反应

------------------------------------------------------------------------

# 69.5 Astro 名词

  -------------------------------------------------------------------------------------
  名词                    含义                     作用
  ----------------------- ------------------------ ------------------------------------
  Astro                   Web 框架                 用于构建网页、组件、路由和静态网站

  `.astro`                Astro 文件扩展名         保存 Astro 页面或组件

  Frontmatter             文件顶部 `---` 区域      编写页面构建阶段的
                                                   JavaScript/TypeScript 逻辑

  `{}`                    Astro 表达式语法         把变量或 JavaScript 表达式输出到
                                                   HTML

  Component / 组件        可复用的页面模块         把复杂页面拆成小模块

  Props                   组件属性                 父组件向子组件传递数据

  Slot                    插槽                     允许父组件向子组件传入 HTML 内容

  Layout                  布局                     给多个页面复用统一结构

  Route / 路由            URL 与页面的对应关系     决定访问某个 URL 显示哪个页面

  File-based Routing      文件系统路由             根据 `src/pages` 中的文件自动生成
                                                   URL

  Static Site             静态网站                 构建时生成 HTML/CSS/JS 等文件

  Build / 构建            编译和整理项目           把源码转换成最终部署产物

  Adapter                 适配器                   让 Astro
                                                   针对特定部署平台生成相应运行方式

  `astro:build`           Astro                    生成网站产物
                          构建命令对应的构建过程   

  `src/pages`             Astro 页面目录           页面文件和路由入口

  `src/components`        组件目录                 保存可复用组件

  `src/layouts`           布局目录                 保存公共页面布局

  `public`                静态资源目录             保存可直接访问的图片、图标等资源

  `dist`                  Distribution /           保存 Astro 构建后的最终文件
                          构建产物目录             
  -------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.6 Node.js / npm 名词

  -----------------------------------------------------------------------------
  名词                    含义                    作用
  ----------------------- ----------------------- -----------------------------
  Node.js                 JavaScript 运行时       让 JavaScript
                                                  能在浏览器之外运行

  npm                     Node Package Manager    管理 JavaScript / Node.js
                                                  项目依赖

  package                 软件包                  可被项目安装和使用的代码

  dependency              依赖                    项目运行/构建所需要的软件包

  `package.json`          npm 项目配置文件        保存项目名称、版本、依赖和
                                                  scripts

  `package-lock.json`     npm 依赖锁定文件        记录实际安装的依赖版本

  `node_modules`          依赖目录                保存 npm 安装的软件包

  npm script              package.json            用简短命令执行复杂任务
                          中的命令脚本            

  `npm install`           npm 命令                安装项目依赖

  `npm run dev`           npm script              启动开发服务器

  `npm run build`         npm script              构建项目

  `npm run deploy`        npm script              执行构建并部署

  `npx`                   npm 提供的命令执行工具  直接运行 npm 包提供的 CLI
  -----------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.7 Cloudflare 名词

  ----------------------------------------------------------------------------------------
  名词                    含义                       作用
  ----------------------- -------------------------- -------------------------------------
  Cloudflare              网络基础设施公司及其平台   提供
                                                     DNS、CDN、Workers、安全、网络等服务

  Cloudflare Workers      Cloudflare 的边缘计算平台  在 Cloudflare 网络节点执行代码/提供
                                                     Web 服务

  Worker                  Workers 中的一个部署单元   承载代码和网站服务

  Workers Assets          Worker 提供静态资源的能力  让 Worker 托管 HTML/CSS/JS/图片等文件

  Wrangler                Cloudflare Workers CLI     登录、开发、部署和管理 Worker

  `wrangler.jsonc`        Wrangler 配置文件          定义 Worker 名称、兼容日期、Assets 等

  `wrangler deploy`       Wrangler 部署命令          把 Worker 和相关资源发布到 Cloudflare

  `wrangler dev`          Wrangler 开发命令          在本地运行 Worker 开发环境

  `wrangler whoami`       Wrangler 查询命令          查看当前 Wrangler 登录身份

  `workers.dev`           Cloudflare 提供的 Worker   让 Worker 在没有自定义域名时可以访问
                          公共域名                   

  Custom Domain           自定义域名                 用自己的域名访问 Worker

  Workers Route           Worker 路由                根据域名/路径规则把请求交给 Worker

  Pages                   Cloudflare Pages           Cloudflare 的网站部署平台之一

  Cloudflare Dashboard    Cloudflare 网页控制台      在浏览器中管理 Cloudflare 服务

  Account ID              Cloudflare 账号标识        标识 Cloudflare 账户

  Enterprise              企业级套餐                 提供更多企业功能和服务能力

  China Network           Cloudflare 中国网络        面向中国大陆网络场景的 Cloudflare
                                                     网络服务
  ----------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.8 网络排查名词

  ------------------------------------------------------------------------------------------------
  名词                         含义                    作用
  ---------------------------- ----------------------- -------------------------------------------
  DNS 解析                     Domain Name Resolution  将域名转换为 IP

  `nslookup`                   Windows DNS 查询工具    查询域名对应的 DNS 记录

  TCP Connect                  TCP 建立连接            判断客户端能否与目标 IP/端口建立 TCP 连接

  `Test-NetConnection`         PowerShell 网络测试命令 测试主机、端口、Ping 等

  Ping                         ICMP 连通性测试         判断网络层是否能够获得 ICMP 响应

  ICMP                         Internet Control        Ping 等网络诊断工具常使用的协议
                               Message Protocol        

  TCP 443                      HTTPS 的常见 TCP 端口   建立 HTTPS TCP 连接

  IPv4                         第四版互联网协议        常见 IP 地址体系

  IPv6                         第六版互联网协议        新一代 IP 地址体系

  Proxy / 代理                 中间网络转发服务器      让客户端通过代理访问网络

  VPN                          Virtual Private Network 建立加密网络通道，可改变网络出口/访问路径

  WinHTTP                      Windows HTTP 网络组件   Windows 程序使用的一套 HTTP 通信机制

  `netsh`                      Windows                 查看和修改 Windows 网络配置
                               网络配置命令工具        

  `netsh winhttp show proxy`   WinHTTP 代理查询命令    查看 WinHTTP 是否使用代理

  WLAN                         Wireless LAN            无线局域网接口

  SourceAddress                源地址                  当前设备发起网络连接时使用的本地地址

  RemoteAddress                远程地址                当前连接目标的 IP

  RemotePort                   远程端口                当前连接目标的端口

  `TcpTestSucceeded`           TCP 测试结果            `True` 表示 TCP 连接成功，`False` 表示失败

  `PingSucceeded`              Ping 测试结果           `True` 表示收到 Ping 响应
  ------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.9 开发软件与工具

  ----------------------------------------------------------------------------------------
  软件 / 工具             类型                    作用
  ----------------------- ----------------------- ----------------------------------------
  Visual Studio Code      代码编辑器              编写 Astro、HTML、CSS、JavaScript 等

  VS Code                 Visual Studio Code 简称 微软开发的代码编辑器

  Windows Terminal /      命令行环境              执行 npm、Astro、Wrangler 等命令
  PowerShell                                      

  PowerShell              Windows                 管理文件、运行程序、进行系统和网络操作
                          命令行与脚本环境        

  Google Chrome           Web 浏览器              测试和访问网站

  Microsoft Edge          Web 浏览器              测试和访问网站

  Cloudflare Dashboard    Web 控制台              管理 Cloudflare 账户、Workers、域名等

  Node.js                 JavaScript 运行环境     为 Astro/npm/Wrangler 提供运行基础

  npm                     包管理工具              安装和管理项目依赖

  Wrangler                Cloudflare CLI          部署和管理 Cloudflare Worker

  Git                     版本控制系统            记录代码历史、比较修改、回滚

  GitHub                  代码托管平台            保存和协作管理 Git 仓库
  ----------------------------------------------------------------------------------------

> 本项目实际使用最核心的软件是：**VS
> Code、Node.js、npm、PowerShell、Wrangler、浏览器、Cloudflare
> Dashboard**。

------------------------------------------------------------------------

# 69.10 项目文件名词

  名词                      作用
  ------------------------- ---------------------------------
  `src/`                    项目源代码
  `src/pages/`              Astro 页面
  `src/pages/index.astro`   网站首页
  `src/components/`         Astro 组件
  `src/layouts/`            Astro 布局
  `public/`                 静态资源
  `public/hero.jpg`         当前主页图片
  `dist/`                   构建后的部署文件
  `node_modules/`           npm 依赖
  `.wrangler/`              Wrangler 本地开发/运行状态
  `package.json`            npm 项目配置
  `package-lock.json`       依赖版本锁定
  `astro.config.mjs`        Astro 配置
  `wrangler.jsonc`          Wrangler/Cloudflare Worker 配置

------------------------------------------------------------------------

# 69.11 配置文件中的常见名词

## `package.json`

``` json
{
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "deploy": "astro build && wrangler deploy"
  }
}
```

这里：

  名词        作用
  ----------- -----------------------------
  `scripts`   保存可以通过 npm 执行的命令
  `dev`       开发命令
  `build`     构建命令
  `deploy`    部署命令

------------------------------------------------------------------------

## `wrangler.jsonc`

``` json
{
  "$schema": "./node_modules/wrangler/config-schema.json",
  "name": "my-homepage",
  "compatibility_date": "2026-09-30",
  "assets": {
    "directory": "./dist"
  }
}
```

这里：

  名词                   作用
  ---------------------- ------------------------------------
  `$schema`              告诉编辑器配置文件应该遵循什么结构
  `name`                 Worker 名称
  `compatibility_date`   Cloudflare 运行时兼容日期
  `assets`               静态资源配置
  `directory`            静态资源目录
  `./dist`               Astro 最终构建目录

------------------------------------------------------------------------

# 69.12 命令名词

  命令                         作用
  ---------------------------- ----------------------------
  `cd`                         切换目录
  `pwd`                        显示当前目录
  `dir`                        查看当前目录文件
  `ls`                         查看目录内容
  `npm install`                安装 npm 依赖
  `npm run dev`                启动 Astro 开发服务器
  `npm run build`              构建 Astro 项目
  `npm run deploy`             构建并部署
  `npx wrangler login`         登录 Cloudflare
  `npx wrangler whoami`        查看 Wrangler 当前登录身份
  `npx wrangler deploy`        部署 Worker
  `npx wrangler dev`           本地运行 Worker
  `nslookup`                   查询 DNS
  `Test-NetConnection`         测试网络连接
  `netsh`                      Windows 网络配置/查询工具
  `netsh winhttp show proxy`   查询 WinHTTP 代理

------------------------------------------------------------------------

# 69.13 本项目中的视觉设计名词

  ---------------------------------------------------------------------------------------------------------
  名词                    含义                         本项目作用
  ----------------------- ---------------------------- ----------------------------------------------------
  Cyberpunk / Cyber       科幻、未来、数字化视觉风格   作为主页整体设计方向

  HUD                     Heads-Up Display，抬头显示器 模拟游戏/科幻系统界面

  Neon                    霓虹视觉                     创建紫色发光效果

  Glitch                  数字故障效果                 模拟屏幕故障、信号干扰

  Scanline                扫描线                       模拟显示器扫描效果

  Scan Beam               扫描光束                     从页面上方到底部移动的发光线

  Crosshair               准星                         HUD 装饰元素

  Overlay                 覆盖层                       叠加在图片或页面上的视觉层

  Glow                    发光                         使用阴影制造霓虹效果

  3D Tilt                 三维倾斜                     鼠标移动时让图片发生 3D 旋转

  Mouse Light             鼠标光晕                     跟随鼠标移动的光照效果

  System HUD              系统状态 HUD                 显示 `SYS / FPS / LOAD`

  FPS                     Frames Per Second            每秒帧数，常用于描述画面刷新流畅度

  LOAD                    Load / 负载                  本项目中只是视觉化的系统负载数字，不代表真实服务器
                                                       CPU 负载

  Responsive              响应式                       让网页根据屏幕尺寸调整布局
  ---------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 69.14 一个特别重要的区分：真实数据 vs 视觉模拟

本项目的：

``` text
SYS ONLINE
FPS 60
LOAD 24%
```

主要是视觉设计。

特别是：

``` js
const load = Math.floor(18 + Math.random() * 25);
```

这个 `LOAD` 是随机生成的数字。

它：

> **不是 Cloudflare Worker 的真实服务器负载。**

同理：

``` text
FPS 60
```

也是页面展示用的视觉信息，而不是实时测量浏览器真实 FPS。

理解这一点很重要：

> **UI 显示的数据不一定等于真实系统监控数据。**

如果以后想做真正的服务器监控，就需要接入真实的数据源/API。

------------------------------------------------------------------------

# 69.15 最容易混淆的几个概念

## Astro vs JavaScript

不是：

``` text
Astro = JavaScript
```

而是：

``` text
JavaScript
    ↓
Astro 使用 JavaScript/TypeScript
    ↓
构建网页
```

Astro 是框架，JavaScript 是编程语言。

------------------------------------------------------------------------

## npm vs Node.js

不是：

``` text
npm = Node.js
```

关系更像：

``` text
Node.js
   ↓
提供 JavaScript 运行环境

npm
   ↓
管理 Node.js 项目的软件包
```

------------------------------------------------------------------------

## Wrangler vs Cloudflare Workers

不是同一个东西：

``` text
Cloudflare Workers
   ↓
真正运行网站/代码的平台

Wrangler
   ↓
操作 Workers 的命令行工具
```

可以类比：

``` text
服务器
   +
管理服务器的 SSH/CLI 工具
```

------------------------------------------------------------------------

## `src` vs `dist`

``` text
src
↓
你写的源码

dist
↓
构建后的最终文件
```

因此：

> 一般修改 `src`，不要直接修改 `dist`。

------------------------------------------------------------------------

## `public` vs `src`

简单理解：

``` text
src
↓
需要经过 Astro 处理的源码

public
↓
可以直接作为静态资源使用的文件
```

例如：

``` text
public/hero.jpg
```

页面可以：

``` html
<img src="/hero.jpg">
```

------------------------------------------------------------------------

## DNS vs HTTP

``` text
DNS
↓
“这个域名对应哪个 IP？”

HTTP/HTTPS
↓
“连接上以后，服务器给我什么网页？”
```

两者不是一回事。

------------------------------------------------------------------------

## Ping vs TCP

``` text
Ping
↓
主要测试 ICMP 层面的可达性

TCP 443
↓
测试能不能真正建立 HTTPS 所需的 TCP 连接
```

所以：

``` text
Ping 成功
```

并不代表：

``` text
HTTPS 一定能打开。
```

这次排查就是一个很好的例子。

------------------------------------------------------------------------

# 69.16 最终建立自己的知识地图

学习这些名词时，可以把它们放进下面这张图：

``` text
                    Web
                     │
        ┌────────────┼────────────┐
        │            │            │
       HTML         CSS      JavaScript
        │            │            │
        └────────────┼────────────┘
                     │
                   Astro
                     │
               Node.js / npm
                     │
                  Build
                     │
                   dist
                     │
                 Wrangler
                     │
             Cloudflare Workers
                     │
        ┌────────────┴────────────┐
        │                         │
       DNS                    HTTPS/TCP
        │                         │
        └────────────┬────────────┘
                     │
                  浏览器
                     │
                    用户
```

真正掌握 Web 开发，不是背很多名词，而是知道：

> **这些东西之间是怎么连接起来的。**

------------------------------------------------------------------------

# 69.17 最终速查表

如果以后看到一个陌生名词，可以先判断它属于哪一层：

``` text
HTML
→ 页面结构

CSS
→ 页面外观

JavaScript
→ 页面行为

Astro
→ 页面框架/构建/组件/路由

Node.js
→ JavaScript 运行环境

npm
→ 软件包管理

Wrangler
→ Cloudflare Workers CLI

Cloudflare Workers
→ 网站/代码运行平台

DNS
→ 域名找 IP

TCP
→ 建立网络连接

HTTPS
→ 加密网页通信

Browser
→ 显示网页
```

------------------------------------------------------------------------

# 69.18 一句话理解所有核心软件

### VS Code

> 写代码的编辑器。

### Node.js

> 让 JavaScript 可以在电脑上运行。

### npm

> 安装和管理 JavaScript 项目的依赖。

### Astro

> 帮你组织、生成和构建现代网页。

### Wrangler

> 用命令行管理和部署 Cloudflare Workers。

### Cloudflare Workers

> 让你的网页/代码运行在 Cloudflare 网络上。

### Cloudflare Dashboard

> 用浏览器管理 Cloudflare 服务。

### Chrome / Edge

> 用来访问和测试网页的浏览器。

### PowerShell

> Windows 上执行命令和脚本的工具。

### Git

> 记录代码修改历史。

### GitHub

> 在线保存和协作管理 Git 项目。

------------------------------------------------------------------------

# 69.19 最后的学习原则

看到：

``` text
Astro
Wrangler
Cloudflare
npm
Node.js
DNS
TCP
HTTPS
```

不要把它们看成一堆互不相关的名词。

把它们理解成一个完整系统：

``` text
你写代码
    ↓
Astro
    ↓
Node.js / npm 提供开发环境
    ↓
Astro Build
    ↓
dist
    ↓
Wrangler
    ↓
Cloudflare Workers
    ↓
DNS 找到服务器
    ↓
TCP 建立连接
    ↓
HTTPS 传输网页
    ↓
Chrome / Edge
    ↓
用户看到网站
```

这条链真正理解以后，你就不再只是"会部署一个网页"，而是开始理解一个现代
Web 网站从源码到用户浏览器之间到底发生了什么。
