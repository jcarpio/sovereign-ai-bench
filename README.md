# 🖥️ Local LLM Benchmark: Sub-€3,000 Workstations

> **A rigorous, independent, and fully reproducible study comparing local AI performance on Windows, Linux, and macOS.**

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Status: Protocol Open for Review](https://img.shields.io/badge/Status-Protocol%20Open%20for%20Review-yellow)]()
[![Conflict of Interest: None](https://img.shields.io/badge/Conflict%20of%20Interest-None-green)]()

---

## Why This Project Exists

Most hardware reviews of AI-capable PCs suffer from the same problems: uncontrolled test conditions, cherry-picked benchmarks, no power measurements, and undisclosed commercial relationships with manufacturers. This project does things differently.

**We published the full methodology *before* collecting any data.** You can read, critique, and improve it before a single number is recorded.

## Hardware Tested

| Machine | OS | Role |
|---------|-----|------|
| HP Z2 Mini G1A (AMD + NPU) | Windows 11 + Ubuntu 24.04 | Primary — AMD ecosystem |
| MacBook Pro M5 (16 GB) | macOS Sequoia | Primary — Apple Silicon |

## What We Measure

- 🚀 **LLM inference speed** (tokens/sec) — models from 1B to 70B parameters
- ⚡ **Energy efficiency** (joules per token, tokens/watt)
- 🎨 **Image generation** via ComfyUI (SD1.5, SDXL, FLUX)
- 🎬 **Video generation** via ComfyUI
- 🧠 **Output quality** (HellaSwag, MMLU, HumanEval)
- 💶 **3-year Total Cost of Ownership**

## Project Status

| Phase | Status |
|-------|--------|
| Protocol design | ✅ Complete (v0.2-draft) |
| Community review period | 🟡 Open |
| Hardware info collection | ⬜ Pending |
| Data collection | ⬜ Pending |
| Analysis | ⬜ Pending |
| Publication | ⬜ Pending |

## Start Here

👉 Read the full methodology: [`PROTOCOL.md`](./PROTOCOL.md)  
👉 Critique or contribute: [Open an Issue](../../issues)

## Conflict of Interest

All hardware purchased at retail. No sponsorships. No affiliate links. No free products.  
See full statement in [`PROTOCOL.md §12`](./PROTOCOL.md#12-conflict-of-interest-statement).
