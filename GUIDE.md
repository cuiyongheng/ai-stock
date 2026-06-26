# Skills 使用指南

本仓库集成了 [xbtlin/ai-berkshire](https://github.com/xbtlin/ai-berkshire) 的 18 个价值投资研究 skills，位于 `.claude/commands/`，**仅在本仓库目录下生效**。

## 快速使用

在 Claude Code 里直接输入斜杠命令 + 参数：

```
/investment-research 贵州茅台
/quality-screen 600519
/news-pulse 比亚迪
```

> 参数可以是公司名 / 股票代码 / 行业关键词，具体看每个 skill 的设计。

---

## 18 个 Skills 分类速查

### 深度研究（针对单个公司）

| 命令 | 用途 | 典型场景 |
|------|------|----------|
| `/investment-research` | 四大师综合分析框架 | 想全面研究一只股票 |
| `/investment-team` | 四角色 Agent **并行**分析 | 要更彻底，愿意花更多时间 |
| `/deep-company-series` | 拆成 8 篇长文 | 长期标的，建立完整认知 |
| `/management-deep-dive` | 管理层纵深研究 | 买股票=买人 |
| `/private-company-research` | 未上市公司多 Agent 研究 | 调研一级市场标的 |

### 财报解读

| 命令 | 用途 |
|------|------|
| `/earnings-review` | 一手资料精读单份财报 |
| `/earnings-team` | 四大师并行解读 + 输出公众号文 |

### 行业 / 筛选

| 命令 | 用途 |
|------|------|
| `/industry-research` | 产业链全景扫描 + 个股分析 |
| `/industry-funnel` | 全市场 → 3 家精选漏斗流程 |
| `/quality-screen` | 7 条指标快速排除非一流公司 |
| `/investment-checklist` | 巴菲特式买入前 checklist |
| `/bottleneck-hunter` | 全球产业链瓶颈套利机会 |

### 组合管理 / 持仓跟踪

| 命令 | 用途 |
|------|------|
| `/portfolio-review` | 从"研究公司"升级到"管理组合" |
| `/thesis-tracker` | 买入后的论文追踪 / 纪律系统 |
| `/news-pulse` | 股价异动时**4 Agent 并行**快速归因 |

### 思维 / 数据 / 写作

| 命令 | 用途 |
|------|------|
| `/dyp-ask` | 用段永平的方式提问思考 |
| `/financial-data` | 财务数据获取与交叉验证规范 |
| `/wechat-article` | 公众号文章（作者-编辑-读者三 Agent） |

---

## 典型工作流

### 1. 新标的初筛 → 深度研究

```
[听到一个名字]
    │
    ↓
/quality-screen <标的>          ← 7 条指标快筛，淘汰非一流
    │
    ↓ (通过)
/industry-research <行业>       ← 看清行业格局
    │
    ↓
/investment-research <标的>     ← 四大师综合视角
    │
    ↓ (倾向买入)
/investment-checklist <标的>    ← 买入前最终核对
```

### 2. 财报季

```
持有的每只票 ──→ /earnings-review       (快)
重要持仓     ──→ /earnings-team         (慢但全面，附带公众号文)
```

### 3. 持仓日常

```
每月/每季   ──→ /portfolio-review       (整体视角)
每只持仓    ──→ /thesis-tracker         (论文是否仍然成立)
股价异动    ──→ /news-pulse             (快速归因 + 是否需要重审)
```

### 4. 行业扫描

```
/industry-funnel <行业> → /quality-screen 候选 → /investment-checklist 入围
```

### 5. 决策辅助（任何环节插入）

```
卡壳时 ──→ /dyp-ask <问题>     ← 用段永平的方式问自己
取数时 ──→ /financial-data     ← 按规范交叉验证
```

---

## 注意事项

### Python 依赖未声明

`tools/` 下 9 个工具脚本未提供 `requirements.txt`，首次运行多半会 `ImportError`，照报错装包即可。常见依赖：

```
pandas, requests, akshare, beautifulsoup4, lxml
```

### 数据接口频率

`tools/xueqiu_scraper.py`、`ashare_data.py`、`morningstar_fair_value.py` 会抓取外部数据，注意：

- 不要短时间内连续跑
- 雪球等可能需要 cookie，跑不通先看脚本头部说明

### Skills 输出 ≠ 投资建议

```
[skills 跑出的结论]  ──→ 是研究框架的产物，不是事实
[实际下单]           ──→ 你的判断，你的责任
[journal/MM-DD.md]   ──→ 只记录你真实做了什么，不要抄 skill 的话当复盘
```

三件事，三个目录：
- **索引/关注**：`watchlist/<code>-<name>.md`（你自己写，每股一短文件）
- **研究/跟踪**：`.claude/commands/` 里的 skills 跑出来，写入 `reports/`（skill 自动管理）
- **复盘**：`journal/YYYY-MM/MM-DD.md`（你自己写，记录决策与心得）

---

## 卸载

如果某天不想用了：

```bash
rm -rf .claude/commands tools
# 再编辑 CLAUDE.md 删掉相关段落
```

仍然只影响本仓库，不会动到全局 Claude Code 配置。
