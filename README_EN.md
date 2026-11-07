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

**Chanzi AiSAST** is a desktop code security auditing tool built for real-world offensive/defensive scenarios. Point it at a source directory or a Git repository, and it automatically runs the full pipeline — *project analysis → global audit → applicability prescreening → API extraction → per-API audit*: for every endpoint it collects the complete call chain, audits each vulnerability category, validates findings against business context, and finally delivers **call-chain code evidence + visualized attack paths + a reviewable HTML report**.

Everything runs on your machine — code is only sent to the LLM provider you configure. A bundled JRE means zero setup.

> This repository only hosts installers and release notes. No source code is published here.

## Capability Overview

| Module | Capabilities |
| --- | --- |
| Scan engine | Five-stage audit pipeline, four-layer API extraction defense, quick scan mode |
| Vulnerability auditing | Per-API per-category audit, component CVE audit, vulnerability validation, unified audit criteria |
| Visualization | Attack path graphs (per-API + task-level aggregate), hop-by-hop chain code |
| AI collaboration | Follow-up Q&A on attack graphs, chain code and vulnerability details |
| Efficiency | Resume from checkpoint, dynamic exclusion, live SSE progress, task state recovery |
| Cost | Zero-token skipping via applicability prescreen, cached reuse, LLM usage & cost observability |
| Output | Dark/light HTML report, process analysis for post-hoc review |
| Deployment | Fully local execution, loopback-only communication, bundled JRE, Windows / macOS |

## Why Chanzi AiSAST

| Advantage | Description |
| --- | --- |
| **Deep auditing** | Multiple agents split by vulnerability category; every endpoint gets chain collection plus category-by-category auditing — not a one-shot rough scan of the whole repo |
| **Fewer false positives** | Applicability prescreening removes categories without attack preconditions; validation then judges each finding against business context |
| **Evidence-based** | Every finding expands into call-chain code and an attack path graph showing the full entry → transit → sink route |
| **Cost efficient** | Inapplicable categories are skipped at zero token cost; attack graphs are generated on demand and cached; prompts are laid out for LLM prefix caching |
| **Resume anytime** | Stage progress is checkpointed to disk in real time and fully restored after a restart; rescans reuse previous results |
| **Data stays yours** | The audit runs entirely on your machine; front and back end communicate only over 127.0.0.1 loopback, and code goes only to your own LLM provider |

## Five-Stage Scan Pipeline

| Stage | What it does | Why it matters |
| --- | --- | --- |
| **1. Project Analysis** | Identifies project structure, tech stack, dependency manifests, auth schemes and sensitive config; compresses them into shared global context | Every later stage shares the same understanding of the project — no repeated explanation per endpoint |
| **2. Global Audit** | Audits hardcoded secrets, security misconfiguration, CORS, JWT/Session and other project-wide risks — once per category | These are not endpoint-specific; auditing once avoids duplicate token spend |
| **3. Applicability Prescreen** | Static signatures plus AI judgement check whether each vulnerability category has an attack precondition in this project | Categories without preconditions are skipped entirely, cutting wasted calls and noise |
| **4. API Extraction** | Statically scans framework route declarations, AI cross-checks and completes the list, then verifies every symbol actually exists | An incomplete API list means everything downstream misses issues; symbol verification stops hallucinated endpoints |
| **5. Per-API Audit** | Collects upstream/downstream chains in parallel, audits category by category, and persists every verdict with evidence | This is the main body: each endpoint × category has its own verdict and evidence |

### Four-Layer Defense for API Extraction

Missing endpoints and hallucinated endpoints are the two classic failure modes. Chanzi AiSAST defends with four layers:

