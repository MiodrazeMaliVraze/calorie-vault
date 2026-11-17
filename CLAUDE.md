# Vault — notes for Claude

Read-only calorie-vault dashboard (PWA), hosted on GitHub Pages from `main` (repo root). Same pattern and same Firebase project (`life-todo-334bf`, email/password Auth) as `life-todo`. This repo is **public**: never commit personal data (weights, meals, targets). Everything shown comes from Firestore.

## Files
- `index.html`: the whole app (CSS, classic script for UI and vault arithmetic, module script for Firebase).
- `sw.js`: offline cache. **Bump `CACHE` (`vault-vN`) on every change** and set `APP_VERSION` in `index.html` to the same N (shown at the bottom of the Vault tab).
- `manifest.json`, `icon-*.png`: home-screen install.

## Data (written by the private `calorie-vault-data` repo's sync job; the app never writes)
- `users/{uid}/vault/state`: `{ vault:{startDate,startWeight,goalWeight,kcalPerLb,size}, targets:{phase,note,training,rest}, weighIns:[{date,lb,note}], adjustments:[{date,kcal,reason}], updatedAt }`
- `users/{uid}/vaultDays/{YYYY-MM-DD}`: `{ date, dayType, status, target, intake, burn:{conservative,low,high,watch}, meals:[{time,name,detail,kcal,protein,fat,carbs}], training:[{time,name,detail,kcal}], note }`

## Vault rules
- Withdrawal for a day = `burn.conservative − intake.kcal`. Positive lowers the vault; negative (a surplus) raises it.
- `status`: `open` (in progress, shown but not counted), `provisional` (day over, estimate counted, awaiting final watch data), `closed` (final).
- `adjustments[].kcal` is a scale true-up: positive takes more out of the vault, negative puts calories back.
- Finish date shows from the first counted day, from the average of the last 14 counted days; it is labelled rough until 7 days are in.

## Mobile constraints (owner uses an iPhone home-screen icon)
- Zoom disabled; inputs 16px; status bar style `default`; body is `user-select: none` except inputs.
- Fonts are iOS system faces (DIN Condensed, Bodoni 72, Avenir Next) so nothing loads from the network.

## Workflow
- Local preview without Firebase: define `window.VAULT_DATA = { state, days }` in a script before the app scripts.
- Test at iPhone SE and iPhone 13 sizes with Playwright. Commit, push to `main`; Pages redeploys in 1–2 minutes.
