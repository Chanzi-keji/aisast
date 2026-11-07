# 铲子 AiSAST

<p align="center">
  <img src="https://raw.githubusercontent.com/Chanzi-keji/aisast/main/assets/app.png" width="110" alt="铲子 AiSAST">
</p>

<h3 align="center">AI 多智能体代码安全审计工作台</h3>

<p align="center">
  <a href="https://github.com/Chanzi-keji/aisast/releases/latest"><img alt="Release" src="https://img.shields.io/github/v/release/Chanzi-keji/aisast?color=5e6ad2"></a>
  <img alt="Windows" src="https://img.shields.io/badge/Windows-10%2F11-0078D6">
  <img alt="macOS" src="https://img.shields.io/badge/macOS-Apple%20Silicon%20%7C%20Intel-000000">
</p>

<p align="center">
  <b>简体中文</b> | <a href="README_EN.md">English</a>
</p>

---

**铲子 AiSAST** 是一款 AI 多智能体协作的代码安全审计桌面工具。输入源码目录或 Git 仓库,它会自动完成「项目分析 → 全局审计 → 漏洞预筛 → API 提取 → 逐 API 审计」全流程,为每一处漏洞给出调用链代码证据与可视化攻击链路,并产出可复核的 HTML 审计报告。

> 本仓库仅发布安装包与版本说明,不包含源代码。

## 核心优势

| 优势 | 说明 |
| --- | --- |
| **审得深** | 多智能体分工协作,逐 API、逐漏洞类别深度审计,而非一次性粗扫 |
| **误报低** | 适用性预筛 + 漏洞有效性验证双重把关,结合业务场景判定真伪 |
| **有证据** | 漏洞 → 链路代码 → 攻击链路图三层闭环,每条结论可定位、可复核 |
| **省成本** | 未命中攻击前提的类别零 token 跳过;攻击图按需生成、缓存复用 |
| **不怕中断** | 阶段进展实时落盘,重启续扫、成果复用,长任务不用重来 |
| **数据自主** | 审计全程在本机执行,代码仅发送至你自己配置的大模型 |

## 五阶段扫描流程

| 阶段 | 说明 |
| --- | --- |
| 1. 项目整体分析 | 自动识别项目结构、技术栈、依赖与鉴权方案,压缩为全局背景供后续阶段复用 |
| 2. 全局漏洞审计 | 硬编码敏感信息、安全配置错误、CORS、JWT/Session 等全局风险,每类仅审一次 |
| 3. 适用性预筛 | 特征匹配 + AI 判定双重把关,未命中攻击前提的漏洞类别直接跳过 |
| 4. API 提取 | 静态线索扫描主流框架路由声明,AI 核对补全,符号存在性校验防幻觉 |
| 5. 逐 API 漏洞审计 | 并行收集上下游调用链,逐接口逐类别审计,结果与证据同步落盘 |

## 功能特性

### 攻击链路图

- 每个 API 一键生成攻击数据流图,前端自绘 SVG 渲染(蓝=入口 → 灰=传递/校验 → 红=危险点)
- 任务级攻击链路总图:按漏洞类型聚合,自上而下展示攻击请求从入口到危险点的完整传播链路
- 按需生成、懒加载 + 本地缓存,重复查看不重复计费;扫描进行中即可生成
- 行号三层校准,点击节点跳转定位准确

### 链路代码与 AI 追问

- 每个 API 的完整上下游调用链代码证据,逐跳展开定位问题根源
- 攻击图、链路代码、漏洞详情均支持 AI 追问,SSE 流式回答,会话持久化、重启可恢复

### 快速扫描模式

- 高危 API 打标「AI + 静态规则」双引擎,专用并发通道 + 动态池扩容,快速聚焦高危接口

### 组件漏洞审计

- 自动分析 pom.xml、package.json、requirements.txt 等依赖声明,识别 fastjson、Log4j、Shiro、Struts2、Django、Flask、lodash 等跨语言高危组件及其已知 CVE

### 漏洞有效性验证

- 结合业务场景判定漏洞真伪:误报打标红色「误报」、确认漏洞标记绿色「已确认」
- 支持增量续验,已判定项自动复用,结论同步体现在结果页与报告中

### 断点续扫与动态排除

- 每个阶段的进展即时落盘快照,软件重启后完全还原进度,重扫自动复用已有成果
- 扫描中可随时排除单个 API,即时生效、状态持久化

### 审计报告

- 一键生成明暗双主题 HTML 报告,逐 API 展开查看调用链代码证据
- 过程分析自动梳理扫描日志,审计全程可回溯、可复核

### 大模型支持与成本观测

- 支持 DeepSeek、通义千问、智谱 GLM、Kimi 等 11+ 国内主流大模型厂商自动填充,支持自定义 OpenAI 兼容接口,「测试连接」即时验证
- 各阶段 token 消耗与费用实时统计,高峰/低谷计费一目了然,成本可控

## 支持的语言与入口

- **语言生态**:Java、Go、Python、Node.js、PHP、C#、Ruby、Rust、C/C++
- **入口类型**:HTTP、RPC、WebSocket、MQ 四类远程调用入口;主流框架由静态线索辅助提取,自研框架由 AI 泛化兜底

## 下载安装

前往 [Releases](https://github.com/Chanzi-keji/aisast/releases/latest) 下载对应平台安装包:

| 平台 | 文件 |
| --- | --- |
| Windows 10/11(64 位) | `Chanzi-AiSAST-Setup-x.y.z.exe` |
| macOS Apple Silicon(M 系列芯片) | `Chanzi-AiSAST-x.y.z-arm64.dmg` |
| macOS Intel 芯片 | `Chanzi-AiSAST-x.y.z-x64.dmg` |

- 备用下载(腾讯微云):https://share.weiyun.com/7n4mAIlW
- 安装包内置 JRE 与全部运行时依赖,**无需预装任何环境**

## 快速上手

1. 下载并安装对应平台的版本
2. 启动软件,在设置页选择大模型厂商并填入你自己的 API Key(可用「测试连接」验证)
3. 新建扫描任务:选择本地源码目录或填写 Git 仓库地址
4. 等待扫描完成,查看漏洞列表、攻击链路图与 HTML 报告

## 版本更新

- **v2026.4.0**:快速扫描模式、行号定位精准化、审计效率提升、漏洞详情级 AI 追问、精细化审计标准、补链与复审增强、成本优化、体验与稳定性改进
- 完整历史版本请查看 [Releases](https://github.com/Chanzi-keji/aisast/releases)

## 反馈与支持

- 官网:https://www.chanzikeji.com
- 问题反馈:[Issues](https://github.com/Chanzi-keji/aisast/issues)

## 声明

- 本仓库仅用于发布安装包,**不包含任何源代码**
- 软件著作权归北京铲子科技有限公司所有
- 请遵守相关法律法规,仅对你有权测试的目标使用本工具
