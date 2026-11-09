# FitPulse 错误姿势知识库（后台 · 错误姿势与纠正）

> 版本：`1.0.0` · 更新日期：`2026-11-15` · 语言：简体中文  
> 维护人：陈辉腾（领域知识研究员 B）  
> 用途：为 FitPulse 的「动作纠错」提供结构化、可检索的**错误姿势表现、成因、风险与纠正**知识，与标准动作库（`exercises/`）和肌肉发力库（`muscle-activation/`）配合使用。

## 1. 本批次交付内容

本批次覆盖居家训练最常出现的 **6 类错误姿势**，每个条目包含：

- 1 份 RAG 友好的 Markdown 条目（YAML frontmatter + 固定章节）；
- 1 张原创双栏对比示意图（SVG，常见错误 vs 纠正后）；
- 可批量入库的 [`manifest.json`](manifest.json)（摘要索引；完整字段以 Markdown frontmatter 为准）；
- 集中式来源说明 [`SOURCES.md`](SOURCES.md)。

| ID | 错误姿势 | 部位 | 风险 | 条目 | 示意图 |
|---|---|---|---|---|---|
| EP-001 | 腰椎过伸（塌腰） | 腰背 | 中 | [打开](entries/EP-001-lumbar-hyperextension.md) | [查看](assets/EP-001-lumbar-hyperextension.svg) |
| EP-002 | 腰椎屈曲（弓背） | 腰背 | 高 | [打开](entries/EP-002-lumbar-flexion-rounding.md) | [查看](assets/EP-002-lumbar-flexion-rounding.svg) |
| EP-003 | 膝盖内扣（膝外翻） | 膝关节 | 中 | [打开](entries/EP-003-knee-valgus.md) | [查看](assets/EP-003-knee-valgus.svg) |
| EP-004 | 耸肩与颈部代偿 | 肩颈 | 中 | [打开](entries/EP-004-shoulder-shrugging-neck.md) | [查看](assets/EP-004-shoulder-shrugging-neck.svg) |
| EP-005 | 骨盆前倾 | 骨盆 | 中 | [打开](entries/EP-005-anterior-pelvic-tilt.md) | [查看](assets/EP-005-anterior-pelvic-tilt.svg) |
| EP-006 | 借力与惯性摆动 | 全身 | 中 | [打开](entries/EP-006-momentum-swinging.md) | [查看](assets/EP-006-momentum-swinging.svg) |

## 2. 目录结构

```text
error-posture/
├── README.md                 # 本文件：规范、入库方式、验收标准
├── SOURCES.md                # 集中式知识来源与引用说明
├── manifest.json             # 6 个条目的机器可读索引
├── entries/                  # 6 份错误姿势 Markdown
│   ├── EP-001-lumbar-hyperextension.md
│   └── ...
└── assets/                   # 6 张原创双栏对比 SVG
    ├── EP-001-lumbar-hyperextension.svg
    └── ...
```

建议在仓库中的落点（与标准动作库并列）：

```text
backend/rag/knowledge_base/error-posture/   # 本目录整体复制到这里
```

## 3. Markdown 条目规范

每个条目由两段组成：

1. **YAML frontmatter**：供数据库、Chroma metadata、检索过滤使用；
2. **固定 H2 章节**：供 RAG 切块、动作纠错引用使用。

### 3.1 frontmatter 字段

| 字段 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `id` | string | 是 | 稳定主键，格式 `EP-001` |
| `slug` | string | 是 | URL / 文件名用英文短横线标识 |
| `name` | string | 是 | 中文错误姿势名 |
| `aliases` | string[] | 是 | 检索别名、俗称、英文名 |
| `category` | string | 是 | 固定为 `错误姿势` |
| `body_region` | string | 是 | 涉及身体部位（腰背 / 膝关节 / 肩颈 / 骨盆 / 全身动作控制） |
| `risk_level` | string | 是 | 风险等级 `低` / `中` / `高` |
| `common_in` | string[] | 是 | 常见出现的动作 |
| `related_exercises` | string[] | 是 | 关联标准动作条目 `EX-xxx` |
| `tags` | string[] | 是 | 检索标签 |
| `image` | string | 是 | 相对 Markdown 文件的图片路径 |
| `image_alt` | string | 是 | 图片替代文本 |
| `source_ids` | string[] | 是 | 对应 `SOURCES.md` 的来源编号 |
| `version` | string | 是 | 条目版本 |
| `last_reviewed` | string | 是 | 最近复核日期，`YYYY-MM-DD` |