1. **Rule-scan layer** — covers mainstream declarative routes (Spring, gin, Flask, Django, Express, etc.), convention-based routing such as ThinkPHP, and PHP 8 attributes; generic convention scanning recognizes public methods under controller/handler directories
2. **AI extraction layer** — AI cross-checks and completes rule-scan hints, with a round budget that forces output on deadline plus a follow-up round as backup
3. **Deterministic fallback layer** — if AI extraction fails, rule-scan hints are converted directly into an endpoint list so the pipeline continues
4. **Batched extraction layer** — unfamiliar in-house frameworks are processed in batches by top-level directory to keep per-call context manageable

Only when all four layers fail is the task marked failed — tasks remain visible and retryable, never silently lost.

## Quick Scan Mode

Built for "get a first read on a large project" scenarios. You don't have to audit everything up front:

- **High-risk API marking** — dual engines (AI + static rules) label every endpoint, prioritizing those with missing authorization, dangerous function calls, or parameters flowing straight into sinks
- **Dedicated concurrency** — flagged high-risk endpoints get their own concurrency quota, so they are not queued behind ordinary endpoints
- **Dynamic pool resizing** — the concurrency pool scales with task size during the scan, so even huge projects produce a first batch of verdicts quickly

## Attack Path Graph — Visualized Exploit Route

Every audited endpoint has an attack propagation path, but prose is slow to grasp. Chanzi AiSAST draws it:

- **Per-API graph** — click "Attack Graph" on any API; AI reads all risk findings and the call chain of that API, fuses them into a structured attack data-flow graph, and the frontend renders it as hand-drawn SVG
- **Color semantics** — blue = attack entry (endpoint parameters), gray = data transit / validation nodes, red = dangerous sink (dangerous calls, sensitive operations)
- **Task-level aggregate graph** — after a scan you can generate an aggregate graph that groups all per-API graphs by vulnerability type, showing every exploit route from entry to sink top-down — the project's risk landscape in one picture
- **On-demand + cached** — graphs are generated when opened, lazy-loaded and cached locally; repeated views of the same API cost nothing, and the cache is keyed by vulnerability fingerprints so graphs regenerate only when findings change
- **Available during scans** — risky endpoints can generate graphs right from the progress page, no need to wait for completion
- **Three-layer line calibration** — node line references go through pre-audit calibration, identifier reverse lookup (±40-line window with uniqueness check) and read-time calibration of old caches, so clicking a node lands on the exact source line
- **Full-size in a new tab** — graphs and chain code open in new tabs with hover-to-zoom interaction, so large graphs stay readable

## Chain Code — Hop-by-Hop Call Chain

- The full upstream/downstream chain per API, collected by security sensitivity: dangerous sinks keep complete code, ordinary branches record call sites only — full fidelity where it matters, cost control elsewhere
- Expand any hop in the results page and jump straight to the corresponding source line
- Pairs with the attack graph: the graph shows the route, the code shows the details

## AI Follow-up Q&A — Ask Directly on Audit Results

Not a separate chat window — ask on the results themselves:

- **Three entrypoints** — the attack graph page, the chain code page, and every vulnerability detail
- **Streaming answers** — SSE-based streaming, rendered token by token
- **Persistent sessions** — sessions are kept per "task + API + page type" and saved to disk, resumable after a restart
- **On-demand depth** — the Q&A agent answers from audit material first and only reads sandboxed code or searches the web when material is insufficient
- **Separate accounting** — follow-up token usage is tracked independently from scan cost
- **Graceful failures** — model errors or timeouts return explicit messages instead of silently closing the stream

## Component Vulnerability Audit

- Automatically parses dependency manifests such as pom.xml, build.gradle, package.json and requirements.txt for key components and versions
- Detects known CVEs in cross-language high-risk components including fastjson, Log4j, Shiro, Struts2, Django, Flask and lodash
- Treated as a project-wide category: audited once per project and included in the HTML report

## Vulnerability Validation

Every finding from the scan can go through an extra business-context validation pass:

