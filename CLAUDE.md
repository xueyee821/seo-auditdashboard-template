# SEO Dashboard Setup Guide for Claude Code

你好！这是一个 SEO 自动监控 dashboard 模板。帮用户完成以下设置流程。

---

## 你的任务

用户已经下载了这个模板。你需要：

1. **收集品牌信息**（逐一询问，不要一次全问）
2. **生成本地 dashboard 预览**
3. **引导用户完成 GitHub 部署**

---

## 第一步：收集信息

依序问用户以下问题（每次只问一个，等他回答再问下一个）：

1. 你的**品牌名称**是什么？（例如：Iconfit Fitness Club）
2. 你的**网站网址**是什么？（例如：https://iconfit.com.my）
3. 你有 **SE Ranking** 账号吗？（有 / 没有）
   - 如果有：你的 SE Ranking API Key 和 Audit ID 是什么？

Google API 的部分（GSC、GA4）告诉用户：先完成本地预览，Google 权限在部署到 GitHub 时再设置。

---

## 第二步：生成本地预览

收集到品牌名称和网址后，执行：

```bash
pip install google-api-python-client google-analytics-data google-auth requests 2>/dev/null || true
BRAND_NAME="用户填的品牌名" SITE_URL="用户填的网址" python scripts/generate_dashboard.py
```

注意：`audit.py` 需要 Google API 权限才能运行，本地预览跳过它，直接用现有的 `data/latest.json` 生成 dashboard。

生成后告诉用户：`docs/index.html` 已更新，可以用浏览器打开预览效果。

---

## 第三步：引导 GitHub 部署

告诉用户以下步骤（用中文，简洁）：

### 3a. 上传到 GitHub
```bash
git init
git add .
git commit -m "My SEO Dashboard"
```
然后在 GitHub 建立新 repo，push 上去。

### 3b. 加入 GitHub Secrets
进入 repo → Settings → Secrets and variables → Actions，加入：

| Secret | 内容 |
|--------|------|
| `BRAND_NAME` | 品牌名称 |
| `SITE_URL` | 网站网址 |
| `GOOGLE_SERVICE_ACCOUNT_JSON` | Google 服务账号 JSON（见下方说明）|
| `GA4_PROPERTY_ID` | `properties/` + 数字 |
| `GSC_SITE_URL` | `sc-domain:yourdomain.com` |
| `SERANKING_API_KEY` | （选填）|
| `SERANKING_AUDIT_ID` | （选填）|

### 3c. 启用 GitHub Pages
Settings → Pages → Branch: main → Folder: /docs → Save

### 3d. 手动触发第一次运行
Actions → Weekly SEO Audit → Run workflow

---

## Google 服务账号设置说明（用户需要时提供）

1. 前往 [Google Cloud Console](https://console.cloud.google.com) → 建立新项目
2. 启用：**Google Search Console API** 和 **Google Analytics Data API**
3. IAM → 服务账号 → 建立 → 角色：查看者 → 建立密钥 → JSON → 下载
4. 把服务账号邮箱加入 GSC 权限（查看者）和 GA4 权限（查看者）
5. 把整个 JSON 文件内容复制贴入 `GOOGLE_SERVICE_ACCOUNT_JSON` secret

---

## 注意事项

- `data/latest.json` 是示例数据，本地预览用。正式数据由 GitHub Actions 每周更新。
- 如果用户问 SE Ranking 但没有账号，告诉他可以跳过，dashboard 其他部分照常运作。
- 所有 dashboard 文字是中文，适合中文用户。如果用户想要英文版，告诉他可以修改 `scripts/generate_dashboard.py`。
