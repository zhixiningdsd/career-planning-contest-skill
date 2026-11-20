# career-planning-contest-skill

一个给 AI agent 用的操作手册（DSH skill）：**帮人完成「全国大学生职业规划大赛」的校内报名，并把平台里的「生涯闯关」8 关打通。**

**只问 4 项**——账号、赛道组别、目标职业、指导教师；其余（方向口径、能力自评、生涯榜样、核心课程、访谈对象、STAR 经历、技能提升计划、两份材料的素材）**由 agent 自己推断完成**，不再逐项追问。交付时逐项标清哪些是推断的、哪些需要本人替换。

不是给选手看的鸡汤指南，是一份「哪一步点什么、字段叫什么、哪里会踩坑」的实操手册——写它的原因是这套流程已经在真实平台上跑通过两遍（本人一次、帮同学一次），第二遍几乎没返工。

## 这是哪个比赛、哪个网站

- **比赛**：全国大学生职业规划大赛（教育部主办，各高校组织校内选拔）。分 **成长赛道**（在校本科生）与 **就业赛道**，下设 **高教组 / 职教组**。
- **网站**：学信网承办的大赛平台 **https://zgs.chsi.com.cn** —— 报名、上传材料、「生涯闯关」都在这里。
  - 报名页：`/center/signup/filed`
  - 生涯闯关：`/mine/info`（8 张徽章卡）

关键词：全国大学生职业规划大赛、职业规划大赛、生涯闯关、学信网、zgs.chsi.com.cn、成长赛道、高教组、生涯发展报告。
English: China national college career-planning contest, career planning competition, CHSI platform, agent skill.

## 它帮你做什么

0. **只问 4 项**：账号、赛道组别、目标职业、指导教师；其余自己推断（见下一节）。
1. **报名表四步**：赛道/组别核对 → 目标职业（级联选择）→ 目标行业（10 项，自己按职业推）→ 指导教师（含院校代码搜索的坑）。
2. **两份材料怎么来**：生涯发展报告 PDF（正文 ≤1500 字、图表 ≤5 张）与生涯发展展示 PPT（≤50MB）——生成方式、版式自检、页数压缩手法。
3. **上传与提交**：两个文件输入框的定位、PPT 必传 mime（否则 OSS 403）、诚信声明、「确认参赛」前的复核要求。
4. **生涯闯关 8 关**：每关的路由、barrier id、字段 id、字数区间、提交点，以及「笃行有多张图就是多个子任务」这类关键规律。
5. **验收与踩坑**：徽章是否点亮的判据、20 条「症状 → 真因 → 对策」。

## 安装

把 `skills/career-planning-contest` 整个目录复制到任意工作区的 `.dsh/skills/` 下：

```bash
# Windows PowerShell
Copy-Item -Recurse .\skills\career-planning-contest "<你的工作区>\.dsh\skills\"
```

```bash
# macOS / Linux
cp -r ./skills/career-planning-contest "<你的工作区>/.dsh/skills/"
```

然后对 agent 说一句「按 career-planning-contest 这个 skill 帮我报名职规赛」即可触发。

## 开工前 agent 该向用户问哪些信息

只有 **4 项**（完整版见 `skills/career-planning-contest/SKILL.md` 第 1 节）：

| 类别 | 要问的 |
|---|---|
| 账号 | 用谁的学信网账号；登录由本人完成（agent 不碰密码与验证码） |
| 赛道 + 组别 | 成长/就业 × 高教/职教 |
| 目标职业 | 一句话即可，agent 负责落到平台清单的二级项 |
| 指导教师 | 姓名、手机号、所在院校 |

其余（方向口径、能力自评、榜样、课程、访谈对象、STAR、技能计划、材料素材）**不问，由 agent 自己推断**，交付时逐项说明「哪些是推断的、哪些可以换」。

**不要问、也不要记**：密码、验证码、身份证号、学号。

## 兼容性

- **DSH 原生**：放进 `<工作区>/.dsh/skills/` 即可被 `skill` 工具加载。
- **Agent Skills 开放标准**：本文件就是标准形态的 `SKILL.md`（必填 `name` + `description`，另有 `license` / `compatibility`），Claude Code、GitHub Copilot、VS Code、Cursor 等支持 [agentskills.io 规范](https://agentskills.io/specification) 的宿主都能读取，只是各自的技能目录不同（例如 Claude Code 用 `.claude/skills/`）。
- **正文工具名需要按宿主替换**：`dsh_browser_*` → 宿主的浏览器操作；`ask_user_question` → 宿主的提问方式（没有就照 §1.4 的「没有弹窗工具时」把四问写成一段话）；CDP `Page.printToPDF` → 宿主能用的打印方式。**不承诺开箱即用。**
- **必须有用户可见的浏览器窗口**：登录只能本人做，纯 headless 的宿主没有窗口可登，这条流程走不通。

## 边界与免责

- 登录在用户自己的浏览器里完成，agent 不处理密码、验证码，也不做任何绕过验证的操作。
- 「确认参赛」是不可逆提交：agent 只准备内容与填表，**点之前必须由本人复核并明确同意**。
- 代写的材料（例如按口述整理的访谈报告）在交付时标注「按你提供的真实经历写成，可替换」，不编造成果、奖项、单位。
- 本仓库与大赛主办方、学信网无关，不是官方文档；平台改版后页面结构与字段可能变化。
- **时效**：内容按 2026 年 11 月的平台页面整理，遇到不一致时以页面实际为准。

## 仓库结构

```
README.md                       # 你正在看的这份
LICENSE                         # MIT
skills/career-planning-contest/
├── SKILL.md                    # 主流程：报名四步 / 材料 / 上传 / 闯关总览 / 验收
└── references/
    ├── levels.md               # 8 关逐关：路由、字段 id、字数区间、提交点
    ├── materials.md            # 报告 PDF 与 PPT 的制作与自检
    └── pitfalls.md             # 20 条「症状 → 真因 → 对策」
docs/publish-to-github.md       # 这份仓库自己是怎么建、怎么上传、怎么维护的
```

## 许可

MIT，见 [LICENSE](LICENSE)。
