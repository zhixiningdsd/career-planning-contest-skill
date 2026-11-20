# 把这个项目发到 GitHub：从本地目录到公开仓库

这份文档是给**第一次发 GitHub 的人**看的。每一步都写：命令、这条命令在做什么、跑完怎么确认。

---

## 0. 先理解三件事

1. **git（本地）**：在你电脑上记录「文件每一次变化」的工具。它把历史存在项目根目录下一个隐藏文件夹 `.git` 里。删掉 `.git`，项目就变回一堆普通文件，历史全没了。
2. **GitHub（远程）**：托管在网络上的另一个仓库副本。你和别人的电脑通过 `push`（上传）/ `pull`（下载）与它同步。
3. **一次提交（commit）= 一个存档点**：改了文件不会自动记录，要 `git add`（挑文件）+ `git commit`（存一个点）之后才算数。

---

## 1. 本地 git 在哪

- **仓库位置**：本项目根目录，例如 `F:\ws\刷课\github\career-planning-contest-skill`
- **git 数据**：同目录下的隐藏文件夹 `.git`（在资源管理器里要把「显示隐藏项目」打开才看得到）
  - `.git` 里放的是全部历史、分支、远程地址；`config` 文件里是这个仓库的本地配置
- **确认这里是不是仓库**：

```bash
cd /d F:\ws\刷课\github\career-planning-contest-skill
git status
```

看到 `On branch main` + 文件列表 → 这里已经是仓库。

---

## 2. 怎么从零建立（建立仓库时用过的命令）

```bash
cd /d F:\ws\github\career-planning-contest-skill   # 先进入项目目录
git init -b main                                   # 建仓库，默认分支直接叫 main
git config user.name  "你的名字或昵称"              # 这个仓库里的提交作者名（只影响本仓库）
git config user.email "你的邮箱"                    # 同上（GitHub 用它把提交关联到你的账号）
git add -A                                         # 把所有文件放进「暂存区」
git status                                         # 确认清单里没有 .pdf / .pptx 之类的私人材料
git commit -m "feat: career planning contest skill v1.0.0"   # 存第一个存档点
```

说明：

- `-b main`：GitHub 现在默认主分支叫 `main`，一开始就对齐，省得以后改名。
- `git config` **不加 `--global`** 就只对当前仓库生效——不会动你电脑上其他项目。
- `git add -A` 之前一定先 `git status` 看一眼：`.gitignore` 已经把 `*.pdf`、`*.pptx` 排除了，但确认一次不吃亏。

---

## 3. 怎么上传到 GitHub

### 3.1 在网页建一个空仓库

1. 打开 https://github.com/new
2. Repository name 填 `career-planning-contest-skill`
3. 选 **Public**
4. **不要**勾 Add a README / .gitignore / License（我们本地都有了，勾了会多出一个提交，第一次推送就得先合并）
5. Create repository

### 3.2 把本地仓库连上去并推送

```bash
git remote add origin https://github.com/<你的账号>/career-planning-contest-skill.git
git push -u origin main
```

- `git remote add origin <地址>`：给远程起个名字叫 `origin`（约定俗成的默认名），只做一次。
- `git push -u origin main`：把本地 `main` 推上去，`-u` 记住「以后 `git push` 就推这里」。
- **第一次推送会弹窗登录**（Git Credential Manager）：选「Sign in with your browser（在浏览器中登录）」→ 在浏览器里点授权 → 凭据存进 Windows 凭据管理器，之后不用再登。

确认：刷新仓库网页，能看到 README 渲染、`LICENSE` 标签显示 **MIT license**。

### 3.3 三种认证方式怎么选

