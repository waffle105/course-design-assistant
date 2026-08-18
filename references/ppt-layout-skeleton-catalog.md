# PPT 版式骨架目录（提炼自 Gorden / tristan-mcinnis / Office-PowerPoint-MCP-Server）

> 本文件用于课程开发助手阶段 6 课件制作师。它和我们已有的 `ppt-design-guide.md` 互补：
> - `ppt-design-guide.md` 是"美化规则"（配色/字体/节奏/accent 用法）
> - 本文件是**"版式骨架工程化"**——页面角色分类 + slot 寻址 + 字号层级表 + 出框检测
>
> 三家开源 PPT skill 各有所长，本目录提炼它们能直接被 python-pptx 复用的精华。

---

## 一、页面角色（page role）分类

源自 Gorden 的 detail.json `page_roles` 字段。一份结构化 PPT 应该由这些角色类型组成，**不强造不存在角色**：

| 角色 | 数量建议 | 用途 | 关键元素 |
|---|---|---|---|
| **cover** | 1 | 封面/标题页 | 主标题（中外）+ 副标 + 日期/作者 |
| **agenda** | 1 | 目录 | 章节序号 + 中/英文章节名（通常 4 章固定） |
| **section_divider** | 4 章→4 个 | 章节扉页 | 超大"01/02/03/04"+ 中文章名 + 英文章名 |
| **content** | 每章 2-3 页 | 内容主体 | 顶部面包屑（章节定位）+ 中部主体布局 + 底部页码 |
| **ending** | 1 | 结尾页 | 谢谢/Q&A/总结/联系方式 |

> 规则：模板有 cover 才当封面用；agenda 空就不加目录；ending 空就用最后一张 content 页收尾。**不要强造模板没有的角色**。

---

## 二、slot 寻址规则（edits.json 思想）

源自 Gorden 的 `edits.json` + tristan-mcinnis 的 `[HINT: layout_name]` 标签。

### 2.1 slot 是什么
- 一页 PPT 由若干个**位置固定的形状（shape）**组成，每个 shape = 一个 slot
- 每个 slot 有唯一的 `slot_id`（如 `cover_title_cn`, `p4_item1_title`），运行期可寻址
- slot 分两类：
  - **editable=true**：可换文字的标题/正文/章节名
  - **editable=false**：装饰性序号/百分比/图形（数字与图形不联动）

### 2.2 slot 字段规范
```json
{
  "slot_id": "p4_item1_title",
  "editable": true,
  "max_chars": 72,          // 字数上限（软参考）
  "level": 11,              // type_scale 层级
  "font_size_pt": 16        // 字号
}
```

### 2.3 同级 slot 字号一致性
源自 Gorden 第 8 条编辑铁律：**同一 `level` 的 slot 必须保持相同字号**。

| level | role | 推荐字号（python-pptx pt 值）| 用途 |
|---|---|---|---|
| 1 | 主标（最大）| 54-72 | cover/agenda 主标题 |
| 2 | 一级标题 | 44-54 | cover 中文 / 章节扉页章名 |
| 3 | 二级标题 | 36-44 | 章节扉页英文 / 卡片主标 |
| 4 | 三级标题 | 28-36 | cover 英文副标 / 章节扉页英文 |
| 5 | 面包屑英文 | 24-28 | 内容页右上面包屑英文 |
| 6 | 面包屑中文 | 20-24 | 内容页右上面包屑中文 |
| 7 | 小标 | 18-22 | 图片 caption / 小标 |
| 8 | 段落标题 | 16-20 | 内容块标题 |
| 9 | 关键词/序号 | 16-20 | 菱形关键词 / 序号标签 |
| 10 | 中心标签 | 18 | METHOD 中心 / 焦点词 |
| 11 | 长内容小标 | 14-16 | 多行内容的小标 |
| 12 | 正文 | 12-14 | 卡片正文 |
| 13 | 长正文 | 10.5-12 | 段落正文 |
| 14 | 卡片正文 | 10 | 长卡片正文 |

> 规则：所有 `level=8` 的 slot 必须用同字号（如 16pt）；不要为了让某处"塞下文字"把字号改小。

---

