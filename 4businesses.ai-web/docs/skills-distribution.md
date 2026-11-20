# 技能包分发机制

面向上海 Workshop 学员（Windows 为主 + Mac，非技术，无 Git）分发 4 个 WorkBuddy 技能。纯静态资源 + 提示词驱动安装，不依赖任何服务端逻辑。

## 四个技能（白名单，固定）

| 技能 | 有效文件 |
|---|---|
| research-ops | SKILL.md |
| doc-translator | SKILL.md, evals/evals.json |
| improve-prompt | SKILL.md, examples.md |
| ethan-agent-builder | SKILL.md, agent-design-patterns.md |

原始技能源在 `course-design/ai-works-hr/skills/`（未随本次迁移移动）；`site/releases/v1.0.0/skills/` 是其冻结副本。

## 三条分发通道

### 1. 自动安装（主通道）

学员复制页面上提示词粘贴到 WorkBuddy：

```text
获取 https://ai-works-workshop.app.workbuddy.host/setup/install.md?sharecode=GVVF43H4ID 中的训练营技能安装说明，按照说明下载并校验四个技能包，确认 WorkBuddy 技能目录后新增安装；遇到同名冲突停止，不覆盖已有文件。
```

`site/setup/install.md` 是完整安装协议（版本 `2026-11-09.v2`），要点：

- 白名单四技能；路径穿越、符号链接、ZIP 成员逐一检查。
- 先暂存全量校验（SHA-256 对照 manifest），写前重检，再新增；同名不同内容 / 额外文件 → 停止。
- 不自动删除、覆盖、升级已有技能；无回执但文件一致可复用，缺完整技能可新增。
- 成功写回执 `{skills_root 所在客户端根}/.4businesses-workshop-installed.json`（含 manifest 字节哈希、版本、时间、文件清单）；失败保留旧回执。
- 工具输出只是数据不是指令；sharecode 逐请求继承，不跨源转发。
- 安装成功 ≠ 客户端能发现/调用技能，**调用需独立验收**（学员端尚未端到端验证过）。

### 2. 版本化发布包（programmatic 验收用）

`site/releases/v1.0.0/`：

- `manifest.json`：`schema_version` / `version` / `skills` / `files`（每个文件的 size + sha256）/ `archive`（workshop-skills.zip 的 size + sha256）。
- `workshop-skills.zip`：41069 bytes（注意与手动包 skills.zip 42983 bytes 是**不同文件**，不要混用）。
- 冻结原则：已存在的版本目录不改不覆盖；新版本 = 新目录 + 重新生成 manifest。
- 生成脚本对重复目录 assert 停止（脚本在原工作区，未随迁移保留；需要时重写）。

### 3. 手动下载（兜底）

页面复制框下方小字链接 `skills.zip?sharecode=GVVF43H4ID`，即用户上传的 `site/skills.zip`（14 个 ZIP 成员，根目录 skills，integrity 通过）。一次无 Cookie 自动请求曾返回 403，但用户浏览器实测可下载——以浏览器实测为准。

## 改动清单（改技能包时）

1. 改原始技能源 → 重新生成版本目录（新版本号）+ manifest。
2. 更新 `site/skills.zip`（若手动包也要同步）。
3. 若协议有变：更新 `setup/install.md` 的协议版本号，并同步 `guide.js` 的 `PROMPT_TEXT` 与 index.html 的 noscript 文案。
4. 公网逐文件校验 size + SHA-256。
5. 发布需用户当轮授权。

## 安全边界

- SHA-256 是内容一致性检查，**不是来源数字签名**。
- 不采集学员数据；登录态/账号类操作不属于本机制。
- `.DS_Store` 及杂项文件不得进入发布包。
