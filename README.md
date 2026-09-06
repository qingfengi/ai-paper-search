# AI 查论文 · 数据·算法·论文索引 Skill

Opencode `quant-data-index` skill 的独立仓库。用于快速查找币圈 / A股 / 港股 / 美股的
行情数据下载源、量化算法库，以及通过 arXiv / Crossref / OpenAlex 检索学术论文。

> 原 skill 位于 Opencode 项目 `.opencode/skills/quant-data-index/`，此为独立备份。

## 目录结构

```
ai-paper-search/
├── SKILL.md                    # Skill 主文档：使用规则、请求头要求、限速、合规底线
├── reference/
│   ├── data-sources.md         # 数据源索引：API 端点、爬虫路径、批量下载地址（已实测可用性标记）
│   ├── algorithms.md           # 算法索引：因子挖掘、预测模型、执行算法、开源框架
│   └── papers.md               # 论文检索：arXiv / Crossref / OpenAlex 接口 + 必读清单
└── scripts/
    ├── probe_sources.py        # 批量验证 URL 可用性（占位，待实现）
    └── fetch_examples.py       # 取真实数据跑通链路（占位，待实现）
```

## 论文检索（AI 查论文核心）

三个免 key 的学术检索接口，均已从中国大陆网络实测可用：

| 接口 | 用途 | 限速 |
|---|---|---|
| arXiv API | 预印本论文检索，返回 Atom XML | ≥ 3 秒/请求 |
| Crossref API | 正式发表论文元数据，带 `mailto=` 进礼貌池 | ≤ 50 req/s |
| OpenAlex | 学术图谱，作者/机构/引用关系，带 `mailto=` | ≤ 10 req/s |

使用示例见 `reference/papers.md`。

## 可用性标记

每条源后面的标记：`[OK]` 匿名直连 / `[OK+H]` 需补请求头 / `[OK+K]` 需免费 key /
`[CN-BLOCK]` 大陆被拦 / `[COOKIE]` 需登录态 / `[PAID]` 商业授权。

## 合规底线

- 交易所行情数据再分发通常需要授权，自用研究和对外发布是两回事。
- 商业数据库（Wind / CSMAR / CRSP / Compustat）导出数据外传是违约。
- 不要绕过验证码、不要伪造登录态卖数据。
