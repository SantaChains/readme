# GitHub Profile README 配置指南

> 本仓库包含 GitHub 个人主页的完整配置说明和效果展示

---

## 快速开始

1. 创建与 GitHub 用户名相同的仓库（如 `SantaChains/SantaChains`）
2. 设置为 **Public** 仓库
3. 勾选 **Add a README file**
4. 复制 `README.md` 中的内容到个人仓库
5. 替换占位符（用户名、邮箱、链接等）
6. 配置 GitHub Actions 工作流（可选）

---

## 文件结构

| 文件 | 说明 |
|------|------|
| `README.md` | 个人主页效果展示模板 |
| `profile.md` | 本配置说明文档 |
| `.github/workflows/` | GitHub Actions 工作流配置 |
| `CODE_OF_CONDUCT.md` | 行为准则 |
| `CONTRIBUTING.md` | 贡献指南 |

---

## 占位符说明

### 必需替换项

| 占位符 | 说明 | 示例 |
|--------|------|------|
| `YOUR_NAME` | 显示名称 | John Doe |
| `YOUR_ROLE` | 职位/角色 | Full Stack Developer |
| `YOUR_EMAIL` | 联系邮箱 | your@email.com |
| `YOUR_LINKEDIN` | LinkedIn 用户名 | johndoe |
| `YOUR_TWITTER` | Twitter 用户名 | johndoe |

### 主题列表

| 主题 | 代码 |
|------|------|
| Tokyo Night | `theme=tokyonight` |
| Dracula | `theme=dracula` |
| One Dark | `theme=onedark` |
| Nord | `theme=nord` |

### 技能图标

访问 [skillicons.dev](https://skillicons.dev) 查看完整列表

**常用图标**: `js`, `ts`, `python`, `java`, `go`, `rust`, `react`, `vue`, `nodejs`, `docker`, `git`, `aws`

---

## 统计卡片参数

### GitHub Stats
```markdown
![Stats](https://github-readme-stats.vercel.app/api?username=SantaChains&show_icons=true&theme=tokyonight)
```

| 参数 | 说明 | 值 |
|------|------|-----|
| `show_icons` | 显示图标 | `true` / `false` |
| `theme` | 主题 | `tokyonight`, `dracula` |
| `hide_border` | 隐藏边框 | `true` / `false` |

### 编程语言排行
```markdown
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=SantaChains&layout=compact&theme=tokyonight)
```

---

## GitHub Actions 工作流配置

### 1. 贡献蛇图 (grid-snake.yml)

生成贪吃蛇动画吃贡献图的 SVG。

**文件位置**: `.github/workflows/grid-snake.yml`

```yaml
name: generate animation

on:
  schedule:
    - cron: "0 */12 * * *"  # 每12小时运行
  workflow_dispatch:        # 手动触发
  push:
    branches: [main]

jobs:
  generate:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: Generate github-contribution-grid-snake.svg
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?palette=github-dark
      
      - name: Push to output branch
        uses: crazy-max/ghaction-github-pages@v3.1.0
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**README 引用**:
```markdown
![Snake animation](https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_USERNAME/output/github-contribution-grid-snake.svg)
```

---

### 2. 综合指标 (Metrics.yml)

使用 lowlighter/metrics 生成详细的 GitHub 统计卡片。

**文件位置**: `.github/workflows/Metrics.yml`

```yaml
name: Metrics
on:
  schedule: [{cron: "0 0 * * *"}]  # 每天运行
  workflow_dispatch:
  push: {branches: ["master", "main"]}

jobs:
  github-metrics:
    runs-on: ubuntu-latest
    steps:
      - uses: lowlighter/metrics@latest
        with:
          token: ${{ secrets.METRICS_TOKEN }}
          user: YOUR_USERNAME           # ← 替换为你的用户名
          config_timezone: Asia/Shanghai # ← 替换为你的时区
          committer_message: "chore: update metrics"
          template: classic
          base: header, activity, community, repositories, metadata
          
          # 插件配置
          plugin_introduction: yes
          plugin_isocalendar: yes
          plugin_isocalendar_duration: full-year
          plugin_languages: yes
          plugin_languages_details: bytes-size, percentage
          plugin_achievements: yes
          plugin_achievements_threshold: X
          plugin_achievements_display: detailed
          plugin_calendar: yes
          plugin_calendar_limit: 11
          plugin_habits: yes
          plugin_habits_facts: yes
          plugin_habits_charts: yes
          plugin_licenses: yes
          plugin_stargazers: yes
          plugin_topics: yes
```

**配置说明**:
- 需要创建 `METRICS_TOKEN` Secret（见下方）
- `user`: 你的 GitHub 用户名
- `config_timezone`: 时区（如 `Asia/Shanghai`, `America/New_York`）

---

### 3. 3D 贡献图 (profile-3d.yml)

生成 3D 风格的贡献图 SVG。

**文件位置**: `.github/workflows/profile-3d.yml`

```yaml
name: GitHub-Profile-3D-Contrib

on:
  schedule:
    - cron: "0 18 * * *"  # 每天 UTC 18:00 运行
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    name: generate-github-profile-3d-contrib
    steps:
      - uses: actions/checkout@v2
      - uses: yoshi389111/github-profile-3d-contrib@0.6.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          USERNAME: ${{ github.repository_owner }}
      - name: Commit & Push
        run: |
          git config user.name YOUR_USERNAME      # ← 替换
          git config user.email YOUR_EMAIL        # ← 替换
          git add -A .
          git commit -m "generated"
          git push
```

**README 引用**:
```markdown
<img src="./profile-3d-contrib/profile-green-animate.svg" width="100%">
```

---

## Secret 配置

### 创建 METRICS_TOKEN

1. 访问 GitHub Settings → Developer settings → Personal access tokens
2. 生成新 Token，勾选以下权限：
   - `public_repo` 或 `repo`（访问仓库数据）
   - `read:user`（读取用户信息）
   - `read:org`（读取组织信息，可选）
3. 复制 Token
4. 进入 Profile 仓库 → Settings → Secrets and variables → Actions
5. 点击 **New repository secret**
6. 名称填写 `METRICS_TOKEN`，值粘贴刚才的 Token

### GITHUB_TOKEN

此 Token 由 GitHub 自动提供，无需手动创建。

---

## 徽章制作

使用 [shields.io](https://shields.io):

```markdown
![Badge](https://img.shields.io/badge/LABEL-MESSAGE-COLOR?style=STYLE&logo=LOGO)
```

### 社交媒体徽章
```markdown
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/YOUR_USERNAME)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/YOUR_USERNAME)
[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/SantaChains)
```

---

## 资源链接

| 资源 | 链接 |
|------|------|
| 技能图标 | [skillicons.dev](https://skillicons.dev) |
| 徽章制作 | [shields.io](https://shields.io) |
| 主题预览 | [github-readme-stats Themes](https://github-readme-stats.vercel.app/demo/themes/) |
| SVG 动画 | [readme-typing-svg](https://readme-typing-svg.demolab.com) |
| 贡献蛇图 | [Platane/snk](https://github.com/Platane/snk) |
| Metrics | [lowlighter/metrics](https://github.com/lowlighter/metrics) |
| 3D 贡献图 | [yoshi389111/github-profile-3d-contrib](https://github.com/yoshi389111/github-profile-3d-contrib) |

---

## 注意事项

1. 用户名 `SantaChains` 已预填充到模板中
2. 免费 API 有请求限制，高流量时可能暂时不可用
3. 建议所有卡片使用相同主题保持一致性
4. 统计卡片有缓存，非实时更新
5. GitHub Actions 工作流首次运行可能需要手动触发
