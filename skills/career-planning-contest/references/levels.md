# 生涯闯关 8 关速查

平台页面全是自定义 div/span，`snapshot`/`a11y` 常常返回「no interactive elements」。定位策略：**类名前缀 + 可见文本双条件**，用 `dsh_browser_execute` 执行 IIFE 查找并 `dispatchEvent` 点击。类名带哈希后缀（`scroll_content_img___DtPs3`），改版会变——先读一次 DOM 再写死。

通用节奏：进入 `/checkpoint/<name>` 引导语页 → 底部三张 `img.scroll_content_img___DtPs3`，**DOM 顺序固定为** idx0 = 新知 `/newknowledge`，idx1 = 笃行 `/practiceearnestly`（实际落 `/practiceearnestly/entrance`），idx2 = 领悟 `/comprehend`。每做完一段回引导语再点下一张。

> 手输 `/checkpoint/x/practiceearnestly`（不带子路径）会 404，只能从引导语点图进。

---

## 关卡总表

| 关卡 | 页面名 | 路由 | barrier id |
|---|---|---|---|
| 1 | 生涯愿景 | `/checkpoint/first` | 1 |
| 2 | 专业探索 | `/checkpoint/second` | 2 |
| 3 | 职业瞭望 | `/checkpoint/third` | 3 |
| 4 | 兴趣揭秘 | `/checkpoint/fifth` | 5 |
| 5 | 目标锚定 | `/checkpoint/sixth` | 6 |
| 6 | 能力盘点 | `/checkpoint/seventh` | 7 |
| 7 | 技能提升 | `/checkpoint/eighth` | 8 |
| 8 | 生涯启航 | `/checkpoint/ninth` | 9 |

（barrier 4 不存在，所以第 4 关的路由是 `fifth`，页面却显示「04」。）

---

## 第 1 关 生涯愿景

- **新知** `/newknowledge`：读内容即可（有的关卡底部有「下一步」，有的没有，别硬找）。
- **笃行** `/practiceearnestly/entrance`：两张图 `one_practiceearnestly_left` / `_right`，是**两个子任务**。
  1. 「我的生涯榜样」`/practiceearnestly/professionalmodeledit`：2~3 位榜样，每行 `姓名 / 职位身份 / 单位`。**每行实际有 4 个 input，第 4 个是 `id="id"` 的隐藏框**，按 `querySelectorAll('input')` 顺序填会整体错位；正确做法是 `[...document.querySelectorAll('input')].filter(i => i.offsetParent !== null)`，只填可见的。
  2. `/practiceearnestly/professionalmodelshow`：点 `div.form_item___uWAy8` 卡片进入 `/practiceearnestly/{id}`，3 个 textarea 各 **100–300 字**（对照人物分析）。
- **领悟** `/comprehend`：单个 textarea「生涯愿景」**100–500 字**。

## 第 2 关 专业探索

- **新知**：专业探索三方法（资料查阅 / 访谈 / 实践）。
- **笃行**：页面有 `div.add___3C0ws`「探索专业」卡片（可以加第二个专业，非必须），点第一个 → `/practiceearnestly/record`：
  - `#major_name` 专业名称 **2–20 字**
  - `#major_course` 主要课程 **2–300 字**
  - `#tunnel` / `#interviewer` / `#practice_mode` 三段各 **100–300 字**（资料查阅 / 访谈 / 实践）
  - 提交后页面出现「XX 编辑 删除」，**还要回笃行页点它自己的「提交」**。
- **领悟**：两个 textarea，第一个 **200–500 字必填**，第二个选填。

## 第 3 关 职业瞭望

- **笃行**：点「探索职业方向」→ `/practiceearnestly/record?index=1`：
  - `#job` 被访谈人职务 **2–20 字** ← **这个字段会作为第 6 关的「心仪职业」显示**，填真实岗位名（如「石油与天然气工程技术人员」），别填「XX 主任」这类职务
  - `#major`、`#profession`（**2–100 字**）、`#interviewer`（**100–500 字**）
  - 提交 → 回笃行页「提交」。
- **领悟**：`#profession_name_0..n`（**2–30 字**），点「添加职业方向」加行，一般写 2–3 条。

## 第 4 关 兴趣揭秘

- **笃行**：两张图 `fifth_interest_one` / `_two`，**必须两张都做**（只做一张时领悟会一直报「请先完成 新知 和 笃行 环节」）。
  1. `/practiceearnestly/evaluate`「职业兴趣探索（1）」：84 个 `div.barrir_label___2awat` 词条，点选后类名变 `barrir_label_action___1At_K`；再点 `label.barrier_radius___2Lar1` 给类型排序（共 6 个，顺序：实际型 / 研究型 / 艺术型 / 社会型 / 管理型 / 常规型；点过之后标题显示 1/2/3）。排序后页面只显示已排序的类型 → 点「提交」。
  2. `/practiceearnestly/analyse`「职业兴趣探索（2）」：3 个 textarea，各 **50–100 字**。
- **领悟**：两段，各 **100–500 字**。

## 第 5 关 目标锚定

