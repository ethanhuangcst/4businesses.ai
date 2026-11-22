# 托管、发布与域名

## WorkBuddy 托管（当前方案）

- 发布工具：WorkBuddy 内置「发布应用」（sites deploy）。
- 发布参数（历史实际值，重新发布时沿用）：

```json
{
  "directory": "<本项目>/4businesses.ai-web/site",
  "language": "static",
  "entryHtml": "index.html",
  "appName": "智能员工Workshop指南",
  "domainPrefix": "ai-works-workshop",
  "userAskedToPublish": true
}
```

- 分享链接稳定为：`https://ai-works-workshop.app.workbuddy.host/?sharecode=GVVF43H4ID`。
- 管理入口：设置 → 数据管理 → 应用。

## ⚠️ 迁移目录后的发布归属（重要）

发布工具按 **发布源目录** 决定覆盖哪个应用：同一目录重复发布覆盖原应用、保持原链接；**换一个目录首次发布可能生成新应用与新链接**。本次项目已从 `course-design/ai-works-hr/mini-site/page` 迁移到 `ai4business/4businesses.ai-web/site`，因此：

1. 下次发布前先做一次测试发布，核验返回的 shareLink 是否仍是原链接。
2. 若生成了新链接：旧链接由旧目录继续管理（原目录与 `.workbuddy` 应用记录仍在 course-design 工作区），或以新链接为准并同步更新 Cloudflare 跳转、`guide.js` 中 PROMPT_TEXT 里的站点地址、noscript 文案。
3. 不要盲目删除旧目录里的应用记录，先确认新链接可用。

## 发布纪律

- 每次发布都是对外覆盖：**本轮用户明确说「发布」才发布**，之前的授权不延续。
- 发布后工具返回 `verified: true` 只代表发布链路通，不代表下载/安装/客户端调用全部验证完成；重要变更需另做公网文件校验。

## 公网校验方法（无 Cookie 独立请求）

```bash
python3 - <<'EOF'
import urllib.request, hashlib
base = "https://ai-works-workshop.app.workbuddy.host/"
sc = "?sharecode=GVVF43H4ID"
files = ["index.html", "guide.js", "styles/guide.css", "setup/install.md", "skills.zip", "releases/v1.0.0/manifest.json", "releases/v1.0.0/workshop-skills.zip"]
for f in files:
    r = urllib.request.urlopen(base + f + sc)
    print(f, r.status, len(r.read()))
EOF
```

逐字节比对时用本地文件的 SHA-256 对照。

## 域名 4businesses.ai（Cloudflare）

### 现状：根域 302 跳转

- DNS：根域 A 记录 `@ → 192.0.2.1`（占位），橙云 Proxied，仅承接跳转。
- Redirect Rule：

```text
(http.host eq "4businesses.ai" and http.request.uri.path eq "/")
→ 302 到完整分享链接（关闭 Preserve query string）
```

- 效果：访问 `4businesses.ai` 会跳到 WorkBuddy 分享链接，**地址栏会变成 workbuddy.host 域名**。

### 目标方案：地址栏保持 4businesses.ai（未实施）

WorkBuddy 托管未确认支持原生自定义域名绑定。要保住地址栏，需把同一静态目录真正托管到可绑域的平台，二选一：

1. **Cloudflare Pages（推荐）**：上传 `site/` 目录，Custom domains 绑定 `4businesses.ai`；然后停用跳转规则、删除占位 A 记录。零成本、同生态。
2. **阿里云 OSS（香港节点）**：静态网站托管 + 自定义域名 + HTTPS；香港节点无需 ICP 备案，`.ai` 域名内地备案此前查到不支持，部署前再确认最新规则。

迁移 Cloudflare Pages 后注意：
- `sharecode` 参数机制是 WorkBuddy 托管层的；Pages 上没有它。若Pages 版不再需要 sharecode 鉴权，需同步清理 `guide.js` PROMPT_TEXT 与 `install.md` 中的 sharecode 链接。
- 两条渠道并存期间，以一条为准发布，避免双源漂移。

## 页面防缓存

样式或脚本改动发布后用户端不生效时：

1. 给 `<head>` 里的引用加版本参数：`styles/guide.css?v=xxx`、`guide.js?v=xxx`。
2. 历史案例：间距修改已在线上但用户看不到，加 `?v=spacing-2` 后解决。
