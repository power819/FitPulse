# FitPulse 肌肉发力知识库（后台 · 肌肉发力与发力感）

> 版本：`1.0.0` · 更新日期：`2026-11-15` · 语言：简体中文  
> 维护人：陈辉腾（领域知识研究员 B）  
> 用途：为 FitPulse 的「动作查询」「动作纠错」提供结构化、可检索的**肌肉发力与发力感**知识，与标准动作库（`exercises/`）配合使用。

## 1. 本批次交付内容

本批次覆盖居家训练最常需要“找发力感”的 **6 个核心肌群/区域**，每个条目包含：

- 1 份 RAG 友好的 Markdown 条目（YAML frontmatter + 固定章节）；
- 1 张原创示意图（SVG，标注发力位置 + 三条激活提示）；
- 可批量入库的 [`manifest.json`](manifest.json)（摘要索引；完整字段以 Markdown frontmatter 为准）；
- 集中式来源说明 [`SOURCES.md`](SOURCES.md)。

| ID | 条目 | 目标肌群 | 难度 | 条目 | 示意图 |
|---|---|---|---|---|---|
| MA-001 | 臀大肌激活与发力 | 臀大肌 | 入门 | [打开](entries/MA-001-glute-activation.md) | [查看](assets/MA-001-glute-activation.svg) |
| MA-002 | 腹部核心收紧与呼吸支撑 | 腹横肌 | 入门 | [打开](entries/MA-002-core-bracing.md) | [查看](assets/MA-002-core-bracing.svg) |
| MA-003 | 背阔肌与背部发力 | 背阔肌 | 进阶 | [打开](entries/MA-003-lat-back-activation.md) | [查看](assets/MA-003-lat-back-activation.svg) |
| MA-004 | 胸大肌发力 | 胸大肌 | 进阶 | [打开](entries/MA-004-chest-activation.md) | [查看](assets/MA-004-chest-activation.svg) |
| MA-005 | 股四头肌发力 | 股四头肌 | 入门 | [打开](entries/MA-005-quad-activation.md) | [查看](assets/MA-005-quad-activation.svg) |
| MA-006 | 肩胛骨稳定与三角肌发力 | 三角肌 | 进阶 | [打开](entries/MA-006-scapular-shoulder-control.md) | [查看](assets/MA-006-scapular-shoulder-control.svg) |

## 2. 目录结构

```text
muscle-activation/
├── README.md                 # 本文件：规范、入库方式、验收标准
├── SOURCES.md                # 集中式知识来源与引用说明
├── manifest.json             # 6 个条目的机器可读索引
├── entries/                  # 6 份发力感 Markdown
│   ├── MA-001-glute-activation.md
│   └── ...
└── assets/                   # 6 张原创 SVG 示意图
    ├── MA-001-glute-activation.svg
    └── ...
```

建议在仓库中的落点（与标准动作库并列）：

```text
backend/rag/knowledge_base/muscle-activation/   # 本目录整体复制到这里
```

## 3. Markdown 条目规范

每个条目由两段组成：

1. **YAML frontmatter**：供数据库、Chroma metadata、检索过滤使用；
2. **固定 H2 章节**：供 RAG 切块、动作查询与纠错引用使用。

### 3.1 frontmatter 字段

| 字段 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `id` | string | 是 | 稳定主键，格式 `MA-001` |
| `slug` | string | 是 | URL / 文件名用英文短横线标识 |
| `name` | string | 是 | 中文条目名 |
| `aliases` | string[] | 是 | 检索别名、俗称、英文名 |
| `category` | string | 是 | 固定为 `肌肉发力` |
| `target_muscle` | string | 是 | 主要目标肌群 |
| `related_muscles` | string[] | 是 | 协同/稳定肌群 |
| `related_patterns` | string[] | 是 | 关联动作模式（`squat` / `hinge` / `horizontal_pull` 等） |
| `related_exercises` | string[] | 是 | 关联标准动作条目 `EX-xxx` |
| `difficulty` | string | 是 | `入门` / `进阶` |
| `equipment` | string[] | 是 | 所需器械；无器械写 `["无"]` |
| `tags` | string[] | 是 | 检索标签 |
| `image` | string | 是 | 相对 Markdown 文件的图片路径 |
| `image_alt` | string | 是 | 图片替代文本 |
| `source_ids` | string[] | 是 | 对应 `SOURCES.md` 的来源编号 |
| `version` | string | 是 | 条目版本 |
| `last_reviewed` | string | 是 | 最近复核日期，`YYYY-MM-DD` |

### 3.2 固定章节

