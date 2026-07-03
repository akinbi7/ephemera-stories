# Ephemera — Stories Feature

A client-side "Story" feature inspired by Instagram / WhatsApp Status: post an image, it shows up as a
circle in a story bar, and it disappears automatically after 24 hours. No backend, no build step —
one HTML file.

- **Project brief:** <!-- paste the roadmap.sh project page URL here -->
- **Live demo:** <!-- paste your GitHub Pages URL here, e.g. https://<your-username>.github.io/<your-repo>/ -->

## Features

- **Post a story** — click the `+` button, pick an image. It's downscaled to fit within
  1080×1920, encoded as base64 JPEG, and saved to `localStorage`.
- **Story bar** — each story gets its own circle labeled with a relative time ("2m ago", "3h ago").
  Unviewed stories get an animated amber ring; viewed ones turn flat gray.
- **Full-screen viewer** — tap a circle to open it. A segmented progress bar fills over 3 seconds
  per story and auto-advances. Tap the left/right half of the screen, swipe, or use the arrow keys
  to move between stories.
- **Delete with confirmation** — the trash icon pauses playback and asks you to confirm before
  removing a story.
- **Auto-expiry** — a sweep every 30 seconds removes anything older than 24 hours; expired stories
  are also filtered out on page load.
- **Responsive** — circles and layout adapt down to mobile widths.

## Running it

No install, no server, no dependencies required.

**Option A — just open the file:**
Download or clone this repo, then double-click `index.html` (or right-click → Open With → your browser).

**Option B — clone from GitHub:**
```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
open index.html   # macOS; on Windows use `start index.html`, on Linux `xdg-open index.html`
```

That's it — the story bar, upload, viewer, expiry, and delete confirmation all work immediately in
any modern browser, with data persisted locally via `localStorage`.

## How it works

Everything lives in `index.html` — plain HTML, CSS, and vanilla JavaScript. No frameworks, no
npm packages, no CDN scripts.

| Concern | Approach |
|---|---|
| Storage | A single `localStorage` key (`ephemera:stories`) holding a JSON array of story objects: `{ id, src, createdAt, expiresAt, viewed }` |
| Image resizing | Uploaded file → `FileReader` → `<img>` → drawn to an off-screen `<canvas>` scaled to fit within 1080×1920 (only ever shrinks) → exported via `canvas.toDataURL('image/jpeg', 0.85)` |
| Expiry | Each story stores `expiresAt = createdAt + 24h`. Checked on load and swept every 30s with `setInterval` |
| Story playback | `requestAnimationFrame` loop drives each 3-second progress segment; unified `pointerdown`/`pointerup` handlers cover tap-to-navigate and swipe-to-navigate on both mouse and touch |

### Why not a React/CDN version?

`localStorage` is a hard requirement here, and sandboxed live-preview environments (like Claude.ai's
artifact viewer) block browser storage APIs. Vanilla JS with zero dependencies sidesteps that
entirely and keeps the project trivially portable — it runs the same way in any browser, including
directly off the filesystem.

## Known limitations

- `localStorage` has a per-origin size ceiling (commonly ~5–10MB across browsers). Since images are
  stored as base64, that's roughly a few dozen photos depending on size. A toast warns you if a save
  fails because storage is full.
- Stories are cleared if the user clears site data / uses a different browser or private window —
  there's no server, so nothing syncs across devices.
- This models a single user's own story reel (per the original spec) — there's no concept of other
  users or accounts.

## Possible next steps

- Swap `localStorage` for `IndexedDB` to raise the storage ceiling.
- Add captions or stickers on top of the uploaded image.
- Add a "mute during hold" gesture so long-pressing pauses playback without opening the confirm flow.

## License

MIT — do whatever you'd like with it.
