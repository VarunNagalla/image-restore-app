# Roadmap: v2 (accounts, queue, admin, payments)

v1 (shipped in this folder) is intentionally local-first: no accounts, no database, no
server-side storage of user photos. That's what makes the "we don't store your
images" privacy claim true today.

The newly requested scope — user accounts, a processing queue, secure server-side
file storage, an admin dashboard, and payments/subscriptions — is a different
architecture, not an incremental add-on. Building it well means making a few
real decisions first:

1. **Database & auth**: e.g. Postgres + Prisma/Drizzle, with auth via NextAuth/
   Clerk/Auth.js. Needed for accounts, history-per-user, and admin views.
2. **File storage**: e.g. S3-compatible object storage (S3, R2, Supabase
   Storage) with signed URLs and a retention/deletion policy — needed once
   photos persist server-side instead of staying in the browser.
3. **Processing queue**: a real job queue (e.g. BullMQ + Redis, or a managed
   queue) so restoration runs async, can be retried, and doesn't block the
   request — needed once jobs might call a slow external AI API.
4. **Admin dashboard**: scope to define — view users/jobs, retry failures,
   inspect usage. Needs its own auth/role check separate from regular users.
5. **Payments**: Stripe is the standard choice — needs plans/limits defined
   (free tier size, paid tier features) before wiring billing.
6. **Privacy implications**: once images are stored server-side for queueing/
   history, the privacy story changes from "never stored" to "stored, with
   retention/deletion controls." That messaging needs to be accurate.

None of this is implemented yet. It's a meaningful scope and cost increase
(hosting a DB, object storage, a queue worker, and Stripe) versus the current
zero-infra static-friendly app. Recommend confirming the decisions above
before writing the v2 code, so the result is production-correct rather than
a guess.
