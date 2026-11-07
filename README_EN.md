# Chanzi AiSAST

<p align="center">
  <img src="https://raw.githubusercontent.com/Chanzi-keji/aisast/main/assets/app.png" width="110" alt="Chanzi AiSAST">
</p>

<h3 align="center">AI Multi-Agent Code Security Auditing Workbench</h3>

<p align="center">
  <a href="https://github.com/Chanzi-keji/aisast/releases/latest"><img alt="Release" src="https://img.shields.io/github/v/release/Chanzi-keji/aisast?color=5e6ad2"></a>
  <img alt="Windows" src="https://img.shields.io/badge/Windows-10%2F11-0078D6">
  <img alt="macOS" src="https://img.shields.io/badge/macOS-Apple%20Silicon%20%7C%20Intel-000000">
</p>

<p align="center">
  <a href="README.md">简体中文</a> | <b>English</b>
</p>

---

**Chanzi AiSAST** is a desktop code security auditing tool powered by AI multi-agent collaboration. Point it at a source directory or a Git repository, and it automatically runs the full pipeline — *project analysis → global audit → applicability prescreen → API extraction → per-API audit* — delivering call-chain code evidence and visualized attack paths for every finding, plus a reviewable HTML report.

> This repository only hosts installers and release notes. No source code is published here.

## Why Chanzi AiSAST

| Advantage | Description |
| --- | --- |
| **Deep auditing** | Multiple agents divide the work and audit every API and vulnerability category in depth — not a one-pass rough scan |
| **Fewer false positives** | Applicability prescreening plus vulnerability validation cross-check every finding against your business context |
| **Evidence-based** | Vulnerability → chain code → attack graph, a three-layer closed loop where every conclusion is locatable and reviewable |
| **Cost efficient** | Categories that fail the attack-precondition check are skipped at zero token cost; attack graphs are generated on demand and cached |
| **Resume anytime** | Stage progress is checkpointed to disk in real time; restart resumes with prior results reused — long scans never start over |
| **Data stays yours** | Auditing runs entirely on your machine; code is only sent to the LLM provider you configure |

## Five-Stage Scan Pipeline

| Stage | Description |
| --- | --- |
| 1. Project Analysis | Identifies project structure, tech stack, dependencies and auth schemes; compresses them into shared global context |
| 2. Global Audit | Audits hardcoded secrets, security misconfiguration, CORS, JWT/Session and other global risks — once per category |
| 3. Applicability Prescreen | Static signatures plus AI judgement; categories without attack preconditions are skipped entirely |
| 4. API Extraction | Static scanning of mainstream framework routes, AI cross-checking, and symbol existence verification against hallucination |
| 5. Per-API Audit | Parallel collection of upstream/downstream call chains, then per-endpoint, per-category auditing with evidence persisted |

## Feature Highlights

### Attack Path Graph

- One-click attack data-flow graph per API, rendered as self-drawn SVG (blue = entry, gray = transit/validation, red = dangerous sink)
- Task-level aggregate graph: all per-API graphs grouped by vulnerability type, showing complete propagation from entry to sink
- Generated on demand with lazy loading and local caching — repeated views cost nothing; available even while scanning is running
- Three-layer line-number calibration keeps node code navigation accurate

### Chain Code & AI Follow-up Q&A

- Full upstream/downstream call-chain code evidence per API, expanding hop by hop
- AI follow-up Q&A on attack graphs, chain code and vulnerability details, with SSE streaming and persistent sessions

### Quick Scan Mode

- High-risk API marking via dual engines (AI + static rules) with a dedicated concurrency channel and dynamic pool resizing

### Component Vulnerability Audit

- Parses pom.xml, package.json, requirements.txt and more; detects vulnerable fastjson, Log4j, Shiro, Struts2, Django, Flask, lodash and known CVEs

### Vulnerability Validation

- Verdicts grounded in your business context: false positives marked red, confirmed issues marked green
- Incremental re-validation with automatic reuse of previous verdicts, reflected in both results and reports

### Resume & Dynamic Exclusion

- Stage-level snapshots restore full progress after a restart; rescans reuse existing results and skip completed work
- Exclude individual APIs mid-scan with immediate effect and persisted state

### Reporting

- One-click HTML report with dark/light themes; expand every API to inspect chain-code evidence
- Process analysis digests scan logs, making the whole audit traceable and reviewable

### LLM Support & Cost Observability

- Auto-fill for 11+ mainstream LLM providers (DeepSeek, Qwen, Zhipu GLM, Kimi, etc.) plus custom OpenAI-compatible endpoints with one-click connection tests
- Per-stage token usage and cost tracking, including peak/off-peak pricing visibility

## Supported Languages & Entrypoints

- **Languages**: Java, Go, Python, Node.js, PHP, C#, Ruby, Rust, C/C++
- **Entrypoints**: HTTP, RPC, WebSocket and MQ; mainstream frameworks extracted via static hints, custom frameworks handled by AI generalization

## Download & Installation

Get the installer for your platform from [Releases](https://github.com/Chanzi-keji/aisast/releases/latest):

| Platform | File |
| --- | --- |
| Windows 10/11 (64-bit) | `Chanzi-AiSAST-Setup-x.y.z.exe` |
| macOS Apple Silicon (M-series) | `Chanzi-AiSAST-x.y.z-arm64.dmg` |
| macOS Intel | `Chanzi-AiSAST-x.y.z-x64.dmg` |

- Mirror (Tencent Weiyun): https://share.weiyun.com/7n4mAIlW
- A bundled JRE and all runtime dependencies are included — **no environment setup required**

## Quick Start

1. Download and install the build for your platform
2. Launch the app, pick an LLM provider in Settings and enter your own API key (verify with "Test Connection")
3. Create a scan task from a local source directory or a Git repository URL
4. When the scan finishes, review findings, attack graphs and the HTML report

## Changelog

- **v2026.4.0**: Quick scan mode, precise line-number calibration, faster auditing, vulnerability-level AI Q&A, refined audit criteria, chain supplement & re-audit improvements, cost optimizations, UX and stability fixes
- See [Releases](https://github.com/Chanzi-keji/aisast/releases) for the full history

## Support

- Website: https://www.chanzikeji.com
- Issues: https://github.com/Chanzi-keji/aisast/issues

## Disclaimer

- This repository hosts installers only; **no source code is included**
- Copyright belongs to Beijing Chanzi Technology Co., Ltd.
- Use this tool only against targets you are authorized to test, and comply with applicable laws
