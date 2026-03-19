# Local LLM Benchmark Protocol: Sub-€3,000 Workstations
### A Rigorous, Reproducible Performance Evaluation for Edge AI Deployment Across Windows, Linux, and macOS

**Version:** 0.2-draft  
**Status:** 🔓 Open for community review  
**License:** CC BY 4.0  
**Authors:** [Your Name] — contributions welcome via Pull Request  

> **Preregistration note:** This protocol is published *before* data collection begins, establishing methodology independently of results. This is a core principle of reproducible science.

---

## Table of Contents

1. [Motivation & Context](#1-motivation--context)
2. [Research Questions](#2-research-questions)
3. [Hardware Under Test (HUT)](#3-hardware-under-test-hut)
4. [Operating System Matrix](#4-operating-system-matrix)
5. [Software Stack & Version Registry](#5-software-stack--version-registry)
6. [Models Under Test](#6-models-under-test)
7. [Benchmark Suite](#7-benchmark-suite)
8. [Measurement Protocol](#8-measurement-protocol)
9. [Total Cost of Ownership (TCO)](#9-total-cost-of-ownership-tco)
10. [Statistical Methodology](#10-statistical-methodology)
11. [Repository Structure](#11-repository-structure)
12. [Conflict of Interest Statement](#12-conflict-of-interest-statement)
13. [Reproducibility Checklist](#13-reproducibility-checklist)
14. [Contributing & Peer Review](#14-contributing--peer-review)
15. [References](#15-references)

---

## 1. Motivation & Context

### 1.1 The Problem with Consumer Technology Reviews

The rapid proliferation of AI-capable personal computers has generated an enormous volume of content on platforms such as YouTube. While this democratises access to technical information, it also introduces well-documented structural incentive problems. Reviewers with large audiences frequently receive early hardware access, sponsored placements, or affiliate revenue arrangements that may — consciously or not — bias their evaluations toward positive outcomes for the products under review.

Beyond commercial incentives, common **methodological shortcomings** observed in the consumer review ecosystem include:

- **No controlled environment:** Tests run under different OS versions, driver versions, background processes, and thermal states, making comparisons meaningless.
- **Cherry-picked workloads:** Benchmarks chosen because the reviewed hardware performs particularly well on them.
- **Incomplete model size coverage:** Only small models tested, concealing severe performance degradation at larger parameter counts.
- **Absence of power measurement:** Tokens per second presented without watts consumed, making efficiency comparisons impossible.
- **Anecdotal quality assessment:** Subjective impressions ("it felt fast") instead of standardised quality benchmarks.
- **No operating system comparison:** A single OS tested, ignoring that the same hardware may perform very differently under Windows vs. Linux.
- **No reproducibility:** No published scripts, model hashes, configuration files, or driver version records.

This document describes a rigorous, transparent, and fully reproducible evaluation methodology designed to address **every one** of these shortcomings.

### 1.2 Why the OS Dimension Matters

The operating system is not a neutral layer. For local AI inference, the OS determines:

- Which acceleration backends are available (DirectML, CUDA, ROCm, Metal, Vulkan, OpenCL)
- Memory management and page allocation behaviour under sustained inference load
- Thermal management and CPU/GPU power state policies
- Driver maturity for NPU and integrated GPU acceleration
- Available tooling and ecosystem (e.g., ROCm is Linux-first; Metal is macOS-exclusive)

**A hardware comparison without OS comparison is incomplete.** This study treats the combination of `{Hardware} × {OS}` as the experimental unit.

### 1.3 Scope

This study evaluates the suitability of sub-€3,000 workstations for **local deployment of Large Language Models (LLMs) and generative AI image/video pipelines** across all three major operating systems. The target audiences are:

- Individuals and organisations considering private, offline AI deployments
- IT procurement decision-makers at SMEs and public institutions
- Researchers requiring reproducible baseline data for edge AI hardware

---

## 2. Research Questions

| ID | Research Question |
|----|-------------------|
| **RQ1** | What is the inference throughput (tokens/sec) achievable on each `{hardware} × {OS}` combination across model sizes from 1B to 70B+ parameters? |
| **RQ2** | How does the AMD XDNA NPU on the HP Z2 G1A compare to the Apple Neural Engine on M5, and does OS choice affect their relative performance? |
| **RQ3** | What is the energy cost per 1,000 tokens generated, and how does it vary by model size, hardware, and operating system? |
| **RQ4** | For a 3-year total cost of ownership, which `{hardware} × {OS}` combination offers the best performance-per-euro for local AI workloads? |
| **RQ5** | What is the maximum model size that each combination can run at acceptable interactive speed (≥ 10 tokens/sec prompt, ≥ 5 tokens/sec generation)? |
| **RQ6** | How do the platforms compare on ComfyUI image and video generation throughput and quality? |
| **RQ7** | Does Linux outperform Windows on the HP Z2 G1A for LLM inference, and if so, by what margin? |

---

## 3. Hardware Under Test (HUT)

### 3.1 Machine Inventory

> **⚠️ ACTION REQUIRED:** Replace all `[TBC]` values with exact figures before v1.0. Commands to extract system info are provided in [`/scripts/collect-system-info/`](#).

#### Machine A — HP Z2 Mini G1A

| Attribute | Value |
|-----------|-------|
| **Form factor** | Mini PC Workstation |
| **CPU model** | `[TBC — run: wmic cpu get name]` |
| **CPU cores / threads** | `[TBC]` |
| **NPU** | AMD XDNA — `[TBC — document TOPS rating]` |
| **RAM installed** | `[TBC] GB` |
| **RAM type / speed** | `[TBC — e.g., DDR5-5600]` |
| **iGPU** | AMD Radeon integrated — `[TBC model]` |
| **iGPU shared VRAM** | `[TBC] GB` |
| **Storage** | `[TBC model, capacity, interface]` |
| **OS A** | Windows 11 Pro — `[TBC build number]` |
| **OS B** | Ubuntu 24.04 LTS — `[TBC kernel version]` |
| **Purchase price (incl. VAT)** | `[TBC] €` |
| **Purchase date** | `[TBC]` |
| **BIOS version** | `[TBC]` |

**Commands to run on this machine:**
```bash
# Linux
uname -a
lscpu
lshw -short
sudo dmidecode -t memory
cat /proc/driver/amdgpu/*/info   # GPU info

# Windows (PowerShell)
Get-ComputerInfo
wmic cpu get name,numberofcores,numberoflogicalprocessors
wmic memorychip get capacity,speed,manufacturer
```
Save full output to: `hardware/machine-A-hp-z2/system-report-[OS]-[date].txt`

---

#### Machine B — MacBook Pro (M5)

| Attribute | Value |
|-----------|-------|
| **Form factor** | Laptop |
| **SoC** | Apple M5 |
| **CPU cores** | `[TBC — e.g., 10-core: 4P + 6E]` |
| **GPU cores** | `[TBC — e.g., 10-core]` |
| **Neural Engine** | Apple Neural Engine — 38 TOPS (TBC) |
| **Unified Memory** | 16 GB LPDDR5X |
| **Storage** | `[TBC model, capacity]` |
| **OS** | macOS `[TBC — e.g., Sequoia 15.3.2]` |
| **Purchase price (incl. VAT)** | `[TBC] €` |
| **Purchase date** | `[TBC]` |

**Command to run on this machine:**
```bash
system_profiler SPHardwareDataType SPMemoryDataType SPStorageDataType
sw_vers
```
Save full output to: `hardware/machine-B-macbook-m5/system-report-macos-[date].txt`

### 3.2 Important Architectural Note

The HP Z2 G1A and MacBook Pro M5 represent **two fundamentally different design philosophies** for AI acceleration:

| Aspect | HP Z2 G1A (AMD) | MacBook Pro (Apple M5) |
|--------|----------------|----------------------|
| **AI acceleration** | Dedicated NPU (XDNA) separate from CPU/GPU | Neural Engine integrated in unified SoC |
| **Memory architecture** | CPU RAM + iGPU shares system RAM | All components share one unified pool |
| **Memory bandwidth** | CPU and GPU compete for bandwidth | Single high-bandwidth unified pool |
| **LLM effective VRAM** | Limited by iGPU shared memory config | Full 16 GB available to all compute units |
| **OS flexibility** | Windows + Linux possible | macOS only |
| **Software ecosystem** | DirectML (Win), ROCm (Linux), Vulkan | Metal, CoreML |

This architectural difference means that **comparing raw tokens/sec is necessary but not sufficient** — the study must also document *which compute unit* (CPU, iGPU, or NPU) handled each inference workload on each OS.

---

## 4. Operating System Matrix

Each test will be executed on every applicable `{hardware} × {OS}` combination, yielding the following test cells:

| Cell ID | Hardware | OS | Inference Backend | Status |
|---------|----------|----|-------------------|--------|
| **A-WIN** | HP Z2 G1A | Windows 11 Pro | DirectML / CPU (llama.cpp) | ⬜ Pending |
| **A-LIN** | HP Z2 G1A | Ubuntu 24.04 LTS | ROCm / Vulkan / CPU (llama.cpp) | ⬜ Pending |
| **B-MAC** | MacBook Pro M5 | macOS Sequoia | Metal (llama.cpp) / CoreML | ⬜ Pending |

> macOS is not available on the HP Z2 G1A (Apple restricts macOS to Apple hardware), so cell `A-MAC` does not exist. Windows is not tested on the MacBook (Boot Camp is discontinued on Apple Silicon), so cell `B-WIN` does not exist.

### 4.1 OS-Specific Backend Notes

#### Windows 11 (Cell A-WIN)
- Primary backend: **llama.cpp with DirectML** or **Vulkan** for iGPU offload
- NPU acceleration: Document whether Ollama / llama.cpp can utilise the XDNA NPU via DirectML or the Windows AI Platform (ONNX Runtime)
- AMD driver version: Document Adrenalin / ROCm-for-Windows version

#### Ubuntu 24.04 LTS (Cell A-LIN)
- Primary backend: **llama.cpp with ROCm** (AMD GPU compute) or **Vulkan**
- NPU: Document XDNA Linux driver (`amdxdna` kernel module) status and whether inference engines can target it
- Kernel and driver versions: Must be recorded precisely

#### macOS Sequoia (Cell B-MAC)
- Primary backend: **llama.cpp with Metal** — the most mature Apple Silicon backend
- Neural Engine: Accessible via CoreML; document whether any tested framework routes to ANE
- Thermal and power: macOS is tightly optimised for M-series; document any throttling behaviour

---

## 5. Software Stack & Version Registry

> **⚠️ CRITICAL:** Every piece of software used in this study MUST have its exact version recorded. Version differences — even minor ones — can produce measurably different performance results. This is one of the most common sources of irreproducibility in hardware benchmarks.

### 5.1 Version Recording Template

For each test session, populate the file `versions/session-[CELL_ID]-[DATE].md` using this template:

```markdown
## Version Record — Cell: [A-WIN / A-LIN / B-MAC] — Date: [YYYY-MM-DD]

### Operating System
- OS Name:
- OS Version / Build:
  - Windows: Settings > System > About > OS Build
  - Linux: `uname -r` + `cat /etc/os-release`
  - macOS: `sw_vers`
- Kernel (Linux only): `uname -a`

### Hardware Drivers
- AMD GPU Driver (Windows): Device Manager > Display Adapters > Driver Version
- AMD ROCm version (Linux): `rocminfo | grep "ROCm"` or `apt show rocm-libs`
- AMD XDNA NPU driver (Linux): `dkms status` or `modinfo amdxdna`
- macOS Metal version: included in system_profiler output

### Inference Engines
- Ollama version: `ollama --version`
- llama.cpp git commit hash: `git rev-parse HEAD` (in llama.cpp directory)
- llama.cpp build flags: document exact cmake / make flags used
- LM Studio version (if used): Help > About

### Python Environment
- Python version: `python --version`
- pip version: `pip --version`
- Key packages (run `pip freeze > requirements-[CELL]-[DATE].txt` and commit file):
  - torch version:
  - transformers version:
  - accelerate version:
  - bitsandbytes version:
  - onnxruntime version (Windows):

### ComfyUI
- ComfyUI git commit hash: `git rev-parse HEAD`
- ComfyUI-Manager version:
- Custom nodes (list name + git commit hash for each):
  - ComfyUI-VideoHelperSuite:
  - [other nodes]:
- PyTorch version used by ComfyUI:
- xFormers version (if used):

### Benchmarking Scripts
- This repository git commit hash: `git rev-parse HEAD`
```

### 5.2 Model File Integrity

All model files must be verified with checksums to ensure identical files are used across platforms:

```bash
# Generate SHA-256 hash for each model file
sha256sum models/*.gguf > models/SHA256SUMS.txt

# Verify before each test session
sha256sum -c models/SHA256SUMS.txt
```

---

## 6. Models Under Test

All LLM tests use **GGUF format** files downloaded from HuggingFace. The same file (verified by SHA-256) is used on all platforms to eliminate model variation as a confound.

### 6.1 LLM Model Matrix

| Model | Size | Quantisation | File size (approx.) | Source | Rationale |
|-------|------|-------------|-------------------|--------|-----------|
| **Phi-3.5 Mini** | 3.8B | Q4_K_M | ~2.4 GB | Microsoft / HF | Small: fits in any RAM config |
| **Gemma 2 2B** | 2B | Q4_K_M | ~1.6 GB | Google / HF | Small: energy efficiency baseline |
| **Llama 3.1 8B** | 8B | Q4_K_M | ~4.9 GB | Meta / HF | Medium: industry standard benchmark |
| **Mistral 7B v0.3** | 7B | Q4_K_M | ~4.4 GB | Mistral AI / HF | Medium: strong reasoning reference |
| **Llama 3.1 8B** | 8B | Q8_0 | ~8.5 GB | Meta / HF | Medium: quality vs. speed tradeoff |
| **Llama 3 13B** | 13B | Q4_K_M | ~8.0 GB | Meta / HF | Large: RAM pressure test |
| **Mistral 22B** | 22B | Q4_K_M | ~13 GB | Mistral AI / HF | Large: near memory limit of M5 16 GB |
| **Llama 3.3 70B** | 70B | Q4_K_M | ~43 GB | Meta / HF | Very large: only feasible on HP Z2 |
| **Llama 3.3 70B** | 70B | Q2_K | ~26 GB | Meta / HF | Very large: aggressive quantisation |

> **Note on 70B models:** The MacBook Pro M5 with 16 GB unified memory **cannot load a 70B Q4_K_M model** (requires ~43 GB). This is an empirical finding, not a limitation of the study design. The study will document the maximum runnable model size for each platform.

### 6.2 Image Generation Models (ComfyUI)

| Model | Type | Size | Notes |
|-------|------|------|-------|
| **Stable Diffusion 1.5** | Image | ~2 GB | Baseline; well-optimised for all platforms |
| **SDXL Base 1.0** | Image | ~7 GB | Higher quality; GPU memory pressure test |
| **FLUX.1 [schnell]** | Image | ~12 GB | State-of-art; demanding on memory |
| **Wan2.1 (or equivalent)** | Video | ~14 GB | Short video generation |

### 6.3 Standard Prompts for Image Generation

To ensure comparability, all image generation tests use a fixed set of prompts. These are defined in `/benchmarks/comfyui/prompts.json` and versioned with the repository.

---

## 7. Benchmark Suite

### 7.1 LLM Inference Performance

#### Metric Definitions

| Metric | Definition | Unit | How Measured |
|--------|-----------|------|-------------|
| **Prompt processing speed** | Tokens ingested per second during prompt evaluation | tok/s | Ollama logs / llama.cpp `-p` output |
| **Generation speed** | New tokens generated per second | tok/s | Ollama logs / llama.cpp output |
| **Time to first token (TTFT)** | Latency from prompt submission to first output token | ms | Script timer |
| **Memory usage** | RAM / VRAM occupied during inference | GB | OS tools (see §8) |
| **Context length tested** | Number of tokens in prompt + generation | tokens | Fixed per test run |

#### Test Procedure — LLM

For each `{model} × {cell}` combination:

1. **Cold start:** Restart inference engine. Wait 60 seconds.
2. **Warm-up run:** Execute 1 inference with the standard prompt. Discard results.
3. **Measurement runs:** Execute 5 consecutive inferences. Record all 5.
4. **Report:** Mean ± standard deviation of the 5 runs.

**Standard prompt (fixed for all LLM tests):**
```
Explain the process of photosynthesis in detail, including the light-dependent 
and light-independent reactions, the role of chlorophyll, and how glucose is 
produced from CO2 and water. Then summarise in three bullet points.
```
- Prompt length: ~50 tokens
- Target generation length: 512 tokens (set via `--predict 512` or equivalent)

#### Batch of Prompts for Stress Testing

A secondary 20-prompt batch (defined in `/benchmarks/llm/stress-prompts.json`) covering: reasoning, code generation, multilingual text, and long-context summarisation. This tests sustained throughput, not just peak performance.

---

### 7.2 ComfyUI — Image Generation

#### Metrics

| Metric | Unit | Notes |
|--------|------|-------|
| **Iterations per second** | it/s | Reported by ComfyUI |
| **Total generation time** | seconds | Wall clock, script-measured |
| **GPU/MPS memory peak** | GB | Measured during generation |
| **Output quality (FID proxy)** | score | See §7.4 |

#### Test Parameters (Fixed)

| Parameter | Value |
|-----------|-------|
| Resolution | 512×512 (SD1.5), 1024×1024 (SDXL, FLUX) |
| Steps | 20 (for SD1.5, SDXL), 4 (for FLUX schnell) |
| CFG Scale | 7.0 |
| Sampler | DPM++ 2M Karras |
| Seed | Fixed: `42` for reproducibility |
| Batch size | 1 |

#### Standard Workflows

ComfyUI workflows (`.json` files) are versioned in `/benchmarks/comfyui/workflows/` and loaded directly — no manual configuration during testing.

---

### 7.3 ComfyUI — Video Generation

| Parameter | Value |
|-----------|-------|
| Resolution | 512×320 |
| Frame count | 16 frames |
| Steps | 20 |
| Seed | Fixed: `42` |

Metrics: total generation time (seconds), peak memory (GB), frames per second generated.

---

### 7.4 Reasoning & Output Quality

Raw speed is meaningless without assessing output quality. Quality degradation is a known side effect of aggressive quantisation.

#### Benchmarks Used

| Benchmark | What it measures | Dataset | Script |
|-----------|-----------------|---------|--------|
| **HellaSwag** (subset, n=200) | Commonsense reasoning | HuggingFace | `/benchmarks/quality/hellaswag.py` |
| **MMLU** (subset, 5 subjects, n=100) | Multi-domain knowledge | HuggingFace | `/benchmarks/quality/mmlu.py` |
| **HumanEval** (subset, n=50) | Code generation | OpenAI HumanEval | `/benchmarks/quality/humaneval.py` |
| **Custom reasoning set** (n=30) | Logical reasoning in Spanish & English | This repo | `/benchmarks/quality/custom.json` |

These benchmarks produce **accuracy scores (0–100%)**, enabling quality-speed tradeoff analysis: is a 2× faster quantised model worth a 5% accuracy drop?

---

### 7.5 Power Consumption & Efficiency

#### Measurement Equipment

| Platform | Method | Tool |
|----------|--------|------|
| HP Z2 G1A (both OS) | Smart power meter on mains socket | Tapo P110 / similar (document exact model) |
| MacBook Pro M5 | Software (built-in, accurate on Apple Silicon) | `powermetrics --samplers cpu_power,gpu_power -i 1000` |

#### Power Metrics

| Metric | Definition |
|--------|-----------|
| **Idle power** | Watts at desktop, no load, 60s average |
| **Inference power** | Watts averaged over a full 512-token generation run |
| **Peak power** | Maximum instantaneous watts during inference |
| **J/token** | Joules per token = (inference power × generation time) / tokens generated |
| **tokens/watt** | Inverse efficiency metric |

---

## 8. Measurement Protocol

### 8.1 Environment Control

The following conditions must be identical across all test sessions:

| Variable | Required Condition |
|----------|--------------------|
| **Room temperature** | 20–23 °C (document with thermometer) |
| **Machine thermal state** | Machine idle for ≥ 30 minutes before testing |
| **Background processes** | All non-essential applications closed; document what is running |
| **Network** | Ethernet connected but no active traffic (or Wi-Fi disabled) |
| **Power mode** | "High Performance" (Windows), `performance` governor (Linux), "Never sleep" (macOS) |
| **Display** | External display disconnected on HP Z2 during tests |
| **Battery** | MacBook plugged into mains power for all tests |
| **Antivirus** | Document status (enabled/disabled) and version |

### 8.2 Session Log Template

Every test session must produce a log file at `logs/session-[CELL]-[MODEL]-[DATE]-[TIME].json`:

```json
{
  "session_id": "A-LIN-llama3-8b-q4-2025-01-15-14h30",
  "cell": "A-LIN",
  "model": "llama-3.1-8b-q4_k_m.gguf",
  "model_sha256": "abc123...",
  "date": "2025-01-15",
  "time_start": "14:30:00",
  "time_end": "14:52:00",
  "room_temp_celsius": 21,
  "runs": [
    {
      "run_id": 1,
      "prompt_tokens": 52,
      "generation_tokens": 512,
      "prompt_speed_tok_s": 0.0,
      "generation_speed_tok_s": 0.0,
      "ttft_ms": 0,
      "wall_time_s": 0.0,
      "peak_power_w": 0.0,
      "avg_power_w": 0.0,
      "peak_ram_gb": 0.0
    }
  ],
  "notes": ""
}
```

A Python script (`/scripts/run-benchmark.py`) will automate data collection and write these files.

### 8.3 Data Collection Scripts

| Script | Purpose | Platform |
|--------|---------|----------|
| `scripts/collect-system-info.sh` | Dump all hardware/driver/version info | Linux / macOS |
| `scripts/collect-system-info.ps1` | Same for Windows | Windows |
| `scripts/run-benchmark.py` | Execute LLM benchmark runs, log results | All |
| `scripts/run-comfyui-benchmark.py` | Trigger ComfyUI workflows, log results | All |
| `scripts/measure-power-linux.sh` | Poll `/sys/class/powercap/` RAPL sensors | Linux |
| `scripts/aggregate-results.py` | Combine all JSON logs into summary CSV | All |
| `scripts/plot-results.py` | Generate charts for the paper | All |

---

## 9. Total Cost of Ownership (TCO)

TCO is calculated over a **3-year horizon** and includes:

| Cost Component | HP Z2 G1A | MacBook Pro M5 |
|---------------|-----------|----------------|
| Purchase price | [TBC] € | [TBC] € |
| Electricity (€/kWh × daily hours × days) | Calculated from measured watts | Calculated from measured watts |
| Assumed electricity rate | 0.25 €/kWh (Spain, 2025) | same |
| Daily usage assumption | 4 hours active inference | same |
| Warranty / support | [TBC] | [TBC] |
| **Total 3-year TCO** | **Calculated** | **Calculated** |
| **TCO per 1M tokens** | **Calculated** | **Calculated** |

> Electricity rate source will be cited. Users can adjust the rate in `/scripts/tco-calculator.py`.

---

## 10. Statistical Methodology

- **Central tendency:** Arithmetic mean of 5 runs per `{model} × {cell}` combination
- **Dispersion:** Standard deviation and coefficient of variation (CV%)
- **Outlier policy:** If any run deviates > 2σ from the mean, it is flagged, the cause investigated, and the run repeated. Outlier runs are **not silently discarded** — they are kept in the raw data with a flag.
- **Significance testing:** Where two cells are compared on the same model, a two-tailed paired t-test (α = 0.05) is applied.
- **Reporting format:** All results reported as `mean ± SD` with n=5. Charts show individual run points overlaid on bars.
- **Raw data:** All raw JSON logs are committed to the repository. Aggregated CSVs are derived files and regenerated by script.

---

## 11. Repository Structure

```
local-llm-benchmark/
├── README.md                          ← Project overview and quick start
├── PROTOCOL.md                        ← This document
├── CHANGELOG.md                       ← Version history of this protocol
│
├── hardware/
│   ├── machine-A-hp-z2/
│   │   ├── system-report-windows-[date].txt
│   │   └── system-report-linux-[date].txt
│   └── machine-B-macbook-m5/
│       └── system-report-macos-[date].txt
│
├── versions/
│   ├── session-A-WIN-[date].md        ← Full version log per session
│   ├── session-A-LIN-[date].md
│   └── session-B-MAC-[date].md
│
├── models/
│   └── SHA256SUMS.txt                 ← Model file checksums
│
├── benchmarks/
│   ├── llm/
│   │   ├── standard-prompt.txt
│   │   └── stress-prompts.json
│   ├── comfyui/
│   │   ├── prompts.json
│   │   └── workflows/
│   │       ├── sd15-baseline.json
│   │       ├── sdxl-base.json
│   │       ├── flux-schnell.json
│   │       └── wan-video.json
│   └── quality/
│       ├── hellaswag.py
│       ├── mmlu.py
│       ├── humaneval.py
│       └── custom.json
│
├── scripts/
│   ├── collect-system-info.sh
│   ├── collect-system-info.ps1
│   ├── run-benchmark.py
│   ├── run-comfyui-benchmark.py
│   ├── measure-power-linux.sh
│   ├── aggregate-results.py
│   ├── plot-results.py
│   └── tco-calculator.py
│
├── logs/                              ← Raw JSON logs (auto-generated, committed)
│   └── session-[CELL]-[MODEL]-[DATE]-[TIME].json
│
├── results/
│   ├── raw/                           ← Aggregated CSVs (derived from logs)
│   └── figures/                       ← Charts (derived, regenerated by script)
│
└── paper/
    ├── whitepaper-draft.md            ← The final publication document
    └── references.bib
```

---

## 12. Conflict of Interest Statement

The authors declare the following:

- No hardware, software, or services used in this study were received free of charge, on loan, or at discount from any manufacturer or vendor.
- No author has received or expects to receive financial compensation from any entity with a commercial interest in the results of this study.
- All hardware was purchased at retail price. Purchase receipts are available upon request.
- This study was not commissioned by any third party.

> If any of the above changes (e.g., a manufacturer offers to provide hardware for testing), this statement will be updated immediately and the change logged in `CHANGELOG.md`.

---

## 13. Reproducibility Checklist

Before publishing any results, verify:

- [ ] All `[TBC]` fields in §3 have been filled with exact values
- [ ] Version record files exist for every test session (`versions/`)
- [ ] All model SHA-256 hashes are recorded in `models/SHA256SUMS.txt`
- [ ] Raw JSON logs are committed for every reported test run
- [ ] Results can be fully regenerated from raw logs by running `scripts/aggregate-results.py`
- [ ] All scripts run without modification on all three OS environments
- [ ] The standard prompt is identical (character-for-character) across all tests
- [ ] ComfyUI workflows (`.json`) are versioned and identical across platforms
- [ ] Power measurement equipment model and calibration date are documented
- [ ] Room temperature was recorded for every session
- [ ] Conflict of interest statement is current and accurate

---

## 14. Contributing & Peer Review

This protocol is published openly to invite scrutiny **before** results are collected. Contributions are welcome:

- **Methodology critique:** Open a GitHub Issue with label `methodology`
- **Additional hardware proposals:** Open an Issue with label `hardware`
- **Script contributions:** Open a Pull Request against `main`
- **Result replication:** If you have access to similar hardware, replication attempts are welcomed — open an Issue with label `replication`

All substantive changes to the protocol after data collection begins will be documented in `CHANGELOG.md` with a clear explanation of why the change was made.

---

## 15. References

> *To be populated. Suggested references:*

- Dettmers, T. et al. (2022). *LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale.* NeurIPS 2022.
- Frantar, E. et al. (2022). *GPTQ: Accurate Post-Training Quantization for Generative Pre-trained Transformers.* arXiv:2210.17323.
- llama.cpp project. https://github.com/ggerganov/llama.cpp
- Ollama project. https://github.com/ollama/ollama
- ComfyUI project. https://github.com/comfyanonymous/ComfyUI
- MLPerf Inference Benchmark Suite. https://mlcommons.org/en/inference-edge/

---

*This document is versioned. See `CHANGELOG.md` for full history of changes.*  
*Last updated: [DATE] — Protocol v0.2-draft*
