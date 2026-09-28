# Docker Desktop 部署、D 盘迁移与项目容器化学习资料

> 整理日期：2026-09-28
>
> 适用项目：
> `D:\MZM\学习\毕业最终\22软件06-220216060624-苗子明\基于Spring Boot架构的家乡特色农产品管理系统设计与实现`
>
> 适用环境：
> Windows 11 家庭版中文版、Docker Desktop 4.92.0、WSL 2.7.14.0、Spring Boot 3.4.1、Vue 2、MySQL 8.0

---

## 1. 本次部署的最终结果

系统已经通过 Docker Compose 成功启动：

```text
浏览器
  |
  v
Nginx 前端容器 ncp-frontend
  http://localhost:8080
  |
  | /api/*
  v
Spring Boot 后端容器 ncp-backend
  http://localhost:1234
  |
  v
MySQL 容器 ncp-mysql
  localhost:3306
  database: db_aps
```

当前容器：

```text
ncp-mysql        mysql:8.0                 127.0.0.1:3306 -> 3306
ncp-backend      ncp-system-backend        127.0.0.1:1234 -> 1234
ncp-frontend     ncp-system-frontend       0.0.0.0:8080 -> 80
```

浏览器访问地址：

```text
http://localhost:8080
```

已经验证：

- 前端首页返回 HTTP 200。
- 后端 Spring Boot 成功启动。
- 后端成功连接 MySQL。
- 数据库 `db_aps` 成功导入。
- 数据库包含 17 张业务表。
- 用户表包含 7 条初始用户数据。
- 图片接口 `/api/img/...` 返回 HTTP 200。
- Docker Compose、Nginx、Spring Boot、MySQL 日志均正常。
- Docker CLI、Docker Compose 和 Buildx 可用。

---

## 2. 为什么最终选择 Docker

这个项目原本需要安装和配置：

```text
Java 17
Maven
MySQL 8
Redis（项目引入了依赖，但实际代码没有真正使用 Redis 缓存）
Node.js
npm
Nginx 或 Vue CLI 开发服务器
```

检查本机后发现：

```text
Java      未安装
Maven     未安装
MySQL     未安装
Node.js   已安装，版本 v22.19.0
npm       已安装，但 PowerShell 执行策略阻止 npm.ps1
Docker    已安装并可运行
```

如果继续使用本机环境，需要：

1. 安装 JDK 17。
2. 安装 Maven。
3. 安装 MySQL 8。
4. 创建数据库并导入 SQL。
5. 修改 Spring Boot 数据库连接信息。
6. 安装和启动 Redis，或修改依赖。
7. 构建前端并启动开发服务器。
8. 配置前端代理到后端。
9. 分别启动前后端进程。

使用 Docker 后，这些环境都封装进容器：

```text
MySQL 运行在 MySQL 官方镜像中
Maven 和 JDK 17 只存在于后端构建阶段
Node.js 和 npm 只存在于前端构建阶段
Nginx 负责运行构建后的 Vue 页面
Spring Boot 运行在 Java 17 JRE 镜像中
```

所以本地不需要安装 Java、Maven、MySQL 和 Nginx。

---

## 3. Docker Desktop 和 WSL 2 的关系

Docker Desktop 在 Windows 上运行 Linux 容器时，底层通常使用 WSL 2。

可以把结构理解成：

```text
Windows
  |
  +-- Docker Desktop 程序
  |
  +-- WSL 2
        |
        +-- docker-desktop 发行版
              |
              +-- main/ext4.vhdx
              |      WSL 系统盘
              |
              +-- disk/docker_data.vhdx
                     Docker 镜像、容器、卷、构建缓存
```

两个 VHDX 分别是：

```text
main/ext4.vhdx
    docker-desktop WSL 发行版自己的 Linux 根文件系统。

disk/docker_data.vhdx
    真正保存 Docker 镜像层、容器可写层、Docker 卷和 BuildKit 构建缓存。
```

