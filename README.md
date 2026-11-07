# Volume

A tiny web app that logs the daily volume you put into your main goals. One tap = one unit.
All data lives in this repository, in [`data/volume.json`](data/volume.json). Every tap becomes a commit.

Live app: **https://msh-max.github.io/Volume/**

## One-time setup

### 1. Turn on GitHub Pages
The workflow in `.github/workflows/pages.yml` publishes the site automatically. If the first run fails
with a Pages error, enable it once by hand: **Settings → Pages → Build and deployment → Source: GitHub Actions**,
then re-run the workflow from the **Actions** tab.

### 2. Create a token so the app can write to the repo
The page is static, so it needs a token to commit on your behalf.

1. Open https://github.com/settings/personal-access-tokens/new
2. Repository access: **Only select repositories** → `Volume`
3. Repository permissions → **Contents: Read and write**
4. Set the longest expiry you are comfortable with, then **Generate token** and copy it.

### 3. Save it to your iPhone home screen
1. Open the live URL in Safari.
2. Tap **Share → Add to Home Screen**. The app opens full-screen like a native app.
3. Open it from the home screen, tap the gear icon, paste the token, tap **Save & test**.

The token is stored only in that app's local storage on your phone. It never goes into the repo.
When the token expires, generate a new one and paste it again.

## How it works

- The page loads `data/volume.json` through the GitHub Contents API (so it is never stale) and renders
  today, this week, streaks, the last 12 weeks, a day-by-day heatmap, and a table.
- Each `+` tap is queued locally and committed about a second later with a message like
  `Log: +1 Workout (2026-11-07)`. If you are offline, taps wait and sync when you are back.
- Writes always re-read the latest file first and replay your taps on top, so two devices never
  overwrite each other.
- Goals (name, icon, color) and the week-start day are stored in the same file and can be edited in settings.

## Data format

```json
{
  "version": 1,
  "settings": { "weekStart": 1 },
  "goals": [{ "id": "workout", "name": "Workout", "icon": "💪", "color": 1 }],
  "log": { "2026-11-07": { "workout": 1, "work": 3 } }
}
```

`color` is a slot 1–8 in a colorblind-safe palette. `log` is keyed by local date, then goal id, then units.
