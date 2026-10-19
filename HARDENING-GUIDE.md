# Editor — Hardened Deployment Guide

This is a hardened fork of the Writing Tool PWA. The app is identical in features but the **fingerprint it leaves on a monitored work laptop** has been dramatically reduced. This guide explains what changed, why, and how to deploy + use it safely.

---

## Quick Start (5 minutes)

1. **Create a fresh GitHub account** (optional but recommended — see "Identity" below). Use a non-identifiable handle like `notes-sync` or `editor-user`.
2. **Create a new GitHub repo** with a neutral name. Good names: `notes`, `editor`, `workspace`, `scratch`. Bad names: `my-novel`, `writing-tool`, `book-project`.
3. Set the repo to **Private** if your plan allows GitHub Pages from private repos (Pro+). Otherwise Public is fine — the content is just the app code, not your novel.
4. **Upload all files in this folder** to the repo root: `index.html`, `manifest.webmanifest`, `sw.js`, `LICENSE`, `CHANGELOG.md`, `README.md`.
5. **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**.
6. Wait 1–2 minutes. Open `https://YOUR-NAME.github.io/REPO-NAME/`.
7. **Install as a PWA** (Chrome/Edge address bar → install icon, or `⋮ → Install app`). This makes it show up as a standalone app with the name "Editor" rather than a browser tab.

---

## What Was Hardened (and why each change matters)

### 1. Static window title (was the biggest leak)

**Before**: `pageTitle()` updated `document.title` to `"Chapter Name · Writing Tool"` every time you switched chapters or typed in a chapter title.

**After**: `document.title` is always `"Editor"`, regardless of which chapter you're in.

**Why this matters**: ActivTrak logs window titles. With the original code, every chapter name you wrote ("Chapter 7: The Murder of King Aurelias") would appear in the IT admin's Activity Log as a separate entry. Now the window title is constant and uninformative.

### 2. Sync timer slowed from 4s to 5min

**Before**: `gsSoon()` triggered a sync to GitHub 4 seconds after you stopped typing. Plus a periodic sync every 90 seconds when the tab was visible.

**After**: Sync fires 5 minutes after you stop typing. The 90-second periodic sync is removed entirely. Visibility-change sync (when you tab back to the app) and online-event sync are kept because they're useful for cross-device workflow.

**Why this matters**: A network request to `api.github.com` every 4 seconds is a very obvious, very regular pattern in any network log. At 5-minute intervals, it blends into normal background traffic. The 90-second periodic sync was the most suspicious — it fired even when you weren't typing, creating a perfectly regular heartbeat that's trivial to spot.

### 3. Gist description renamed from `"Writing Tool data"` to `"notes"`

**Before**: The private GitHub Gist used for sync was created with description `"Writing Tool data"`. If your GitHub account were ever audited (by your employer, by GitHub, by anyone with the token), the gist name would immediately identify what it was.

**After**: The gist is named `"notes"`. The `meta.json` file inside it also has `app: "notes"` instead of `app: "writing-tool"`.

**Why this matters**: GitHub Gists are visible to anyone with the token, and the gist list API returns descriptions. A gist named `"notes"` is plausible as work-related scratch storage. A gist named `"Writing Tool data"` is not.

### 4. App rebranded as "Editor" everywhere

**Before**: Manifest name, short_name, apple-mobile-web-app-title, page title, and meta description all said "Writing Tool" or referenced "chapters, corkboard, sync between your devices."

**After**: All neutralized to "Editor" / "Notes and documents."

**Why this matters**: When you install as a PWA, the app name shows up in:
- The OS app launcher
- The browser's installed apps list
- The window title (which ActivTrak logs)
- Any MDM-managed app inventory scan

"Editor" is plausibly a work tool. "Writing Tool" is not.

### 5. Backup filename and token URL neutralized

**Before**: Backup files downloaded as `writing-tool-backup-2026-10-19.json`. The "Create token" link prefilled the GitHub token creation form with description `Writing%20Tool`.

**After**: Backups download as `notes-backup-2026-10-19.json`. Token creation link prefills description as `notes`.

