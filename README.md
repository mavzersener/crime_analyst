# Crime Analyst — Multilingual Scientific Video Studio

Local-first educational video production for crime statistics in **Turkish**, **English**, and **Mandarin Chinese**.

## Goals
- Explain statistical distributions applied to crime data from introductory to advanced levels.
- Generate scientifically reviewed 9:16 visual lessons, scripts, captions and narration in tr / en / zh-CN.
- Use only synthetic or explicitly licensed datasets in the public teaching examples.
- Build a **voice-consented, license-reviewed** speech synthesis workflow: never upload or clone a speaker's audio without permission.
- Target Windows 11 Pro and NVIDIA RTX 5090 (32 GB); favor reproducible Python / FFmpeg tooling.

## Status
**Project initialized / planning only.** No validated three-language video renderer or personal voice clone is installed yet.

## Start here
1. Read [Codex handover](CODEX_HANDOVER_CRIME_ANALYST.md).
2. Review [implementation plan](docs/IMPLEMENTATION_PLAN.md).
3. Review the [Poisson pilot scripts](content/episodes/poisson_intro/README.md).
4. In Codex, ask for a repository audit and architecture gap analysis **before writing application code**.

## Architecture
`content/episodes` scripts/storyboards; `data/synthetic` reproducible simulations; `stats` PMF/PDF and diagnostics; `visuals` animations; `voice` consented local voice adapters; `media` rendering and captions; `quality` verification; `tests` automated checks.

## Safety and rigor
Never interpret statistical clustering alone as proof of individual criminality or causation. Identify simulated figures. Verify model and asset licenses before monetized distribution; do not commit personal voice recordings, access tokens, or private incident-level data.

## Repository
https://github.com/mavzersener/crime_analyst
