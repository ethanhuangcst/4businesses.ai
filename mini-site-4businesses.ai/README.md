# 4businesses.ai — 智能员工 Workshop 指南网站

「AI that WOKRS!」半天实操训练营的指南单页 + 技能分发设施。纯静态站点，无构建框架、无数据库、无登录。

## 目录结构

```
4businesses.ai-web/
├── README.md            # 本文件：概览与维护入口
├── site/                # 网站发布源（WorkBuddy 托管的就是这个目录）
│   ├── index.html       # 单页入口
│   ├── guide.js         # 复制提示词 / 手动复制降级 / 案例复制逻辑
│   ├── styles/
│   │   ├── guide.css    # 页面样式（sdd.works 风格：近白底、黑字、胶囊复制框）
│   │   └── tokens.css   # 设计 token
│   ├── images/          # title.png / workbuddy.png / wechat.png
│   ├── setup/install.md # 学员端技能自动安装协议（2026-11-09.v2）
│   ├── skills.zip       # 手动下载技能包（用户提供，42983 bytes）
│   └── releases/v1.0.0/ # 冻结的版本化发布：manifest.json + workshop-skills.zip + 4 技能 7 文件
└── docs/
    ├── publishing.md    # 托管、发布、域名
    └── skills-distribution.md  # 技能包分发机制与验收
```

## 线上信息

| 项 | 值 |
|---|---|
| 分享链接 | `https://ai-works-workshop.app.workbuddy.host/?sharecode=GVVF43H4ID` |
| sharecode | `GVVF43H4ID`（每次对同源资源的请求都要显式携带，不假设 Cookie） |
| 应用名 | 智能员工Workshop指南 |
| 域名前缀 | `ai-works-workshop` |
| 域名 | `4businesses.ai`（Cloudflare 托管，目前根域 302 跳转到分享链接） |

## 关键约定

1. **标题拼写固定为 `AI that WOKRS!`**（用户指定，不是笔误，禁止改成 WORKS）。
2. 页面视觉沿用 sdd.works 风格；改动样式后给 `guide.css` 引用加版本参数（如 `?v=spacing-2`）防缓存。
3. 技能安装 Prompt 指向 `setup/install.md?sharecode=GVVF43H4ID`，改动安装协议须同步更新 `guide.js` 里的 `PROMPT_TEXT` 与 noscript 文案。
4. `releases/v1.0.0/` 是冻结发布包：不覆盖、不就地修改；出新版本用新版本号目录，manifest 记录每个文件的 size 与 SHA-256。
5. 学员名单、课程设计文档仍在原工作区 `course-design/ai-works-hr/`，本目录只承担网站与分发设施。

## 下一步维护

- 改页面 / 改文案：编辑 `site/`，本地确认后经 WorkBuddy「发布应用」同步线上（每次发布需当轮明确授权）。
- 更新技能包：见 `docs/skills-distribution.md`。
- 域名与地址栏方案：见 `docs/publishing.md`。
