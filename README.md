# 😺 Bilibili Auto Like & Fav Bot

> B站秒赞秒收藏机器人，支持批量处理视频，自动点赞、收藏、增加浏览量。

## ✨ 功能

- **👍 自动点赞** — 对指定视频批量点赞（APP端 access_key 认证）
- **⭐ 自动收藏** — 对指定视频批量收藏至默认收藏夹（Web端 Cookie + csrf 认证）
- **👀 增加浏览量** — 模拟播放增加视频热度（APP端 access_key 认证）
- **🔐 扫码登录** — 通过B站TV端二维码扫码登录，获取 access_key
- **🍪 Web端Cookie管理** — 手动配置 Web Cookie 用于收藏接口
- **📊 实时仪表盘** — Web 管理界面，实时查看任务进度与日志
- **⏱️ 延迟控制** — 可自定义每个操作之间的延迟，避免触发风控
- **📋 黑名单** — 支持配置已操作过的视频黑名单，避免重复处理

## 🖥️ 截图

![仪表盘](static/bei.png)

## 📁 项目结构

```
bilibili-like/
├── app.py                  # Flask 主程序（API + 前端路由）
├── bili_login.py           # 扫码登录 & access_key 刷新
├── bili_wbi.py             # WBI 签名（未使用，预留）
├── bili_cookie.json        # 存储 Web端 Cookie 和 access_key
├── requirements.txt        # Python 依赖
├── blacklist.json          # 通用黑名单
├── like_blacklist.json     # 点赞黑名单
├── fav_blacklist.json      # 收藏黑名单
├── proxy_pool.json         # 代理池配置（空，未启用）
├── static/
│   ├── css/
│   │   └── style.css       # 前端样式
│   ├── js/
│   │   ├── dashboard.js    # 控制面板前端逻辑
│   │   └── login.js        # 扫码登录前端逻辑
│   └── bei.png             # 背景图 / 预览图
└── venv/                   # Python 虚拟环境
```

## 🚀 快速开始

### 1. 环境要求

- Python 3.8+
- 依赖安装：

```bash
pip install flask==3.1.1 requests==2.32.3 Pillow==11.1.0 qrcode[pil]==8.0
```

### 2. 启动服务

```bash
cd bilibili-like
python3 app.py
```

服务默认运行在 `http://0.0.0.0:8892`

### 3. 登录 & 配置

#### 方式一：扫码登录（获取 access_key）
1. 访问 `http://<你的IP>:8892/login`
2. 使用B站APP扫描二维码（TV端登录码）
3. 登录成功后自动获取 `access_key`，用于点赞和浏览量

#### 方式二：配置 Web Cookie（用于收藏）
1. 从浏览器F12开发者工具 → 应用 → Cookie 中复制完整 Cookie 字符串
2. 打开 `bili_cookie.json`，填入 `cookie_str` 字段
3. 同时填入 `bili_jct`（即 Cookie 中的 `bili_jct` 值，用于 csrf 鉴权）

> ⚠️ **注意**：B站 Web Cookie 的 `buvid3` 字段会周期性变化，若收藏功能失效需更新 Cookie。

### 4. 使用仪表盘

访问 `http://<你的IP>:8892`

- **视频池**：粘贴或批量输入B站视频 BV 号或 av 号
- **操作选择**：勾选 点赞 / 收藏 / 浏览量
- **延迟设置**：每个操作之间的等待时间（秒）
- **开始任务**：点击即开始顺序处理

## 🛠️ API 接口

| 端点 | 方法 | 说明 |
|------|------|------|
| `/` | GET | 控制面板首页 |
| `/login` | GET | 扫码登录页 |
| `/api/bili/qrcode` | GET | 获取登录二维码 |
| `/api/bili/qrcode/poll` | GET | 轮询扫码状态 |
| `/api/bili/like` | POST | 点赞单个视频 |
| `/api/bili/fav` | POST | 收藏单个视频 |
| `/api/bili/view` | POST | 增加浏览量 |
| `/api/bili/status` | GET | 获取当前配置状态 |

## ⚙️ 配置说明

### `bili_cookie.json`

```json
{
    "access_key": "xxx",
    "refresh_token": "xxx",
    "cookie_str": "DedeUserID=...; SESSDATA=...; bili_jct=...; buvid3=...",
    "bili_jct": "xxx"
}
```

| 字段 | 必需 | 用途 |
|------|------|------|
| `access_key` | 点赞/浏览量必需 | APP端鉴权 |
| `refresh_token` | 可选 | access_key 自动刷新 |
| `cookie_str` | 收藏必需 | Web端完整 Cookie 字符串 |
| `bili_jct` | 收藏必需 | 用于 csrf 参数 |

## ❗ 常见问题

### 收藏失败/鉴权失败
- Web Cookie 过期或 `buvid3` 字段被B站刷新，请重新从浏览器复制最新 Cookie

### access_key 丢失
- 退出重新登录会刷新 access_key，需重新扫码登录

### 风控提示
- 建议调大延迟（3-5秒），避免短时间大量操作
- 单次处理的视频数量不宜过多（建议 ≤ 20）

## 📄 许可证

本项目仅供学习交流使用，请勿用于商业或非法用途。