**Why this matters**: If you ever back up to disk on a work laptop, the filename is visible in any disk audit. If you ever click the token-creation link, the URL (and the resulting token's description in your GitHub settings) reveals the project.

### 6. Backwards-compatible backup format

The restore function still accepts backups from the original Writing Tool format (`app: "writing-tool"` or `app: "binderline"`), so if you have old backups you can import them. New backups are written with `app: "notes"`.

---

## Identity: Use a Separate GitHub Account

This is the single highest-leverage thing you can do beyond the code changes.

If your work laptop is managed by an MDM that audits installed browser apps, or if ActivTrak's Enhanced Details is on (so URLs are logged), the URL you visit will be `https://YOUR-GITHUB-USERNAME.github.io/REPO-NAME/`.

If your GitHub username is `john.smith`, that's identifiable. Create a fresh GitHub account specifically for hosting this — something like `editor-tools`, `notes-user`, or `workspace-2026`. Sign up with a separate email. Use the same account for the gist-sync token.

**Cost**: ~5 minutes to set up.
**Benefit**: Decouples your writing activity from your professional identity even if every other layer fails.

---

## Operational Hygiene on the Work Laptop

### Recommended (do these)

1. **Install as a PWA** — gives it a standalone process and app launcher entry named "Editor" instead of a Chrome/Edge tab.
2. **Use a separate browser profile** for this — Chrome/Edge lets you create a "Work" profile and a "Personal" profile. The Personal profile's localStorage is isolated from the Work profile.
3. **Configure sync once, then forget it** — paste the gist token once. Don't visit `github.com/yourname/your-repo` from the work browser; that URL is more identifiable than the PWA URL itself.
4. **Use Back up now** periodically — exports a JSON file you can move to a personal device. Belt-and-suspenders alongside gist sync.
5. **Mix with real work activity** — don't have 4 uninterrupted hours of nothing-but-Editor. Interleave with Slack, your real IDE, email. The pattern looks like a normal workday.

### Avoid (don't do these)

1. **Don't open the GitHub repo page** in your work browser. The repo URL is more identifiable than the PWA URL.
2. **Don't push git commits from the work laptop**. If you maintain a fork, do it from a personal device.
3. **Don't paste company code or secrets** into the editor. Even though ActivTrak doesn't keylog, screenshots (if Screen Details add-on is enabled) can capture your content during activity-triggered events.
4. **Don't write during a PIP or performance review cycle** — that's when managers escalate and pull timelines.

---

## What's Still Visible (the honest list)

Even after hardening, the following are still observable by IT:

| Signal | Visibility | Mitigation |
|---|---|---|
| `api.github.com` network requests | Visible in any network log | Slowed to 5min intervals; blends into background |
| PWA URL on first visit (if Enhanced Details) | One-time entry in URL log | Use a neutral-named repo on a separate GitHub account |
| App process name "Editor" | Visible in app/process reports | Already neutral — looks like a generic productivity app |
| Total time in app | Visible in activity reports | Unavoidable; rely on undefined=productive policy |
| Screenshots (if Screen Details add-on) | Could capture your chapter content during activity events | Close other windows during sensitive writing; consider minimizing app when not actively typing |
| GitHub gist token in localStorage | Recoverable from disk forensics | Token has `gist` scope only — minimal blast radius if leaked |

What is **not** visible:
- Chapter content (no keylogging)
- Specific text you type
- LLM/AI tool categorization (this isn't an AI tool)
- Mouse jiggler / false-activity flags (you're doing real activity)
- Time-theft framing (you're not simulating work)

---

## Sync Token Recovery Plan

If you ever lose access to the work laptop (laid off, laptop seized, IT reclaims it):

1. **Revoke the gist token immediately** at `github.com/settings/tokens`. The token has only `gist` scope, so revoking it cuts off all access.
2. **Generate a new token** and reconnect from your personal device. The data is in the gist; your personal device already has it (since sync is bidirectional).
3. **Optionally delete the gist** entirely from GitHub. All your local copies on personal devices remain intact.

---

## Repo Setup Checklist

- [ ] Created a fresh GitHub account with a non-identifiable handle
- [ ] Created a new repo with a neutral name (`notes`, `editor`, `workspace`)
- [ ] Uploaded all 6 files from this folder to the repo root
- [ ] Enabled GitHub Pages (Settings → Pages → main / root)
- [ ] Opened the deployed URL in your personal browser first to verify
- [ ] Generated a gist-scope-only token at `github.com/settings/tokens/new?scopes=gist&description=notes`
- [ ] Installed as a PWA on your personal device first, configured sync, verified it works
- [ ] Only then: opened on work laptop, installed as PWA, configured sync
- [ ] Did not visit the GitHub repo page from the work browser

---

## File Inventory

| File | Purpose | Changed from original? |
|---|---|---|
| `index.html` | The whole app — single HTML file | Yes — see hardening notes above |
| `manifest.webmanifest` | PWA metadata (name, icons, theme) | Yes — name/title/description neutralized |
| `sw.js` | Service worker for offline use | Yes — version bumped to force update |
| `LICENSE` | MIT license | No |
| `CHANGELOG.md` | Original changelog | No |
| `README.md` | Original author's README | No (this file is the deployment guide) |

---

## One Last Thing

You've made an informed choice. The hardening above reduces your fingerprint substantially, but it doesn't eliminate it. The only fully safe option is a personal device.

If you ever publish the novel, **finalize the manuscript on a personal device** — last edits, clean draft, the version you submit. The provenance of the final version matters more for IP purposes than the provenance of the rough draft.

Good luck with the book.
