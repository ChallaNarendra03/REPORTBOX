# Report Box — SV Sudeep Car Wash

A single-page vehicle-log and business-reporting app, storing data in Supabase
and deployable as a fully static site (GitHub Pages).

## Quick start

1. **Restore the hero photo.** To keep this handoff safe, the hero image was
   replaced with `assets/carwash-hero.jpg` (a placeholder path). Drop your
   original photo at `assets/carwash-hero.jpg`, or edit the `<img src="...">`
   in `index.html` to point wherever you like (a base64 data URI also still
   works if you prefer no extra file).
2. **Set up Supabase** (free tier is enough):
   - Create a project at supabase.com.
   - Open the SQL editor, paste in `supabase_setup.sql`, run it.
   - Copy your Project URL and `anon` public key into `index.html`
     (`SUPABASE_URL` / `SUPABASE_ANON_KEY` near the top of the `<script>`).
3. **Push to GitHub**, enable Pages (Settings → Pages → Source: GitHub
   Actions). The included workflow (`.github/workflows/deploy.yml`) deploys
   automatically on every push to `main` — that's your CI/CD pipeline.
4. Default owner login: name `SUDEEP`, password `SUDEEP@0` — change it
   immediately from Settings once you're in.

## What's new in this pass

| Feature | Where |
|---|---|
| Dark / light theme toggle | Top bar + login screen (🌙/☀️ button), preference saved in `localStorage` |
| Search & filters | Vehicles page: search by name + filter by type. Reports page: date-range filter |
| PDF export | Reports page, Report Summary page (`jsPDF` + `jspdf-autotable`, runs entirely in-browser) |
| Excel export | Vehicles page, Reports page (`SheetJS`, runs entirely in-browser) |
| Audit log | Dashboard → **Audit Log** — records every vehicle add/delete, report add/delete, worker creation, access changes, password changes, exports, sign-ins (separate from the existing Login History) |
| KPI dashboard cards | Dashboard menu now shows vehicles logged, today's revenue, today's profit, this month's profit at a glance |
| Responsive layout | Extra breakpoints for phones; filter bars and stat grids stack on narrow screens |
| Optional email reports | Report Summary page → **Email Summary** button, powered by EmailJS (see below) |
| CI/CD pipeline | `.github/workflows/deploy.yml` — auto-deploys to GitHub Pages on push |

## Why some items from the wishlist aren't "built" the normal way

GitHub Pages serves static files only — there's no process that can run
your own server code, cron jobs, or send email on its own. Rather than
bolt on something that would silently fail once deployed, here's what each
item actually needs:

- **REST API Backend** — already satisfied. Supabase auto-generates a full
  REST API (PostgREST) over the `reportbox_data` table; that *is* your
  backend API, and it's what `index.html` already talks to.
- **Dockerization** — irrelevant to the Pages deployment itself (no
  container runs there), but a `Dockerfile` is included for local preview
  if you want production-like local testing.
- **Email Reports** — added via [EmailJS](https://www.emailjs.com) (free
  tier, sends email straight from the browser, no server needed). Fill in
  `EMAILJS_PUBLIC_KEY`, `EMAILJS_SERVICE_ID`, `EMAILJS_TEMPLATE_ID`,
  `EMAILJS_TO_ADDRESS` near the top of the script in `index.html`. Until
  those are filled in, the button shows a setup hint instead of failing
  silently.
- **Scheduled Reports** — true server-side scheduling needs something that
  runs independent of a browser tab. `.github/workflows/scheduled-report.yml.optional`
  is a template: GitHub Actions' free cron scheduler calling a Supabase
  Edge Function you write (which can then email/Slack a summary on its
  own). Rename the file to drop `.optional` once that function exists.
- **AI Summary / Anomaly Detection, Public Report Links, Multi-tenant
  Organizations** (2–3 star items) — left out of this pass as genuinely
  separate projects (an AI summary needs an LLM API call from somewhere,
  which again means a small serverless function; multi-tenant needs an
  auth + row-level-security redesign). Flagging them here as the natural
  next milestones rather than half-implementing them.

## Roles

- **Owner** (`SUDEEP`): full access + Settings (password, worker accounts).
- **Edit Access** workers: can add/edit/delete vehicles and reports.
- **View Access** workers: read-only across the app.

## File map

```
index.html                                  the whole app (one file, static)
supabase_setup.sql                          run once in Supabase SQL editor
.github/workflows/deploy.yml                CI/CD: auto-deploy to Pages
.github/workflows/scheduled-report.yml.optional   template for cron reports (opt-in)
Dockerfile                                  optional local preview only
assets/carwash-hero.jpg                     put your hero photo here
```
