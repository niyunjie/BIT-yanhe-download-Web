# BIT 延河课程下载 Web

一个以浏览器为主的 Web 应用，用于浏览 BIT 延河课程、播放课程录像、下载 HLS TS 视频流，以及从屏幕流中提取类似课件页面的画面并导出为 PDF。

本项目包含一个 Vue 前端和一个轻量 Node.js 后端。后端负责 BIT/延河认证、课程元数据请求、播放列表解析和媒体分片代理。前端负责在线播放、TS 文件保存和浏览器端 PDF 生成。

## 功能

- BIT 统一身份认证登录，并提取延河 token。
- 为需要二次验证的账号提供手动延河 token 登录兜底方案。
- 课程搜索，分页大小会根据浏览器视口动态调整。
- 课程详情页，用于选择具体课次。
- 通过 `hls.js` 在浏览器中播放 HLS。
- 在浏览器端下载主视频或屏幕流的 TS 文件。
- 通过 `jsPDF` 在浏览器端从屏幕流中提取课件页面并导出 PDF。
- 当前浏览器下载和 PDF 导出流程不需要本地安装 FFmpeg。

## 项目结构

```text
backend/   Node.js API 服务和 Yanhe/CAS 集成
frontend/  Vue 3 + Vite 浏览器应用
```

## 隐私与安全

本仓库不应包含个人凭据、延河 token、Cookie、下载的视频、生成的 PDF 或本地运行文件。

重要说明：

- 用户密码只会在登录请求中提交给后端，本应用不会有意持久化保存密码。
- 延河 token 会存储在浏览器 local storage 中，用于本地恢复登录状态。
- 不要提交 `.env` 文件、包含账号信息的浏览器截图、下载的课程视频、生成的 PDF 或 `backend/downloads/`。
- 仅在你有权限访问的账号和课程材料范围内使用本项目。

## 环境要求

- Node.js `20.19+` 或 `22.12+`
- npm
- 推荐使用现代 Chromium 内核浏览器，以获得更好的 HLS 播放和浏览器端导出体验。

## 本地开发

安装依赖：

```bash
cd backend
npm install

cd ../frontend
npm install
```

启动后端：

```bash
cd backend
npm run dev
```

启动前端：

```bash
cd frontend
npm run dev
```

默认本地地址：

```text
Frontend: http://127.0.0.1:5173/
Backend:  http://127.0.0.1:8787/
```

## 前端配置

可以通过 `VITE_API_BASE_URL` 指定前端使用的后端地址。

本地开发时，应用默认使用：

```text
http://<current-hostname>:8787
```

如果通过 Nginx 做同源生产部署，可以这样构建：

```bash
VITE_API_BASE_URL=/api npm run build
```

Windows PowerShell：

```powershell
$env:VITE_API_BASE_URL="/api"
npm run build
```

## 后端配置

后端支持以下环境变量：

```text
HOST=0.0.0.0
PORT=8787
```

示例：

```bash
HOST=0.0.0.0 PORT=8787 npm start
```

## 生产部署

最简单的部署方式是在同一台服务器上托管前端和后端：

```text
https://your-server/
  -> frontend/dist 静态文件

https://your-server/api/
  -> 反向代理到 127.0.0.1:8787 上的后端服务
```

推荐的生产环境组件：

- Nginx 用于托管前端静态文件，并将 `/api/` 反向代理到后端。
- PM2 或 systemd 用于保持后端进程常驻。
- 如果服务暴露在非可信网络中，建议启用 HTTPS。

Nginx 配置示例：

```nginx
location / {
  root /path/to/BIT-yanhe-download-Web/frontend/dist;
  try_files $uri $uri/ /index.html;
}

location /api/ {
  proxy_pass http://127.0.0.1:8787/api/;
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
}
```

## 构建检查

前端：

```bash
cd frontend
npm run build
```

后端语法检查：

```bash
cd backend
npm run check
```

## 致谢

本项目开发过程中参考了以下开源项目：

- [bit-admin/Yanhekt-AutoSlides](https://github.com/bit-admin/Yanhekt-AutoSlides)：参考了从延河屏幕流录像中提取类似课件页面的思路。
- [BITNP/BIT_yanhe_download](https://github.com/BITNP/BIT_yanhe_download)：参考了延河课程下载相关流程。

## 免责声明

本项目用于个人学习、备份和授权访问流程。请遵守课程内容政策、平台条款和相关法律法规。
