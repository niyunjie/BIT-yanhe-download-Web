# BIT Yanhe Download Web

A browser-first web application for browsing BIT Yanhe courses, playing course recordings, downloading HLS transport streams, and extracting slide-like screen frames into PDF files.

This project contains a Vue frontend and a lightweight Node.js backend. The backend handles BIT/Yanhe authentication, course metadata requests, playlist parsing, and media segment proxying. The frontend handles playback, TS saving, and PDF generation in the browser.

## Features

- BIT SSO login flow with Yanhe token extraction.
- Manual Yanhe token login fallback for accounts that require second verification.
- Course search with dynamic page size based on the browser viewport.
- Course detail view for selecting sessions.
- HLS playback in the browser through `hls.js`.
- Browser-side TS download for main video or screen stream.
- Browser-side screen-stream slide extraction and PDF export through `jsPDF`.
- No local FFmpeg requirement for the current browser download and PDF workflow.

## Project Structure

```text
backend/   Node.js API server and Yanhe/CAS integration
frontend/  Vue 3 + Vite browser application
```

## Privacy And Security

This repository should not contain personal credentials, Yanhe tokens, cookies, downloaded videos, generated PDFs, or local runtime files.

Important notes:

- User passwords are submitted to the backend only for the login request and are not intentionally persisted by this application.
- Yanhe tokens are stored in the browser local storage so the session can be restored locally.
- Do not commit `.env` files, browser screenshots containing account data, downloaded course videos, generated PDFs, or `backend/downloads/`.
- Use this project only with accounts and course materials you are authorized to access.

## Requirements

- Node.js `20.19+` or `22.12+`
- npm
- A modern Chromium-based browser is recommended for HLS playback and browser-side export.

## Local Development

Install dependencies:

```bash
cd backend
npm install

cd ../frontend
npm install
```

Start the backend:

```bash
cd backend
npm run dev
```

Start the frontend:

```bash
cd frontend
npm run dev
```

Default local URLs:

```text
Frontend: http://127.0.0.1:5173/
Backend:  http://127.0.0.1:8787/
```

## Frontend Configuration

The frontend can be pointed at a custom backend URL with `VITE_API_BASE_URL`.

For local development, the app defaults to:

```text
http://<current-hostname>:8787
```

For same-origin production deployment behind Nginx, build with:

```bash
VITE_API_BASE_URL=/api npm run build
```

On Windows PowerShell:

```powershell
$env:VITE_API_BASE_URL="/api"
npm run build
```

## Backend Configuration

The backend supports these environment variables:

```text
HOST=0.0.0.0
PORT=8787
```

Example:

```bash
HOST=0.0.0.0 PORT=8787 npm start
```

## Production Deployment

The simplest deployment model is one server hosting both the frontend and backend:

```text
https://your-server/
  -> frontend/dist static files

https://your-server/api/
  -> reverse proxy to backend on 127.0.0.1:8787
```

Recommended production pieces:

- Nginx for static frontend hosting and `/api/` reverse proxying.
- PM2 or systemd to keep the backend process alive.
- HTTPS if the service is exposed outside a trusted network.

Example Nginx shape:

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

## Build Checks

Frontend:

```bash
cd frontend
npm run build
```

Backend syntax check:

```bash
cd backend
npm run check
```

## Acknowledgements

This project was built with reference to the following open-source projects:

- [bit-admin/Yanhekt-AutoSlides](https://github.com/bit-admin/Yanhekt-AutoSlides) for ideas around extracting slide-like frames from Yanhe screen recordings.
- [BITNP/BIT_yanhe_download](https://github.com/BITNP/BIT_yanhe_download) for Yanhe course download workflow references.

## Disclaimer

This project is intended for personal study, backup, and authorized access workflows. Respect course content policies, platform terms, and applicable laws.
