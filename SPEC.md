# Image Restore — Product Spec (v1)

## 1. Summary
Image Restore is a web app that lets people upload old, damaged, blurry, noisy, scratched, faded, or low-resolution photos and get a cleaned-up, sharper version back. Users pick which restorations to apply, preview the result against the original with a before/after slider, and download the output. Nothing is uploaded to permanent storage unless the user explicitly opts in to keep local history.

## 2. Target users
Photographers, families restoring old prints, content creators, editors, and everyday users improving low-quality photos.

## 3. Core flow
1. User lands on the marketing homepage, understands what the tool does, clicks "Restore a Photo."
2. User drags/drops or picks a JPG/PNG/WEBP file (max 15MB).
3. App validates file type/size client- and server-side.
4. User selects restoration options (or uses "Auto Restore").
5. User clicks Restore → loading state → result appears.
6. User compares original vs. restored with a slider, downloads the result, or resets and tries again.
7. User may optionally save the result to local history (stored only in the browser, never on a server).

## 4. Restoration options (v1, shipped)
- **Auto Restore** — sensible default bundle (denoise + sharpen + color correction).
- **Denoise** — reduce grain/sensor noise.
- **Deblur / Sharpen** — recover edge detail.
- **Scratch & Dust Cleanup** — smooth out small print damage.
- **Color Correction** — fix faded color/contrast/white balance.
- **Face Detail Enhancement** — extra clarity pass (general-purpose, not face-detection-based in v1).
- **Colorize (old photo)** — AI-only feature; disabled with a clear tooltip unless a real AI provider key is configured.
- **Upscale 2x / 4x** — resize up using a high-quality resampling algorithm.

## 5. Honesty requirement (important)
v1 ships **without** a paid AI image-restoration API key. It uses a real, working **algorithmic image-processing pipeline** (sharpen/denoise/normalize/resize via `sharp`/libvips) — not a fake spinner that returns the same image. This is clearly labeled in the UI and README as "Standard Engine (algorithmic)." Colorization specifically requires real AI, so that option is visibly gated behind "Requires AI provider" rather than faked.

The service layer is provider-agnostic: swapping in Replicate / Stability AI / OpenAI later means writing one new class, not touching UI or API routes.

## 6. Privacy (v1)
- Uploaded images are processed in-memory on the server for a single request and are not written to disk or a database.
- "Recent Restores" history lives only in the browser (IndexedDB), is opt-in per result, and can be cleared at any time.

## 7. Pages (v1, shipped)
- **/** — Landing page
- **/restore** — Workspace (upload, options, preview, compare, download)
- **/history** — Local-only recent restores
- **/settings** — Engine status + how to configure a real AI provider

## 8. Stack (v1)
Next.js 15 (App Router) + TypeScript + Tailwind CSS + shadcn-style UI components, `sharp` for server-side image processing, Vitest for unit tests, IndexedDB for local history, next-themes for dark/light mode.

## 9. Requested v2 scope (not yet built — see ROADMAP.md)
Accounts, a real processing queue, server-side secure file storage, an admin dashboard, and payments/subscriptions were requested as a follow-on. These require introducing a database, auth, and a background job system — a meaningfully different architecture from the local-first v1 above. See `ROADMAP.md` for the proposed approach and open decisions before that work starts.