| 方式 | 适合 | 做法 |
|---|---|---|
| GCM 浏览器登录 | 桌面 Windows，最省事 | `git push` 时弹窗点授权即可（本机已配置 `credential.helper=manager`） |
| PAT（个人访问令牌） | 弹窗不出现 / 无桌面环境 | GitHub → Settings → Developer settings → Tokens (classic) → 勾 `repo` → 生成，**当密码用**（用户名填 GitHub 用户名） |
| SSH 密钥 | 长期多仓库、想完全免输入 | `ssh-keygen -t ed25519` → 把 `~/.ssh/id_ed25519.pub` 内容贴到 GitHub → Settings → SSH keys → 远程地址改成 `git@github.com:...` |

---

## 4. 以后怎么维护

### 4.1 日常改内容（最常见）

```bash
git status                 # 看哪些文件变了
git diff                   # 看具体改了哪几行
git add -A                 # 或 git add skills/career-planning-contest/references/levels.md
git commit -m "docs: 补充第 5 关字段说明"
git push
```

提交信息用「类型: 说明」开头最省事：`docs:`（改文档）、`fix:`（修错误）、`feat:`（加新内容）、`chore:`（杂项）。

### 4.2 发布新版本

```bash
git tag -a v1.1.0 -m "v1.1.0：适配 2027 赛季页面"
git push origin v1.1.0
```

再去网页 Releases → Draft a new release → 选这个 tag → 写更新说明。**打 tag 的价值**：别人能明确知道自己装的是哪一版，平台改版后你能说清「v1.0 对应 2026-11 的页面」。

### 4.3 改坏了想撤回

```bash
git restore <文件>          # 放弃这个文件未提交的修改
git restore --staged <文件> # 只是从暂存区拿出来，文件内容不动
git revert <提交号>         # 生成一个「反向提交」抵消某次提交（已推送时用这个，别改历史）
git log --oneline           # 看提交历史，拿提交号
```

### 4.4 想试验又不影响主线

```bash
git switch -c try-new-levels   # 开一个分支
# ……随便改……
git push -u origin try-new-levels   # 推上去，网页上可以开 Pull Request 对比
git switch main                     # 回到主线
```

### 4.5 别人提 issue / PR

- Issue：只读不改代码，直接在网页回复；确认是真问题再动。
- Pull Request：网页 Files changed 里逐行看，能跑就 Merge，跑不通就在评论里说明。**别直接往 main 推别人的东西**。

### 4.6 平台改版后要更新什么

按顺序看这三个文件：

1. `references/levels.md` —— 路由、字段 id、字数区间（最容易变）
2. `references/pitfalls.md` —— 新的报错与对策往上加
3. `SKILL.md` —— 流程有结构性变化时改主流程

改完在 README 的「时效」那句更新日期，然后 `commit` + `push` + 打新 tag。

---

## 5. 常见报错对照

| 报错 | 真因 | 处理 |
|---|---|---|
| `fatal: not a git repository` | 当前目录不是仓库 | `cd` 到项目根目录 |
| `remote origin already exists` | 远程名重复 | `git remote set-url origin <新地址>` |
| `failed to push some refs / non-fast-forward` | 远程有你本地没有的提交（建仓库时勾了 README 最容易这样） | `git pull --rebase origin main` 后重新 `push`；或 `git pull --allow-unrelated-histories` |
| `Authentication failed` | 凭据过期或用了账号密码 | 清凭据后重推：`git credential-manager erase`，再 `git push` 重新授权 |
| `Please tell me who you are` | 没设 `user.name` / `user.email` | 补 `git config user.name/user.email`，再 `git commit --amend --reset-author` |
| `Support for password authentication was removed` | GitHub 不再接受账号密码 | 用 PAT 或 GCM/SSH |
| 推送很慢/超时 | 网络到 GitHub 不通 | 开代理后重试；或 `git config --global http.proxy http://127.0.0.1:7897`（端口按你的代理填） |

---

## 6. 一句话总结

**本地改文件 → `git add` → `git commit`（存点）→ `git push`（同步到 GitHub）**。
建仓库（`init` + `remote add`）只做一次，以后就是这四步循环；`tag` 用来标记「哪个时间点的版本」。