- **Contextual judgement** — reads the finding's context and business logic to decide whether an attack is actually feasible, not just whether a dangerous pattern exists
- **Visual verdicts** — false positives are marked red "False positive", confirmed issues green "Confirmed", and verdicts appear in both the results page and the report
- **Incremental re-validation** — re-runs only process undecided or changed items, reuse previous verdicts automatically, and can run in parallel with other scans
- **Independent validation model** — reuse the scan model by default, or configure a dedicated model (provider / key / model ID); each verdict records the model actually used for traceability

## Unified Audit Criteria

- **Categories**: SQL injection, XXE, authorization bypass, RCE, prompt injection, replay attacks, XSS, CORS, sensitive data exposure, race conditions, business logic and more
- **Consistent severity** — high/medium/low criteria are fixed in audit and validation rules; the same code location referenced by multiple endpoints always gets the same severity, avoiding drift between contexts
- **False-positive exemptions** — built-in exemption rules (e.g., full-field echo on admin endpoints is not a leak; no browser frontend means no CORS attack surface) to cut mechanical noise
- **Evidence-first** — verdicts must rest on visible code evidence; speculative reporting is disallowed

## Resume & Dynamic Exclusion

- **Stage snapshots** — progress is checkpointed in real time; after a crash or manual stop, a restart restores it exactly
- **Result reuse** — rescanning the same project reuses completed stages and endpoints and processes only what remains
- **Dynamic exclusion** — exclude endpoints mid-scan when they are out of scope or offline; exclusion takes effect immediately and persists, with a confirmation step to prevent misclicks

## LLM Support & Cost Observability

- **11+ providers with auto-fill** — pick DeepSeek, Qwen, Zhipu GLM, Kimi, etc. and the endpoint URL plus recommended models are filled in automatically
- **Custom endpoints** — any OpenAI-compatible API with custom base URL, model ID and parameters
- **Connection test** — verify an API key with one click before saving
- **Pre-flight check** — creating a task runs a minimal call first; missing key, 401/403 auth failure, 402 insufficient balance, 404 unknown model or 429 rate limiting are reported immediately instead of after a wasted run
- **Usage observability** — token usage and cost per stage and per session, drillable to detail
- **Peak/off-peak pricing** — built-in time-of-day pricing for mainstream providers makes cost estimates match real bills

## Reporting & Process Analysis

- **HTML report** — export a dark/light themed report with per-API findings and chain-code evidence, openable standalone
- **One-click reveal** — jump straight to the exported file in the system file manager
- **Process analysis** — an automated review of scan logs: what each stage did, how long it took, which categories were skipped, retries and failures — the audit is no longer a black box

## Supported Languages & Entrypoints

- **Languages**: Java, Go, Python, Node.js, PHP, C#, Ruby, Rust, C/C++
- **Mainstream frameworks**: Spring, gin, Flask, Django, Express, ThinkPHP and more via static hints; in-house frameworks handled by AI generalization
- **Entrypoints**: HTTP, RPC, WebSocket and MQ

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
3. Create a scan task from a local source directory or a Git repository URL; enable quick scan mode first for large projects
4. When the scan finishes, open the attack graph for the route, expand chain code for evidence, and ask AI follow-up questions on anything unclear
5. Run vulnerability validation when needed, then export the HTML report for archival

## Changelog

- **v2026.4.0**: Quick scan mode, precise line-number calibration, faster auditing (per-category long-lived agents, overlapped stages), vulnerability-level AI Q&A, refined audit criteria, chain supplement & re-audit improvements, cost optimizations, UX and stability fixes
- See [Releases](https://github.com/Chanzi-keji/aisast/releases) for the full history

## Support

- Website: https://www.chanzikeji.com
- Issues: https://github.com/Chanzi-keji/aisast/issues

## Disclaimer

- This repository hosts installers only; **no source code is included**
- Copyright belongs to Beijing Chanzi Technology Co., Ltd.
- Use this tool only against targets you are authorized to test, and comply with applicable laws
