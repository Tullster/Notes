# Writing Tool

A writing tool for one person. Write in chapters, plan on a corkboard, and use the same book on every device. It is a single HTML file with no server, account, or tracking.

## Features

- **Writing.** Focus mode, typewriter scrolling, optional typewriter sound, smart quotes and dashes, and a choice of serif, sans, or mono type. Right-to-left languages work.
- **Formatting.** Text is plain Markdown, so your files stay portable. A formatting bar gives one-tap bold, italic, heading, quote, list, and scene break, with shortcuts (Ctrl+B, Ctrl+I, Ctrl+Alt+H, Ctrl+Alt+Q, Ctrl+Alt+L, Ctrl+Enter). Bold and italic toggle off again, typing `*` over a selection wraps it, and Enter continues a list or quote (Enter on an empty item ends it). **Preview** (Ctrl+/) shows the chapter as it will read, with smart quotes, headings, quotes, lists, and scene breaks. **Clean up formatting** fixes double spaces, trailing spaces, extra blank lines, straight quotes, dashes, ellipses, and text pasted with single line breaks. It saves a version first and can be undone. Page width and line spacing are adjustable.
- **Chapters.** Reorder by dragging or with Alt+arrows, rename, duplicate, delete with Undo. Ctrl+Alt+↓/↑ opens the next or previous chapter.
- **Corkboard.** Flat, minimal cards pinned to a cork board: scenes, characters, places, plot points, ideas, and heading labels. A card's label shows as a slim colour bar, a coloured pin, and the label name. Connect cards with red thread by dragging from a pin, or click a pin and then another card. Click selects a card, double-click edits it, and a small toolbar appears over the selection. Cards snap to each other with alignment guides (hold Alt to turn snapping off). Arrange the board as a grid, grouped by label, or grouped by type. Search, filter, multi-select, undo, redo, and a list view are included.
- **Versions.** Save a version of any chapter, compare it with the current text, and restore it. Find and replace saves a version of every chapter it changes.
- **Import and export.** Import Word (.docx), Markdown, or text. Export the book or one chapter to Word in a Manuscript style (Times New Roman, double spaced) or a Book style (Georgia, single spaced, justified), export the book to Markdown, print or save as PDF, or download a full JSON backup.
- **Sync.** Optional. Uses a private GitHub Gist that only you can open.
- **Offline and install.** Installs on phones, tablets, and computers, and opens without a connection after the first visit.

## Publish on GitHub Pages

1. Create a repository and upload these files, or push them.
2. In **Settings > Pages**, set **Source** to **GitHub Actions**. The included workflow deploys the site. You can also choose **Deploy from a branch** with `main` and `/ (root)`.
3. After the deployment finishes, open `https://YOUR-NAME.github.io/YOUR-REPO/`.

## Install

- **iPhone or iPad (Safari):** Share, then Add to Home Screen.
- **Android (Chrome):** menu, then Install app.
- **Computer (Chrome or Edge):** use the install icon in the address bar.

## Sync between devices

1. Open the **⋯ menu** and choose **Sync…**
2. Follow the link to create a GitHub token with only the **gist** permission.
3. Paste the token and press **Connect**. Repeat on each device with the same token.

The book is stored in a private gist named "Writing Tool data". Changes sync a few seconds after you stop typing and whenever you return to the app. If two devices change the same chapter, both versions are kept and the other one appears under Versions. Deleted chapters are deleted on every device.

The token is stored in the browser only and is not included in backups. Anyone with the token can read the gist, so treat it like a password and revoke it on GitHub if a device is lost.

## Your data

- Data is stored in the browser on each device. Browsers allow about 5 MB per site, roughly 600,000 words including versions. The menu shows how full storage is. Older versions are trimmed first when space runs low.
- **Back up now** downloads a full backup. **Restore from backup** loads one, including on a new device.
- One book per browser profile. To work on several books, back up each one and restore the one you want.
- Clearing site data deletes the local copy. With Sync on, reconnect and the book is restored.

## Keyboard

| Keys | Action |
|---|---|
| Ctrl/⌘ K | Go to a chapter or command |
| Ctrl/⌘ B, I | Bold, italic |
| Ctrl/⌘ Enter | Scene break |
| Ctrl/⌘ Shift F | Find and replace |
| Ctrl/⌘ \ | Hide or show the chapter list |
| Ctrl+Alt+↓ / ↑ | Next or previous chapter |
| Alt+↑ / ↓ in the chapter list | Move the chapter |
| ? | All shortcuts |

Board: double-click the cork to add a card, double-click a card to edit it, Shift-click or Shift-drag to select several, arrow keys to move between cards, Alt+arrows to nudge, L to connect the selected card with a thread, Ctrl+D to duplicate, Delete to remove, scroll to pan, Ctrl+scroll to zoom, 0 to fit.

## Browser support

Current Chrome, Edge, Safari 16.4 or later, and Firefox 115 or later. Importing .docx files needs `DecompressionStream`, which all of these support. Text and Markdown import work in any browser.

## Open it from a web address

Use the GitHub Pages address or another web server, not a double-click on `index.html`. Browsers treat local files differently and can lose recent saves if a local page is reloaded quickly. Only the web address can be installed and used offline.

## Privacy

The app makes no network requests of its own except to GitHub when Sync is on. It uses system fonts, loads no scripts or fonts from other sites, and has no analytics. The GitHub build sets a Content-Security-Policy that enforces this.

## Troubleshooting

- **Sync error: check your token.** GitHub no longer accepts the token. Create a new one with the gist permission and reconnect.
- **Offline: saved on this device.** Nothing is lost. The book syncs when the connection returns.
- **Saved data could not be read.** The stored copy was damaged. The app kept it under a separate name in this browser and started a new book. Restore from a backup, or ask for help recovering the kept copy.
- **This device is full, or storage is blocked.** Back up, then delete old versions or move older chapters into a backup.
- **Print does nothing.** Some browsers block printing inside embedded frames. Open the app in its own tab.

## Development

There is no build step. `index.html` is the whole app. `sw.js` provides offline use. Change `VERSION` in it whenever `index.html` changes so installed copies update. MIT license.