## 三、内容页 8 种版式骨架（综合三家，提炼为"主干骨架"）

源自 `ppt-design-guide.md` 第 8 种布局骨架 + Gorden minimal-business-summary slide 4/5/6/8/9/11/12/14/tristan 的 master layout 思想。

### 骨架 A·3 要点卡片（来自 Gorden slide 4）
- **顶部**：面包屑（章节定位）
- **左侧 / 上半部**：3 张卡片或图标块（图标 + 序号 + 标题 + 简述）
- **右侧 / 下半部**：3 个 Question 标题 + 多行正文
- **底部**：页码
- **适用于**：分点论述、并列方案、3 步骤展示

### 骨架 B·4 列等宽卡片（来自 Gorden slide 5/11）
- **顶部**：面包屑
- **主体**：4 列等宽（每列：顶部图标块/底色条 + 标题 36-40pt + 正文 10.5-12pt + 底部装饰）
- **底部**：页码
- **适用于**：四象限、4P/4C/4S 框架、对比 4 个方案、四种能力

### 骨架 C·2×2 拼图（来自 Gorden slide 8）
- **顶部**：面包屑
- **主体**：四象限（左上深色文字卡 / 右上图片 / 左下图片 / 右下浅色文字卡）
- **底部**：页码
- **适用于**：SWOT、PEST、2x2 矩阵、四分法、案例对比

### 骨架 D·中心辐射 4 叶轮（来自 Gorden slide 9 METHOD）
- **顶部**：面包屑
- **中央**：中心词（如 METHOD / 主题 / 总览）
- **四周**：4 个圆球（编号 01-04）围中心 + 4 个标题 + 4 段正文
- **底部**：页码
- **适用于**：4 步流程、4 要素模型、4 维评估、辐射状概念图

### 骨架 E·菱形关键词矩阵（来自 Gorden slide 12）
- **顶部**：面包屑
- **主体**：2×2 网格，4 个菱形装饰 + 4 个关键词（20pt）+ 4 段长正文（11pt）
- **底部**：页码
- **适用于**：4 个核心概念、4 个关键要素、并列 4 论点

### 骨架 F·S 形里程碑路线（来自 Gorden slide 14）
- **顶部**：面包屑
- **主体**：S 形蜿蜒路线 + 4 面"旗"（每旗旁有标题 + 正文）
- **底部**：页码
- **适用于**：4 阶段时间线、发展历程、学习路径、流程节点

### 骨架 G·左文右数 / 左文右图（综合 Gorden slide 8 + 通用）
- **顶部**：面包屑
- **左侧**：标题 + 一段说明 + 多行要点
- **右侧**：大数字 callout 或图片（占 30-40% 宽度）
- **底部**：页码
- **适用于**：数据冲击、痛点呈现、案例 + 数据、对比

### 骨架 H·标题 + 长正文（来自 Gorden slide 11/14 简化）
- **顶部**：面包屑
- **中部**：大标题居中或左上 + 下方 2-3 段长正文/引用/列表
- **底部**：页码
- **适用于**：知识讲解、长论述、引用页、过渡页

---

## 四、出框检测与容量控制（源自 Gorden build_pptx.py）

### 4.1 max_chars 软原则
- max_chars **是软性参考**，不是硬上限
- 容量本身留 20% 余量（PPT 文本框有弹性）
- 真的超框时，按优先级：
  1. **精炼措辞重写**（不是截断，是真正概括）
  2. 减少要点条数
  3. 换空间更大的版式
  4. 宁超不截断（绝不用省略号）

### 4.2 python-pptx 实现出框检测
```python
from pptx.util import Pt

def check_overflow(shape, text):
    """粗版出框检测：每行 N 字（按 font_size 估算）"""
    if not shape.has_text_frame or not text:
        return False
    w = shape.width / 914400  # inches
    h = shape.height / 914400
    # 保守估算：一行字数 ≈ 宽度(in) × 字号(pt) / 14
    run = next(shape.text_frame.paragraphs[0].runs)
    font_size = run.font.size.pt if run.font.size else 14
    line_chars = max(1, int(w * font_size / 14))
    line_height = font_size / 72 * 1.3  # 含行距（inches）
    max_lines = int(h / line_height) if line_height > 0 else 1
    needed_lines = sum(len(line) // line_chars + 1 for line in text.split('\n') if line)
    return needed_lines > max_lines
```

