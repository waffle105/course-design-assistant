# PPT 美化设计指南（合并版）

> 本文件是课程开发助手阶段 6 课件制作师必须遵守的设计方法论。
> 来源：guizang-ppt-skill（设计方法）+ GordenPPTSkill（slot/level 寻址纪律）+ Office-PowerPoint-MCP-Server（原生 chart / 可编辑性）。
> 课件制作师在生成 .pptx 时，按本指南套用——产出物**必须在 PowerPoint 里可二次编辑**（可改文字、可改数据、可换图表），禁止"导出图片当 PPT 页面"这类作弊。

---

## 〇、三条不可违背的总铁律

### 铁律 1 · 可编辑性第一
- **用 python-pptx 原生 API**，不作弊：
  - 数据图 → 必须用 `shapes.add_chart()` 调用原生 chart（OOXML 写出，可在 PowerPoint 里改数据/换类型）
  - 文本 → 必须用 `textbox`（可在 PPT 里改文字、字号、颜色）
  - 形状 → 必须用 `MSO_SHAPE`（可在 PPT 里选、移动、改色）
- **禁止**：
  - 把整页导出 PNG 然后 add_picture（编辑不了）
  - 把 chart 转成图再插入（编辑不了）
  - 用 SVG 渲染冒充 PPT 页（不是 PPT 格式）

### 铁律 2 · 同一 level 强制同字号（Gorden 第 8 条）
- 全文规定 14 个 level（详见第三章 §字号层级表）
- **同一 level 在 PPT 所有页面必须用相同字号**——这是版式稳定感的核心
- 例：H1 在封面 44pt / 单元扉页 40pt **不行**——H1 在所有页都是 44pt
- 例：body 在 P5 是 18pt，在 P7 是 20pt **不行**——body 全篇一律 18pt

### 铁律 3 · 非破坏性编辑（形状与文本分离）
- 装饰形状（序号圆/分隔线/底色矩形）和文本框**分开创建**，不要把序号放进文本框里
- 这样：换页文字不会破坏装饰、改装饰不会丢文字
- 例：步骤 01 的 "01" 数字应该是独立形状（oval），不是文本框第 1 段的 run

---

## 一、主题色板（9 套预设，禁止自定义 hex）

> 来源：guizang-ppt-skill 的 themes.md + themes-swiss.md。
> 一份 PPT 只用一套主题，中途不换色。禁止混搭（A 组的 ink 配 B 组的 paper）、禁止用户自定义 hex。

### A 组·电子杂志风（5 套，衬线标题 + 暖色底，适合人文/文化/管理类培训）

| 主题名 | ink（文字主色）| paper（底色）| paper-tint（区块底）| 适合 |
|---|---|---|---|---|
| 🖋 墨水经典 | #0a0a0b | #f1efea | #e8e5de | 通用、商业、科技（默认） |
| 🌊 靛蓝瓷 | #0a1f3d | #f1f3f5 | #e4e8ec | 科技/研究/数据/工程师 |
| 🌿 森林墨 | #1a2e1f | #f5f1e8 | #ece7da | 自然/可持续/文化/环保 |
| 🍂 牛皮纸 | #2a1e13 | #eedfc7 | #e0d0b6 | 怀旧/人文/历史/文学 |
| 🌙 沙丘 | #1f1a14 | #f0e6d2 | #e3d7bf | 艺术/设计/创意/时尚 |

### B 组·瑞士国际主义风（4 套，无衬线 + 单一高亮色，适合科技/数据/汇报类培训）

| 主题名 | ink | paper | accent（高亮色）| accent-on（高亮色上的字）| 适合 |
|---|---|---|---|---|---|
| 🔵 克莱因蓝 | #0a0a0a | #fafaf8 | #002FA7 | #ffffff | 通用/AI/科技/设计（默认） |
| 🟡 柠檬黄 | #0a0a0a | #fafaf8 | #FFD500 | #0a0a0a | 年轻/活力/消费/零售 |
| 🟢 柠檬绿 | #0a0a0a | #fafaf8 | #C5E903 | #0a0a0a | 生态/未来/Z世代/新兴 |
| 🟠 安全橙 | #0a0a0a | #fafaf8 | #FF6B35 | #ffffff | 工业/警示/汽车/紧迫感 |

> 每套还共享灰阶：grey-1=#f0f0ee（浅灰底）/ grey-2=#d4d4d2（分割线）/ grey-3=#737373（辅助文字）

