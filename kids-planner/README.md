# ⭐ Star Chart — a task planner for kids

Kids tick off their daily tasks, earn stars, and trade stars for rewards.
Parents manage kids, tasks and rewards behind a lock screen.

It is a single static page (`index.html`) with no build step and no dependencies.

## Run it locally

Open `index.html` in a browser. To test offline mode and "Add to Home Screen",
serve it over HTTP instead (service workers don't run from `file://`):

```sh
cd kids-planner && python3 -m http.server 8000   # then open http://localhost:8000
```

## Put it online (GitHub Pages)

The workflow `.github/workflows/deploy-star-chart.yml` publishes this folder
whenever `master` changes it.

1. Merge this branch into `master`.
2. On GitHub: **Settings → Pages → Build and deployment → Source: GitHub Actions**.
   (On a free GitHub plan the repository must be public. Only the app's code is
   published, never the family's data.)
3. Run the workflow from the **Actions** tab, or push a change.
4. The app will be at `https://<your-username>.github.io/<repo>/`.

## Install it on each device

- **iPhone / iPad (Safari):** Share → *Add to Home Screen*.
- **Android (Chrome):** menu ⋮ → *Install app* / *Add to Home screen*.
- **Computer (Chrome / Edge):** install icon in the address bar.

It then opens full-screen like a normal app and works offline.

## Where the data lives

Right now each device keeps its own data in the browser (`localStorage`).
Use **Parents → Download backup / Restore backup** to copy data between devices.
Shared family sync is the next step. See the plan below.

## Roadmap: shared family data

Goal: tick a task on the kids' tablet → it shows instantly on both parents' phones.

Proposed backend: [Supabase](https://supabase.com) (free tier: hosted Postgres,
login and live updates; nothing to run yourself).

- **Login:** one family account (email + password) signed in once on every
  device. The kid lock screen stays as it is.
- **Data model:** `kids`, `tasks`, `rewards`, `completions (kid, task, date)`
  and `star_ledger (rewards spent, bonus stars)`, each tagged with a
  `family_id`. Database row-level security makes sure a family can only read
  and write its own rows.
- **Sync:** each tick is its own row, so two devices ticking at the same time
  never overwrite each other. Devices subscribe to live changes. Offline ticks
  are queued and sent when the connection returns.
- **Migration:** the first sign-in uploads the current device's data, so nothing
  that's already been entered is lost.