日常占空间最快的通常是：

```text
disk/docker_data.vhdx
```

---

## 4. 部署前的磁盘情况

部署前检查到：

```text
C 盘：Windows-SSD，约 200 GB
D 盘：Data，约 274.7 GB，剩余约 192 GB
```

项目、VS Code、微信等已经在 D 盘。

项目位置：

```text
D:\MZM\学习\毕业最终\22软件06-220216060624-苗子明\基于Spring Boot架构的家乡特色农产品管理系统设计与实现
```

项目主要目录：

```text
system-ncp     Spring Boot 后端
vue            Vue 前端
数据库          db_aps.sql
```

---

## 5. 将 Docker 数据迁移到 D 盘

### 5.1 为什么不能只改一个普通设置

Docker Desktop 4.92.0 的界面中有一个：

```text
Settings -> Resources -> Advanced -> Disk image location
```

前端资源里可以看到对应设置键：

```text
vm.resources.wslDataFolder
```

但是本次实验发现：

- 直接向 `settings-store.json` 写入 `WslDataFolder`，Docker 日志报 `unknown settings found`。
- 直接向 `settings-store.json` 写入 `vm.resources.wslDataFolder`，Docker 同样报 unknown。
- 该版本不会因为手工添加这个键就自动迁移数据。
- 官方可行入口是 Docker Desktop 图形界面中的 Disk image location，而不是随意编辑设置文件。

因此本次采用更可靠、对 Docker 透明的方案：

```text
把 C 盘的两个旧数据目录删除，
在原位置创建 Windows 目录 junction，
让 C 盘旧路径自动指向 D 盘真实目录。
```

最终结构：

```text
C:\Users\27215\AppData\Local\Docker\wsl\main
    -> D:\DockerWSLData\main

C:\Users\27215\AppData\Local\Docker\wsl\disk
    -> D:\DockerWSLData\disk
```

### 5.2 什么是 junction

Junction 是 Windows 的目录链接。

它类似快捷方式，但区别是：

- 快捷方式是给用户点击的文件。
- Junction 对程序透明。
- 程序访问 C 盘 junction 路径时，文件系统实际会把操作转给 D 盘目标目录。
- Docker 不需要知道真实文件在 D 盘。

所以 Docker Desktop 设置页仍可能显示：

```text
C:\Users\27215\AppData\Local\Docker\wsl
```

但实际写入位置是：

```text
D:\DockerWSLData
```

### 5.3 迁移前必须停止 Docker 和 WSL

不能在 VHDX 正在运行时直接复制或替换。

停止 Docker Desktop：

```powershell
docker desktop stop
```

如果当前终端还没有加载 Docker 路径，可以使用完整路径：

```powershell
$docker = "$env:LOCALAPPDATA\Programs\DockerDesktop\resources\bin\docker.exe"
& $docker desktop stop
```

停止 WSL：

```powershell
wsl --shutdown
```

确认 WSL 状态：

```powershell
wsl --list --verbose
```

期望看到：

```text
docker-desktop    Stopped    2
```

### 5.4 复制并校验 VHDX

本次迁移复制了：

```text
C:\Users\27215\AppData\Local\Docker\wsl\main\ext4.vhdx
C:\Users\27215\AppData\Local\Docker\wsl\disk\docker_data.vhdx
```

到：

```text
D:\DockerWSLData\main\ext4.vhdx
D:\DockerWSLData\disk\docker_data.vhdx
```

复制后使用 SHA-256 校验，确保源和目标完全一致。

示例：

```powershell
$source = "$env:LOCALAPPDATA\Docker\wsl"
$target = "D:\DockerWSLData"

robocopy "$source\main" "$target\main" /E /COPY:DAT /DCOPY:DAT
robocopy "$source\disk" "$target\disk" /E /COPY:DAT /DCOPY:DAT

Get-FileHash "$source\main\ext4.vhdx" -Algorithm SHA256
Get-FileHash "$target\main\ext4.vhdx" -Algorithm SHA256

Get-FileHash "$source\disk\docker_data.vhdx" -Algorithm SHA256
Get-FileHash "$target\disk\docker_data.vhdx" -Algorithm SHA256
```

