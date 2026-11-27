# Delivery Care Agent · 配送关怀外卖客服与核损工作台

> **Bilingual (中文 / English) AI Customer Support & Human-in-the-Loop Merchant Review Demo**  
> 一个面向外卖配送场景的双语智能客服 Agent 与商家核损审批工作台纯前端演示项目（内置确定性规则引擎、准时保 T1/T2 责任清分与 SHA-256 电子审计链）。

🌐 **Live Demo (GitHub Pages)**: https://892069773-stack.github.io/delivery-care-agent/  
- 顾客客服端 / Customer Portal: `./`
- 商家与专员工作台 / Merchant & Reviewer Workbench: `./review/`

---

## 核心亮点 / Key Features

- **确定性规则引擎 + 生成式 AI 护栏（Deterministic Guardrails）**
  - 严格执行超时补偿边界规则（`delivered_at - expected_at > 30 min`，严格超过 30 分钟方符合条件，自动区分 `29:59`、`30:00`、`30:01` 边界订单），杜绝大模型口头乱承诺金额或跳过审核。
  - 所有订单核实、补偿资格、确认卡与审批状态均由规则引擎确定性执行。
- **T1 出餐 / T2 配送责任清分与骑手保护（Subrogation Cost-Split）**
  - 区分商家出餐延误（T1）与高峰路况/运力延误（T2），支持**平台准时保池 100% 代偿**与**绿灯 STP 免审直赔**，兼顾商家成本与骑手免罚保护。
- **人机协同与食安 P0 绿色通道（Human-in-the-Loop & Food Safety Escalation）**
  - 支持顾客一键转人工、多轮实时追问、图片凭证上传（含二进制魔数校验与 SHA-256 重复图哈希检测）以及多角色审批流（门店专员 `R001`、全平台品控主管 `R002`、只读观察员 `R003`）。
- **不可篡改审计链与合规设计（Audit Chain & PDPO PICS）**
  - 底层工单与审核操作记录 SHA-256 哈希链，内置 PDPO PICS 收集个人资料声明与双语（中/英）无缝切换。
- **零后端开箱即用 & 零密钥泄露设计（Zero-Backend & Safe BYOK）**
  - 完全纯静态前端运行（`localStorage` 模拟前后端共享状态），**仓库不含任何硬编码 API Key**。默认以内置确定性规则引擎开箱运行；访客也可选在浏览器本地自行配置兼容 OpenAI 格式的 API Key（BYOK）。

---

## 演示账号与测试订单 / Demo Accounts

### 顾客端（`/`）
| 账号 | 说明 | 内置测试单 |
|---|---|---|
| `U001`（推荐） | 模拟顾客 01 | `ORD001`（晚 50m 混合延误）、`ORD002`（晚 10m 容差区）、`ORD003`（恰好 30:00）、`ORD004`（边界 29:59）、`ORD005`（边界 30:01 可赔）、`ORD006`（配送中）、`ORD007`（弱网缺签收时间）、`ORD008`（雨天晚 31m 可赔） |
| `U002` | 跨账号对照号 | `ORD101`（用于验证跨顾客权限隔离） |

### 商家与专员工作台（`/review/`）
| 账号 | 角色 | 权限 |
|---|---|---|
| `R001` | 67餐厅履约核损台 | 门店工单审核、人工回复、绿灯直通代偿 |
| `R002` | 平台食安品控专席 | 跨租户监管、自定义 1–20 整数元补偿额、高危食安工单处理 |
| `R003` | 只读质检观察员 | 仅查看会话与工单，无修改权限 |

---

## 快速本地运行 / Quick Start

无需安装任何依赖，使用任意静态 HTTP 服务器即可运行：

```bash
git clone https://github.com/892069773-stack/delivery-care-agent.git
cd delivery-care-agent
python3 -m http.server 8080
```

随后在浏览器访问：
- 顾客端：`http://localhost:8080/`
- 商家工作台：`http://localhost:8080/review/`

### 可选：开启浏览器本地 LLM 对话润色（BYOK）

本项目默认使用内置确定性规则引擎运行，不发送任何外部 API 请求。如需体验大语言模型的多轮对话与自然语言润色，可在浏览器开发者工具控制台输入你自己的 DeepSeek / OpenAI 兼容 API Key（仅保存在当前浏览器的 `localStorage`，不会上传至任何服务器）：

```js
// 开启本地 BYOK 模式
window.ORD001_SET_API_KEY("sk-your-own-key", "deepseek-chat", "https://api.deepseek.com");

// 关闭并清除本地 Key
window.ORD001_SET_API_KEY("");
```

---

## 目录结构 / Project Structure

```text
delivery-care-agent/
├── index.html                 # 顾客端入口 / Customer portal
├── review/
│   └── index.html             # 商家与审批工作台入口 / Merchant workbench
├── static/
│   ├── cloud-runtime.js       # 浏览器端规则引擎、清分模型、审计链与可选 BYOK 适配层
│   ├── app.js                 # 公共图标、吉祥物、主题与接口封装
│   ├── customer.js            # 顾客端对话、订单卡、确认卡与附件上传逻辑
│   ├── review.js              # 商家端工单队列、KPI 看板、STP 批处理与人工专席
│   ├── i18n.js                # 中英双语切换运行时
│   ├── i18n-dict.js           # 中英双语词典
│   ├── app.css                # 响应式样式与深浅色主题
│   └── *.png / ping.svg       # 演示吉祥物插图与静态资源
├── LICENSE
└── README.md
```

---

## 项目来源与免责声明 / Attribution & Disclaimer

- 本项目源自香港城市大学（CityUHK）**IS5542 Group 24** 课程非商业教学演示项目，经团队成员授权后去除全部敏感凭证并整理为纯静态可公开演示版本。
- 页面中所有订单、金额、餐厅与审批账号均为教学演示虚拟数据，不涉及真实交易或个人隐私。