```text
## 一句话要点
## 目标肌群与发力位置
## 为什么要先学发力感
## 激活准备（唤醒）
## 发力技巧与提示语
## 常见的“发力错误感”（代偿信号）
## 自测方法
## 关联动作
## 呼吸配合
## 安全提示
## 常见问题
## 知识出处
```

固定章节的好处：

- “发力技巧与提示语”可作为动作纠错工具的正向提示（cue）；
- “常见的发力错误感（代偿信号）”可与错误姿势库（`error-posture/`）联动检索；
- “自测方法”帮助用户判断发力是否正确；
- “关联动作”通过 `related_exercises` 与标准动作库形成知识图谱关系。

## 4. 建议的入库方式（Chroma）

### 4.1 解析与切块

按 H2 切块。每个 chunk 前拼接条目名与别名，避免短标题失去上下文：

```text
条目：臀大肌激活与发力（别名：臀肌激活、臀部发力感）
章节：发力技巧与提示语
内容：脚跟推地——想象用脚跟把地面“推开”……
```

### 4.2 推荐 metadata

```json
{
  "doc_type": "muscle_activation",
  "entry_id": "MA-001",
  "slug": "glute-activation",
  "name": "臀大肌激活与发力",
  "aliases": "臀肌激活,臀部发力感,Glute Activation",
  "category": "肌肉发力",
  "target_muscle": "臀大肌",
  "related_exercises": "EX-001,EX-002,EX-003,EX-009",
  "difficulty": "入门",
  "equipment": "无",
  "section": "发力技巧与提示语",
  "source_ids": "S3,S4,S6",
  "image_path": "assets/MA-001-glute-activation.svg",
  "version": "1.0",
  "last_reviewed": "2026-11-15"
}
```

### 4.3 建议的检索策略

- 动作纠错：先检索 `error-posture/` 定位错误类型，再用 `related_exercises` / `target_muscle` 反查本库的“发力技巧”与“代偿信号”作为正向提示；
- 动作查询：用户问“怎么找臀部发力感”，按 `name` / `aliases` / `target_muscle` 匹配；
- 回答中必须带 `source_ids`，前端展示为“知识出处”。

## 5. 内容口径与安全边界

- 面向**健康成人 / 大学生新手**的居家健身教学，不构成医疗诊断或个体化康复处方。
- “代偿信号”是教学提示，不是诊断结论；Agent 应使用“建议先咨询医生/康复师”这类措辞。
- 不使用“必须练到力竭”“越酸越有效”等绝对化说法；发力感以“目标肌群有感觉、不代偿”为准。
- 疼痛感与肌肉酸胀要区分：尖锐痛、关节痛、放射痛应停止；延迟性肌肉酸痛可观察。
- 图片为发力位置示意，不用于医学诊断。

## 6. 入库前验收清单

- [ ] 6 个 `id` 唯一，且与 `manifest.json` 一致；
- [ ] frontmatter 可被 YAML 解析器读取；
- [ ] 每个条目都包含固定 H2 章节；
- [ ] Markdown 内图片相对路径存在；
- [ ] SVG 包含 `<title>` 与 `<desc>`；
- [ ] 每个条目的 `source_ids` 都能在 `SOURCES.md` 找到；
- [ ] “发力技巧与提示语”至少 3 条，“代偿信号”至少 3 条；
- [ ] `related_exercises` 引用的 `EX-xxx` 在标准动作库中真实存在；
- [ ] 不含绝对化医疗断言，不含未标注来源的“研究证明”；
- [ ] 图片为原创示意图，未使用未授权网图。

## 7. 如何新增条目

1. 复制 `entries/MA-001-glute-activation.md` 作为模板；
2. 分配下一个 `MA-0xx`，更新 `slug`、文件名、图片名；
3. 按固定章节补全内容，至少给出 3 条发力技巧和 3 条代偿信号；
4. 在 `SOURCES.md` 中确认来源编号存在；
5. 在 `manifest.json` 的 `entries` 数组追加一条；
6. 更新本 README 的目录表与版本号；
7. 按第 6 节清单验收。

## 8. 版权与引用

- 文字条目由 FitPulse 团队依据公开教材、专业机构动作库与教学共识整理，**不是原文复制**；引用来源见 [`SOURCES.md`](SOURCES.md)。
- 6 张 SVG 示意图为本项目原创绘制，仅用于课程项目展示与教学示意。
- 建议在应用内展示：“内容用于健身教学参考，不能替代医生或线下教练的个体化评估。”

---

维护人：陈辉腾（领域知识研究员 B） · 复核人：待团队指定 · 下一批复核：动作纠错上线前