只有两个哈希分别相等后，才可以替换 C 盘旧目录。

### 5.5 创建 D 盘真实目录和 C 盘 junction

本次最终使用的路径：

```text
真实目录：
D:\DockerWSLData\main
D:\DockerWSLData\disk

链接目录：
C:\Users\27215\AppData\Local\Docker\wsl\main
C:\Users\27215\AppData\Local\Docker\wsl\disk
```

创建 junction 的基本命令：

```powershell
New-Item -ItemType Junction `
  -Path "$env:LOCALAPPDATA\Docker\wsl\main" `
  -Target "D:\DockerWSLData\main"

New-Item -ItemType Junction `
  -Path "$env:LOCALAPPDATA\Docker\wsl\disk" `
  -Target "D:\DockerWSLData\disk"
```

检查：

```powershell
Get-Item "$env:LOCALAPPDATA\Docker\wsl\main" -Force |
  Select-Object FullName, LinkType, Target

Get-Item "$env:LOCALAPPDATA\Docker\wsl\disk" -Force |
  Select-Object FullName, LinkType, Target
```

预期：

```text
LinkType = Junction
Target   = D:\DockerWSLData\main
Target   = D:\DockerWSLData\disk
```

### 5.6 WSL 注册表路径

WSL 发行版 `docker-desktop` 的注册信息位于：

```text
HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss\{845e76c3-1b11-49c5-8cab-00812d8f1dbd}
```

关键字段：

```text
DistributionName = docker-desktop
BasePath         = \\?\D:\DockerWSLData\main
VhdFileName      = ext4.vhdx
```

本次已把 `BasePath` 改为 D 盘真实目录。

检查命令：

```powershell
$key = "HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss\{845e76c3-1b11-49c5-8cab-00812d8f1dbd}"
Get-ItemProperty -LiteralPath $key |
  Select-Object DistributionName, BasePath, State, VhdFileName
```

### 5.7 最终数据位置验证

当前实际文件：

```text
D:\DockerWSLData\main\ext4.vhdx
    约 0.09 GB

D:\DockerWSLData\disk\docker_data.vhdx
    约 5.47 GB
```

C 盘路径：

```text
C:\Users\27215\AppData\Local\Docker\wsl\main
    Junction -> D:\DockerWSLData\main

C:\Users\27215\AppData\Local\Docker\wsl\disk
    Junction -> D:\DockerWSLData\disk
```

这意味着：

- C 盘不再保存真实的 Docker VHDX。
- D 盘保存 WSL 系统盘和 Docker 数据盘。
- Docker 设置页显示 C 路径只是逻辑显示。
- Docker 更新、拉镜像和构建项目时，实际增长主要发生在 D 盘。

---

## 6. 磁盘占用到底在哪里

最终检查结果：

```text
C 盘剩余：约 99.73 GB
D 盘剩余：约 185.52 GB
```

Docker 相关目录：

```text
C:\Users\27215\AppData\Local\Programs\DockerDesktop
    约 3363.7 MB
    这是 Docker Desktop 程序本体。

C:\Users\27215\.docker
    约 674.6 MB
    这是 Docker CLI 插件和配置。

C:\Users\27215\AppData\Local\Docker
    约 4.2 MB
    junction 和少量本地状态。

C:\Users\27215\AppData\Roaming\Docker
    约 0.6 MB
    Docker Desktop 设置和分析数据。
```

Docker 引擎内部占用：

```text
mysql:8.0 镜像                 约 1.10 GB
ncp-system-backend 镜像        约 520 MB
ncp-system-frontend 镜像       约 142 MB
MySQL 数据卷                  约 218 MB
BuildKit 构建缓存              约 2.581 GB
```

