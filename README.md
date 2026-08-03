# V2ray/Clash代理连通性测试工具

> 纯前端代理节点批量检测工具 —— 无需后端、无需安装，打开即用。

![纯前端](https://img.shields.io/badge/纯前端-无后端依赖-brightgreen)
![零依赖](https://img.shields.io/badge/依赖-0-blue)
![协议](https://img.shields.io/badge/协议-vmess%20%7C%20vless%20%7C%20trojan%20%7C%20ss%20%7C%20ssr%20%7C%20socks5-orange)
![主题](https://img.shields.io/badge/主题-深色%20%7C%20浅色-purple)

---

## 简介

一个单文件、零依赖的代理节点连通性测试工具。支持多种协议解析、批量导入、并发探测、延迟排序，帮助你快速筛选出可用节点。

**所有数据仅在浏览器本地处理，不会上传到任何服务器。**

---

## 功能特性

### 协议支持

| 协议 | 解析方式 | 备注 |
|------|----------|------|
| `vmess://` | Base64 JSON | 支持 ws/tcp/h2 等传输层 |
| `vless://` | 标准 URI | 支持 reality、grpc 等 |
| `trojan://` | 标准 URI | 支持 sni、跳过证书 |
| `ss://` | 双格式兼容 | `base64@host:port` 和 `base64(method:pass@host:port)` |
| `ssr://` | Base64 编码 | 完整解析混淆/协议 |
| `socks5://` | URL 解析 | — |
| `http(s)://` | URL 解析 | — |

### 导入方式

- **单节点链接** — 粘贴一条链接即可导入
- **订阅链接** — 自动 fetch 拉取（含 CORS 代理回退）
- **批量粘贴** — 每行一条链接或整段 Base64
- **Clash YAML** — 粘贴完整配置文件，自动提取 `proxies` 段

### 测试能力

- WebSocket 探测（ws 节点握手 / 非 ws 节点 TCP 层探测）
- 可配置并发数（1 ~ 100），默认 10
- 测试选中 / 测试全部可测
- 实时进度条 + 百分比
- 测试完成自动按延迟升序排序
- 延迟分级着色（<100ms 绿 / <300ms 黄 / >=300ms 红）

### 表格操作

- 按任意列排序（点击表头）
- 筛选：全部 / 仅 ws / 存活 / 超时
- 行内编辑节点名称
- 删除选中 / 清空列表
- 复制选中节点原始链接
- 导出 CSV / TXT 报告

### 界面

- 深色 / 浅色主题一键切换
- 默认跟随系统 `prefers-color-scheme`
- 手动切换后记住偏好（localStorage）
- 响应式布局，适配移动端

---

## 快速开始

### 方式一：直接打开

将代码保存到本地，双击用浏览器打开即可。

### 方式二：GitHub Pages

1. Fork / Clone 本仓库
2. 在仓库 Settings → Pages 中选择 `main` 分支
3. 访问 `https://<username>.github.io/<repo>/`

### 方式三：本地服务器

```bash
# 任意静态服务器均可
npx serve .
# 或
python -m http.server 8080
```

> **注意**：测试功能依赖 WebSocket。若以 `https://` 访问，所有探测将强制使用 `wss://`；若以 `http://` 访问或 `file://` 打开，则按节点自身 TLS 设置决定。

---

## 测试原理

```
┌─────────────────────────────────────────────────┐
│  ws/wss 节点                                     │
│  → 直接发起 WebSocket 握手                        │
│  → onopen 触发 = 存活，记录耗时                   │
│  → onerror/onclose = 超时                        │
├─────────────────────────────────────────────────┤
│  非 ws 节点（tcp/trojan/ss 等）                   │
│  → 仍用 WebSocket 尝试连接                        │
│  → onerror 触发 = TCP/TLS 层可达（服务器拒绝升级） │
│  → 无响应超时 = 超时                              │
└─────────────────────────────────────────────────┘
```

- 默认超时：**10 秒**
- 并发池模式：N 个 worker 同时执行，完成一个立即取下一个

---

## 项目结构

```
.
├── index.html      # 唯一文件，包含全部 HTML + CSS + JS
└── README.md
```

零构建、零依赖、零框架。

---

## 截图

| 浅色模式 | 深色模式 |
|----------|----------|
| ![浅色](https://github.com/ljy969/proxy-tester/blob/main/proxy-tester/docs/screenshot-light.png) | ![深色](https://github.com/ljy969/proxy-tester/blob/main/proxy-tester/docs/screenshot-dark.png) |

---

## 配置说明

| 项目 | 默认值 | 说明 |
|------|--------|------|
| 并发数 | 10 | 工具栏可手动修改，范围 1~100 |
| 超时时间 | 10000ms | 硬编码于 `testNodeLatency` 函数 |
| 主题 | 跟随系统 | 手动切换后持久化到 localStorage |

如需修改超时时间，搜索 `timeoutMs = 10000` 改为目标值即可。

---

## 贡献

欢迎提交 Issue 和 Pull Request：

1. Fork 本仓库
2. 创建特性分支：`git checkout -b feature/xxx`
3. 提交更改：`git commit -m 'feat: xxx'`
4. 推送分支：`git push origin feature/xxx`
5. 发起 Pull Request

---

## License

[MIT](LICENSE)

---

## 免责声明

- 本工具仅用于学习和测试网络连通性
- 所有解析与探测均在浏览器本地完成，不涉及任何数据传输
- 请勿用于任何违反当地法律法规的用途
- 使用本工具即表示你同意自行承担相关责任
```
```

> ⚠️ **注意**：测试功能依赖 WebSocket。若以 `https://` 访问，所有探测将强制使用 `wss://`；若以 `http://` 访问或 `file://` 打开，则按节点自身 TLS 设置决定。

---

## 测试原理

```
┌─────────────────────────────────────────────────┐
│  ws/wss 节点                                     │
│  → 直接发起 WebSocket 握手                        │
│  → onopen 触发 = 存活，记录耗时                   │
│  → onerror/onclose = 超时                        │
├─────────────────────────────────────────────────┤
│  非 ws 节点（tcp/trojan/ss 等）                   │
│  → 仍用 WebSocket 尝试连接                        │
│  → onerror 触发 = TCP/TLS 层可达（服务器拒绝升级） │
│  → 无响应超时 = 超时                              │
└─────────────────────────────────────────────────┘
```

- 默认超时：**10 秒**
- 并发池模式：N 个 worker 同时执行，完成一个立即取下一个

---

## 截图

### 功能演示

| 批量导入节点 | 并发测试中 | 测试结果排序 |
|:------------:|:----------:|:------------:|
| ![导入](https://raw.githubusercontent.com/ljy969/proxy-tester/main/proxy-tester/docs/demo-import.png) | ![测试](https://raw.githubusercontent.com/ljy969/proxy-tester/main/proxy-tester/docs/demo-testing.png) | ![结果](https://raw.githubusercontent.com/ljy969/proxy-tester/main/proxy-tester/docs/demo-result.png) |
---

## 配置说明

| 项目   | 默认值     | 说明                        |
| ---- | ------- | ------------------------- |
| 并发数  | 10      | 工具栏可手动修改，范围 1~100         |
| 超时时间 | 10000ms | 硬编码于 `testNodeLatency` 函数 |
| 主题   | 跟随系统    | 手动切换后持久化到 localStorage    |

如需修改超时时间，搜索 `timeoutMs = 10000` 改为目标值即可。

---

## 贡献

欢迎提交 Issue 和 Pull Request：

1. Fork 本仓库
2. 创建特性分支：`git checkout -b feature/xxx`
3. 提交更改：`git commit -m 'feat: xxx'`
4. 推送分支：`git push origin feature/xxx`
5. 发起 Pull Request

---

## License

[MIT](LICENSE) © 2026

---

## ⚠️ 免责声明

- 本工具仅用于**学习和测试网络连通性**
- 所有解析与探测均在浏览器本地完成，不涉及任何数据传输
- 请勿用于任何违反当地法律法规的用途
- 使用本工具即表示你同意自行承担相关责任
