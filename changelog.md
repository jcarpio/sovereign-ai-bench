# Changelog

All notable changes to this protocol are documented here.  
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [0.2-draft] — 2025-XX-XX

### Added
- Full operating system matrix (Windows 11 / Ubuntu 24.04 / macOS Sequoia)
- OS-specific backend documentation (DirectML, ROCm, Vulkan, Metal, CoreML)
- Version recording template (`versions/session-[CELL]-[DATE].md`)
- Model SHA-256 checksum verification requirement
- Session log JSON schema
- Repository structure definition
- RQ7: Linux vs Windows performance on HP Z2 G1A

### Changed
- Test cells now defined as `{hardware} × {OS}` combinations
- Software stack section expanded with per-OS version tracking requirements

---

## [0.1-draft] — 2025-XX-XX

### Added
- Initial protocol draft
- Hardware Under Test definitions (HP Z2 G1A, MacBook Pro M5)
- Research questions RQ1–RQ6
- Benchmark suite: LLM, ComfyUI image/video, quality, power
- TCO methodology
- Statistical methodology
- Conflict of interest statement
