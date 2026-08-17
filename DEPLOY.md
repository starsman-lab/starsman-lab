# 部署说明：把这套炫酷主页用起来

## 原理（和原作者一致）
GitHub 有一个特殊规则：**创建一个和你的用户名同名的仓库**，它的 `README.md` 会自动变成你的个人主页（不是仓库首页）。

- `starsman-lab/starsman-lab` 的 `main` 分支上的 `README.md` = 你的主页内容
- 统计图 / 贪吃蛇由 GitHub Action 每日自动生成，推送到同一个仓库的 `output` 分支
- `README.md` 通过 `raw.githubusercontent.com/.../output/xxx.svg` 引用这些图，所以**不依赖会抽风的 vercel 接口**

## 三步上线
1. 在 GitHub 新建仓库，仓库名必须叫 `starsman-lab`（勾选 Public，可顺手加 README）。
2. 把本目录里的两个文件按原样放进去：
   - `README.md` → 仓库根目录
   - `.github/workflows/profile-assets.yml` → 对应路径
3. 进入仓库 **Settings → Actions → General**，确认 Workflow 权限为 `Read and write permissions`（Action 需要写 `output` 分支）。

## 首次生成
- 推送后到 **Actions** 标签页，手动跑一次 `Generate Profile Assets`（或等每天 UTC 0 点自动跑）。
- 跑完后 `output` 分支会出现 `stats.svg` / `top-langs.svg` / 两张 snake svg，主页即可正常显示。

## ⚠️ 统计卡报错 / `Resource not accessible by integration`
GitHub 统计卡（stats / top-langs）需要读取用户级数据，仓库自带的 `GITHUB_TOKEN` 没有 `read:user` 权限，会生成 `Something went wrong` 错误卡片。解决方法：

1. 生成一个 **fine-grained PAT**：GitHub 头像 → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**
   - Token name：`github-readme-stats`
   - Expiration：建议 30~90 天
   - Resource owner：你的 GitHub 账号
   - Repository access：Public repositories（或 All repositories）
   - Permissions → **Account permissions** → `read:user` → 选 **Read-only**
   - 点 **Generate token** 并复制那一串 `ghp_...`
2. 进入你的主页仓库 `starsman-lab/starsman-lab` → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**
   - Name：`STATS_TOKEN`
   - Value：刚复制的 fine-grained PAT
   - 点 **Add secret**
3. 到 **Actions** → **Generate Profile Assets** → 右上角 **Run workflow** → **Run workflow**
4. 等运行完成后刷新主页，统计卡即恢复正常。

> 旧的 classic PAT（如有）建议生成完 `STATS_TOKEN` 后立刻吊销。

## 你可以改的地方（都在 README.md 里标了注释）
- 打字动画的轮播文案：`lines=` 后面用 `;` 分隔
- 技术栈徽章：直接增删 `<img src="https://img.shields.io/badge/...">` 行（去 https://shields.io 选 logo/配色）
- 邮箱 / 社交链接：替换 `mailto:` 和知乎等链接
- 主题色：把 `theme=tokyonight` 换成 `radical` / `gruvbox` / `dracula` 等

## 已验证可用的外部服务（2026-08-17 实测）
- 打字动画 readme-typing-svg.demolab.com ✅
- 贪吃蛇 Platane/snk ✅
- 连续打卡 streak-stats.demolab.com ✅
- 访问量 komarev.com/ghpvc ✅
- ⚠️ github-readme-stats.vercel.app 当前 503，故统计图改为 Action 预生成，无需该服务