### 选主题规则
- 一份 PPT 只用一套主题，中途不换色
- 主题选择由阶段 6 课件制作师根据课程内容推荐，或问用户选（给 2 个推荐方案）
- **禁止混搭**（不能 A 组的 ink 配 B 组的 paper）
- **禁止用户自定义 hex 值**——只从 9 套预设里选
- 不要用纯白 #fff 或纯黑 #000——会损失质感（A 组用暖米白，B 组用 #fafaf8 暖白）

---

## 二、字体配对规则

### A 组·电子杂志风
- **标题**：衬线体。中文用"思源宋体/方正书宋"，英文用"Playfair Display/Georgia"
- **正文**：无衬线体。中文用"思源黑体/微软雅黑"，英文用"Inter/Helvetica"
- **元数据**（页码/时间/标签）：等宽体。"JetBrains Mono/Consolas"

### B 组·瑞士国际主义风
- **全程无衬线**：Inter / Helvetica Neue + 中文字体（思源黑体/微软雅黑）
- **字重控制**（关键）：
  - 大标题：200 ExtraLight（不要用 Bold，瑞士风的灵魂是"细"）
  - 正文：300 Light
  - 小标/分类：600 SemiBold
- **数字**：等宽体 JetBrains Mono，开 `tnum` 等宽数字特性

> python-pptx 实现提示：如果目标机器没装 Inter/Playfair，回退到"微软雅黑"（无衬线）和"方正书宋/宋体"（衬线）。字重通过 `font.bold=False` + 字号差异化实现（python-pptx 不支持 ExtraLight，用大字号 + 不加粗模拟"细"的感觉）。

---

## 三、字号层级表（level 字号纪律·同 level 必同字号）

> 来源：GordenPPTSkill 的 detail.json type_scale。
> **整份 PPT 严格用这 14 个 level，同一 level 全篇同字号。**

| level | 角色 | 字号 | 用法 |
|---|---|---|---|
| L1 | display-cover | 60pt | 封面主标题（极特殊，如课程名） |
| L2 | h1 | 44pt | 单元扉页标题 / 节标题 |
| L3 | h2 | 30pt | 页内大标题（如"WHY 学这个"） |
| L4 | h3 | 22pt | 卡片主标题 / 概念名 |
| L5 | h4 | 18pt | 小标题 / 分类标签 |
| L6 | lead | 16pt | 引语 / lead 段 |
| L7 | body | 13pt | 正文 |
| L8 | body-sm | 11pt | 小字说明 / 图注 |
| L9 | caption | 10pt | 辅助 / 元数据 / 页码 |
| L10 | micro | 8pt | 微文 / 边注 |
| L11 | kpi-xl | 72pt | KPI 大数字（强调数据） |
| L12 | kpi-lg | 48pt | KPI 中数字 |
| L13 | kpi-md | 32pt | KPI 小数字 |
| L14 | quote | 26pt | 金句居中页 |

> 例：L3 在 P1 是 30pt，在 P3 是 30pt，在 P5 也是 30pt——**绝对不混用**。这是版式稳定感的硬纪律。

### 中文标题字号规则（与 guizang 一致）
中文方块字视觉面积比英文重，按标题长度降级：

| 中文标题形态 | 推荐字号 |
|---|---|
| 1 行，≤8 字 | L2 (44pt) |
| 2 行，每行≤8 字 | L3 (30pt) |
| 2 行，任一行 9-12 字 | L4 (22pt) |
| 3 行或更长 | 改写标题；实在不行用 L5 (18pt) |

规则：中文标题优先改短，其次降字号，不要让标题挤占下方图文区域。

---

## 四、页面角色（page_roles）· 用 5 类定页型

> 来源：GordenPPTSkill 的 page_role 分类。
> 整份 PPT 由这 5 类角色组成。**模板有什么角色就用什么角色，不要强造**（比如没有 chapter_title 角色就别造一个）。

| 角色 | 页数比例 | 作用 | 视觉特征 |
|---|---|---|---|
| **cover** | 1 张 | 封面 | 整页居中大字 L1、深底白字/白底黑字仪式感 |
| **agenda** | 1 张 | 目录 | 章节序号 + 章节名列表、light 底 |
| **section_divider** | 每章 1 张 | 章节扉页 | 整页大编号（如 "01" 占 60%）+ 章名 |
| **content** | 占 70% | 知识页 / 案例 / 数据 / 练习 | 见第五章 8 种骨架 |
| **ending** | 1 张 | 收尾 / Q&A / 行动 | 大字仪式、含 take-away 列表 |

### 培训课件标准节奏模板（20 页版）

