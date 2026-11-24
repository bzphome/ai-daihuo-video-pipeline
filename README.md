# AI 带货短视频全自动流水线 — 方案汇总

> 生成日期：2026-11-24
> 场景：输入人物图 + 产品图 → 自动生成带货广告视频 → 自动发布抖音

---

## 核心工具链

| 环节 | 工具 | GitHub | Stars | 部署方式 |
|------|------|--------|-------|----------|
| **视频生成** | ClipForge | [xixihhhh/clipforge](https://github.com/xixihhhh/clipforge) | ⭐ 1,387 | Docker / Windows exe |
| **自动发布** | douyin-video-publish | [jiaxinmmhh/douyin-video-publish](https://github.com/jiaxinmmhh/douyin-video-publish) | ⭐ 0 | Node.js |
| **备选发布** | ShortVideo.AutoPublisher | [dorisoy/ShortVideo.AutoPublisher](https://github.com/dorisoy/ShortVideo.AutoPublisher) | ⭐ 177 | Java / Docker |
| **备选发布** | PostFlow | [jefftko/PostFlow](https://github.com/jefftko/PostFlow) | ⭐ 7 | Playwright 自动化 |

---

## 一、ClipForge — 视频生成

### 工作流
1. 上传产品图
2. AI 提取卖点，写种草文案脚本
3. 生成视频画面（可锁定产品不变形）
4. AI 配音（Edge TTS 免费版 / Seedance 付费版）
5. 自动加字幕 + BGM
6. 输出适配抖音/快手/小红书格式的短视频

### 两种模式
| 模式 | API Key | 成本 | 效果 |
|------|---------|------|------|
| 免费版 | 不需要 | ¥0 | Edge TTS + Pexels/Pixabay 免费素材拼贴，~2分钟/条 |
| AI 版 | 需配置 | 按秒计费 | Seedance 2.5 / 万相 3.0 / MiniMax H3 等生成真实视频片段 |

### Docker 部署
```powershell
docker run -d `
  --name clipforge `
  -p 3000:3000 `
  -v clipforge-data:/data `
  ghcr.io/xixihhhh/clipforge:latest
# 浏览器打开 http://localhost:3000
```

### Windows 直接安装
下载 Release 包：[ClipForge.Setup.0.10.0.exe](https://github.com/xixihhhh/clipforge/releases)

### 技术栈
- Next.js 16 + React 19 + TypeScript
- FFmpeg（Debian 发行版，非静态二进制）
- SQLite + Drizzle ORM
- AGPL-3.0 开源

---

## 二、自动发布到抖音

### 方案 A：douyin-video-publish（推荐）
- 用 CDP（Chrome DevTools Protocol）驱动**已登录的 Chrome 浏览器**
- 一条命令完成：上传视频 + 标题 + 简介 + 话题标签 + 合集 + 定时发布
- 支持批量排期（按天双更）
- 定时窗口：2小时 ~ 14天
- 支持 Claude Code / Cursor 集成

### 方案 B：ShortVideo.AutoPublisher
- 支持多平台：抖音、百家号、小红书、视频号、头条
- 需要配置各平台的登录态

### 方案 C：PostFlow
- 基于 Playwright 浏览器自动化
- 支持抖音/小红书/视频号/B站/快手/百家号/TikTok

---

## 三、完整部署步骤

### Step 1：部署 ClipForge
```powershell
docker run -d --name clipforge -p 3000:3000 -v clipforge-data:/data ghcr.io/xixihhhh/clipforge:latest
```

### Step 2：克隆 douyin-video-publish
```powershell
cd F:\Codex\2026-11-24\github-skill
git clone https://github.com/jiaxinmmhh/douyin-video-publish.git
cd douyin-video-publish
npm install
```

### Step 3：登录抖音（手动一次）
- 在浏览器手动登录抖音创作者后台
- douyin-video-publish 用 CDP 复用该登录态

### Step 4：运行流水线
1. 在 ClipForge 上传人物图 + 产品图
2. 等待视频生成（免费版约 2 分钟/条）
3. 导出视频文件
4. 用 douyin-video-publish 上传到抖音

---

## 四、关于 GPU

1080 Ti（11GB）跑不动本地视频生成模型，**所有视频生成都走云端 API**。
ClipForge 免费版不需要额外 API Key，零成本起步；AI 版按需付费。

---

## 五、关键链接

- ClipForge 教程：[TUTORIAL.md](https://github.com/xixihhhh/clipforge)
- ClipForge 主页：https://xixihhhh.github.io/clipforge/
- ClipForge Releases：https://github.com/xixihhhh/clipforge/releases
- douyin-video-publish：https://github.com/jiaxinmmhh/douyin-video-publish
- ShortVideo.AutoPublisher：https://github.com/dorisoy/ShortVideo.AutoPublisher
