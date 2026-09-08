# AI 查论文 · 数据·算法·论文索引 Skill

Opencode `quant-data-index` skill 的独立仓库。用于快速查找币圈 / A股 / 港股 / 美股的
行情数据下载源、量化算法库，以及通过 arXiv / Crossref / OpenAlex 检索学术论文。

> 原 skill 位于 Opencode 项目 `.opencode/skills/quant-data-index/`，此为独立备份。

## 目录结构

```
ai-paper-search/
├── SKILL.md                    # Skill 主文档：使用规则、请求头要求、限速、合规底线
├── reference/
│   ├── data-sources.md         # 数据源索引：API 端点、爬虫路径、批量下载地址（历史可用性标记）
│   ├── algorithms.md           # 算法索引：因子挖掘、预测模型、执行算法、开源框架
│   └── papers.md               # 论文检索：arXiv / Crossref / OpenAlex 接口 + 必读清单
├── scripts/
│   ├── probe_sources.py        # 检查内置公开接口，校验数据结构
│   └── fetch_examples.py       # 获取公开样本，保存到明确指定的新文件
├── tests/test_sources.py       # 离线边界和命令行测试
└── IMPLEMENTATION_TASK.md      # 本次审计、修复与验证记录
```

## 论文检索（AI 查论文核心）

索引包含以下学术检索接口。鉴权、额度和网络可达性会变化，历史标记不是当前保证：

| 接口 | 用途 | 限速 |
|---|---|---|
| arXiv API | 预印本论文检索，返回 Atom XML | ≥ 3 秒/请求 |
| Crossref API | 正式发表论文元数据，可带 `mailto=` 提供联系信息 | 以响应头和官方政策为准 |
| OpenAlex | 学术图谱，作者/机构/引用关系 | 鉴权和额度以官方当前说明为准 |

使用示例见 `reference/papers.md`。

## 运行工具

需要 Python 3.10 或以上版本，无需安装第三方依赖。在本仓库根目录运行：

```powershell
python scripts/probe_sources.py --list
python scripts/probe_sources.py --group crypto --timeout 10
python scripts/probe_sources.py --all --output output/probe.json
python scripts/fetch_examples.py --source gate-futures --limit 5 --output output/gate-futures.json
python scripts/fetch_examples.py --source crossref --query "quantitative trading" --output output/papers.json
python -m unittest discover -s tests -v
```

`--source` 支持 `gate-spot`、`gate-futures`、`binance`、`arxiv`、`crossref`；`--group` 支持 `crypto`、`papers`。`--all` 检查这五个内置样本接口，不会遍历参考文档中的全部链接，也不会读取任何本地密钥或登录状态。

探活是发起小型真实请求，检查 HTTP 状态、返回格式和必要字段；即使接口返回 200，错误页面或业务错误也会判为失败。任一探活失败时退出码为 1，全部成功为 0，命令参数错误为 2。退出码是供终端或自动化程序判断结果的数字。失败不意味着服务永久停用，可能是地域限制、超时、限流或接口变更。工具不自动重试；请求超时为每次网络操作的等待上限，响应体最多 2 MiB。

取样必须显式选择来源和输出文件，已有文件不会被覆盖；失败时不保存伪造样本。JSON 同时记录公开请求 URL、获取时间和原始精度的价格文本。Gate 现货成交量单位是 USDT，Gate 合约是合约张数，Binance 现货是 BTC，不可直接相加。K 线样本可能包含尚未结束的当日数据，不能直接当作完整回测数据集。

论文检索可能正常返回零条结果；探活使用固定关键词，零条结果会标为未验证成功。arXiv 连续请求请至少间隔 3 秒。自动检查采用串行请求，测试使用本地构造的数据，不依赖服务商在线。

## 可用性标记

每条源后面的标记：`[OK]` 匿名直连 / `[OK+H]` 需补请求头 / `[OK+K]` 需免费 key /
`[CN-BLOCK]` 大陆被拦 / `[COOKIE]` 需登录态 / `[PAID]` 商业授权。

## 合规底线

- 交易所行情数据再分发通常需要授权，自用研究和对外发布是两回事。
- 商业数据库（Wind / CSMAR / CRSP / Compustat）导出数据外传是违约。
- 不要绕过验证码、不要伪造登录态卖数据。