```
cover (1)
agenda (2)
section_divider (3) ← "01 为什么学"
content × 3 (4-6)
section_divider (7) ← "02 核心概念"
content × 3 (8-10)
section_divider (11) ← "03 实操步骤"
content × 3 (12-14)
section_divider (15) ← "04 应用练习"
content × 3 (16-18)
ending (19) ← 课程总结
Q&A (20) ← 大字收尾
```

**全套 20 页**，每章 1 扉页 + 3 内容页 + 章间过渡自然。复杂课程可延长章节内容页数。

---

## 五、内容页 8 种版式骨架（A-H）

> 来源：合并 guizang 10+22 种 + Gorden tristan-mcinnis 真实 layouts + MCP-Server 25 种。
> 每种骨架都有：①适用内容 ②页面元素位置 ③python-pptx 实现关键 ④与哪种 page_role 匹配。

### 骨架 A · 3 要点卡片（KSA）
- **适用**：本节要点 / 三个关键概念
- **结构**：顶部 L3 标题 → 下方 3 张等宽卡片（每张 paper-tint 底）→ 每卡：L4 标题 + L7 正文
- **节奏**：light 底为主
- **实现**：3 个 `MSO_SHAPE.RECTANGLE`（卡片底）+ 3 个 textbox（卡内文字）

### 骨架 B · 4 列等宽（4P / PEST / 4个并列概念）
- **适用**：4 个并列项（4P、PEST、产品四维度）
- **结构**：L3 标题 → 4 列等宽网格 → 每列：accent 色数字 / 标签 / 简述
- **节奏**：light 底
- **实现**：4 个等宽列区 + 列内数字 L11/L12 + 标签 L4

### 骨架 C · 2×2 拼图（SWOT / 矩阵）
- **适用**：四象限分析、SWOT、2x2 矩阵
- **结构**：L3 标题 → 4 个矩形组成 2×2 → 每格 L4 标签 + L7 短文
- **节奏**：light 底（避免深底导致象限难辨）
- **实现**：4 个矩形精确定位（每格约 6×3 英寸）

### 骨架 D · 4 叶轮（PDCA / 4 阶段循环）
- **适用**：PDCA、戴明环、4 阶段流程
- **结构**：L3 标题 → 中心圆 + 4 个外围圆 → 中心是主题，4 外围各阶段 L4 标题 + L7 描述
- **节奏**：**dark 底（accent 色作为步骤色）**
- **实现**：5 个 `MSO_SHAPE.OVAL`（1 大 + 4 小）+ 居中摆放

### 骨架 E · 菱形关键词（中心扩散）
- **适用**：关键词聚类 / 主题辐射 / 中心概念
- **结构**：L3 标题 → 中心 1 大关键词 L1 + 4-6 个周边关键词 L4 → 周边词间用细线连接
- **节奏**：light 底（让中心词突出）
- **实现**：中心 L1 字号大 + 周边 4-6 个 L4 + 直线 `MSO_CONNECTOR.STRAIGHT`

### 骨架 F · S 路线（4 阶段递进）
- **适用**：4 阶段路线 / 漏斗 / 递进关系
- **结构**：L3 标题 → S 形曲线连接 4 个圆 → 每圆 L12 编号 + L4 阶段名 + L7 描述
- **节奏**：light 底（accent 色用于阶段连接）
- **实现**：4 个 `MSO_SHAPE.OVAL` 错落摆位 + 路径线段

### 骨架 G · 左文右大数字（KPI 强调）
- **适用**：一个核心数据 + 解释
- **结构**：左 60% 是 L7 正文 + L6 引语 / 右 40% 是 L11 大数字（accent 色）
- **节奏**：light 底
- **实现**：左 textbox + 右 accent 色文字块（72pt 大字）

### 骨架 H · 标题 + 长正文（深度解析）
- **适用**：一页说透一个概念 / 方法论展开
- **结构**：L3 标题 → L7 正文段（4-6 行）→ 可选 L8 脚注 / 引用
- **节奏**：light 底（信息密度页用浅底防疲劳）
- **实现**：上下排版的两 textbox

---

## 六、主题节奏（light/dark 交替，避免疲劳）

> 来源：guizang themes + 通用版式节奏原则。
> 整份 PPT 按此节奏交替，**不要连续 3 页同底色**。

| 页类型 | 底色 | 效果 |
|---|---|---|
| cover | dark | 仪式感开场 |
| agenda | light | 呼吸 |
| section_divider | light 或 paper-tint | 章节呼吸 |
| 内容页（要点/概念） | light | 信息密度页，浅底易读 |
| 内容页（流程/步骤） | **dark** | 节奏变化，防疲劳 |
| 内容页（数据/KPI） | light 或 dark 均可 | 跟前后节奏 |
| ending | dark | 收尾仪式感 |
| Q&A | light 或 accent 底 | 开放结束 |