这些镜像、卷和缓存实际位于：

```text
D:\DockerWSLData\disk\docker_data.vhdx
```

结论：

- Docker Desktop 程序本体仍在 C 盘，约 3.4 GB。
- CLI 相关约 0.7 GB 也在 C 盘。
- 镜像、MySQL 数据、容器层和构建缓存都在 D 盘。
- 后续镜像和容器增长主要消耗 D 盘。

---

## 7. Resource Saver 是什么

Docker Desktop 中的：

```text
Resource Saver
```

是省资源功能。

它不是磁盘位置，也不是数据迁移功能。

它的作用是：

- Docker Desktop 空闲时降低 CPU 和内存占用。
- 空闲一段时间后可能停止或暂停 Docker 引擎。
- 下次使用时再恢复。

如果运行长期在线服务，例如 QQ 机器人：

```text
建议关闭 Resource Saver。
```

否则机器人可能因为没有消息而进入空闲状态，Docker 引擎暂停后导致机器人掉线。

---

## 8. `.wslconfig` 是什么

Docker Desktop 界面提示：

```text
You are using the WSL 2 backend, so resource limits are managed by Windows.
```

含义是：

- Docker Desktop 不再直接管理 WSL 2 的内存、CPU 和 swap。
- 这些资源由 Windows 的 WSL 2 配置管理。

Windows 配置文件一般位于：

```text
C:\Users\27215\.wslconfig
```

示例：

```ini
[wsl2]
memory=8GB
processors=8
swap=2GB
swapFile=D:\\DockerWSLData\\swap.vhdx
```

注意：

- `.wslconfig` 文件很小。
- 它只是配置，不会像 VHDX 一样持续变大。
- 如果启用 swap，建议通过 `swapFile` 放到 D 盘。
- 当前机器未创建 `.wslconfig`，也未检测到常规 WSL swap 文件。

修改 `.wslconfig` 后需要执行：

```powershell
wsl --shutdown
```

然后重启 Docker Desktop。

---

## 9. 项目容器化设计

项目不是单体部署，而是前后端分离：

```text
浏览器
  |
  v
frontend 容器
  Nginx 静态页面
  /api/* 反向代理到 backend
  |
  v
backend 容器
  Spring Boot 3.4.1
  Java 17
  端口 1234
  |
  v
mysql 容器
  MySQL 8.0
  数据库 db_aps
```

没有单独启动 Redis，因为检查代码后确认：

- `pom.xml` 引入了 `spring-boot-starter-data-redis`。
- Java 代码中没有实际使用 `RedisTemplate` 或 `StringRedisTemplate`。
- 缓存使用的是 Caffeine：

```yaml
spring:
  cache:
    type: caffeine
```

所以为减少容器数量和资源占用，本 Compose 没有添加 Redis 服务。

---

## 10. 创建的后端 Dockerfile

文件：

```text
system-ncp\Dockerfile
```

内容：

```dockerfile
FROM maven:3.9.9-eclipse-temurin-17 AS build

WORKDIR /workspace

COPY pom.xml .
COPY src ./src

RUN mvn -B -DskipTests package

FROM eclipse-temurin:17-jre

WORKDIR /app

COPY --from=build /workspace/target/*.jar /app/app.jar

RUN mkdir -p /app/files/img

EXPOSE 1234

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

解释：

1. 第一阶段使用 Maven + JDK 17 构建 JAR。
2. 第二阶段只保留 JRE 和 JAR，减小最终镜像。
3. `mvn -DskipTests package` 跳过测试，加快首次构建。
4. 容器工作目录为 `/app`。
5. 上传文件根目录因此变成 `/app/files`。

---

## 11. 创建的前端 Dockerfile

文件：

```text
vue\Dockerfile
```

内容：

```dockerfile
FROM node:20-bookworm-slim AS build

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --legacy-peer-deps

