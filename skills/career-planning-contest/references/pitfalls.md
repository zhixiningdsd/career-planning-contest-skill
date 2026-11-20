# 踩坑与对策（都是真踩过的）

按「症状 → 真因 → 对策」写，遇到相似症状先对照这张表，别从头试。

## 浏览器操作类

1. **`dsh_browser_upload` 传第二个文件时把第一个覆盖了**
   真因：`target.index` 被忽略，永远命中页面上第一个 `input[type=file]`。
   对策：先查 DOM 拿准选择器，用 `div.upload_box___1zGG0:last-of-type input[type=file]` 这类**结构性选择器**命中第二个（`:last-of-type` 有效，`:nth-of-type(2)` 无效）。

2. **PPT 上传 OSS 返回 403**
   真因：没带 mime，OSS 拒收。
   对策：`dsh_browser_upload` 显式传 `application/vnd.openxmlformats-officedocument.presentationml.presentation`；PDF 传 `application/pdf`。

3. **antd 下拉点不开 / 点选项没反应**
   真因：antd 监听的是 mousedown，`click()` 不触发。
   对策：对 `.ant-select-selector`（或 cascader 的输入框）`dispatchEvent(new MouseEvent('mousedown', { bubbles: true }))` 打开，再对 `.ant-select-item-option` 依次 mousedown / mouseup / click。

4. **学校选择框里一直「暂无数据呦～」**
   真因：程序化 setValue 会重复写入，把关键词弄成「XX 大学」。
   对策：用 `dsh_browser_click` 点输入框 → `dsh_browser_type` 打字 → `ArrowDown` → `Enter`，走真实键盘路径。

5. **按 index 填表单，值整体错位一格**
   真因：DOM 里有隐藏 input（`id="id"`、`type=hidden`），`querySelectorAll` 会算进去。
   对策：`[...document.querySelectorAll('input')].filter(i => i.offsetParent !== null)` 只填可见项；或直接按 id 填。多 textarea 时一次 execute 里按可见顺序 0..n 用原生 setter 赋值 + `dispatchEvent(new Event('input', {bubbles:true}))`，别用 `dsh_browser_set_value` 的 index。

6. **`dsh_browser_execute` 等 promise 超时 30s，但页面其实已经提交成功**
   真因：脚本里 await 了接口，而响应慢或跨域。
   对策：写「点完就返回」的脚本，再用 `dsh_browser_state` / 再查一次 DOM 确认结果；不要在同一次 execute 里既切 select 又填一堆字段又等响应（拆成多步）。

7. **`dsh_browser_open` 打不开本地 HTML（`file://`）**
   真因：被拒。
   对策：本地 HTML → PDF 用 ego 浏览器（CDP）：`ego_space_open` 一次 → `page.goto('file:///...')` → `cdp('Page.printToPDF', {...})` → base64 落盘。ego 没有 tab 时 `cdp()` 会报 `no active tab to attach session`，先开 space。

8. **`dsh_browser_screenshot` 只回一句「Screenshot captured」，读不到图**
   真因：该载体不返回图像。
   对策：核对状态用 `dsh_browser_state`、`dsh_browser_execute` 读文本/属性；真要看画面就用 ego 的 `ego_screenshot` + `read_image`。

## 平台逻辑类

9. **「职业全览」里点了职业没反应**
   真因：那是只读展板（`.ant-modal` + grid 表格），不是选择器。
   对策：关掉弹窗，用职业输入框的 cascader 搜关键词选中。

10. **领悟提交报 404「请先完成 新知 和 笃行 环节」**
    真因：笃行的子任务没做完（尤其**笃行有多张图 = 多个子任务**的关卡：1、4、6、7）。
    对策：回笃行 `entrance`，把每张图都点开、每个子表单都提交，再回领悟。

11. **笃行里点「提交」没反应，或点了子表单的提交就以为过关**
    真因：子表单提交只落库；关卡是否完成看笃行页那个「提交」按钮。
    对策：子表单 toast 成功 → 回笃行页点「提交」→ 回引导语 → 最后用徽章点亮做最终判定。

12. **手输 `/checkpoint/<name>/practiceearnestly` 得到 404 页**
    真因：只有 `.../practiceearnestly/entrance` 存在。
    对策：从引导语点 `img.scroll_content_img___DtPs3` 的 idx1 进入。

13. **`/center/checkpoint` 的「去闯关」弹出新窗口，切不过去**
    真因：那是 `window.open` 出来的页面，`dsh_browser_switch_tab` 不认。
    对策：直接 `dsh_browser_open(..., newTab=true)` 打开 `https://zgs.chsi.com.cn/mine/info`。

14. **字数明明够了还被判「页面内容不能为空，请填写完整哦！」**
    真因：某个字段没到 min 字数（常见：`#eventName` 要 ≥20、`#expect_result` 要 ≥50）。
    对策：把每个字段的字数区间对一遍，`[...document.querySelectorAll('textarea,input')].map(e => [e.id, e.value.length])` 自查。

15. **徽章不亮但文字显示「去闯关」/反过来**
    真因：列表文字有缓存，判定只能看徽章图的 `background-image`。
    对策：刷新后读 `div.medal_img___g6sj6` 的 inline style；`medal_light_*` 才算亮，注意 `medal_light_new_*` 变体。

## 材料制作类

16. **PDF 排版在浏览器里看着正常，打印出来最后一页只剩一行页脚**
    真因：`@page` 边距只在打印时生效，屏幕预览宽度和打印宽度不同。
    对策：以 PDF 页数为准；把图宽缩 10–20mm、图注改短、footer 的 margin-top 调小即可压回一页。核对用 PyMuPDF 之类的页数统计或直接在浏览器 PDF 阅读器里数。

17. **Word COM 打 HTML → PDF 失败（「Word 未能引发事件」/ RPC 0x800706BE）**
    对策：别绕，直接用 CDP `Page.printToPDF`，速度快、样式也可控。

## 数据/接口类（做诊断时有用）

18. 想确认某一步到底有没有发出去：在页内 hook `XMLHttpRequest.prototype.open/send` 与 `window.fetch`，把 `{m, u, st, body}` push 进 `window.__log`，再触发点击。**跨域 fetch 直接调会被 CORS 拒，必须用页面内的 hook。**
19. `/barriers` 这个接口返回 8 关的 id / 名称 / `is_player`（是否通关），比逐个点页面判断快。
20. OSS 静态资源裸链 403：加 `Referer: https://zgs.chsi.com.cn/` 就能下（PowerShell 的 `Invoke-WebRequest` 可能报 TLS，用 Python `urllib` 更稳）。