> 默认节奏建议：**DARK → L → L → D → L → L → D → L → DARK**（封面深/章扉页深/收尾深夹在浅底中段间）
> 节流阀：每 2-3 页浅底后插一页深底。

---

## 七、Slot 寻址机制（非破坏性编辑的工程实现）

> 来源：GordenPPTSkill 的 edits.json + slot_id 设计。
> python-pptx 实现要点：

### 1. 每页只创建可寻址 shape，不创建"装饰文字"
- 序号圆（"01"）→ 独立 `MSO_SHAPE.OVAL`，数字文本独立 textbox 叠加在圆上
- 分隔线 → `MSO_SHAPE.RECTANGLE` 高 0.02 英寸的细条
- 卡片底 → `MSO_SHAPE.RECTANGLE` 加 fill
- 文本 → 独立 textbox

### 2. shape.name 命名约定（方便二次编辑）
python-pptx 支持给 shape 设 `name` 属性，便于在 PowerPoint 里选中：
```python
shape.name = "p5_step01_oval"   # p5 步骤 01 圆
shape.name = "p5_step01_text"   # p5 步骤 01 文本
shape.name = "p3_title"         # p3 标题
shape.name = "p8_kpi_73"        # p8 KPI 大数字
```

### 3. 出框检测（max_chars 软上限，宁超不截断）
> 来源：Gorden detail.json 的 max_chars 字段。
> 每个 slot 设 `max_chars` 软上限——**超过不强制截断**，但要在交付说明里提示"该页文字超出 max_chars，可能影响版式美观"。

| slot 类型 | max_chars | 超过后果 |
|---|---|---|
| L3 标题 | 16 字 | 换行或挤占正文区 |
| L4 卡片标题 | 10 字 | 撑破卡片 |
| L7 正文（每卡） | 50 字 | 卡片溢出 |
| KPI 大数字 | 4 字（首字符 + 单位） | 单位撑破版式 |

> 例：P5 步骤 01 描述写了 60 字，max_chars=50——不截断，但提示教务长"该页文字可能影响美观，建议精简"。

---

## 八、原生 chart 用法（数据图必须用 python-pptx 原生 chart）

> 来源：Office-PowerPoint-MCP-Server 的 `add_chart()` API + "round-trip 保留所有元素" 设计哲学。
> 这是可编辑性的核心——**chart 必须是原生 chart，不是图**。

### 1. 支持的 chart 类型

| chart 类型 | python-pptx XL_CHART_TYPE | 适用数据 |
|---|---|---|
| 柱状图 | COLUMN_CLUSTERED | 多类别单数值对比 |
| 簇状柱 | COLUMN_CLUSTERED | 同图多系列对比 |
| 折线图 | LINE | 时间序列 / 趋势 |
| 饼图 | PIE | 单系列占比 |
| 环形图 | DOUGHNUT | 多层占比 |
| 条形图 | BAR_CLUSTERED | 横向对比 |
| 面积图 | AREA | 累积量 |

### 2. 创建 chart 的最小代码
```python
from pptx.chart.data import CategoryChartData
from pptx.enum.chart import XL_CHART_TYPE, XL_LEGEND_POSITION
from pptx.util import Inches

chart_data = CategoryChartData()
chart_data.categories = ['Q1', 'Q2', 'Q3', 'Q4']
chart_data.add_series('2024', (45, 60, 35, 70))
chart_data.add_series('2023', (40, 55, 30, 60))

chart_shape = slide.shapes.add_chart(
    XL_CHART_TYPE.COLUMN_CLUSTERED,
    Inches(1), Inches(2), Inches(11), Inches(4),
    chart_data
).chart

chart.has_title = True
chart.chart_title.text_frame.text = "季度营收对比"
chart.has_legend = True
chart.legend.position = XL_LEGEND_POSITION.BOTTOM
chart.legend.include_in_layout = False
```

### 3. 用法纪律
- 标题字号统一 L4（22pt）
- 图例放底部（XL_LEGEND_POSITION.BOTTOM）
- **必须给 chart 加数据源说明**（"数据来源：xxx"），L8 字号 11pt
- 不指定 `chart.font.size` 时跟主题同步（章标题字号大小）
- 改数据只改 `add_series` 的元组，**不要**画新 chart

### 4. 出错兜底
- 如果数据为 None 或空 → 改用骨架 B（4 列等宽）呈现"暂无数据"
- 如果 chart 类型不识别 → fallback 到柱状图

