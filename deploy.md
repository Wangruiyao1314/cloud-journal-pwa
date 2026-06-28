# Cloud Journal PWA v2 — 部署指南

## 文件清单

```
cloud-journal-pwa/
  ├── index.html       ← 完整应用 (HTML/CSS/JS)
  ├── manifest.json    ← PWA 清单
  ├── sw.js            ← Service Worker (离线缓存)
  └── icons/           ← App 图标
      ├── icon-180.png
      ├── icon-192.png
      └── icon-512.png
```

## 第一步：部署到 HTTPS 服务器

任选一种（均免费）：

- **GitHub Pages**: 创建新仓库 → 上传文件 → Settings → Pages → 选择分支 → 获得 `https://xxx.github.io/cloud-journal-pwa`
- **Netlify**: 拖拽文件夹到 netlify.com → 自动部署
- **Vercel / Cloudflare Pages**: 同理

## 第二步：获取 Google OAuth Client ID

这是关键步骤，做完就能在 App 里一键登录 Google Drive：

1. 前往 https://console.cloud.google.com
2. 新建项目（或选已有项目）→ 搜索并启用 **Google Drive API**
3. 左侧菜单 → **OAuth 同意屏幕**
   - User Type 选「外部」→ 填写应用名称 (Cloud Journal) → 添加你的 Gmail 为测试用户
   - 不需要提交验证
4. 左侧菜单 → **凭据** → **创建凭据** → **OAuth 客户端 ID**
   - 应用类型：**Web 应用**
   - 名称：Cloud Journal PWA
   - 已获授权的重定向 URI：填写 `https://你的域名/oauth-callback.html`
   - 创建后**复制 Client ID**
5. 在 App 中：设置 → Google OAuth Client ID → 粘贴 → 保存
6. 点击「使用 Google 账号登录」→ 授权 → 完成

## 第三步：安装到 iPhone

1. 用 **Safari** 打开你部署的 HTTPS 网址
2. 点击底部**分享按钮**（方框 + 箭头）
3. 滑动找到 **「添加到主屏幕」** → 点击右上角「添加」
4. 像原生 App 一样使用

## 技术细节

- **OAuth PKCE**: Authorization Code + PKCE 流程，token 不离开设备
- **Token 刷新**: access_token 过期后自动用 refresh_token 刷新
- **access_type=offline + prompt=consent**: 保证拿到 refresh_token
- **Dropbox 备用**: 仍然支持手动输入 Dropbox Access Token

## 如果不用 Google，用 Dropbox

设置 → 连接 Dropbox → 粘贴 Access Token（在 dropbox.com/developers/apps 获取）

## 安全说明

- Tokens 存在浏览器本地，不会上传任何第三方
- Google access token 在过期前 5 分钟自动刷新
- 所有数据直接从浏览器到云服务，不经过中间服务器
