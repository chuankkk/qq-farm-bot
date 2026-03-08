# QQ 农场多账号挂机 + Web 面板

基于 Node.js 的 QQ 农场自动化工具，支持多账号管理、Web 控制面板、实时日志与数据分析。

## 技术栈

**后端**

[<img src="https://skillicons.dev/icons?i=nodejs" height="48" title="Node.js 20+" />](https://nodejs.org/)
[<img src="https://skillicons.dev/icons?i=express" height="48" title="Express 4" />](https://expressjs.com/)
[<img src="https://skillicons.dev/icons?i=socketio" height="48" title="Socket.io 4" />](https://socket.io/)

**前端**

[<img src="https://skillicons.dev/icons?i=vue" height="48" title="Vue 3" />](https://vuejs.org/)
[<img src="https://skillicons.dev/icons?i=vite" height="48" title="Vite 7" />](https://vitejs.dev/)
[<img src="https://skillicons.dev/icons?i=ts" height="48" title="TypeScript 5" />](https://www.typescriptlang.org/)
[<img src="https://cdn.simpleicons.org/pinia/FFD859" height="48" title="Pinia 3" />](https://pinia.vuejs.org/)
[<img src="https://skillicons.dev/icons?i=unocss" height="48" title="UnoCSS" />](https://unocss.dev/)

**部署**

[<img src="https://skillicons.dev/icons?i=docker" height="48" title="Docker Compose" />](https://docs.docker.com/compose/)
[<img src="https://skillicons.dev/icons?i=pnpm" height="48" title="pnpm 10" />](https://pnpm.io/)
[<img src="https://skillicons.dev/icons?i=githubactions" height="48" title="GitHub Actions" />](https://github.com/features/actions)

---

## 功能特性

### 多账号管理
- 账号新增、编辑、删除、启动、停止
- 扫码登录（QQ / 微信）与手动输入 Code
- 账号被踢下线自动删除
- 账号连续离线超时自动删除
- 账号离线推送通知（支持 Bark、自定义 Webhook 等）

### 自动化能力
- 农场：收获、种植、浇水、除草、除虫、铲除、土地升级
- 仓库：收获后自动出售果实
- 好友：自动偷菜 / 帮忙 / 捣乱
- 任务：自动检查并领取
- 好友黑名单：跳过指定好友
- 静默时段：指定时间段内不执行好友操作

### Web 面板
- 概览 / 农场 / 背包 / 好友 / 分析 / 账号 / 设置页面
- 实时日志，支持按账号、模块、事件、级别、关键词、时间范围筛选
- 深色 / 浅色主题切换

### 分析页
支持按以下维度排序作物：
- 经验效率 / 普通肥经验效率
- 净利润效率 / 普通肥净利润效率
- 等级要求

---

## 环境要求

- 源码运行：Node.js 20+，pnpm（推荐通过 `corepack enable` 启用）
- Docker 部署：Docker + Docker Compose（推荐，无需手动安装 Node.js）
- 二进制发布版：无需任何运行时

---

## 安装与启动（源码方式）

### Windows

```powershell
# 1. 安装 Node.js 20+（https://nodejs.org/）并启用 pnpm
node -v
corepack enable
pnpm -v

# 2. 安装依赖并构建前端
cd qq-farm-bot-ui
pnpm install
pnpm build:web

# 3. 启动
pnpm dev:core

# （可选）设置管理密码后启动
$env:ADMIN_PASSWORD="你的强密码"
pnpm dev:core
```

### Linux（Ubuntu/Debian）

```bash
# 1. 安装 Node.js 20+
sudo apt update && sudo apt install -y curl
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
corepack enable

# 2. 安装依赖并构建前端
cd qq-farm-bot-ui
pnpm install
pnpm build:web

# 3. 启动
pnpm dev:core

# （可选）设置管理密码后启动
ADMIN_PASSWORD='你的强密码' pnpm dev:core
```

启动后访问面板：
- 本机：`http://localhost:3000`
- 局域网：`http://<你的IP>:3000`

---

## Docker 部署（推荐）

Docker Compose 包含三个服务：

| 服务名 | 说明 | 默认端口 |
|--------|------|---------|
| `youxian-qq-farm-bot-ui` | 主服务（核心引擎 + Web 面板） | `8081` |
| `youxian-ws-keeper` | QQ WebSocket 保活服务 | `4001` |
| `youxian-ws-keeper-wx` | 微信 WebSocket 保活服务 | `4002` |

### 快速启动

```bash
# 1. 克隆项目
git clone https://github.com/你的用户名/qq-farm-bot-ui.git
cd qq-farm-bot-ui

# 2. 修改管理密码（重要！）
# 编辑 docker-compose.yml，将 ADMIN_PASSWORD 改为你的强密码

# 3. 构建并后台启动
docker compose up -d --build

# 4. 查看实时日志
docker compose logs -f

# 5. 停止并移除容器
docker compose down
```

访问面板：`http://服务器IP:8081`

### 设置管理密码

在 `docker-compose.yml` 的 `environment` 中配置：

```yaml
services:
  youxian-qq-farm-bot-ui:
    environment:
      ADMIN_PASSWORD: 你的强密码   # 修改此处
```

修改后执行 `docker compose up -d` 重启生效。

### 数据持久化

`docker-compose.yml` 已将数据目录挂载至宿主机：

| 宿主机路径 | 容器内路径 | 说明 |
|-----------|-----------|------|
| `./data`  | `/app/core/data` | 账号、配置、运行数据 |

账号与配置数据保存在：
- `./data/accounts.json` — 账号列表
- `./data/store.json` — 运行状态与配置

### 容器常用命令

```bash
# 查看运行中的容器
docker ps

# 进入主容器 Shell
docker exec -it youxian-qq-farm-bot-ui sh

# 重启单个服务
docker compose restart youxian-qq-farm-bot-ui

# 拉取最新代码后重新构建
git pull && docker compose up -d --build
```

---

## 二进制发布版（无需 Node.js）

### 构建

```bash
pnpm install
pnpm package:release
```

产物输出在 `dist/` 目录：

| 平台 | 文件名 |
|------|--------|
| Windows x64 | `qq-farm-bot-win-x64.exe` |
| Linux x64 | `qq-farm-bot-linux-x64` |
| macOS Intel | `qq-farm-bot-macos-x64` |
| macOS Apple Silicon | `qq-farm-bot-macos-arm64` |

### 运行

```bash
# Windows：双击 exe 或在终端执行
.\qq-farm-bot-win-x64.exe

# Linux / macOS
chmod +x ./qq-farm-bot-linux-x64 && ./qq-farm-bot-linux-x64
```

程序会在可执行文件同级目录自动创建 `data/` 并写入 `store.json`、`accounts.json`。

---

## 登录与安全

- 面板首次访问需要登录
- 默认管理密码：`admin`
- **建议部署后立即修改为强密码**（通过环境变量 `ADMIN_PASSWORD` 设置）
- 生产环境建议在面板前添加 Nginx 反向代理并配置 HTTPS

---

## 项目结构

```
qq-farm-bot-ui/
├── core/                  # 后端（Node.js 机器人引擎）
│   ├── src/
│   │   ├── config/        # 配置管理
│   │   ├── controllers/   # HTTP API（账号、管理、日志等）
│   │   ├── gameConfig/    # 游戏静态数据（作物、等级等 JSON）
│   │   ├── models/        # 数据模型与持久化（accounts、store）
│   │   ├── proto/         # Protobuf 协议定义
│   │   ├── runtime/       # 运行时引擎与 Worker 管理
│   │   └── services/      # 业务逻辑（农场、好友、任务、仓库等）
│   ├── panel/             # 内嵌前端静态资源（build:web 产物）
│   ├── Dockerfile
│   └── client.js          # 主进程入口
├── web/                   # 前端（Vue 3 + Vite）
│   ├── src/
│   │   ├── api/           # API 客户端（封装 fetch/axios）
│   │   ├── components/    # 通用 Vue 组件
│   │   ├── stores/        # Pinia 状态管理
│   │   └── views/         # 页面（概览/农场/背包/好友/分析/设置）
│   └── vite.config.ts
├── ws-keeper/             # WebSocket 保活中间层（QQ / 微信）
│   ├── index.js           # 保活服务主逻辑
│   ├── utils/             # 加密工具（crypto-wasm）
│   └── Dockerfile
├── sniff9988.py           # QQ 扫码登录回调抓取工具（开发辅助）
├── docker-compose.yml     # Docker 一键部署配置
├── pnpm-workspace.yaml    # pnpm monorepo 配置
└── package.json
```

---

## 常见问题

**Q: 扫码后账号不上线？**

确认 `youxian-ws-keeper` 容器正常运行，并检查端口 `4001` 是否在防火墙中放开。

**Q: 面板无法访问？**

检查 `docker compose ps` 中主服务状态是否为 `Up`，并确认服务器防火墙放开了 `8081` 端口。

**Q: 数据丢失了？**

`./data/` 目录挂载到宿主机，只要 `./data/accounts.json` 文件存在，数据就不会丢失。重建容器不会影响数据。

**Q: 如何更新到最新版本？**

```bash
git pull
docker compose up -d --build
```

---

## 特别感谢

- 核心功能：[linguo2625469/qq-farm-bot](https://github.com/linguo2625469/qq-farm-bot)
- 部分功能：[QianChenJun/qq-farm-bot](https://github.com/QianChenJun/qq-farm-bot)
- 扫码登录：[lkeme/QRLib](https://github.com/lkeme/QRLib)
- 推送通知：[imaegoo/pushoo](https://github.com/imaegoo/pushoo)
- WASM加密：[sulimu2/qq-farm-bot](https://github.com/sulimu2/qq-farm-bot)

## 免责声明

本项目仅供学习与研究用途。使用本工具可能违反游戏服务条款，由此产生的一切后果由使用者自行承担。