> 真正的出框检测应该交给 PowerPoint 渲染时自动处理。**日常不要把这种检测做成阻断性错误**（会诱导截断），只做提示。

---

## 五、模板/版式命名规范（用于 PPT 文件命名）

源自 tristan-mcinnis 的 `[HINT: layout_name]` + Gorden 的 `page_roles`：

- **PPT 文件名** 反映"用了什么风格/版式"（如 `PPT生成测试-课程开发教研组介绍.pptx`）
- **当复用模板**时，**不要修改用户原始模板文件**，所有产出写到新文件
- **复制页面**时索引会变，需重新探测

---

## 六、非破坏性编辑铁律（源自 Gorden + tristan 共识）

1. **不改排版**：形状的位置、大小、颜色、字体、字号、行距，都不动
2. **占位文字必须全替换**：模板里的 "Question 1" / "项目名称" 等占位词不能留在最终稿
3. **不混搭装饰与真实数据**：环形图 / 进度条 / 旗帜是装饰形状，改数字不联动弧长
4. **真实数据用真实图表**：python-pptx 的 `add_chart()` 真图表，数据可改；装饰弧线不可改
5. **同级标题字号必须一致**：level N 的 slot 字号统一，不要逐处改
6. **章节名一致性**：改 agenda 章节名，对应的 section_divider + content 面包屑都要同步改
7. **真实数据 vs 装饰分离**：当数据要求超过模板内置装饰范围时，换用真图表骨架

---

## 七、批量化生产教辅课件的版式选择（针对培训课件场景）

> 培训课件 1-3 小时课程通常 12-20 页；5-7 章；4-5 章最稳。
> 推荐版式组合（来自上节骨架 + 培训节奏）：

| 页序 | 角色 | 推荐骨架 | 备注 |
|---|---|---|---|
| 1 | cover | H（标题居中）| 课程名+讲师+日期 |
| 2 | agenda | B 简化（4 行目录）| 4 章节序号 + 中英双语 |
| 3 | section_divider_01 | 全屏 88pt "01" | 章名+章标题 |
| 4 | content WHY | G（左文右痛点）| 痛点 + 大数字 callout |
| 5 | content WHAT | A（3 要点）| 3 个核心概念卡 |
| 6 | content HOW | D（中心 4 叶轮）| 4 步流程或 4 要素 |
| 7 | section_divider_02 | 全屏 88pt "02" | |
| 8 | content 案例 | C（2×2 拼图）| 案例 + 反思 |
| 9 | section_divider_03 | 全屏 88pt "03" | |
| 10 | content 实操 | F（S 形路线）| 学习路径/4 阶段 |
| 11 | content 小结 | E（菱形关键词）| 4 个 take-away |
| 12 | ending | A 简化（3 takeaway）| 总结+行动+Q&A |

> 这是"通用培训课件骨架"，课件制作师可按课程特点增删，但保留 cover/agenda/4 个 section_divider/8 个 content/ending 的节奏。

---

## 八、与已有 `ppt-design-guide.md` 的协同

| 模块 | 来自 | 输出 |
|---|---|---|
| 配色 9 套预设 | guizang → ppt-design-guide | 主题色板 |
| 字体 + 字号分档 | guizang → ppt-design-guide | A/B 组字体 + 中文标题分档 |
| 主题节奏（light/dark 交替）| guizang → ppt-design-guide | 节奏规则 |
| **版式骨架（8 种）** | Gorden + tristan → 本文件 | 可寻址的页面结构 |
| **slot 寻址 + level + max_chars** | Gorden → 本文件 | 工程化编辑 |
| **页面角色分类** | Gorden → 本文件 | page_roles 字段 |
| **非破坏性编辑铁律** | Gorden + tristan → 本文件 | 编辑纪律 |

> ppt-design-guide 告诉你"做漂亮"，本文件告诉你"做规范且可二次编辑"。两者联合覆盖了"设计 + 工程化"两层需求。
