# 部署与开源指南

## 项目概览
- 类型：纯静态网站（无构建步骤、无后端）
- 主要文件：
  - `index.html`：站点首页与交互逻辑
  - `robots.txt` / `sitemap.xml`：SEO 相关文件
  - `android_en_name.apk`：安卓客户端下载包（如无需，可删除或替换）
  - `liuhe.mobileconfig`：iOS 描述文件（如无需，可删除或替换）

## 隐私与合规
- 已移除个人二维码图片与QQ群邀请链接。
- 已将站点域名引用替换为占位符 `https://example.com/`。
- 开源前请自查：
  - 是否需要继续移除或替换第三方下载链接（蓝奏云、蒲公英等）。
  - APK 或描述文件中是否包含私人信息。
  - 如需新增联系方式，请自行在 `index.html` 中以通用占位内容替换，避免暴露个人隐私。

## 环境要求
- 任意可托管静态文件的服务均可：
  - Nginx / Apache / Caddy
  - GitHub Pages / GitLab Pages
  - Vercel / Netlify / Cloudflare Pages
  - 任意对象存储 + CDN（例如阿里云OSS、腾讯云COS、七牛云等）

## 部署前准备
- 替换域名：
  - `index.html` 中的 `og:url`、`canonical`、JSON-LD `url` 改为你的正式域名。
  - `robots.txt` 与 `sitemap.xml` 中的域名改为你的正式域名。
- 资源校验：
  - 如不提供安卓或 iOS 下载，请删除 `android_en_name.apk` 与 `liuhe.mobileconfig`，并同步更新 `index.html` 的下载按钮或文案。
  - 如需提供自有资源，请将文件替换为你的版本并确认路径一致。

## 部署到 Nginx（示例）
1. 将仓库所有文件上传到服务器目录，例如 `/var/www/site`
2. 示例站点配置（放置到 `/etc/nginx/sites-available/site.conf` 并软链到 `sites-enabled`）：
   ```
   server {
     listen 80;
     server_name example.com;
     root /var/www/site;
     index index.html;

     location / {
       try_files $uri $uri/ /index.html;
     }

     types {
       application/octet-stream apk;
       application/xml xml;
       text/plain txt;
     }
   }
   ```
3. 申请并开启 HTTPS（推荐使用 Let’s Encrypt / ACME），并开启 GZIP/ Brotli 压缩与合理缓存。

## 部署到 GitHub Pages
1. 新建公开仓库并推送代码（不建议包含大型二进制，如 APK）。
2. 在仓库 Settings → Pages 中选择 Source 为 `main` 分支，`/root` 路径。
3. Pages 启用后，将 `example.com` 换为 Pages 分配的域名或你的自定义域名。

## 部署到 Vercel / Netlify / Cloudflare Pages
- 直接导入仓库即可构建为静态站点，构建命令留空（无构建），输出目录为仓库根目录。
- 设置自定义域名并启用 HTTPS。

## SEO 建议
- `sitemap.xml` 的 `<lastmod>` 建议按发布日期更新。
- 为 `index.html` 保持合理的 `title`、`description`、`keywords`、OG 元信息。
- 如果不使用 OG 图片，可将 `og:image` 指向你站点的通用占位图。

## 常见问题
- 页面在微信/QQ/抖音等内置浏览器打开受限：
  - 站点已包含拦截提示与“复制当前链接”功能，引导用户在系统浏览器打开。
- 下载按钮不能点击：
  - 确认文件路径有效；如在内置浏览器，提示会阻止下载以保护用户。

## 开源与许可
- 建议添加开源许可（例如 MIT 或 Apache-2.0），根据你的分发与商业需求选择。
- 如含第三方资源（APK、描述文件、图标等），请确认你有合法分发权或在开源版本中移除。

## 目录与文件说明
- `index.html`：页面结构、样式与交互脚本均在此文件，修改后即可生效。
- `robots.txt`：搜索引擎爬虫策略；更新为你的站点域名。
- `sitemap.xml`：站点地图；`<loc>` 改为你的站点首页地址。
- `android_en_name.apk` / `liuhe.mobileconfig`：如需分发客户端，保留并替换为你的文件；否则删除并调整页面按钮。

## 部署验证
- 部署完成后，打开你的域名检查：
  - 首页能正常显示，按钮跳转正常。
  - `https://你的域名/robots.txt` 与 `https://你的域名/sitemap.xml` 能正常访问。
  - HTTPS 有效，浏览器安全锁标识正常。

