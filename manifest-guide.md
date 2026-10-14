# manifest.csv 字段说明与使用指南

> 配套文件：本仓库 manifest.csv（智能体安全调研全量语料索引，4,913 篇）｜ 快照日期：2026-10-13 ｜ 配套报告：reports/ 下两份中文报告

## 1. 这是什么

- 一行 = 一篇 arXiv 论文；4,913 行覆盖智能体安全三大域（agent 智能体 / rag 检索增强 / embodied 具身）。
- 与报告关系：报告"全量论文表" 4,165 条 = 本表剔除 verdict=D 的行；阅读清单 152 篇核心集 = 全部有本地 PDF 与 MinerU 全文解析。
- 数据来源：arXiv API 检索（2021–2026）+ Semantic Scholar 引用量快照（2026-10-13）。

## 2. 字段说明（16 列）

| 列名 | 含义 | 取值/格式 | 备注/示例 |
|---|---|---|---|
| `arxiv_id` | 归一化 arXiv ID（去版本号） | 字符串，唯一主键 | `2601.04795`（不带 v1/v2） |
| `title` | 论文标题 | arXiv 原文 | 与 arXiv 页面一致 |
| `year` | 发表年份 | 2021–2026 | 按 arXiv 首次提交年 |
| `first_author` | 第一作者 | 字符串 | 取作者列表首位 |
| `authors` | 完整作者列表 | 逗号分隔 | 来自 arXiv 元数据 |
| `abstract` | 摘要全文 | arXiv 原文 | 全表零空值 |
| `citation_count` | Semantic Scholar 引用量快照 | 整数；空值=无记录 | 空 13 篇、覆盖 4,900/4,913（99.7%）；中位数 2，最大 7523（2108.07258） |
| `venue` | S2 会议/期刊字段 | 字符串 | Top：arXiv.org、ACL、ICRA、EMNLP、RA-L、NeurIPS、AAAI、IROS |
| `domain` | 所属领域 | `agent` / `rag` / `embodied`；跨域为组合值 | 跨域示例：`agent;embodied`（分号分隔） |
| `subtopic` | 子主题编码 | A1–A9 / B1 / C1；空值=未分类 | 见下方子主题表 |
| `verdict` | 筛选判定 | `K` / `S` / `D` / `baseline` / `union` | 见下方判定说明 |
| `note` | 一句话核心贡献（中文） | 仅 K 行有值 | 如"解析工具结果过滤注入指令" |
| `source` | 来源追踪 | `baseline-agent` / `baseline-rag` / `increment` / `embodied` / `union-oldkb` / `union-recheck` | 组合值表示多来源命中，如 `increment;embodied` |
| `pdf_local` | 本地是否存有 PDF | `True` / `False` | 搜索路径：智能体安全知识库 / 近期关键词文章（智能体安全、RAG 工具目录）/ 旧知识库 |
| `parsed` | 是否已有 MinerU 全文解析 | `True` / `False` | 输出路径：`解析输出\<id>\auto\<id>.md`；核心集 152 篇全部为 True |
| `in_old_kb_topic` | 曾收录于旧知识库的主题 | 字符串；空=未收录 | 有值即表示旧知识库中有对应条目 |

**子主题编码（11 类）**

| 编码 | 主题 | 编码 | 主题 |
|---|---|---|---|
| A1 | 提示注入 | A7 | 评测基准 |
| A2 | 工具与 MCP 供应链 | A8 | 系统防御 |
| A3 | 记忆安全 | A9 | 治理与事件 |
| A4 | 多智能体风险 | B1 | RAG 安全 |
| A5 | 智能体网络攻防 | C1 | 具身安全 |
| A6 | 权限与授权 | | |

空值 = 未分类（主要是 D 行）。

**verdict 判定说明**

| 取值 | 含义 | 数量 | 口径 |
|---|---|---|---|
| `K` | 核心相关 | 1,414 | 增量批逐篇精读判定 |
| `S` | 边缘相关 | 1,011 | 增量批逐篇精读判定 |
| `D` | 排除 | 748 | 增量批逐篇精读判定 |
| `baseline` | 前代语料 | 1,708 | 沿用前代分类 |
| `union` | 补录 | 32 | 旧知识库补录 |

增量批 2,499 篇经逐篇精读判定；2026-10-13 旧关键词库复检批次新纳入 674 篇（K=354/S=320，`source=union-recheck`），判定口径与增量批一致；前代语料沿用前代分类。

**source 编码说明**

- `baseline-agent` / `baseline-rag`：前代关键词库命中；
- `increment`：2026-09-01 → 2026-10-12 增量检索命中；
- `embodied`：具身新域检索命中；
- `union-oldkb`：旧知识库补录；
- `union-recheck`：旧关键词库复检纳入（2026-10-13 复检批次，674 篇）；
- 分号组合值（如 `baseline-agent;embodied`）表示同一篇命中多个来源。

## 3. 快速查找（常用配方）

### Excel 筛选（3 条）

1. **读某主题最好的论文**：`subtopic` 列筛选 `A1`，再叠加 `verdict` 筛选 `K`，按 `citation_count` 降序取前 20。
2. **找有本地全文的论文**：`parsed` 列筛选 `True`（即已有 MinerU 全文解析，可直接读 Markdown）。
3. **按 ID 定位单篇**：`arxiv_id` 列使用"文本筛选 → 精确匹配"输入目标 ID（不带版本号）。

