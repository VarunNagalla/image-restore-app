# Implementation Plan (v1 — shipped)

1. **Scaffold**: Next.js (TS, Tailwind, App Router, ESLint), `sharp`, `next-themes`, `idb-keyval`, `vitest`.
2. **lib/validation**: file type/size checks, shared between client and API route.
3. **lib/image-processing**: `RestorationService` interface, `RestorationOptions`/`RestorationResult` types, `AlgorithmicRestorationService` (sharp-based, implements every option honestly), provider stub files (`replicate.ts`, `stability.ts`) for future use, `getRestorationService()` factory that reads env vars.
4. **lib/storage**: IndexedDB wrapper for opt-in local history.
5. **app/api/restore/route.ts**: parses multipart form, validates, calls service, returns base64 result + processing meta + warnings.
6. **UI primitives**: button, card, switch, tooltip, badge (hand-rolled, Tailwind, no external registry fetch).
7. **Landing page**: hero, how-it-works, feature grid, before/after demo, privacy callout.
8. **Workspace page**: dropzone + file picker, options panel, compare slider, loading/error states, download, reset, "save to history."
9. **History page**: IndexedDB-backed grid, delete one / clear all.
10. **Settings page**: active engine + env var instructions.
11. **Tests**: Vitest for validation + AlgorithmicRestorationService.
12. **Quality pass**: lint, build, test — all passing.
13. **Ship**: source copied into this project folder, README + `.env.example` included.

Status: all 13 steps complete and verified (lint clean, `tsc --noEmit` clean, 14/14 tests passing, production build succeeds, API smoke-tested end to end).

For the requested v2 scope (accounts, processing queue, secure server storage, admin dashboard, payments), see `ROADMAP.md` — that work has open architectural decisions that should be confirmed before implementation starts.
