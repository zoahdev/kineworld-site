# kineworld-site

勘境 KINEWORLD 官网源码 — kineworld.com

正式世界模型名称为 **KineOne-WM**。`KINE-EXP-001` 与 `KINE-JEPA` 仅作为历史实验编号和兼容代号保留；KineOne-WM 与第三方榜单中既有的 `KineWorld` 条目无关。

> 我们把官网也开源：一个宣称"开放、可复现"的公司，网站本身不该是黑箱。

## 内容

- `index.html` — 主站（定位 / 技术路线 / 开源计划 / 公开实验 / 路线图）
- `report.html` — KINE-EXP-001 训练报告页（损失曲线随训练更新）
- `kine-bench.html` — KINE-Bench 消费级世界模型评测基准提案

## 部署

静态站点，托管于阿里云 OSS 静态网站，域名 `kineworld.com` / `www.kineworld.com`，HTTPS（Let's Encrypt，自动续期）。无构建步骤，直接编辑 HTML。

## 相关仓库

- [kine-datapipe](https://github.com/zoahdev/kine-datapipe) — 视频数据管线
- [kine-jepa](https://github.com/zoahdev/kine-jepa) — 世界模型训练代码（KINE-EXP-001）

## 许可

MIT
