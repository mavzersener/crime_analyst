# Initial Implementation Plan — pending Codex repository audit

**Phase 0: Audit.** Confirm local Windows versions, Python/CUDA compatibility, FFmpeg, fonts, existing web lab and license limitations. Deliver ARCHITECTURE_GAP_ANALYSIS.md and update this plan. No code in phase 0.

**Phase 1: Reproducible Poisson pilot (no voice cloning).** Deterministic Poisson(3) data; validated PMF; one 9:16 chart animation; language-specific scripts and timed SRT; fallback narrator track; MP4 x3. Unit/integration render and ffprobe checks.

**Phase 2: Voice evaluation.** Locally consented recordings; adapter for multilingual engine; test TR/EN/ZH pronunciations; commercial-use licensing table; human signoff and safe .gitignore defaults.

**Phase 3: Negative Binomial.** NB2 variance μ+αμ², matched-mean Poisson comparison, fit diagnostics, visuals and localized explanation.

**Phase 4: Batch workflow.** Episode schema, reusable themes, QC report, dry-run, local UI only if needed. No automated posting.

**Acceptance rule:** Each phase produces run commands, exact tests, warnings, and documented artifacts; only progress after verifiable acceptance.
