# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质

这不是代码项目，而是**个人 A 股交易日志 / 复盘库**。用户每天用 Markdown 记录当日的交易操作，并定期回顾、总结、提炼经验。

未来 Claude 实例在这里的典型任务：

- 新建当日交易记录文件
- 汇总某段时间的交易（盈亏统计、持仓变化、操作频次）
- 复盘分析：找出重复出现的失误模式、验证某种判断逻辑的胜率
- 按主题/标的检索历史记录

## 目录结构（三层分工）

```
ai-berkshire/
├── .claude/commands/          xbtlin 的 18 个项目级 skill
├── tools/                     skill 依赖的 Python 工具
├── watchlist/                 用户持仓 + 关注的股票索引（每股 1 短文件）
│   ├── README.md              总索引 ⭐ Claude 必读
│   └── <code>-<name>.md       每只股的精炼摘要 + 链接到 reports/
├── reports/                   skill 自动写入的研究产出（每股可能 N 文件）
│   ├── {公司}-thesis.md       投资论文 (/thesis-tracker)
│   ├── {公司}-earnings-*.md   财报跟踪 (/earnings-review)
│   └── portfolio-latest.md   组合视图 (/portfolio-review)
├── journal/                   交易日志（用户自己写，决策后的复盘）
│   └── YYYY-MM/MM-DD.md       例：journal/2026-06/06-26.md
├── CLAUDE.md                  本文件
└── GUIDE.md                   skills 使用指南（面向用户）
```

## 三个目录的分工

| 目录 | 写入者 | 内容粒度 | 用途 |
|------|--------|----------|------|
| `watchlist/` | **用户** | 每股 1 短文件（< 100 行） | 索引：用户关注什么，关键价位，链接到详报 |
| `reports/` | **skill 自动写** | 每股多个长文件 | 研究产出：快筛/深研/论文/财报/异动归因 |
| `journal/` | **用户** | 每天 1 文件 | 决策日志：真实做了什么、为什么 |

## 文件命名约定

- **交易日志**：`journal/YYYY-MM/MM-DD.md`（月和日均补零）
- **自选股**：`watchlist/<6位代码>-<英文简称或拼音>.md`，例：`603986-gigadevice.md`
- **研究产出**：`reports/` 由 skill 自动管理，遵循 xbtlin 默认命名

## Claude 操作规范

### 会话开始时

**当用户讨论股票相关问题时**，先读 `watchlist/README.md` 了解用户当前关注的股票范围。不在清单里的股票要先确认是否要加入。

### 跑 skill 时

- skill 输出**只**写入 `reports/`，不要写到 `watchlist/` 或 `journal/`
- skill 输出后，提示用户**是否更新 `watchlist/` 里的对应文件**（新增链接、更新关键价位、记录跟踪日期）
- **不要主动写入 `journal/`**——那是用户自己写的复盘，Claude 只在用户明确说"记一下"时才动

### reports/ 的特殊性

- 这是 **skill 的工作目录**，由 4 个跟踪类 skill 自动读写：
  - `/thesis-tracker` `/earnings-review` `/portfolio-review` `/news-pulse`
- skill 是无状态的，"记忆"完全靠 `reports/` 里的 Markdown 文件
- **不要手动编辑 skill 输出的报告**——会被下次跑 skill 覆盖；要记录自己的想法去 `journal/` 或 `watchlist/{code}.md`

## 交易记录字段

用户明确表示"字段之后会规定，先不写"。

- **不要**自作主张设计字段模板（标的、方向、数量、成本、盈亏……都不要预设）
- 用户写第一条完整记录后，再观察其结构、与用户确认后才能固化为模板
- 帮用户新建日报时，如果当天还没有任何字段约定，就建空文件或只写日期标题，让用户自己填

## 市场范围

仅 A 股（沪深两市，含创业板/科创板）。涉及代码、涨跌幅限制、交易时段、T+1 等规则时按 A 股默认，不要混入美股/港股语境。

## 工作规范

- 这个项目里**几乎没有"代码"可写**——主要工作是读写 Markdown、做统计、做分析
- 涉及统计/计算时，优先用一次性脚本（Python/shell），不要为复盘工具搭建框架或抽象
- 复盘结论要基于用户实际写下的内容，**不要编造交易细节或心理活动**——用户没写的就是没写

## 集成的外部 skills（项目级，仅本仓库可用）

`.claude/commands/` 下安装了 [xbtlin/ai-berkshire](https://github.com/xbtlin/ai-berkshire) 的 18 个价投研究 skills（巴菲特/芒格/段永平/李录方法论）。配套 Python 工具在 `tools/`，11 个 skill 依赖之。

**这套 skills 的定位是"下单前的研究"**，本仓库自身的定位是"下单后的复盘"——两者互补，不要混淆：

```
[研究/决策] ───→ [实际下单] ───→ [日志/复盘]
  .claude/commands/                journal/YYYY-MM/MM-DD.md
  (xbtlin 的 skills)                (本仓库主要内容)
```

使用注意：

- `tools/*.py` 的 Python 依赖**未声明**（无 requirements.txt），首次运行会缺包，按报错装即可
- 部分工具会抓取雪球/晨星/A 股数据接口，注意频率
- skills 输出的"建议"只是分析框架，**最终交易决策与日志记录是用户的责任**——不要把 skill 跑出来的结论直接当作复盘事实
