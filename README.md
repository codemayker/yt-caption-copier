<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>YouTube Caption Copier — Chrome Extension</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,300&display=swap" rel="stylesheet"/>
<style>
  /* ─── Reset & Base ─────────────────────────────────────────────────── */
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --red:       #E8190A;
    --red-soft:  #FF3B2E;
    --red-glow:  rgba(232, 25, 10, 0.18);
    --dark:      #0A0A0B;
    --surface:   #111113;
    --card:      #17171A;
    --border:    rgba(255,255,255,0.07);
    --text:      #E8E8EC;
    --muted:     #7A7A85;
    --green:     #22C55E;
    --yellow:    #F59E0B;
    --font-head: 'Syne', sans-serif;
    --font-body: 'DM Sans', sans-serif;
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--dark);
    color: var(--text);
    font-family: var(--font-body);
    font-size: 16px;
    line-height: 1.65;
    -webkit-font-smoothing: antialiased;
    overflow-x: hidden;
  }

  /* ─── Noise texture overlay ─────────────────────────────────────────── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 0;
    opacity: 0.4;
  }

  /* ─── Layout ────────────────────────────────────────────────────────── */
  .wrap { max-width: 860px; margin: 0 auto; padding: 0 24px; position: relative; z-index: 1; }

  section { padding: 72px 0; }
  section + section { border-top: 1px solid var(--border); }

  /* ─── Hero ──────────────────────────────────────────────────────────── */
  .hero {
    padding: 80px 0 64px;
    position: relative;
    overflow: hidden;
  }

  .hero::after {
    content: '';
    position: absolute;
    top: -120px; left: 50%;
    transform: translateX(-50%);
    width: 600px; height: 400px;
    background: radial-gradient(ellipse, rgba(232,25,10,0.12) 0%, transparent 70%);
    pointer-events: none;
  }

  .badge {
    display: inline-flex;
    align-items: center;
    gap: 7px;
    background: rgba(232,25,10,0.12);
    border: 1px solid rgba(232,25,10,0.3);
    border-radius: 100px;
    padding: 5px 14px;
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.04em;
    color: #FF6B60;
    margin-bottom: 28px;
    text-transform: uppercase;
  }

  .badge-dot {
    width: 6px; height: 6px;
    background: var(--red-soft);
    border-radius: 50%;
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.5; transform: scale(0.8); }
  }

  h1 {
    font-family: var(--font-head);
    font-size: clamp(38px, 6vw, 64px);
    font-weight: 800;
    line-height: 1.05;
    letter-spacing: -0.03em;
    margin-bottom: 24px;
  }

  h1 em {
    font-style: normal;
    color: var(--red-soft);
    position: relative;
  }

  .hero-sub {
    font-size: 18px;
    color: var(--muted);
    max-width: 560px;
    margin-bottom: 40px;
    font-weight: 300;
    line-height: 1.7;
  }

  .hero-sub strong { color: var(--text); font-weight: 500; }

  .cta-row {
    display: flex;
    flex-wrap: wrap;
    gap: 14px;
    align-items: center;
    margin-bottom: 56px;
  }

  .btn {
    display: inline-flex;
    align-items: center;
    gap: 9px;
    padding: 13px 26px;
    border-radius: 8px;
    font-family: var(--font-head);
    font-size: 14px;
    font-weight: 700;
    letter-spacing: 0.02em;
    cursor: pointer;
    text-decoration: none;
    transition: all 0.18s ease;
    border: none;
  }

  .btn-primary {
    background: var(--red);
    color: #fff;
    box-shadow: 0 0 32px rgba(232,25,10,0.3);
  }
  .btn-primary:hover { background: var(--red-soft); transform: translateY(-2px); box-shadow: 0 4px 40px rgba(232,25,10,0.45); }

  .btn-ghost {
    background: transparent;
    color: var(--muted);
    border: 1px solid var(--border);
  }
  .btn-ghost:hover { color: var(--text); border-color: rgba(255,255,255,0.2); transform: translateY(-1px); }

  /* ─── Scenario cards ────────────────────────────────────────────────── */
  .section-label {
    font-family: var(--font-head);
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    color: var(--red-soft);
    margin-bottom: 16px;
  }

  h2 {
    font-family: var(--font-head);
    font-size: clamp(26px, 4vw, 38px);
    font-weight: 800;
    letter-spacing: -0.025em;
    line-height: 1.15;
    margin-bottom: 14px;
  }

  .section-sub {
    font-size: 16px;
    color: var(--muted);
    max-width: 520px;
    margin-bottom: 44px;
    font-weight: 300;
  }

  .scenario-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
    gap: 16px;
  }

  .scenario-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 24px;
    transition: border-color 0.2s, transform 0.2s;
    position: relative;
    overflow: hidden;
  }

  .scenario-card::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, var(--red-glow) 0%, transparent 60%);
    opacity: 0;
    transition: opacity 0.3s;
  }

  .scenario-card:hover { border-color: rgba(232,25,10,0.3); transform: translateY(-3px); }
  .scenario-card:hover::before { opacity: 1; }

  .scenario-icon {
    font-size: 28px;
    margin-bottom: 14px;
    display: block;
  }

  .scenario-card h3 {
    font-family: var(--font-head);
    font-size: 15px;
    font-weight: 700;
    margin-bottom: 8px;
    color: var(--text);
  }

  .scenario-card p {
    font-size: 13.5px;
    color: var(--muted);
    line-height: 1.6;
  }

  /* ─── How it works ──────────────────────────────────────────────────── */
  .steps-list { display: flex; flex-direction: column; gap: 0; }

  .step-row {
    display: grid;
    grid-template-columns: 52px 1fr;
    gap: 20px;
    align-items: flex-start;
    padding: 28px 0;
    border-bottom: 1px solid var(--border);
    position: relative;
  }
  .step-row:last-child { border-bottom: none; }

  .step-num {
    width: 44px; height: 44px;
    border-radius: 12px;
    background: linear-gradient(135deg, var(--red) 0%, #B30000 100%);
    display: flex; align-items: center; justify-content: center;
    font-family: var(--font-head);
    font-size: 18px;
    font-weight: 800;
    color: #fff;
    flex-shrink: 0;
    box-shadow: 0 4px 16px rgba(232,25,10,0.3);
  }

  .step-content h3 {
    font-family: var(--font-head);
    font-size: 17px;
    font-weight: 700;
    margin-bottom: 6px;
  }

  .step-content p { font-size: 14.5px; color: var(--muted); }

  .step-content code {
    display: inline-block;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 3px 10px;
    font-family: 'Courier New', monospace;
    font-size: 13px;
    color: #7DD3FC;
    margin-top: 8px;
  }

  .step-tag {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    background: rgba(34,197,94,0.12);
    border: 1px solid rgba(34,197,94,0.25);
    border-radius: 100px;
    padding: 2px 10px;
    font-size: 11px;
    font-weight: 600;
    color: var(--green);
    margin-left: 10px;
    vertical-align: middle;
  }

  /* ─── Keyboard table ────────────────────────────────────────────────── */
  .kb-table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 8px;
  }

  .kb-table th {
    font-family: var(--font-head);
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
    text-align: left;
    padding: 10px 16px;
    border-bottom: 1px solid var(--border);
  }

  .kb-table td {
    padding: 12px 16px;
    font-size: 14px;
    border-bottom: 1px solid var(--border);
    vertical-align: middle;
  }

  .kb-table tr:last-child td { border-bottom: none; }
  .kb-table tr:hover td { background: rgba(255,255,255,0.02); }

  kbd {
    display: inline-flex;
    align-items: center;
    background: var(--surface);
    border: 1px solid rgba(255,255,255,0.15);
    border-bottom-width: 2px;
    border-radius: 6px;
    padding: 3px 9px;
    font-family: 'Courier New', monospace;
    font-size: 12px;
    color: #E0E0FF;
    white-space: nowrap;
    gap: 4px;
  }

  /* ─── Security section ──────────────────────────────────────────────── */
  .shield-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    gap: 14px;
  }

  .shield-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 20px;
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .shield-card .icon { font-size: 22px; }
  .shield-card h4 { font-family: var(--font-head); font-size: 14px; font-weight: 700; }
  .shield-card p { font-size: 13px; color: var(--muted); line-height: 1.55; }

  .never-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-top: 24px;
  }

  .never-row {
    display: flex;
    align-items: flex-start;
    gap: 12px;
    padding: 14px 18px;
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 10px;
  }

  .never-row .chk { font-size: 16px; flex-shrink: 0; margin-top: 1px; }
  .never-row p { font-size: 14px; color: var(--text); }
  .never-row p span { color: var(--muted); font-size: 13px; display: block; margin-top: 2px; }

  /* ─── Permission table ──────────────────────────────────────────────── */
  .perm-table {
    width: 100%;
    border-collapse: collapse;
    background: var(--card);
    border-radius: 12px;
    overflow: hidden;
    border: 1px solid var(--border);
  }

  .perm-table th {
    font-family: var(--font-head);
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
    padding: 12px 20px;
    text-align: left;
    background: rgba(255,255,255,0.03);
    border-bottom: 1px solid var(--border);
  }

  .perm-table td {
    padding: 14px 20px;
    font-size: 14px;
    border-bottom: 1px solid var(--border);
    vertical-align: top;
  }

  .perm-table tr:last-child td { border-bottom: none; }
  .perm-table td:first-child { font-family: 'Courier New', monospace; font-size: 13px; color: #7DD3FC; width: 35%; }
  .perm-table td:last-child { color: var(--muted); }

  /* ─── Stats row ─────────────────────────────────────────────────────── */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    gap: 14px;
    margin-top: 10px;
  }

  .stat-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 20px 18px;
    text-align: center;
  }

  .stat-value {
    font-family: var(--font-head);
    font-size: 28px;
    font-weight: 800;
    color: var(--red-soft);
    display: block;
    margin-bottom: 4px;
  }

  .stat-label { font-size: 12px; color: var(--muted); }

  /* ─── Removal section ───────────────────────────────────────────────── */
  .info-box {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 24px;
    margin-bottom: 16px;
  }

  .info-box h3 {
    font-family: var(--font-head);
    font-size: 16px;
    font-weight: 700;
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .info-box ol, .info-box ul {
    padding-left: 22px;
  }

  .info-box li {
    font-size: 14.5px;
    color: var(--muted);
    margin-bottom: 8px;
    line-height: 1.6;
  }

  .info-box li strong { color: var(--text); }

  /* ─── Feedback ──────────────────────────────────────────────────────── */
  .feedback-box {
    background: linear-gradient(135deg, rgba(232,25,10,0.08) 0%, rgba(232,25,10,0.02) 100%);
    border: 1px solid rgba(232,25,10,0.2);
    border-radius: 16px;
    padding: 40px;
    text-align: center;
  }

  .feedback-box h2 { margin-bottom: 12px; }
  .feedback-box p { color: var(--muted); max-width: 460px; margin: 0 auto 28px; font-size: 15px; }

  .email-chip {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 100px;
    padding: 10px 22px;
    font-family: 'Courier New', monospace;
    font-size: 15px;
    color: var(--text);
    text-decoration: none;
    transition: all 0.2s;
    margin-bottom: 16px;
  }

  .email-chip:hover { border-color: rgba(232,25,10,0.4); background: rgba(232,25,10,0.08); transform: translateY(-2px); }

  .feedback-types {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
    margin-top: 20px;
  }

  .chip {
    padding: 6px 14px;
    border-radius: 100px;
    font-size: 12.5px;
    font-weight: 500;
    border: 1px solid var(--border);
    color: var(--muted);
    background: var(--card);
  }

  /* ─── Footer ────────────────────────────────────────────────────────── */
  footer {
    padding: 40px 0;
    border-top: 1px solid var(--border);
    text-align: center;
  }

  footer p { font-size: 13px; color: var(--muted); }
  footer a { color: var(--muted); text-decoration: none; }
  footer a:hover { color: var(--text); }

  /* ─── Inline notice ─────────────────────────────────────────────────── */
  .notice {
    background: rgba(245, 158, 11, 0.08);
    border: 1px solid rgba(245, 158, 11, 0.2);
    border-radius: 10px;
    padding: 14px 18px;
    font-size: 13.5px;
    color: #FCD34D;
    margin-top: 16px;
    display: flex;
    gap: 10px;
    align-items: flex-start;
    line-height: 1.55;
  }

  /* ─── Animations ─────────────────────────────────────────────────────── */
  .fade-up {
    opacity: 0;
    transform: translateY(22px);
    animation: fadeUp 0.55s ease forwards;
  }

  @keyframes fadeUp {
    to { opacity: 1; transform: translateY(0); }
  }

  .fade-up:nth-child(1) { animation-delay: 0.05s; }
  .fade-up:nth-child(2) { animation-delay: 0.12s; }
  .fade-up:nth-child(3) { animation-delay: 0.18s; }
  .fade-up:nth-child(4) { animation-delay: 0.24s; }
  .fade-up:nth-child(5) { animation-delay: 0.30s; }
  .fade-up:nth-child(6) { animation-delay: 0.36s; }

  /* ─── Responsive ─────────────────────────────────────────────────────── */
  @media (max-width: 600px) {
    .cta-row { flex-direction: column; }
    .btn { width: 100%; justify-content: center; }
    .step-row { grid-template-columns: 44px 1fr; gap: 14px; }
    .feedback-box { padding: 28px 20px; }
  }
</style>
</head>
<body>

<!-- ─── HERO ──────────────────────────────────────────────────────────────── -->
<div class="hero">
  <div class="wrap">
    <div class="badge fade-up"><span class="badge-dot"></span> v1.0 &nbsp;·&nbsp; Free Chrome Extension &nbsp;·&nbsp; Open Source &nbsp;·&nbsp; No Account Needed</div>

    <h1 class="fade-up">
      Stop retyping what<br/>
      you can <em>already see</em>
    </h1>

    <p class="hero-sub fade-up">
      <strong>YouTube Caption Copier</strong> lets you copy any text from a YouTube video — captions, product names, model numbers, slide text — without retyping it. Press <kbd style="background:rgba(255,255,255,0.15);border-radius:4px;padding:1px 6px;font-size:13px;">P</kbd> to freeze the frame, drag over the text to select it, press Ctrl+C to copy, press P to resume.
    </p>

    <div class="cta-row fade-up">
      <a href="https://github.com/codemayker/yt-caption-copier/releases/download/v1.0.0/yt-caption-copier-v1.0.zip" class="btn btn-primary" download>
        <svg width="16" height="16" viewBox="0 0 16 16" fill="none"><path d="M8 1v9M4 7l4 4 4-4M2 13h12" stroke="white" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/></svg>
        Download &amp; Install Free
      </a>
      <a href="#how-it-works" class="btn btn-ghost">
        See how it works →
      </a>
    </div>

    <div class="stats-row fade-up">
      <div class="stat-card">
        <span class="stat-value">15 KB</span>
        <span class="stat-label">Total extension size</span>
      </div>
      <div class="stat-card">
        <span class="stat-value">0</span>
        <span class="stat-label">Network requests ever made</span>
      </div>
      <div class="stat-card">
        <span class="stat-value">2</span>
        <span class="stat-label">Permissions requested</span>
      </div>
      <div class="stat-card">
        <span class="stat-value">~400 KB</span>
        <span class="stat-label">RAM while active on YouTube</span>
      </div>
      <div class="stat-card">
        <span class="stat-value">0 KB</span>
        <span class="stat-label">RAM everywhere else</span>
      </div>
    </div>
  </div>
</div>

<!-- ─── REAL-WORLD SCENARIOS ──────────────────────────────────────────────── -->
<section>
  <div class="wrap">
    <p class="section-label">Why people use it</p>
    <h2>Real moments where this saves you</h2>
    <p class="section-sub">Anything showing as a caption on YouTube — you can now copy with one drag.</p>

    <div class="scenario-grid">
      <div class="scenario-card fade-up">
        <span class="scenario-icon">🛍️</span>
        <h3>Shopping from a product video</h3>
        <p>Saw a product name, model number, or brand on screen? Drag to copy it, paste straight into Amazon, Flipkart, or Google Search. No toggling tabs, no typos in long model numbers.</p>
      </div>
      <div class="scenario-card fade-up">
        <span class="scenario-icon">📚</span>
        <h3>Book or course titles</h3>
        <p>An author mentions a book, a podcast references a course, a speaker names a journal article — copy the title directly into your notes, Google Books, or Goodreads. Gone in seconds.</p>
      </div>
      <div class="scenario-card fade-up">
        <span class="scenario-icon">🖥️</span>
        <h3>Slide text in webinars</h3>
        <p>Presentations, online lectures, corporate webinars — grab bullet points, statistics, URLs, or technical terms from slides without pausing to write them down.</p>
      </div>
      <div class="scenario-card fade-up">
        <span class="scenario-icon">💻</span>
        <h3>Code from tutorials</h3>
        <p>A developer tutorial shows a command, a config snippet, or a package name. Copy it directly from the caption instead of squinting and typing character-by-character.</p>
      </div>
      <div class="scenario-card fade-up">
        <span class="scenario-icon">📝</span>
        <h3>Research &amp; study notes</h3>
        <p>Watching a lecture or documentary? Capture quotes, terminology, names, or dates directly into a Notepad or Word document as you watch — no screenshot, no rewind needed.</p>
      </div>
      <div class="scenario-card fade-up">
        <span class="scenario-icon">🌐</span>
        <h3>URLs &amp; social handles</h3>
        <p>Someone mentions their website, Instagram handle, or a link in a video. Copy it straight from the caption and open it in a new tab immediately.</p>
      </div>
    </div>
  </div>
</section>

<!-- ─── HOW IT WORKS ───────────────────────────────────────────────────────── -->
<section id="how-it-works">
  <div class="wrap">
    <p class="section-label">Usage</p>
    <h2>How it works</h2>
    <p class="section-sub">Four steps. No typing. No plugins. No account.</p>

    <div class="steps-list">
      <div class="step-row">
        <div class="step-num">1</div>
        <div class="step-content">
          <h3>Press P to pause and lock the video</h3>
          <p>Press <kbd>P</kbd> on your keyboard while watching any YouTube video. The video pauses immediately and all playback controls are blocked so the frame stays exactly where you need it. A tooltip appears inside the video confirming it is locked.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">2</div>
        <div class="step-content">
          <h3>Drag to select the text</h3>
          <p>Click and drag with your mouse over any caption text or on-screen text. The selected area highlights in yellow. For text that is part of the video image — model numbers, slide text, URLs — drag around it the same way and the extension reads it automatically.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">3</div>
        <div class="step-content">
          <h3>Press Ctrl+C to copy</h3>
          <p>Press <kbd>Ctrl</kbd><kbd>C</kbd> to copy the highlighted text to your clipboard. Paste it anywhere with <kbd>Ctrl</kbd><kbd>V</kbd> — a browser search bar, a document, a chat window, anywhere Windows allows text to be pasted.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">4</div>
        <div class="step-content">
          <h3>Press P again to resume</h3>
          <p>Press <kbd>P</kbd> once more. The video resumes from the exact point it was paused and all normal YouTube controls return.</p>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ─── INSTALL ────────────────────────────────────────────────────────────── -->
<section id="install">
  <div class="wrap">
    <p class="section-label">Installation</p>
    <h2>Install in 60 seconds — completely free</h2>
    <p class="section-sub">No Chrome Web Store, no account, no payment. Works on Windows 7, 8, 10 &amp; 11.</p>

    <div class="steps-list">
      <div class="step-row">
        <div class="step-num">1</div>
        <div class="step-content">
          <h3>Download the ZIP</h3>
          <p>Click the button below to download the extension directly. Save it anywhere — your Downloads folder is fine.</p>
          <a href="https://github.com/codemayker/yt-caption-copier/releases/download/v1.0.0/yt-caption-copier-v1.0.zip" class="btn btn-primary" download style="display:inline-flex;align-items:center;gap:8px;margin-top:12px;text-decoration:none;">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none"><path d="M8 1v9M4 7l4 4 4-4M2 13h12" stroke="white" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/></svg>
            Download yt-caption-copier-v1.0.zip
          </a>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">2</div>
        <div class="step-content">
          <h3>Extract the folder</h3>
          <p>Right-click the downloaded ZIP file in File Explorer → <strong>"Extract All…"</strong> → click <strong>Extract</strong>. A folder called <code>yt-text-copy</code> will appear.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">3</div>
        <div class="step-content">
          <h3>Open Chrome's extension page</h3>
          <p>In Chrome, click the address bar and type:</p>
          <code>chrome://extensions</code>
          <p style="margin-top:8px;">Press Enter.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">4</div>
        <div class="step-content">
          <h3>Enable Developer Mode</h3>
          <p>In the top-right corner of the Extensions page, flip the <strong>"Developer mode"</strong> toggle to ON. New buttons will appear — this is normal and safe.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">5</div>
        <div class="step-content">
          <h3>Load the extension</h3>
          <p>Click <strong>"Load unpacked"</strong> → navigate to and select the <code>yt-text-copy</code> folder. Click <strong>Select Folder</strong>.</p>
        </div>
      </div>
      <div class="step-row">
        <div class="step-num">✓</div>
        <div class="step-content">
          <h3>You're done!</h3>
          <p>The red extension icon appears in your Chrome toolbar. Open any YouTube video, press P to lock it, drag to select text, and press Ctrl+C to copy.</p>
        </div>
      </div>
    </div>

    <div class="notice">
      <span>💡</span>
      <span>Chrome will show a small banner saying <em>"Developer mode extensions are enabled."</em> This is normal for extensions installed from GitHub rather than the Chrome Web Store. It doesn't affect how anything works and is purely informational. For the full step-by-step guide with screenshots, see <strong>YouTube-Caption-Copier-User-Guide.docx</strong> included in the download.</span>
    </div>
  </div>
</section>

<!-- ─── SECURITY ───────────────────────────────────────────────────────────── -->
<section id="security">
  <div class="wrap">
    <p class="section-label">Security &amp; Privacy</p>
    <h2>Built with nothing to hide</h2>
    <p class="section-sub">Every protection is documented, verifiable, and enforced at the browser level — not just promised.</p>

    <div class="shield-grid" style="margin-bottom:32px;">
      <div class="shield-card fade-up">
        <span class="icon">🔒</span>
        <h4>Zero network requests</h4>
        <p>This extension never makes a single HTTP call. No data ever leaves your computer. Verified by inspecting the source code.</p>
      </div>
      <div class="shield-card fade-up">
        <span class="icon">📦</span>
        <h4>Manifest V3 compliant</h4>
        <p>Built on Chrome's newest and strictest extension standard. All code is bundled locally — Chrome won't allow remote scripts by design.</p>
      </div>
      <div class="shield-card fade-up">
        <span class="icon">🎯</span>
        <h4>Only runs on YouTube</h4>
        <p>The extension activates only on <code>youtube.com/watch</code> pages. Completely dormant on every other website.</p>
      </div>
      <div class="shield-card fade-up">
        <span class="icon">🛡️</span>
        <h4>Strict Content Security Policy</h4>
        <p>Declared in manifest.json and enforced by Chrome itself. No external script injection is possible, ever.</p>
      </div>
      <div class="shield-card fade-up">
        <span class="icon">🔑</span>
        <h4>Only 2 permissions</h4>
        <p><code>clipboardWrite</code> (to copy your text) and <code>storage</code> (to save your on/off preference). Nothing else is requested.</p>
      </div>
      <div class="shield-card fade-up">
        <span class="icon">🔍</span>
        <h4>Fully open source</h4>
        <p>Every line of code is visible in this repository. No minification, no obfuscation. Any developer can audit it before installing.</p>
      </div>
    </div>

    <h3 style="font-family: var(--font-head); font-size:18px; font-weight:700; margin-bottom:16px;">The extension will NEVER:</h3>
    <div class="never-list">
      <div class="never-row">
        <span class="chk">❌</span>
        <p><strong>Read your passwords, history, or cookies</strong> <span>These permissions were never requested — Chrome wouldn't allow it even if we tried.</span></p>
      </div>
      <div class="never-row">
        <span class="chk">❌</span>
        <p><strong>Run in the background</strong> <span>No background service worker. It uses 0 KB RAM when you're not on YouTube.</span></p>
      </div>
      <div class="never-row">
        <span class="chk">❌</span>
        <p><strong>Modify YouTube's video player or ads</strong> <span>The extension adds a transparent overlay for selection only. It does not touch YouTube's features.</span></p>
      </div>
      <div class="never-row">
        <span class="chk">❌</span>
        <p><strong>Store or transmit caption text</strong> <span>Text passes through your clipboard only. It is never saved, logged, or sent anywhere by this extension.</span></p>
      </div>
      <div class="never-row">
        <span class="chk">❌</span>
        <p><strong>Run on any other website</strong> <span>Hard-coded to youtube.com/watch only. Not even the YouTube homepage.</span></p>
      </div>
    </div>

    <h3 style="font-family: var(--font-head); font-size:18px; font-weight:700; margin: 36px 0 14px;">Permissions — plain English</h3>
    <table class="perm-table">
      <thead><tr><th>Permission</th><th>Why it's needed</th></tr></thead>
      <tbody>
        <tr>
          <td>clipboardWrite</td>
          <td>To copy your selected caption text to clipboard when you finish selecting. Without this, the extension cannot put anything on your clipboard.</td>
        </tr>
        <tr>
          <td>storage</td>
          <td>Saves one single setting: whether the extension is switched on or off. Stored on your computer only. Never transmitted.</td>
        </tr>
        <tr>
          <td>youtube.com access</td>
          <td>Chrome describes any extension that reads text on a website this way. It needs to see the caption text to let you copy it. No YouTube data is stored or transmitted.</td>
        </tr>
      </tbody>
    </table>

    <h3 style="font-family: var(--font-head); font-size:18px; font-weight:700; margin: 36px 0 14px;">Tamper-proof release verification</h3>
    <p style="color: var(--muted); font-size:14.5px; margin-bottom:16px;">Every official release includes SHA-256 hashes for every file. You can verify your download hasn't been modified since it was built using the included <code>verify-integrity.ps1</code> script (double-click on Windows):</p>
    <div class="info-box">
      <h3>🔐 Verify your download (PowerShell)</h3>
      <p style="font-family:'Courier New',monospace; font-size:13px; background:var(--dark); padding:14px 16px; border-radius:8px; color:#7DD3FC; margin-top:8px;">
        Get-FileHash yt-caption-copier-v1.0.zip -Algorithm SHA256
      </p>
      <p style="font-size:13.5px; color:var(--muted); margin-top:10px;">Compare the output to the hash in <code>yt-caption-copier-v1.0.zip.sha256</code>. If they match, your file is unmodified.</p>
    </div>
  </div>
</section>

<!-- ─── SAFE REMOVAL ───────────────────────────────────────────────────────── -->
<section id="remove">
  <div class="wrap">
    <p class="section-label">Uninstall</p>
    <h2>Removing it is just as easy</h2>
    <p class="section-sub">No residue, no leftover data. A clean, complete removal in under 30 seconds.</p>

    <div class="info-box">
      <h3>🗑️ How to uninstall</h3>
      <ol style="padding-left:20px; margin-top:8px;">
        <li>Open Chrome and go to <strong>chrome://extensions</strong></li>
        <li>Find <strong>"YouTube Caption Copier"</strong> in the list</li>
        <li>Click the <strong>"Remove"</strong> button</li>
        <li>Confirm by clicking <strong>"Remove"</strong> in the dialog</li>
      </ol>
      <p style="font-size:13.5px; color:var(--muted); margin-top:14px;">✅ Chrome completely removes the extension and all associated data (your on/off preference). Nothing remains.</p>
    </div>

    <div class="info-box">
      <h3>⏸️ Temporarily disable (without uninstalling)</h3>
      <ul style="padding-left:20px; margin-top:8px;">
        <li>Click the extension icon in your Chrome toolbar and use the <strong>toggle switch</strong> in the popup, <em>or</em></li>
        <li>Go to <strong>chrome://extensions</strong> and flip the extension's toggle to OFF</li>
      </ul>
      <p style="font-size:13.5px; color:var(--muted); margin-top:14px;">When disabled, the extension is completely inactive. No code runs anywhere.</p>
    </div>

    <div class="notice">
      <span>📄</span>
      <span>For detailed step-by-step screenshots, install troubleshooting, and keyboard reference, open <strong>YouTube-Caption-Copier-User-Guide.docx</strong> included in your download.</span>
    </div>
  </div>
</section>

<!-- ─── FEEDBACK ───────────────────────────────────────────────────────────── -->
<section id="feedback">
  <div class="wrap">
    <div class="feedback-box">
      <p class="section-label" style="text-align:center;">Get in Touch</p>
      <h2>We'd love to hear from you</h2>
      <p>Found a bug, have a feature idea, or YouTube changed something and it broke? Send us a message. We may get back to you if we have updates on your request.</p>

      <div style="display:flex; flex-direction:column; align-items:center; gap:10px; margin-bottom:4px;">
        <a href="mailto:btkrv1@gmail.com" class="email-chip">
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none"><rect x="1" y="3" width="14" height="10" rx="2" stroke="#888" stroke-width="1.5"/><path d="M1 5l7 5 7-5" stroke="#888" stroke-width="1.5"/></svg>
          btkrv1@gmail.com
        </a>
      </div>

      <div class="feedback-types">
        <span class="chip">🐛 Bug report</span>
        <span class="chip">💡 Feature request</span>
        <span class="chip">🔧 Something broke after a YouTube update</span>
        <span class="chip">🔐 Security concern</span>
      </div>

      <div style="margin-top:36px; padding-top:32px; border-top:1px solid rgba(255,255,255,0.07); text-align:center;">
        <p style="font-family:'Syne',sans-serif; font-size:18px; font-weight:700; color:var(--text); margin-bottom:10px;">&#9829; Support this tool</p>
        <p style="font-size:14px; color:var(--muted); max-width:420px; margin:0 auto 20px; line-height:1.65;">This tool is free and always will be. If it saves you time, consider making a small donation — it helps keep the tool maintained and improving.</p>
        <a href="https://razorpay.me/@codemayker" target="_blank" rel="noopener" class="btn btn-primary" style="display:inline-flex;align-items:center;gap:8px;text-decoration:none;">
          &#9829; Support via Razorpay
        </a>
      </div>

      <div style="margin-top:36px; padding-top:32px; border-top:1px solid rgba(255,255,255,0.07); text-align:center;">
        <p style="font-family:'Syne',sans-serif; font-size:18px; font-weight:700; color:var(--text); margin-bottom:10px;">📢 Share it</p>
        <p style="font-size:14px; color:var(--muted); max-width:420px; margin:0 auto 20px; line-height:1.65;">If this tool saved you time, share it with someone who would find it useful. That is the best way to support a free tool.</p>
        <div style="display:flex; gap:10px; justify-content:center; flex-wrap:wrap;">
          <a href="https://twitter.com/intent/tweet?text=Free+Chrome+extension+that+lets+you+copy+text+from+YouTube+videos+by+dragging+over+it.+Press+P+to+lock+the+frame%2C+drag+to+select%2C+Ctrl%2BC+to+copy.+No+retyping+ever.&url=https%3A%2F%2Fcodemayker.github.io%2Fyt-caption-copier%2F" target="_blank" rel="noopener" class="btn btn-ghost" style="text-decoration:none;font-size:13px;">
            Share on X / Twitter
          </a>
          <a href="https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Fcodemayker.github.io%2Fyt-caption-copier%2F" target="_blank" rel="noopener" class="btn btn-ghost" style="text-decoration:none;font-size:13px;">
            Share on LinkedIn
          </a>
          <a href="https://wa.me/?text=Free+Chrome+extension+to+copy+text+from+YouTube+videos+%E2%80%94+press+P+to+freeze+the+frame+and+drag+to+select.+https%3A%2F%2Fcodemayker.github.io%2Fyt-caption-copier%2F" target="_blank" rel="noopener" class="btn btn-ghost" style="text-decoration:none;font-size:13px;">
            Share on WhatsApp
          </a>
        </div>
      </div>

    </div>
  </div>
</section>

<!-- ─── FOOTER ─────────────────────────────────────────────────────────────── -->
<footer>
  <div class="wrap">
    <p style="margin-bottom:8px;">
      <strong style="color:var(--text);">YouTube Caption Copier</strong> &nbsp;·&nbsp; v1.0 &nbsp;·&nbsp;
      MIT License &nbsp;·&nbsp; Free &amp; Open Source &nbsp;·&nbsp;
      <a href="mailto:btkrv1@gmail.com">btkrv1@gmail.com</a>
    </p>
    <p>No trackers. No ads. No nonsense. Built to solve one small problem really well.</p>
  </div>
</footer>

</body>
</html>