### pandas 代码示例（3 个）

```python
import pandas as pd
df = pd.read_csv("manifest.csv")

# 示例 1：某主题核心论文 Top 20（按引用量降序）
top20 = (df[(df.subtopic == "A1") & (df.verdict == "K")]
         .sort_values("citation_count", ascending=False)
         .head(20))

# 示例 2：找有本地全文/已解析的论文
local = df[df.parsed == True]

# 示例 3：按 ID 定位单篇
row = df[df.arxiv_id == "2601.04795"]
```

### grep / rg 示例（1 个）

```bash
# 按 arxiv_id 在 manifest.csv 中定位单行（注意字段带引号，用逗号锚定列首）
rg '^2601\.04795,' manifest.csv
```

**补充**：找核心集（152 篇）请直接对照阅读清单文件或知识库目录（阅读清单与 `core_set` 全量一致，见第 6 节）。

## 4. 如何补充新论文（增量工作流）

复用本调研管线，按以下步骤操作：

1. **arXiv API 检索**：构造关键词组 + 日期窗（如最近 30 天），拉取元数据（id、title、authors、abstract、year）。
2. **去重**：`arxiv_id` 去版本号后与 manifest 全表比对；另做标题归一化（去大小写/标点/空白）二次比对，防止换 ID 重复入库。
3. **追加行**：新增行 `source` 填 `increment-YYYYMM`（标注补充批次），其余新列先留空。
4. **逐篇筛选**：人工填 `verdict` / `subtopic` / `note`。判定标准——攻击、防御、评测、治理、协议、隐私风险核心为 `K`；相关但非核心为 `S`；与智能体/RAG/具身三域无关为 `D`。
5. **拉取引用量**：调用 Semantic Scholar API 更新 `citation_count` / `venue`，并在快照说明中注明日期。
6. **更新本地资源状态**：下载 PDF 后置 `pdf_local=True`；完成 MinerU 解析后置 `parsed=True`。
7. **同步报告与阅读清单**：联动更新——报告"全量论文表"（剔除新产生的 D 行）、各子主题表、阅读清单（若纳入核心集）及 `screening_stats.md` / `qa_id_check.md` 等 QA 文件。

**注意事项**

- 语料去重以 `arxiv_id` 为主键，标题归一化为辅；
- 跨域论文在 `domain` 用分号合并（如 `agent;embodied`），不拆成两行；
- 每次补充后须在报告中注明口径变化（新增数量、日期窗、verdict 分布变化）。

## 5. 当前数据构成（快照 2026-10-13）

**verdict 分布**

| verdict | 行数 |
|---|---|
| baseline | 1,708 |
| K | 1,414 |
| D | 748 |
| S | 1,011 |
| union | 32 |

**domain 分布**

| domain | 行数 |
|---|---|
| agent | 2,963 |
| embodied | 1,357 |
| rag | 548 |
| agent;embodied | 42 |
| rag;embodied | 3 |

**source 分布**

| source | 行数 |
|---|---|
| embodied | 1,349 |
| baseline-agent | 1,290 |
| increment | 1,105 |
| union-recheck | 674 |
| baseline-rag | 418 |
| union-oldkb | 32 |
| baseline-agent;embodied | 25 |
| increment;embodied | 18 |
| baseline-rag;embodied | 2 |

**年份分布**

| 年份 | 行数 |
|---|---|
| 2021 | 50 |
| 2022 | 51 |
| 2023 | 111 |
| 2024 | 401 |
| 2025 | 1,210 |
| 2026 | 3,090 |

**subtopic 分布**

| subtopic | 行数 | subtopic | 行数 |
|---|---|---|---|
| A1 | 1,019 | A5 | 268 |
| C1 | 837 | A9 | 196 |
| （空） | 761 | A4 | 182 |
| A7 | 447 | A6 | 169 |
| A2 | 430 | B1 | 147 |
| A8 | 340 | A3 | 117 |

## 6. 质量与核验

- **主键唯一性**：`arxiv_id` 唯一无重复；`abstract` 零空值。
- **S2 覆盖率**：Semantic Scholar 覆盖 4,900 / 4,913（99.7%），13 篇无引用记录（`citation_count` 为空）。
- **QA 复核**：145 篇 D 判定但标题/摘要含强安全词的论文已重审，其中 4 篇 D→S 升级；2026-10-13 旧关键词库复检批次新纳入 674 篇（K=354/S=320，424 篇排除留档）（当前 D=748、S=1,011 即复核后终值）。
- **ID 交叉核验**：报告表格、正文、阅读清单中出现的 arXiv ID 与本表全量交叉核验，缺失 0 个（详见 `qa_id_check.md`）；阅读清单 152 篇与核心集 152 篇完全一致。
- **本地状态刷新**：`pdf_local` / `parsed` 两列状态以 2026-10-13 刷新结果为准，新增解析须同步更新。

---

*文档生成：2026-10-13 ｜ 数据快照：manifest.csv（4,913 行 × 16 列）*
