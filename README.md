# YouTube Caption Copier

> **By downloading or installing this extension you agree to the [Terms & Conditions](https://codemayker.github.io/yt-caption-copier/#terms). Please read them before use.**

Copy any text from a YouTube video — captions, on-screen product names, model numbers, slide text — without retyping it.

---

## Install

1. Download the ZIP from the [Releases](../../releases) page
2. Right-click the ZIP → **Extract All** → Extract
3. Open Chrome and go to `chrome://extensions`
4. Turn on **Developer mode** (toggle in the top-right corner)
5. Click **Load unpacked** → select the `yt-text-copy` folder
6. The extension icon appears in your Chrome toolbar
7. On first use, a Terms & Conditions dialog will appear — you must agree before the extension activates

---

## How to Use

**Step 1 — Pause the video**
Pause the video normally using the Spacebar, the on-screen pause button, or a mouse click — exactly as you would on YouTube.

**Step 2 — Press X to lock**
Press `X` on your keyboard to activate Selection Mode. A blinking red ● **LOCKED** badge appears in the top-left corner of the video player confirming it is active. All playback controls are now blocked. A tooltip also appears inside the video with instructions.

**Step 3 — Drag to select**
Click and hold the left mouse button, then drag over any caption or on-screen text. The selected text copies automatically on release.

**Step 4 — Paste**
Press `Ctrl+V` to paste anywhere — a search bar, document, chat window, or anywhere Windows allows text to be pasted.

**Step 5 — Exit Selection Mode**
Press `X` twice. The red badge disappears, all normal YouTube controls return, and the video remains paused so you can resume it yourself with Spacebar when ready.

---

## Status Indicator

When Selection Mode is active, a blinking red ● **LOCKED** badge appears in the top-left corner of the video player. It disappears the moment you press X twice to exit.

| Badge visible | Selection Mode is ON — drag to select text |
|---|---|
| No badge | Selection Mode is OFF — normal YouTube controls |

If you are ever unsure whether Selection Mode is on or off, just look for the badge.

---

## On-Screen Text (OCR)

For text displayed inside the video frame — product model numbers, slide text, URLs — drag a selection box around the text. The extension automatically reads it and copies it as plain text. No extra steps needed.

To scan the entire visible frame, click the 📷 button in the top-right corner of the video player.

---

## Tooltip

The tooltip appears inside the video when you enter Selection Mode. Drag it by the header to move it anywhere on screen. Click ✕ to close it. Press X three times to bring it back.

---

## Plugin Compatibility

If the X key does not activate Selection Mode:

1. Temporarily disable other browser extensions (Adblock Plus, DuckDuckGo, etc.)
2. Reload the YouTube page
3. Try again

---

## Keyboard Reference

| Key | Action |
|---|---|
| `Space` | Pause / resume video (normal YouTube behaviour) |
| `X` | Enter Selection Mode — red badge appears |
| `X` `X` | Exit Selection Mode — red badge disappears |
| `X` × 3 | Reshow the tooltip |
| `Ctrl+V` | Paste copied text |
| `Esc` | Clear current selection |

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

| Permission | Purpose |
|---|---|
| `clipboardWrite` | To copy selected text to clipboard |
| `storage` | To save preferences and consent |
| `youtube.com` access | To read caption text displayed on the page |

---

## Terms & Conditions

By downloading, installing, or using this extension you agree to the [full Terms & Conditions](https://codemayker.github.io/yt-caption-copier/#terms).

In summary:
- Personal, research, educational, and accessibility use only
- Do not reproduce or distribute copyrighted content
- You are solely responsible for how you use the copied text
- The developer is not liable for any misuse or legal consequences

---

## Support

If this tool saves you time, consider supporting its development:

[♥ Support via Razorpay](https://razorpay.me/@codemayker)

---

## Feedback

btkrv1@gmail.com

---

*Free. Open source. No trackers. No ads.*
