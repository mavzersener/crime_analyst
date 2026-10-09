# CODEX HANDOVER — Crime Analyst Multilingual AI Video Studio

## Objective
Build a local-first workflow that turns validated crime-statistics lessons into three synchronized educational Reels: Turkish (tr), English (en), and Mandarin (zh-CN), each with localized title, captions, and narrated audio. Render 1080×1920 H.264 MP4 / AAC when the environment supports it.

## Environment and constraints
Windows 11 Pro, NVIDIA RTX 5090 32 GB. Python, SciPy, Matplotlib or Manim, FFmpeg; Blender only if meaningful. CLI-first; defer elaborate UI. Preserve repository history and avoid breaking refactors.

## Your FIRST assignment — planning only
1. Inspect this repository and the actual machine toolchain. Never assume binaries, models or voice recordings exist.
2. Create `docs/ARCHITECTURE_GAP_ANALYSIS.md` addressing existing assets, reusable pieces, missing parts, licensing, Windows dependencies, data structures, API/CLI, tests, and MVP.
3. Refine `docs/IMPLEMENTATION_PLAN.md` into small additive phases with explicit acceptance checks.
4. Return findings **before implementing** unless user separately authorizes coding.

## Pilot specifications
- Episode 1: Poisson monthly events, λ=3, P(X=0)=exp(-3)≈0.049787; math verified using independent reference checks; visibly label all data as hypothetical.
- Episode 2: Poisson vs Negative Binomial with equal means, dispersion defined and validated.
- Three natural-language scripts; accurate jargon; Chinese language review; Unicode font checks.
- Single language-independent animation timeline with localized title overlays, audio and SRT.
- Synthetic data only, deterministic seeds and documented parameters.

## Voice clone rules
Use only user-owned voice provided with explicit consent. Never download personal audio from GitHub or commit reference WAV files. Keep speaker recordings in ignored local folders. Compare candidate models against primary-source **commercial license** and explicit TR/EN/ZH language support, benchmark pronunciation and identity, and require human listening approval. Provide placeholder/direct-recorded fallback. Do not treat XTTS-v2 weights as unconditionally commercially usable.

## Suggested modules
`content/episodes/`, `data/synthetic/`, `stats/`, `visuals/`, `voice/`, `localization/`, `media/`, `quality/`, `cli/`, `tests/`.

## Definition of done for pilot (later phases)
Reproducible CLI from scratch; each language has correctly synced MP4, subtitles, metadata, narration or explicitly labeled placeholder; ffprobe verifies output streams and dimensions; unit tests for formulas; documented licenses and unresolved blockers; no uploads or Instagram publication without explicit approval.

## Do not
Invent test results, assert deployment works without executing it, infer causation from crime concentration, upload private recordings, or implement an entire studio before architecture review.
