# Image Restore

Restore old, blurry, scratched, faded, noisy, or low-resolution photos. Upload a
photo, pick the restorations you want, compare before/after, and download the
result. Nothing is stored on a server — history is local-only and opt-in.

## Stack

Next.js 15 (App Router) · TypeScript · Tailwind CSS · `sharp` for image
processing · IndexedDB (`idb-keyval`) for local history · Vitest for tests.

## Honesty note on AI

This build ships **without** a paid AI restoration API key. The default
"Standard Engine" performs real, classical image processing (denoise,
sharpen, color normalize, high-quality resize) — not AI. The UI labels this
clearly and never claims AI restoration is active unless a real provider is
configured. Colorization specifically requires AI and is always skipped (with
an explanation) on the Standard Engine, never faked.

The processing layer is provider-agnostic. To add a real AI backend:

1. Set the matching key in `.env.local` (copy from `.env.example`):
   `REPLICATE_API_TOKEN`, `STABILITY_API_KEY`, or `OPENAI_API_KEY`.
2. Implement the API call in `lib/image-processing/providers/<provider>.ts`
   (currently stubs that throw "not implemented").
3. No other code changes needed — `getRestorationService()` in
   `lib/image-processing/service.ts` already routes to it.

## Project structure

```
app/                  Pages & API routes (App Router)
  api/restore/         POST endpoint — validates, processes, returns result
  restore/             Workspace page
  history/             Local-only recent restores
  settings/             Engine status & provider setup instructions
components/
  ui/                  Hand-rolled shadcn-style primitives (Button, Card, Switch, ...)
  workspace/            Dropzone, options panel, compare slider, restore workspace
  landing/              Hero feature grid, before/after demo
  site/                 Navbar, footer
  theme/                Dark/light mode
lib/
  image-processing/     RestorationService interface + algorithmic engine + provider stubs
  validation/           File type/size validation (client + server)
  storage/              IndexedDB-backed local history
  utils/                 cn, id, formatting, client-side image helpers
types/                  Shared TypeScript types
tests/                  Vitest unit tests
```

## Run locally

```bash
npm install
npm run dev
```

Open http://localhost:3000.

## Quality checks

```bash
npm run lint     # ESLint
npx tsc --noEmit # Type check
npm test         # Vitest
npm run build    # Production build
```

All four pass clean in this repo as shipped.

## Privacy

- Uploaded images are processed in memory for the duration of a single
  request and are never written to disk or a database.
- "Save to History" is opt-in per photo and stores thumbnails only in the
  browser's IndexedDB. Clear it any time from the History page.

## Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit: Image Restore app"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

(`.env.local` is git-ignored — never commit real API keys.)

## Limits / known constraints (v1)

- No accounts, payments, or server-side image persistence.
- Face enhancement is a general clarity pass, not face-detection-based.
- Colorization and true AI super-resolution require connecting a provider.
