# YouTube Caption Copier

Copy any text from a YouTube video — captions, on-screen product names, model numbers, slide text — without retyping it.

---

## Install

1. Download the ZIP from the [Releases](../../releases) page
2. Right-click the ZIP → **Extract All** → Extract
3. Open Chrome and go to `chrome://extensions`
4. Turn on **Developer mode** (toggle in the top-right corner)
5. Click **Load unpacked** → select the `yt-text-copy` folder
6. The extension icon appears in your Chrome toolbar

---

## How to Use

**Step 1 — Press P**
Press `P` on your keyboard while watching a YouTube video. The video pauses and all playback controls are locked. A tooltip appears inside the video with instructions.

**Step 2 — Select text**
Click and hold the left mouse button, then drag over any caption or on-screen text. The selected area highlights in yellow.

**Step 3 — Copy**
Press `Ctrl+C` to copy. Paste anywhere with `Ctrl+V`.

**Step 4 — Unlock**
Press `P` again. The video resumes and all controls return to normal.

---

## On-Screen Text (OCR)

For text displayed inside the video frame — product model numbers, slide text, URLs — drag a selection box around the text. The extension automatically reads it and copies it as plain text. No extra steps needed.

To scan the entire visible frame, click the 📷 button in the top-right corner of the video player.

---

## Tooltip

The tooltip appears inside the video when you press P. Drag it by the header to move it anywhere. Click ✕ to close it. Press P three times to bring it back.

---

## Plugin Compatibility

If the P key does not pause the video:

1. Temporarily disable other browser extensions (Adblock Plus, DuckDuckGo, etc.)
2. Reload the YouTube page
3. Press P again

---

## Keyboard Reference

| Key | Action |
|---|---|
| `P` | Lock / unlock the video |
| `P` × 3 | Reshow the tooltip |
| `Ctrl+C` | Copy selected text |
| `Ctrl+V` | Paste anywhere |
| `Esc` | Clear selection |

---

## Disable or Remove

**Temporarily disable:** Click the extension icon in the toolbar and use the toggle, or flip it off on `chrome://extensions`.

**Remove:** Go to `chrome://extensions` → find YouTube Caption Copier → click **Remove** → confirm.

---

## Privacy

- Makes zero network requests
- Runs only on `youtube.com/watch` pages
- Does not run in the background
- All code is open source

|
