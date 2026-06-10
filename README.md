# Alarm Clock

A fully client-side alarm clock web app — no server, no dependencies, no install required.

## Files

```
.
├── index.html     ← entire app (single file, no dependencies)
├── alarm.wav      ← default alarm sound (optional)
├── .nojekyll      ← tells GitHub Pages to skip Jekyll processing
├── _config.yml    ← site metadata for GitHub Pages
└── README.md
```

---

## Deploy to GitHub Pages

Follow the [GitHub Pages Quickstart](https://docs.github.com/en/pages/quickstart):

### 1. Create a repository

Go to [github.com/new](https://github.com/new) and create a new repository.

- To publish at `https://<username>.github.io/`, name it **`<username>.github.io`**
- To publish at `https://<username>.github.io/<repo>/`, use any other name
- Set visibility to **Public** (required for free GitHub Pages)
- Check **"Add a README file"** so the repo is not empty

### 2. Upload the files

On your new repository page click **Add file → Upload files** and upload:

| File | Required |
|---|---|
| `index.html` | ✅ Yes |
| `.nojekyll` | ✅ Yes (prevents Jekyll from interfering) |
| `alarm.wav` | Optional — default alarm sound |
| `_config.yml` | Optional — site title/description |

Commit the files to the `main` branch.

### 3. Enable GitHub Pages

1. In the repository, go to **Settings → Pages**
2. Under **Source**, select **Deploy from a branch**
3. Set branch to **`main`** and folder to **`/ (root)`**
4. Click **Save**

### 4. Visit your site

After up to 10 minutes your app will be live at:

```
https://<username>.github.io/              ← if repo is named username.github.io
https://<username>.github.io/<repo-name>/  ← for any other repo name
```

---

## Features

- **Multiple alarms** — manage any number of independent alarms
- **Repeat interval** — repeat every N hours and/or minutes
- **Loop mode** — repeats daily at the same time until dismissed
- **Custom alarm sound** — choose any audio file; falls back to `alarm.wav`, then a built-in beep
- **Live countdown** — time remaining and progress bar on each alarm
- **Wake Lock** — keeps the screen awake (Chrome, Edge, Safari 16.4+; requires HTTPS)
- **Export / Import** — save and restore alarm configurations as JSON
- **Responsive** — desktop two-column layout; mobile tab-based layout

---

## Wake Lock & HTTPS

The Wake Lock feature requires the page to be served over **HTTPS**. GitHub Pages provides HTTPS automatically — no extra configuration needed.

---

## Export / Import Format

```json
{
  "alarms": [
    {
      "id": 1718000000000,
      "targetTime": "07:30:00",
      "repeatMs": 3600000,
      "loop": false,
      "active": true,
      "soundName": null
    }
  ]
}
```

> Audio file data is not stored in the JSON. Re-select your custom sound file after importing.

---

## License

MIT — free to use, modify, and distribute.
