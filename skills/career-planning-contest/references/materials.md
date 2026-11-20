# 两份材料的做法（报告 PDF + 展示 PPT）

目标是「看起来像本人认真做的」，不是「看起来像 AI 生成的」。所以：数据只用自己的自评，经历只写真实发生过的，句子用第一人称、少形容词。

---

## 一、生涯发展报告（PDF）

### 硬约束
- 正文 **≤1500 字**（数中文汉字：`len(re.findall(r'[\u4e00-\u9fa5]', text))`），图表 **≤5 张**。
- 页面提示「仅支持 pdf，文件小于 10M」。

### 推荐流程
1. 写一个 `make_report.py`：把七节正文放在 `SECTIONS` 列表里，用 f-string 拼出 `report.html`。
2. CSS 手写排版：`@page { size: A4; margin: 16mm 15mm; }`，字体 `"Microsoft YaHei"`，正文 10.5–11pt，行高 1.7。
3. 图用**内联 SVG 手绘**（雷达图 `radar()`、条形对比图 `bar_chart()`、时间轴 `timeline_html()`），不依赖 matplotlib（本机没有，也装不动）。
4. 用 CDP 打印：

```js
await page.goto('file:///.../report.html', { waitUntil: 'load' });
const res = await cdp('Page.printToPDF', {
  printBackground: true, preferCSSPageSize: true,
  marginTop: 0, marginBottom: 0, marginLeft: 0, marginRight: 0,
});
fs.writeFileSync(out, Buffer.from(res.data, 'base64'));
```

5. 核对：页数（2–3 页为宜）、有没有横向溢出、图注有没有折行成两行。

### 内容骨架（七节，约 1000–1250 字刚好）
一、自我认知（兴趣/性格/能力/价值观，各 2–3 句，带具体场景）
二、职业与行业认知（这个岗位到底在做什么；行业这两年怎么变；AI 改变了什么）
三、职业目标（短期本科 / 中期硕士 / 长期）
四、行动计划（大一至大四 + 毕业后，写「做什么、什么时候、产出什么」）
五、生涯路径（四步时间轴：打地基 → 补方法 → 成复合 → 上一线）
六、已有成果与差距（诚实盘点，差距写具体：「只做过基础数据整理」比「有待提升」有用）
七、评估与调整（每学期复盘三项；什么信号触发换方向；主线不变）

### 图表口径（三处必须一致：报告 / PPT / 闯关作答）
- 雷达 6 维：专业基础、野外/实践观察、数据与软件、数据处理与编程、沟通协作、自主学习。
- 条形 5 项：专业与方向知识、现场观察能力、数据/资料解释、编程与建模、英文文献阅读；每项两个数（岗位要求 / 当前水平）。
- 分数用**自评**，差距大的那两项要在正文和 PPT 里都点出来，评委会顺着问。

### 排版常见病
- 最后一页只剩页脚 → 图缩 10–20mm、图注缩短、footer `margin-top` 调小。
- 图注折行 → 图注压到 20 字内，或用「实线＝当前水平，虚线＝岗位要求」这种半句写法。
- 两图并排显挤 → 改纵向堆叠（雷达居中 96mm、条形居中 118mm）。

---

## 二、生涯发展展示（PPT）

### 硬约束
- `.pptx`，**≤50MB**（实际 70KB 左右就够）。
- 16:9（`prs.slide_width = Inches(13.333)`）。

### 推荐流程
1. 写 `build_ppt.py`（python-pptx），工具函数：

```python
solid(shape, color, line=None, lw=None)
rect(slide, x, y, w, h, color, line=None, lw=None, shape=MSO_SHAPE.ROUNDED_RECTANGLE, radius=0.08)
text(slide, x, y, w, h, paras, align=PP_ALIGN.LEFT, anchor=MSO_ANCHOR.TOP, spacing=None)
# paras = [(文本, 字号, 颜色, 粗体, 前置段距pt, 行距)]
header(slide, num, title, sub)   # 编号 + 标题 + 蓝短线 + 副标题 + 页脚
```

2. 11 页骨架：封面 / 内容主线（四卡）/ 自我认知（四卡 + 雷达）/ 职业与行业（三栏 + 结论条）/ 胜任力差距（条形 + 右侧 4 条注）/ 三阶段目标 / 四年行动（三行 × 两列）/ 生涯路径四步（CHEVRON）/ 已有成果与差距（双栏）/ 评估与调整（四环 + 深色底线）/ 结语。
3. 配色：NAVY `0E2B57`、BLUE `1F6FE0`、MID `5B8CDB`、PALE `EAF2FD`、PALE2 `F4F8FE`、GRAY `5A6B7F`；字体统一「微软雅黑」。
4. 版式自检（**必须做**）：PowerShell + PowerPoint COM 导 PNG，逐页看：

```powershell
$ppt = New-Object -ComObject PowerPoint.Application
$pres = $ppt.Presentations.Open($path, $true, $false, $false)
foreach ($i in 1..$pres.Slides.Count) { $pres.Slides.Item($i).Export("$out\s{0:d2}.png" -f $i, "PNG", 1600, 900) }
$pres.Close(); $ppt.Quit()
```

5. 逐页 `read_image` 检查：标题与副标题是否压线、卡片底部是否大片空白、图表标签是否被裁、结论句是否压住卡片。
6. 改口径时**不要手改坐标**，用替换脚本从上一版 `build_ppt.py` 派生（见下）。

### 换人/换方向的复用办法
写 `_transform_ppt.py`：读旧脚本 → 做**单行子串替换** → 断言每个 old 都命中 → 写新脚本 → 跑。

```python
if old not in s: raise SystemExit(f'NOT FOUND: {old[:40]}')
s = s.replace(old, new)
```

踩过的坑：多行替换串的续行缩进必须和原文完全一致，否则 NOT FOUND；改成单行子串替换最稳。

### 版式雷区
- 副标题放在标题和蓝短线之间会压线 → 副标题另起一行放在短线下方（title y=0.44、短线 y=1.13、副标题 y=1.27）。
- 页码用 `len(prs.slides)`，别手写死。
- 雷达图隐藏数值轴（`ch.value_axis.visible = False`），否则 0–100 刻度把图挤乱。
- 目标系列用虚线 + 稍深色（`RGBColor(0x5B,0x8C,0xDB)`、`Pt(1.5)`、`MSO_LINE_DASH_STYLE.DASH`），否则浅蓝看不见。
- 卡片高度收紧到刚好包住文字，别留 2/3 卡高的死白。

---

## 三、辅助材料（只在闯关里要用）

第 5 关要**真实招聘简章 PDF**、两份**访谈报告 PDF**、一份**实习实践报告 PDF**。做法：

- 招聘简章：找真实单位的校招公告页，抽正文 → HTML(A4) → CDP 打 PDF。
- 访谈报告：四段式结构最好用——①访谈对象（表格：对象/单位与职务/从业年限/方式与时长）②访谈提纲（6–8 条）③访谈要点（5 条小标题 + 正文）④访谈启发。内容基于本人真实经历改写，**不要编造单位名称与奖项**。
- 实习实践报告：实习目的 / 内容过程 / 收获与问题 / 下一步，各一段。

生成脚本可用同一套 `page(title, sub, meta, body)` 拼 HTML（`@page margin 18mm 18mm 16mm`、微软雅黑 10.5pt、`h2` 左 3px 蓝条）。