- **新知**：两个 `input#job_name`，**2–50 字**，填职业 1 / 职业 2 → 提交。
- **笃行** `/practiceearnestly`：页面只有一个 `button.ant-btn-primary`「下一步」（**点外层 button**，点内层 span 不跳）。
  1. `/first`「职业目标探索」：`textarea#platform_info`（**2–500 字**，信息平台名称与链接）+ 真实招聘简章 PDF（**1~5 份，单个 ≤50M**）+ 「添加阅读体会」（`span.add_text___eQSZd` → `input#name` + `textarea#experience`，卡内「保存」落卡）→「保存并下一步」。
  2. `/second`：两个 `div.upload_box___1zGG0` 各传一份**访谈报告 PDF**（对应两个职业）。
  3. `/third`：点「添加实习实践记录」→ `input#practice_mode`（**2–20 字**）+ 实习实践报告 PDF + 实践证明 PDF（可跳过，但「实践方式」是硬校验，不能空）→ 卡内「保存」→「保存并下一步」。
  4. `/fourth` 职业信息整理表：两个职业各 9 个 `input.ant-input`，**id 重复**，只能按可见 input 的 index 0–17 填（0–8 = 职业 1，9–17 = 职业 2）。
- **领悟**：`textarea#new_know`，**100–500 字**。

## 第 6 关 能力盘点

- **新知**：`/newknowledge/new1` 底部有「下一步」，`/new2`（STAR 法则）**没有按钮**，读完直接 `dsh_browser_open` 回引导语。
- **笃行** `entrance` 两张图：左 `seven_practiceearnestly_left` → `/practiceearnestly/skill`，右 `_right` → `/practiceearnestly/story`。
  - `/skill`：三个 `div.icon_action_box___xjwhH`（去探索 / 去探索 / 去查看）统一跳 `/practiceearnestly/skillform`，三个 textarea `#knowledge_skill` / `#migrate_skill` / `#self_manage_skill`；第 1 轮 = 心仪专业（**10–100 字**，逗号分隔技能），第 2 轮 = 心仪职业（同），第 3 轮 = 「意向专业及职业的技能需求」（**10–1500 字**）。每轮点「提交」，最后回 `/skill` 点页面「提交」。
  - 心仪职业名来自第 3 关的 `#job`。要改：`/checkpoint/third/practiceearnestly` → 点 `span.icon_text___dAW7w`「编辑」→ 改 `#job` → 提交，回 `/skill` 即更新。
  - `/storyfrom`（从「记录成就故事」进）：`#situation`（**20–200**）、`#task_target`（**20–100**）、`#action_attitude`（**50–200**）、`#result`（**20–100**）、`#show_major_knowledge_skill` / `#show_migrate_skill` / `#show_self_manage_skill`（各 **10–100**）→ 提交 → 卡内「提交」。
- **领悟**：`textarea#comprehension`，**100–500 字**。

## 第 7 关 技能提升

- **新知**：三类技能的提升途径。
- **笃行** 两张图，左 `eight_practiceearnestly_left` → `/practiceearnestly/evaluate`。
  - evaluate 页点 `span.add_text___eQSZd`「探索技能提升途径」→ `/evaluate/record`：
    - `input#type`（antd Select：专业知识技能 / 可迁移技能 / 自我管理技能）
    - `input#name`（**2–20 字**）
    - **先点 `span.event_btn___1gXBS`「手动输入新事件」**，才会出现 `input#eventName`（**20–100 字**）
    - `textarea#behavior`（**50–200**）、`#expect_result`（**50–200**）、`#need_resource`（**20–200**）
    - `input#expect_time_id`（antd Select：高职 1-3 / 本科 1-5 / 研究生 1-3 年级）
    - 提交一条 → 回 evaluate 页继续加，建议 2–3 条；最后点 evaluate 页「提交」。
- **领悟**：`#comprehension`，**100–500 字**。

## 第 8 关 生涯启航

- **新知** `/newknowledge`：**整页只有一张静态大图**（`nine_thought.png`，讲生涯发展报告的结构：前言 / 个人信息 / 自我认知 / 职业探索 / 行业分析 / 职业路径 / 评估调整 / 结语），没有按钮；页面加载即算完成。想看图：OSS 裸链 403，用 Python `urllib` 带 `Referer: https://zgs.chsi.com.cn/` 下载。
- **笃行** `entrance`：只有一张 `nine_new.png`「我的生涯探索成长单 · 点击生成」，**必须对 `img` 本身 dispatch mousedown/mouseup/click**（点父级 div 无效）→ `/practiceearnestly/growth`：7 个只读 textarea 自动汇总前七关的「领悟」，底部只有「下载word版」「提交」，点「提交」。
- **领悟** `/comprehend`：**上传生涯发展报告 PDF**（`input[type=file][accept='.pdf']`，mime 用 `application/pdf`）→「提交」→ 出现「恭喜完成职业生涯闯关！」。

---

## 徽章验收

`/mine/recruit`（或 `/mine/info`）刷新后逐个读 `div.medal_img___g6sj6` 的 inline `background-image`：

```js
[...document.querySelectorAll('div.medal_img___g6sj6')].map(d => getComputedStyle(d).backgroundImage)
```

全部为 `medal_light_*` 即通关（实际出现过：`medal_light_1`、`medal_light_new_2`、`medal_light_new_3`、`medal_light_5`、`medal_light_6`、`medal_light_7`、`medal_light_new_8`、`medal_light_new_9`）。**别用 `medal_light_\d` 这种正则判定**，`_new_` 变体会被漏掉。
