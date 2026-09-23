# ETF投资指南周报（动态版）

数据与页面分离的动态周报：页面只含框架 + 渲染逻辑，数据存放在 `data/` 目录的 JSON 文件中，页面打开时动态拉取渲染。数据文件托管在 GitHub，页面可部署到 GitHub Pages 获得固定访问链接。

## 目录结构

```
etf-report/
├── index.html          # 页面框架 + JS 渲染逻辑（不含数据类型数据）
├── data/
│   ├── latest.json     # 最新一期数据（Wind 取数结果）
│   ├── index.json      # 历史期索引（下拉选择器的期数列表）
│   └── history/        # 历史各期数据，如 20260921.json
└── README.md
```

## 数据格式

`latest.json` 是唯一需要更新的数据文件，结构如下（字段名固定，页面按字段渲染）：

- `asOf` / `weekLabel`：截止日期、周次标签
- `tldr`：结论卡要点（数组）
- `indices`：15 个指数，含 `name`/`code`/`w`(近一周)/`m`(近一月)/`ytd`(今年以来)
- `etfFlow`：`market`（大类）/`broad`（宽基）/`industry`（行业主题）/`crossBorder`（跨境），每项 `{name, value}`，value 单位亿元
- `news`：新闻卡片 `{tag, tag2, title, body}`
- `outlook` / `forecast` / `risk`：展望与风险提示文本
- `products`：推荐产品 `{code, name, type, ytd, q3, size, fee, reason}`

## 数据维护（手动触发）

每次更新周报时：
1. 用 Wind 取数（统计区间 = 近5个交易日，截至最新收盘日），生成新的 `latest.json`
2. 将上一期 `latest.json` 归档为 `data/history/<日期>.json`，并在 `index.json` 追加一条索引
3. `git add` / `git commit` / `git push` 到 GitHub

## 部署

- **GitHub Pages**：仓库 Settings → Pages → 选择 main 分支根目录，访问 `https://<用户名>.github.io/<仓库名>/`
- **本地预览**：`python -m http.server 8000` 后访问 `http://localhost:8000`（直接用 file:// 打开会因浏览器限制无法 fetch JSON）

## 说明

- 图表用纯内联 SVG 动态生成，无任何外部 CDN 依赖，GitHub Pages 与内网环境均可正常渲染。
- 数据来源：Wind（行情 / 申赎净流入）、上海黄金交易所、公开资讯。仅供内部研究参考，不构成投资建议。