COPY . .
RUN npm run build

FROM nginx:1.27-alpine

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80
```

解释：

1. 第一阶段使用 Node.js 20 安装依赖并构建 Vue。
2. `npm ci` 按 `package-lock.json` 安装确定性依赖。
3. `--legacy-peer-deps` 用于兼容旧 Vue 2 依赖关系。
4. 第二阶段只保留 Nginx 和 `dist` 静态文件。

---

## 12. Nginx 配置

文件：

```text
vue\nginx.conf
```

内容：

```nginx
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;
    index index.html;

    client_max_body_size 20m;

    location /api/ {
        proxy_pass http://backend:1234;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

关键点：

- 浏览器只访问 `8080`。
- 前端请求 `/api/*`。
- Nginx 把 `/api/*` 转发到 `backend:1234`。
- `backend` 是 Compose 内部服务名，由 Docker DNS 解析。
- `try_files` 用于 Vue Router 刷新页面时回退到 `index.html`。

---

## 13. Docker Compose 文件

文件：

```text
docker-compose.yml
```

核心结构：

```yaml
name: ncp-system

services:
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
      MYSQL_DATABASE: "db_aps"
    ports:
      - "127.0.0.1:3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
      - "./数据库/db_aps.sql:/docker-entrypoint-initdb.d/01-db_aps.sql:ro"

  backend:
    build:
      context: ./system-ncp
    environment:
      SPRING_DATASOURCE_URL: "jdbc:mysql://mysql:3306/db_aps?..."
      SPRING_DATASOURCE_USERNAME: "root"
      SPRING_DATASOURCE_PASSWORD: "123456"
    ports:
      - "127.0.0.1:1234:1234"
    volumes:
      - "./system-ncp/files:/app/files"

  frontend:
    build:
      context: ./vue
    ports:
      - "8080:80"

volumes:
  mysql-data:
```

关键解释：

### 13.1 MySQL 初始化

```text
./数据库/db_aps.sql
```

挂载到容器：

```text
/docker-entrypoint-initdb.d/01-db_aps.sql
```

MySQL 镜像第一次启动且数据目录为空时，会自动执行这个 SQL 文件。

注意：

- 只在第一次初始化时执行。
- 数据库卷创建后，修改 SQL 不会自动重新导入。
- 需要重新导入时必须明确删除 MySQL 数据卷。

### 13.2 后端数据库地址

代码中原配置是：

```text
jdbc:mysql://localhost:3306/db_aps
```

容器内 `localhost` 表示后端容器自己，不是 MySQL 容器。

Compose 通过环境变量覆盖为：

```text
jdbc:mysql://mysql:3306/db_aps
```

其中 `mysql` 是 Compose 服务名。

### 13.3 上传目录

代码：

```java
public final static String FILE_BASE_PATH =
    System.getProperty("user.dir") + "/files/";
```

后端容器工作目录是：

```text
/app
```

因此使用：

```text
system-ncp\files
```

挂载到：

```text
/app/files
```

优点：

- 上传图片不会写进容器临时层。
- 重建容器后文件仍然存在。
- 项目位于 D 盘，所以上传图片实际也保存在 D 盘。

---

## 14. 首次构建和启动

在项目根目录执行：

```powershell
docker compose up -d --build
```

首次构建实际耗时约：

```text
前端 npm ci：约 47 秒
前端 npm run build：约 92 秒
后端 Maven 构建：约 182 秒
总体构建：约 264 秒
```

最终输出：

```text
Image mysql:8.0 Pulled
Image ncp-system-backend Built
Image ncp-system-frontend Built
Network ncp-system_ncp-network Created
Volume ncp-system_mysql-data Created
Container ncp-mysql Healthy
Container ncp-backend Started
Container ncp-frontend Started
```

浏览器访问：

```text
http://localhost:8080
```

---

## 15. 已验证的系统状态

### 15.1 容器状态

```powershell
docker compose ps
```

结果：

```text
ncp-backend   Up
ncp-frontend  Up
ncp-mysql     Up (healthy)
```

### 15.2 后端日志

```powershell
docker compose logs -f backend
```

关键日志：

```text
Starting SpringbootApplication
HikariPool-1 - Starting...
HikariPool-1 - Start completed.
Tomcat started on port 1234
Started SpringbootApplication
```

说明：

- Spring Boot 启动成功。
- 数据源连接成功。
- Tomcat 监听 1234。

### 15.3 数据库验证

```powershell
docker exec ncp-mysql mysql -uroot -p123456 -N -e `
  "SELECT COUNT(*) FROM information_schema.tables WHERE table_schema='db_aps';"
```

结果：

```text
17
```

用户数：

```powershell
docker exec ncp-mysql mysql -uroot -p123456 -N -e `
  "SELECT COUNT(*) FROM db_aps.user;"
```

结果：

```text
7
```

### 15.4 浏览器和图片接口

首页：

```powershell
Invoke-WebRequest http://localhost:8080 -UseBasicParsing
```

结果：

```text
HTTP 200
```

图片接口：

```powershell
Invoke-WebRequest `
  http://localhost:1234/api/img/1738583441855.png `
  -UseBasicParsing
```

结果：

```text
HTTP 200
```

---

## 16. 日常启动、停止和查看日志

进入项目根目录：

```powershell
cd "D:\MZM\学习\毕业最终\22软件06-220216060624-苗子明\基于Spring Boot架构的家乡特色农产品管理系统设计与实现"
```

启动但不重新构建：

```powershell
docker compose up -d
```

停止容器：

```powershell
docker compose stop
```

启动已停止容器：

```powershell
docker compose start
```

重启后端：

```powershell
docker compose restart backend
```

查看全部日志：

```powershell
docker compose logs -f
```

只看后端：

```powershell
docker compose logs -f backend
```

只看 MySQL：

```powershell
docker compose logs -f mysql
```

只看前端：

```powershell
docker compose logs -f frontend
```

进入后端容器：

```powershell
docker exec -it ncp-backend sh
```

进入 MySQL：

```powershell
docker exec -it ncp-mysql mysql -uroot -p123456
```

---

## 17. 构建相关命令

只重新构建：

```powershell
docker compose build
```

重新构建并启动：

```powershell
docker compose up -d --build
```

只重建后端：

```powershell
docker compose build backend
docker compose up -d backend
```

只重建前端：

```powershell
docker compose build frontend
docker compose up -d frontend
```

查看镜像：

```powershell
docker images
```

查看空间：

```powershell
docker system df
```

详细空间：

```powershell
docker system df -v
```

---

## 18. 数据卷和删除注意事项

普通停止：

```powershell
docker compose down
```

这个命令删除容器和网络，但默认保留：

```text
ncp-system_mysql-data
```

所以数据库数据还在。

危险命令：

```powershell
docker compose down -v
```

`-v` 会删除 Compose 定义的命名卷，包括：

```text
ncp-system_mysql-data
```

后果：

- MySQL 数据全部丢失。
- 下次启动时，如果 SQL 文件仍在，会重新执行初始化 SQL。
- 用户新增的数据不会恢复。

只有在明确需要重置数据库时才能使用。

---

## 19. 常见故障排查

### 19.1 页面打不开

检查容器：

```powershell
docker compose ps
```

检查前端日志：

```powershell
docker compose logs --tail=100 frontend
```

检查端口：

```powershell
Get-NetTCPConnection -LocalPort 8080
```

### 19.2 `/api` 返回错误

检查后端：

```powershell
docker compose logs --tail=200 backend
```

检查 Nginx：

```powershell
docker compose logs --tail=100 frontend
```

从容器内部验证后端：

```powershell
docker exec ncp-frontend wget -qO- http://backend:1234
```

### 19.3 后端连接 MySQL 失败

检查 MySQL 是否健康：

```powershell
docker compose ps mysql
```

检查数据库是否存在：

```powershell
docker exec ncp-mysql mysql -uroot -p123456 -e "SHOW DATABASES;"
```

检查后端环境变量：

```powershell
docker exec ncp-backend env | Select-String SPRING_DATASOURCE
```

### 19.4 修改 SQL 后没有生效

原因：

MySQL 初始化脚本只在空数据目录第一次启动时执行。

如果确实需要重新导入：

```powershell
docker compose down
docker volume rm ncp-system_mysql-data
docker compose up -d
```

再次强调：这会删除现有数据库。

### 19.5 端口冲突

检查 8080：

```powershell
Get-NetTCPConnection -LocalPort 8080
```

检查 1234：

```powershell
Get-NetTCPConnection -LocalPort 1234
```

检查 3306：

```powershell
Get-NetTCPConnection -LocalPort 3306
```

如果占用，可以修改 `docker-compose.yml` 左侧端口。

例如：

```yaml
ports:
  - "8081:80"
```

访问地址就变为：

```text
http://localhost:8081
```

---

## 20. Docker Desktop 汉化情况

检查 Docker Desktop 4.92.0：

```text
frontend\locales\en-US.pak
```

只发现英文语言包。

没有：

```text
zh-CN.pak
zh-Hans.pak
```

因此该版本没有官方简体中文界面。

不建议直接解包并修改：

```text
frontend\resources\app.asar
```

原因：

- Docker Desktop 升级会覆盖。
- 完整性检查可能阻止启动。
- 后端错误、日志和 CLI 仍然是英文。
- 容易破坏正常使用。

---

## 21. VS Code 中文设置

本次安装的扩展：

```text
MS-CEINTL.vscode-language-pack-zh-hans
```

安装命令：

```powershell
code --install-extension MS-CEINTL.vscode-language-pack-zh-hans --force
```

用户的永久参数文件：

```text
C:\Users\27215\.vscode\argv.json
```

已加入：

```json
"locale": "zh-cn"
```

重启 VS Code 后生效。

检查扩展：

```powershell
code --list-extensions --show-versions |
  Select-String "language-pack-zh-hans"
```

---

## 22. 安全注意事项

当前配置适合本地学习和开发，不适合直接暴露到公网。

需要修改的地方：

1. MySQL root 密码当前为 `123456`，过于简单。
2. `application.yml` 中仍然保存数据库密码和邮箱授权信息。
3. MySQL 端口只绑定到 `127.0.0.1`，这个做法是正确的。
4. 后端端口只绑定到 `127.0.0.1`，这个做法正确。
5. 前端对局域网开放 `8080`，如不需要局域网访问应改成：

```yaml
ports:
  - "127.0.0.1:8080:80"
```

更安全的生产方式：

- 使用 `.env` 保存密码，不把密码写进 Compose。
- 使用独立 MySQL 用户，避免后端使用 root。
- 邮箱密码改用环境变量或密钥管理。
- 生产环境使用 HTTPS。
- 定期备份数据库和上传文件。
- 不执行 `docker compose down -v`。

---

## 23. 备份与恢复

### 23.1 备份 MySQL

```powershell
docker exec ncp-mysql mysqldump `
  -uroot -p123456 `
  --single-transaction `
  --routines `
  --triggers `
  db_aps > db_aps_backup.sql
```

建议把备份放在 D 盘。

### 23.2 恢复 MySQL

```powershell
Get-Content .\db_aps_backup.sql |
  docker exec -i ncp-mysql mysql -uroot -p123456 db_aps
```

### 23.3 备份上传文件

上传文件目录：

```text
system-ncp\files
```

直接复制整个目录即可。

### 23.4 备份 Docker VHDX

关闭 Docker 后备份：

```text
D:\DockerWSLData
```

不要在 Docker 运行时直接复制 VHDX 作为一致性备份。

---

## 24. 本次部署最有价值的学习点

1. Docker Desktop 的“程序”和“数据”不是一回事。

```text
程序可以在 C 盘。
镜像和容器数据可以迁移到 D 盘。
```

2. Docker Desktop 设置页显示的路径不一定是真实存储路径。

通过 junction，C 盘路径可以透明指向 D 盘。

3. 不能只看界面显示，必须检查实际 VHDX 的写入时间、文件大小和磁盘空间变化。

4. Docker 数据迁移前必须：

```text
停止 Docker
停止 WSL
复制 VHDX
校验 SHA-256
再替换目录
```

5. Windows 容器构建适合使用：

```text
后端：Maven + JDK 17 多阶段构建
前端：Node 构建 + Nginx 运行
数据库：官方 MySQL 镜像 + 初始化 SQL
```

6. 前后端分离项目必须解决两个地址问题：

```text
浏览器请求：/api
Nginx 代理：http://backend:1234
后端连接：jdbc:mysql://mysql:3306
```

7. 上传文件必须使用挂载卷。

否则重建后端容器后，用户上传的图片会消失。

8. `docker compose down` 和 `docker compose down -v` 风险完全不同。

前者保留数据库卷，后者会删除数据库卷。

9. 首次构建慢，是因为需要下载：

```text
MySQL 镜像
Maven 镜像和依赖
JDK/JRE 镜像
Node 镜像和 npm 依赖
Nginx 镜像
```

后续有缓存后会快很多。

10. 本地开发用 Docker，可以避免污染 Windows 的 Java、Maven、MySQL 和 Node 环境。

---

## 25. 最常用命令速查

启动系统：

```powershell
docker compose up -d
```

首次构建并启动：

```powershell
docker compose up -d --build
```

停止：

```powershell
docker compose stop
```

继续使用已有容器：

```powershell
docker compose start
```

查看状态：

```powershell
docker compose ps
```

查看所有日志：

```powershell
docker compose logs -f
```

查看后端日志：

```powershell
docker compose logs -f backend
```

查看空间：

```powershell
docker system df -v
```

检查 D 盘 Docker 数据：

```powershell
Get-Item "D:\DockerWSLData\main\ext4.vhdx" |
  Select-Object FullName, Length, LastWriteTime

Get-Item "D:\DockerWSLData\disk\docker_data.vhdx" |
  Select-Object FullName, Length, LastWriteTime
```

检查 C 盘 junction：

```powershell
Get-Item "$env:LOCALAPPDATA\Docker\wsl\main" -Force |
  Select-Object FullName, LinkType, Target

Get-Item "$env:LOCALAPPDATA\Docker\wsl\disk" -Force |
  Select-Object FullName, LinkType, Target
```

---

## 26. 最终路径汇总

项目：

```text
D:\MZM\学习\毕业最终\22软件06-220216060624-苗子明\基于Spring Boot架构的家乡特色农产品管理系统设计与实现
```

Compose：

```text
docker-compose.yml
```

后端 Dockerfile：

```text
system-ncp\Dockerfile
```

前端 Dockerfile：

```text
vue\Dockerfile
```

Nginx 配置：

```text
vue\nginx.conf
```

运行说明：

```text
DOCKER-运行说明.md
```

数据库 SQL：

```text
数据库\db_aps.sql
```

上传文件：

```text
system-ncp\files
```

Docker WSL 系统盘：

```text
D:\DockerWSLData\main\ext4.vhdx
```

Docker 镜像和容器数据盘：

```text
D:\DockerWSLData\disk\docker_data.vhdx
```

C 盘链接目录：

```text
C:\Users\27215\AppData\Local\Docker\wsl\main
C:\Users\27215\AppData\Local\Docker\wsl\disk
```

浏览器地址：

```text
http://localhost:8080
```