---

## 九、图片规范（如有配图）

- 永远用标准比例：16:9 / 4:3 / 1:1 / 3:2，不要用奇葩比例
- 图片必须加边框（1px grey-2 细线），不要裸图
- 图文混排用左右分栏（grid 7:3 或 6:4），图片在右，文字在左
- 同组多图必须同高，不要混用不同高度
- 截图/UI 图加白底框实现 `.fit-contain` 效果

---

## 十、python-pptx 实现要点

### 基础设置
```python
from pptx import Presentation
from pptx.util import Inches, Pt, Emu
from pptx.dml.color import RGBColor
from pptx.enum.text import PP_ALIGN, MSO_ANCHOR
from pptx.enum.shapes import MSO_SHAPE, MSO_CONNECTOR

prs = Presentation()
prs.slide_width = Inches(13.333)  # 16:9
prs.slide_height = Inches(7.5)
```

### 主题色映射
```python
# 主题"克莱因蓝"（B 组默认）
INK = RGBColor(0x0a, 0x0a, 0x0a)
PAPER = RGBColor(0xfa, 0xfa, 0xf8)
PAPER_TINT = RGBColor(0xf0, 0xf0, 0xee)
ACCENT = RGBColor(0x00, 0x2F, 0xA7)   # IKB 蓝
ACCENT_ON = RGBColor(0xff, 0xff, 0xff)
GREY_3 = RGBColor(0x73, 0x73, 0x73)
```

### 画一页浅底页
```python
slide = prs.slides.add_slide(prs.slide_layouts[6])  # 空白布局
# 底色
bg = slide.shapes.add_shape(MSO_SHAPE.RECTANGLE, 0, 0, prs.slide_width, prs.slide_height)
bg.fill.solid()
bg.fill.fore_color.rgb = PAPER
bg.line.fill.background()
bg.name = "bg_paper"
# 标题
title = slide.shapes.add_textbox(Inches(0.8), Inches(0.6), Inches(11), Inches(1.2))
title.name = "title"
title.text_frame.text = "核心概念"
title.text_frame.paragraphs[0].runs[0].font.size = Pt(30)  # L3
title.text_frame.paragraphs[0].runs[0].font.color.rgb = INK
title.text_frame.paragraphs[0].runs[0].font.name = "微软雅黑"
```

### 画 accent 色块（步骤号/KPI）
```python
step_box = slide.shapes.add_shape(MSO_SHAPE.OVAL, Inches(1), Inches(3), Inches(0.6), Inches(0.6))
step_box.name = "step01_oval"
step_box.fill.solid()
step_box.fill.fore_color.rgb = ACCENT
step_box.line.fill.background()
step_num = slide.shapes.add_textbox(Inches(1), Inches(3), Inches(0.6), Inches(0.6))
step_num.name = "step01_text"
step_num.text_frame.text = "01"
step_num.text_frame.paragraphs[0].runs[0].font.size = Pt(22)  # L4
step_num.text_frame.paragraphs[0].runs[0].font.color.rgb = ACCENT_ON
step_num.text_frame.paragraphs[0].alignment = PP_ALIGN.CENTER
```

### 画原生 chart
```python
# 见第八章 §原生 chart 用法
```

---

## 十一、交付前自检清单（必须全部勾选）

### 可编辑性（铁律 1）
- [ ] 所有数据图都是 `add_chart()` 原生 chart（非图片）
- [ ] 所有文本都是 `textbox`（非图片）
- [ ] 所有形状都是 `MSO_SHAPE`（非 SVG/PNG）
- [ ] 在 PowerPoint 里打开能正常选中/编辑所有元素

### 字号纪律（铁律 2）
- [ ] 同 level 在所有页面字号一致
- [ ] 中文标题按分档表选字号
- [ ] 无破规字号（如 body 在不同页 18/16/20pt）

### 非破坏性编辑（铁律 3）
- [ ] 装饰形状（序号圆/分隔线）与文本分开
- [ ] 序号用独立 shape + textbox 组合，不是文本框第 1 行
- [ ] shape.name 命名清晰（"p5_step01_oval" 等）

### 设计美感
- [ ] 主题色只用了 9 套预设之一，无自定义 hex
- [ ] 全程未用纯白 #fff / 纯黑 #000 做底色
- [ ] light/dark 节奏交替，无连续 3 页同底色
- [ ] 图片用标准比例 + 加边框
- [ ] 留白充足，单页信息不超载（一页一观点 + 6×6 原则）
- [ ] accent 色只用于强调（步骤号/KPI/关键词），不全屏铺