### 3.2 固定章节

```text
## 一句话要点
## 错误姿势长什么样
## 为什么会发生
## 潜在风险
## 如何自查
## 纠正方法
## 关联动作
## 何时需要就医（红旗信号）
## 常见问题
## 知识出处
```

固定章节的好处：

- “错误姿势长什么样”+“如何自查”可直接作为动作纠错工具的识别上下文；
- “纠正方法”给出分步骤、可执行的改进建议；
- “关联动作”通过 `related_exercises` 与标准动作库形成知识图谱关系；
- “何时需要就医（红旗信号）”给出明确的安全边界。

## 4. 建议的入库方式（Chroma）

### 4.1 解析与切块

按 H2 切块。每个 chunk 前拼接错误姿势名与别名，避免短标题失去上下文：

```text
错误姿势：膝盖内扣（别名：膝内扣、膝外翻、Knee Valgus）
章节：纠正方法
内容：1. 膝盖推外——提示“膝盖推向小脚趾方向”……
```

### 4.2 推荐 metadata

```json
{
  "doc_type": "error_posture",
  "entry_id": "EP-003",
  "slug": "knee-valgus",
  "name": "膝盖内扣（膝外翻）",
  "aliases": "膝内扣,膝盖内收,膝外翻,Knee Valgus",
  "category": "错误姿势",
  "body_region": "膝关节",
  "risk_level": "中",
  "related_exercises": "EX-001,EX-003,EX-011,EX-012",
  "section": "纠正方法",
  "source_ids": "S3,S6,S7",
  "image_path": "assets/EP-003-knee-valgus.svg",
  "version": "1.0",
  "last_reviewed": "2026-11-15"
}
```

### 4.3 建议的检索策略

- 动作纠错：先按 `name` / `aliases` / `body_region` 匹配错误姿势，再取“错误姿势长什么样”“如何自查”“纠正方法”三个章节；
- 正向提示：用 `related_exercises` 反查 `muscle-activation/` 的“发力技巧”作为纠正后的 cue；
- 回答中必须带 `source_ids`，前端展示为“知识出处”。

## 5. 内容口径与安全边界

- 面向**健康成人 / 大学生新手**的居家健身教学，不构成医疗诊断或个体化康复处方。
- “潜在风险”是教学提示，不制造恐慌、不做绝对化结论；Agent 应使用“建议先咨询医生/康复师”这类措辞。
- “何时需要就医（红旗信号）”是通用安全提醒，不构成个体化医疗建议。
- 不使用“绝对不能……”“一定会受伤”等绝对化说法。
- 疼痛感与肌肉酸胀要区分：尖锐痛、关节痛、放射痛应停止；延迟性肌肉酸痛可观察。
- 图片为错误/纠正姿势示意，不用于医学诊断。

## 6. 入库前验收清单

- [ ] 6 个 `id` 唯一，且与 `manifest.json` 一致；
- [ ] frontmatter 可被 YAML 解析器读取；
- [ ] 每个条目都包含固定 H2 章节；
- [ ] Markdown 内图片相对路径存在；
- [ ] SVG 包含 `<title>` 与 `<desc>`；
- [ ] 每个条目的 `source_ids` 都能在 `SOURCES.md` 找到；
- [ ] “错误姿势长什么样”至少 3 条，“纠正方法”至少 4 步；
- [ ] `related_exercises` 引用的 `EX-xxx` 在标准动作库中真实存在；
- [ ] 含“红旗信号”且措辞为非诊断性；
- [ ] 图片为原创示意图，未使用未授权网图。

## 7. 如何新增条目

1. 复制 `entries/EP-001-lumbar-hyperextension.md` 作为模板；
2. 分配下一个 `EP-0xx`，更新 `slug`、文件名、图片名；
3. 按固定章节补全内容，至少给出 3 条表现和 4 步纠正；
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
