<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Alarm Clock</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: 'Segoe UI', system-ui, sans-serif;
      background: #060d1a;
      color: #dbeafe;
      height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: stretch;
      padding: 1.5rem 1.5rem 1.5rem;
      overflow: hidden;
    }

    h1 {
      font-size: 1.75rem;
      font-weight: 700;
      color: #3b82f6;
      margin-bottom: 0.2rem;
      letter-spacing: -0.5px;
    }

    .subtitle {
      color: #64748b;
      font-size: 0.8125rem;
      margin-bottom: 1rem;
    }

    .clock-display {
      font-size: 3rem;
      font-weight: 700;
      font-variant-numeric: tabular-nums;
      color: #f1f5f9;
      background: #0d1f3c;
      border: 1px solid #1a3256;
      border-radius: 1rem;
      padding: 0.75rem 1.5rem;
      margin-bottom: 1.25rem;
      letter-spacing: 2px;
      align-self: flex-start;
    }

    /* ── Two-column layout ── */
    .layout {
      display: grid;
      grid-template-columns: 340px 1fr;
      gap: 1.5rem;
      width: 100%;
      flex: 1;
      min-height: 0;
      align-items: stretch;
    }

    .left-panel {
      display: flex;
      flex-direction: column;
      gap: 0;
      overflow-y: auto;
      scrollbar-width: thin;
      scrollbar-color: #1a3256 transparent;
    }

    .right-panel {
      background: #0a172e;
      border: 1px solid #1a3256;
      border-radius: 1rem;
      display: flex;
      flex-direction: column;
      min-height: 0;
      overflow: hidden;
    }

    .right-panel-header {
      padding: 1rem 1.25rem 0.75rem;
      border-bottom: 1px solid #1a3256;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-shrink: 0;
    }

    .right-panel-header h2 {
      font-size: 0.875rem;
      font-weight: 700;
      color: #60a5fa;
      text-transform: uppercase;
      letter-spacing: 0.08em;
    }

    .alarm-count-badge {
      font-size: 0.75rem;
      font-weight: 700;
      background: #1a3256;
      color: #60a5fa;
      padding: 0.2rem 0.6rem;
      border-radius: 9999px;
    }

    .right-panel-scroll {
      flex: 1;
      overflow-y: auto;
      padding: 1rem 1.25rem;
      display: flex;
      flex-direction: column;
      gap: 0.875rem;
      scrollbar-width: thin;
      scrollbar-color: #1a3256 transparent;
    }

    .right-panel-scroll::-webkit-scrollbar { width: 6px; }
    .right-panel-scroll::-webkit-scrollbar-track { background: transparent; }
    .right-panel-scroll::-webkit-scrollbar-thumb { background: #1a3256; border-radius: 9999px; }

    /* ── Mobile tab switcher (hidden on desktop) ── */
    .mobile-tabs {
      display: none;
      width: 100%;
      max-width: 600px;
      background: #0d1f3c;
      border: 1px solid #1a3256;
      border-radius: 0.75rem;
      padding: 0.3rem;
      margin-bottom: 1rem;
      gap: 0.3rem;
    }

    .mobile-tab {
      flex: 1;
      min-width: unset;
      padding: 0.6rem 0.5rem;
      font-size: 0.875rem;
      background: transparent;
      color: #4d7ab5;
      border: none;
      border-radius: 0.5rem;
      font-weight: 600;
      transition: background 0.15s, color 0.15s;
    }

    .mobile-tab.active {
      background: #1d6ff5;
      color: #fff;
    }

    @media (max-width: 820px) {
      body {
        height: 100dvh;
        overflow: hidden;
        padding: 1rem 1rem 0.75rem;
      }

      h1 { font-size: 1.5rem; }
      .subtitle { margin-bottom: 0.5rem; font-size: 0.8rem; }

      .mobile-tabs { display: flex; }

      .layout {
        grid-template-columns: 1fr;
        flex: 1;
        min-height: 0;
        gap: 0;
      }

      /* Hide/show panels via JS-toggled class */
      .left-panel  { display: none; overflow-y: auto; }
      .right-panel { display: flex; }

      .left-panel.mobile-visible  { display: flex; }
      .right-panel.mobile-hidden  { display: none; }

      /* Clock inside left panel: full width on mobile */
      .clock-display {
        align-self: stretch;
        text-align: center;
        font-size: 2.25rem;
        padding: 0.6rem 1rem;
        margin-bottom: 1rem;
      }

      /* Larger touch targets */
      button {
        padding: 0.75rem 1rem;
        font-size: 1rem;
        min-height: 44px;
      }

      input[type="time"],
      input[type="number"] {
        font-size: 1rem;
        padding: 0.65rem 0.75rem;
        min-height: 44px;
      }

      .checkbox-row input[type="checkbox"] {
        width: 1.375rem;
        height: 1.375rem;
      }

      .alarm-actions button {
        padding: 0.6rem 0.875rem;
        font-size: 0.9rem;
        min-height: 40px;
      }

      .wakelock-bar { margin-bottom: 1rem; }
      .card { padding: 1.25rem; margin-bottom: 1rem; }
      .io-row { margin-bottom: 1rem; }
    }

    /* Extra-small phones */
    @media (max-width: 380px) {
      h1 { font-size: 1.25rem; }
      .clock-display { font-size: 1.875rem; }
      .alarm-time { font-size: 1.125rem; }
    }

    .card {
      background: #0d1f3c;
      border: 1px solid #1a3256;
      border-radius: 1rem;
      padding: 1.5rem;
      width: 100%;
      margin-bottom: 1.5rem;
    }

    .card h2 {
      font-size: 1rem;
      font-weight: 600;
      color: #94a3b8;
      text-transform: uppercase;
      letter-spacing: 0.05em;
      margin-bottom: 1.125rem;
    }

    .field {
      display: flex;
      flex-direction: column;
      gap: 0.375rem;
      margin-bottom: 1rem;
    }

    .field label {
      font-size: 0.8125rem;
      color: #94a3b8;
      font-weight: 500;
    }

    input[type="time"],
    input[type="number"] {
      background: #060d1a;
      border: 1px solid #1a3256;
      border-radius: 0.5rem;
      color: #f1f5f9;
      font-size: 1rem;
      padding: 0.5rem 0.75rem;
      width: 100%;
      outline: none;
      transition: border-color 0.15s;
    }

    input[type="time"]:focus,
    input[type="number"]:focus { border-color: #1d6ff5; }

    /* Repeat row: two side-by-side number inputs */
    .repeat-row {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 0.625rem;
    }

    .repeat-row .sub-field label {
      font-size: 0.75rem;
      color: #64748b;
      display: block;
      margin-bottom: 0.25rem;
    }

    /* Sound picker */
    .sound-picker {
      display: flex;
      align-items: center;
      gap: 0.625rem;
    }

    .sound-picker-btn {
      flex: none;
      min-width: unset !important;
      padding: 0.5rem 0.875rem !important;
      font-size: 0.875rem !important;
      background: #1a3256;
      color: #dbeafe;
      border-radius: 0.5rem;
      white-space: nowrap;
    }

    .sound-name {
      font-size: 0.8125rem;
      color: #64748b;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      flex: 1;
    }

    .sound-name.loaded { color: #60a5fa; }

    .sound-preview-btn {
      flex: none;
      min-width: unset !important;
      padding: 0.4rem 0.7rem !important;
      font-size: 0.8125rem !important;
      background: #0ea5e920;
      color: #38bdf8;
      border-radius: 0.5rem;
    }

    #sound-file-input, #import-file { display: none; }

    .checkbox-row {
      display: flex;
      align-items: center;
      gap: 0.625rem;
      margin-bottom: 1rem;
      cursor: pointer;
      user-select: none;
    }

    .checkbox-row input[type="checkbox"] {
      width: 1.125rem;
      height: 1.125rem;
      accent-color: #1d6ff5;
      cursor: pointer;
    }

    .checkbox-row span { font-size: 0.9375rem; color: #cbd5e1; }

    .btn-row {
      display: flex;
      gap: 0.75rem;
      flex-wrap: wrap;
    }

    button {
      flex: 1;
      min-width: 100px;
      padding: 0.625rem 1rem;
      border: none;
      border-radius: 0.5rem;
      font-size: 0.9375rem;
      font-weight: 600;
      cursor: pointer;
      transition: opacity 0.15s, transform 0.1s;
    }

    button:active { transform: scale(0.97); }

    .btn-primary   { background: #1d6ff5; color: #fff; }
    .btn-secondary { background: #1a3256; color: #dbeafe; }

    button:disabled { opacity: 0.4; cursor: not-allowed; }

    .io-row {
      display: flex;
      gap: 0.75rem;
      flex-wrap: wrap;
      width: 100%;
      margin-bottom: 1.5rem;
    }

    .io-row button {
      flex: 1;
      min-width: 100px;
      background: #1a3256;
      color: #dbeafe;
    }

    /* ── Alarm list (inside scrollable panel) ── */
    #alarm-list {
      display: contents;
    }

    .alarm-item {
      background: #0d1f3c;
      border: 1px solid #1a3256;
      border-radius: 0.875rem;
      padding: 1rem 1.25rem;
      display: flex;
      flex-direction: column;
      gap: 0.625rem;
    }

    .alarm-item.active  { border-color: #1d6ff5; box-shadow: 0 0 0 1px #1d6ff540; }
    .alarm-item.ringing { border-color: #f59e0b; animation: pulse 0.6s infinite alternate; }

    @keyframes pulse {
      from { box-shadow: 0 0 6px #f59e0b44; }
      to   { box-shadow: 0 0 22px #f59e0bbb; }
    }

    .alarm-top {
      display: flex;
      align-items: flex-start;
      gap: 0.875rem;
      flex-wrap: wrap;
    }

    .alarm-info { flex: 1; min-width: 140px; }

    .alarm-time {
      font-size: 1.375rem;
      font-weight: 700;
      font-variant-numeric: tabular-nums;
      color: #f1f5f9;
      line-height: 1.2;
    }

    .alarm-meta {
      font-size: 0.8rem;
      color: #64748b;
      margin-top: 0.2rem;
      line-height: 1.5;
    }

    /* Countdown bar */
    .countdown-wrap {
      display: flex;
      align-items: center;
      gap: 0.625rem;
    }

    .countdown-label {
      font-size: 0.8125rem;
      font-variant-numeric: tabular-nums;
      color: #60a5fa;
      white-space: nowrap;
      min-width: 90px;
      font-weight: 600;
    }

    .countdown-label.ringing-label { color: #fbbf24; }
    .countdown-label.done-label    { color: #475569; }

    .progress-bar {
      flex: 1;
      height: 5px;
      background: #1a3256;
      border-radius: 9999px;
      overflow: hidden;
    }

    .progress-fill {
      height: 100%;
      background: linear-gradient(90deg, #1d6ff5, #60a5fa);
      border-radius: 9999px;
      transition: width 0.5s linear;
    }

    .progress-fill.ringing { background: #f59e0b; width: 100% !important; }

    /* Status badge */
    .alarm-status {
      font-size: 0.75rem;
      font-weight: 700;
      padding: 0.25rem 0.7rem;
      border-radius: 9999px;
      white-space: nowrap;
      background: #1a3256;
      color: #94a3b8;
    }

    .alarm-status.on   { background: #1d6ff530; color: #60a5fa; }
    .alarm-status.ring { background: #f59e0b22; color: #fbbf24; }
    .alarm-status.off  { background: #0d1f3c;   color: #475569; border: 1px solid #1a3256; }

    /* Action buttons */
    .alarm-actions {
      display: flex;
      gap: 0.5rem;
      flex-wrap: wrap;
      align-items: center;
    }

    .alarm-actions button {
      flex: none;
      min-width: unset;
      padding: 0.35rem 0.75rem;
      font-size: 0.8125rem;
    }

    .dismiss-btn { background: #16a34a; color: #fff; }
    .cancel-btn  { background: #1e3a5f; color: #f97316; border: 1px solid #f9731640; }
    .delete-btn  { background: #0d1f3c; color: #4d7ab5; border: 1px solid #1a3256; }

    .empty-state {
      color: #475569;
      text-align: center;
      padding: 2.5rem;
      font-size: 0.9375rem;
    }

    .toast {
      position: fixed;
      bottom: 1.5rem;
      left: 50%;
      transform: translateX(-50%) translateY(120%);
      visibility: hidden;
      opacity: 0;
      background: #1d6ff5;
      color: #fff;
      padding: 0.625rem 1.375rem;
      border-radius: 0.5rem;
      font-size: 0.875rem;
      font-weight: 600;
      transition: transform 0.3s ease, opacity 0.3s ease, visibility 0s linear 0.3s;
      z-index: 100;
      white-space: nowrap;
    }

    .toast.show {
      transform: translateX(-50%) translateY(0);
      visibility: visible;
      opacity: 1;
      transition: transform 0.3s ease, opacity 0.3s ease, visibility 0s linear 0s;
    }

    /* Wake Lock */
    .wakelock-bar {
      display: flex;
      align-items: center;
      gap: 0.875rem;
      margin-bottom: 1.75rem;
    }

    .wakelock-btn {
      flex: none;
      min-width: unset;
      padding: 0.5rem 1.125rem;
      font-size: 0.875rem;
      background: #1a3256;
      color: #dbeafe;
      border: 1px solid #1d6ff540;
      border-radius: 0.5rem;
    }

    .wakelock-btn.active {
      background: #1d6ff530;
      color: #60a5fa;
      border-color: #1d6ff5;
      box-shadow: 0 0 8px #1d6ff540;
    }

    .wakelock-status {
      font-size: 0.8125rem;
      color: #4d7ab5;
    }

    .wakelock-status.active { color: #60a5fa; }
  </style>
</head>
<body>

<!-- Mobile tab switcher (hidden on desktop) -->
<div class="mobile-tabs">
  <button class="mobile-tab active" onclick="switchTab('left')">⚙️ Set Alarm</button>
  <button class="mobile-tab" onclick="switchTab('right')">🔔 Alarms <span id="mobile-badge"></span></button>
</div>

<!-- Two-column layout -->
<div class="layout">

  <!-- LEFT: controls -->
  <div class="left-panel">

    <h1>ALARM CLOCK</h1>
    <p class="subtitle">Set, loop, export, and import your alarms</p>

    <div class="clock-display" id="live-clock">--:--:--</div>

    <div class="wakelock-bar">
      <button id="wakelock-btn" class="wakelock-btn" onclick="toggleWakeLock()">☀️ Enable Wake Lock</button>
      <span class="wakelock-status" id="wakelock-status">Screen may sleep</span>
    </div>

    <div class="card">
      <h2>Add Alarm</h2>

      <div class="field">
        <label for="alarm-time">Alarm Time</label>
        <input type="time" id="alarm-time" step="1" />
      </div>

      <div class="field">
        <label>Repeat Interval (optional — leave both blank for one-shot)</label>
        <div class="repeat-row">
          <div class="sub-field">
            <label for="repeat-h">Hours</label>
            <input type="number" id="repeat-h" min="0" max="999" step="1" placeholder="0" />
          </div>
          <div class="sub-field">
            <label for="repeat-m">Minutes</label>
            <input type="number" id="repeat-m" min="0" max="59" step="1" placeholder="0" />
          </div>
        </div>
      </div>

      <div class="field">
        <label>Alarm Sound (uses alarm.wav by default)</label>
        <div class="sound-picker">
          <button class="sound-picker-btn" onclick="document.getElementById('sound-file-input').click()">
            Choose File
          </button>
          <span class="sound-name" id="sound-name-label">alarm.wav</span>
          <button class="sound-preview-btn" id="preview-btn" onclick="previewSound()">▶ Preview</button>
          <input type="file" id="sound-file-input" accept="audio/*" onchange="onSoundFileChange(event)" />
        </div>
      </div>

      <label class="checkbox-row">
        <input type="checkbox" id="loop-check" />
        <span>Loop alarm (keep repeating until dismissed)</span>
      </label>

      <div class="btn-row">
        <button class="btn-primary" onclick="addAlarm()">Add Alarm</button>
      </div>
    </div>

    <!-- Import / Export -->
    <div class="io-row">
      <button onclick="exportConfig()">Export JSON</button>
      <button onclick="document.getElementById('import-file').click()">Import JSON</button>
      <input type="file" id="import-file" accept=".json" onchange="importConfig(event)" />
    </div>

  </div><!-- /left-panel -->

  <!-- RIGHT: scrollable alarm list -->
  <div class="right-panel">
    <div class="right-panel-header">
      <h2>Active Alarms</h2>
      <span class="alarm-count-badge" id="alarm-count">0</span>
    </div>
    <div class="right-panel-scroll" id="alarm-list">
      <div class="empty-state">No alarms set. Add one on the left.</div>
    </div>
  </div>

</div><!-- /layout -->

<div class="toast" id="toast"></div>

<script>
// ── State ──────────────────────────────────────────────────────────────────
// alarm shape:
// { id, targetTime, repeatMs, loop, active, ringing, cancelled,
//   nextFire (Date), totalMs, soundDataUrl, soundName,
//   _soundTimer, _audioEl }

let alarms = [];
let audioCtx = null;

// Pending sound (null = use alarm.wav)
let pendingSoundDataUrl = null;
let pendingSoundName    = null;   // null means "alarm.wav"

const DEFAULT_SOUND = 'alarm.wav';

// ── Clock ──────────────────────────────────────────────────────────────────
function startClock() {
  function tick() {
    const now = new Date();
    document.getElementById('live-clock').textContent =
      now.toLocaleTimeString('en-US', { hour12: false });
    checkAlarms(now);
    updateCountdowns(now);
  }
  tick();
  setInterval(tick, 500);
}

// ── Sound file selection ───────────────────────────────────────────────────
function onSoundFileChange(event) {
  const file = event.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = e => {
    pendingSoundDataUrl = e.target.result;
    pendingSoundName    = file.name;
    const label = document.getElementById('sound-name-label');
    label.textContent = file.name;
    label.classList.add('loaded');
  };
  reader.readAsDataURL(file);
}

function previewSound() {
  const btn = document.getElementById('preview-btn');
  const src = pendingSoundDataUrl || DEFAULT_SOUND;
  const audio = new Audio(src);

  btn.disabled = true;
  btn.textContent = '⏹ Playing…';

  function reEnable() {
    btn.disabled = false;
    btn.textContent = '▶ Preview';
  }

  audio.addEventListener('ended', reEnable);
  audio.addEventListener('error', () => {
    reEnable();
    showToast('Cannot preview — place alarm.wav next to this file.');
  });

  audio.play().catch(() => {
    reEnable();
    showToast('Cannot preview — place alarm.wav next to this file.');
  });
}

// ── Synth beep (last-resort fallback) ─────────────────────────────────────
function getAudioCtx() {
  if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  return audioCtx;
}

function beep(freq = 880, duration = 0.22, volume = 0.5) {
  const ctx  = getAudioCtx();
  const osc  = ctx.createOscillator();
  const gain = ctx.createGain();
  osc.connect(gain);
  gain.connect(ctx.destination);
  osc.type = 'square';
  osc.frequency.value = freq;
  gain.gain.setValueAtTime(volume, ctx.currentTime);
  gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + duration);
  osc.start(ctx.currentTime);
  osc.stop(ctx.currentTime + duration);
}

function playBeepPattern(alarmId) {
  const alarm = alarms.find(a => a.id === alarmId);
  if (!alarm || !alarm.ringing) return;
  beep(880, 0.22, 0.5);
  setTimeout(() => beep(660, 0.22, 0.5), 280);
  setTimeout(() => beep(880, 0.22, 0.5), 560);
  alarm._soundTimer = setTimeout(() => playBeepPattern(alarmId), 1300);
}

// ── Sound playback ─────────────────────────────────────────────────────────
function startAlarmSound(alarm) {
  const src = alarm.soundDataUrl || DEFAULT_SOUND;
  const audio = new Audio(src);
  audio.loop = true;
  audio.play().then(() => {
    alarm._audioEl = audio;
  }).catch(() => {
    // alarm.wav not found or blocked — fall back to synth beep
    playBeepPattern(alarm.id);
  });
}

function stopAlarmSound(alarm) {
  clearTimeout(alarm._soundTimer);
  alarm._soundTimer = null;
  if (alarm._audioEl) {
    alarm._audioEl.pause();
    alarm._audioEl.currentTime = 0;
    alarm._audioEl = null;
  }
}

// ── Repeat interval helpers ────────────────────────────────────────────────
function readRepeatMs() {
  const h = parseInt(document.getElementById('repeat-h').value, 10) || 0;
  const m = parseInt(document.getElementById('repeat-m').value, 10) || 0;
  const ms = (h * 60 + m) * 60_000;
  return ms > 0 ? ms : null;
}

function repeatLabel(alarm) {
  if (!alarm.repeatMs && !alarm.loop) return 'One-shot';
  if (!alarm.repeatMs && alarm.loop)  return 'Loops daily';
  const totalMin = Math.round(alarm.repeatMs / 60_000);
  const h = Math.floor(totalMin / 60);
  const m = totalMin % 60;
  const parts = [];
  if (h) parts.push(`${h}h`);
  if (m) parts.push(`${m}m`);
  return `Every ${parts.join(' ')}`;
}

// ── Next fire computation ──────────────────────────────────────────────────
function computeNextFire(targetTime, fromDate) {
  const [h, m, s = 0] = targetTime.split(':').map(Number);
  const base = fromDate ? new Date(fromDate) : new Date();
  const fire  = new Date(base);
  fire.setHours(h, m, s, 0);
  if (fire <= base) fire.setDate(fire.getDate() + 1);
  return fire;
}

// ── Alarm check ────────────────────────────────────────────────────────────
function checkAlarms(now) {
  alarms.forEach(alarm => {
    if (!alarm.active || alarm.ringing) return;
    if (now >= alarm.nextFire) triggerAlarm(alarm);
  });
}

function triggerAlarm(alarm) {
  alarm.ringing = true;
  startAlarmSound(alarm);
  renderList();
}

// ── Add alarm ──────────────────────────────────────────────────────────────
function addAlarm() {
  const timeVal = document.getElementById('alarm-time').value;
  if (!timeVal) { showToast('Please set an alarm time.'); return; }

  const hVal = document.getElementById('repeat-h').value;
  const mVal = document.getElementById('repeat-m').value;
  // Reject only when the user explicitly entered values that both resolve to 0
  if ((hVal !== '' || mVal !== '') && (parseInt(hVal, 10) || 0) === 0 && (parseInt(mVal, 10) || 0) === 0) {
    showToast('Repeat interval must be greater than 0. Leave both blank for a one-shot alarm.');
    return;
  }

  const repeatMs = readRepeatMs();
  const loop     = document.getElementById('loop-check').checked;
  const nextFire = computeNextFire(timeVal);

  alarms.push({
    id: Date.now(),
    targetTime:   timeVal,
    repeatMs,
    loop,
    active:       true,
    ringing:      false,
    cancelled:    false,
    nextFire,
    totalMs:      nextFire - new Date(),
    soundDataUrl: pendingSoundDataUrl,
    soundName:    pendingSoundName,    // null = alarm.wav
    _soundTimer:  null,
    _audioEl:     null,
  });

  renderList();
  showToast('Alarm added!');
}

// ── Dismiss (after ringing) ────────────────────────────────────────────────
function dismissAlarm(id) {
  const alarm = alarms.find(a => a.id === id);
  if (!alarm) return;
  stopAlarmSound(alarm);
  alarm.ringing = false;

  if (alarm.repeatMs) {
    let next = new Date(alarm.nextFire.getTime() + alarm.repeatMs);
    if (next <= new Date()) next = new Date(Date.now() + alarm.repeatMs);
    alarm.nextFire = next;
    alarm.totalMs  = alarm.repeatMs;
    alarm.active   = true;
  } else if (alarm.loop) {
    alarm.nextFire = computeNextFire(alarm.targetTime, alarm.nextFire);
    alarm.totalMs  = alarm.nextFire - new Date();
    alarm.active   = true;
  } else {
    alarm.active = false;
  }
  renderList();
}

// ── Cancel (active, not yet ringing) ──────────────────────────────────────
function cancelAlarm(id) {
  const alarm = alarms.find(a => a.id === id);
  if (!alarm) return;
  stopAlarmSound(alarm);
  alarm.active    = false;
  alarm.ringing   = false;
  alarm.cancelled = true;
  renderList();
  showToast('Alarm cancelled.');
}

// ── Delete ─────────────────────────────────────────────────────────────────
function deleteAlarm(id) {
  const alarm = alarms.find(a => a.id === id);
  if (alarm) stopAlarmSound(alarm);
  alarms = alarms.filter(a => a.id !== id);
  renderList();
}

// ── Countdown display ──────────────────────────────────────────────────────
function formatCountdown(ms) {
  if (ms <= 0) return '0s';
  const totalSec = Math.floor(ms / 1000);
  const h = Math.floor(totalSec / 3600);
  const m = Math.floor((totalSec % 3600) / 60);
  const s = totalSec % 60;
  if (h > 0) return `${h}h ${String(m).padStart(2,'0')}m ${String(s).padStart(2,'0')}s`;
  if (m > 0) return `${m}m ${String(s).padStart(2,'0')}s`;
  return `${s}s`;
}

function updateCountdowns(now) {
  alarms.forEach(alarm => {
    const fill  = document.getElementById(`fill-${alarm.id}`);
    const label = document.getElementById(`label-${alarm.id}`);
    if (!fill || !label) return;

    if (alarm.ringing) {
      fill.className    = 'progress-fill ringing';
      label.className   = 'countdown-label ringing-label';
      label.textContent = '🔔 RINGING';
      return;
    }
    if (!alarm.active) {
      fill.style.width  = '0%';
      fill.className    = 'progress-fill';
      label.className   = 'countdown-label done-label';
      label.textContent = alarm.cancelled ? 'Cancelled' : 'Done';
      return;
    }
    const remaining = alarm.nextFire - now;
    const pct = Math.min(100, Math.max(0, 100 - (remaining / alarm.totalMs) * 100));
    fill.className    = 'progress-fill';
    fill.style.width  = pct + '%';
    label.className   = 'countdown-label';
    label.textContent = formatCountdown(remaining);
  });
}

// ── Render ─────────────────────────────────────────────────────────────────
function renderList() {
  const container = document.getElementById('alarm-list');
  const badge = document.getElementById('alarm-count');
  const mobileBadge = document.getElementById('mobile-badge');
  const active = alarms.filter(a => a.active || a.ringing).length;
  if (badge) badge.textContent = active;
  if (mobileBadge) mobileBadge.textContent = active > 0 ? `(${active})` : '';

  if (alarms.length === 0) {
    container.innerHTML = '<div class="empty-state">No alarms set. Add one on the left.</div>';
    return;
  }

  container.innerHTML = alarms.map(alarm => {
    const fireStr     = alarm.nextFire.toLocaleTimeString('en-US', { hour12: false });
    const fireDateStr = alarm.nextFire.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
    const soundLabel  = alarm.soundName ? `♪ ${alarm.soundName}` : `♪ alarm.wav`;

    const isRepeating = alarm.active && !alarm.ringing && (alarm.repeatMs || alarm.loop);
    const statusClass = alarm.ringing ? 'ring' : alarm.active ? 'on' : 'off';
    const statusText  = alarm.ringing   ? 'RINGING'
                      : isRepeating     ? 'Repeating'
                      : alarm.active    ? 'Active'
                      : alarm.cancelled ? 'Cancelled' : 'Done';
    const itemClass   = alarm.ringing ? 'alarm-item ringing'
                      : alarm.active  ? 'alarm-item active'
                      : 'alarm-item';

    const actionBtns = alarm.ringing
      ? `<button class="dismiss-btn" onclick="dismissAlarm(${alarm.id})">Dismiss</button>`
      : alarm.active
        ? `<button class="cancel-btn" onclick="cancelAlarm(${alarm.id})">Cancel</button>
           <button class="delete-btn" onclick="deleteAlarm(${alarm.id})">Delete</button>`
        : `<button class="delete-btn" onclick="deleteAlarm(${alarm.id})">Remove</button>`;

    return `
      <div class="${itemClass}" id="alarm-${alarm.id}">
        <div class="alarm-top">
          <div class="alarm-info">
            <div class="alarm-time">${alarm.targetTime}</div>
            <div class="alarm-meta">
              ${repeatLabel(alarm)} &bull; Next: ${fireStr} (${fireDateStr})<br>${soundLabel}
            </div>
          </div>
          <span class="alarm-status ${statusClass}">${statusText}</span>
          <div class="alarm-actions">${actionBtns}</div>
        </div>
        <div class="countdown-wrap">
          <span class="countdown-label" id="label-${alarm.id}">--</span>
          <div class="progress-bar">
            <div class="progress-fill" id="fill-${alarm.id}" style="width:0%"></div>
          </div>
        </div>
      </div>`;
  }).join('');

  updateCountdowns(new Date());
}

// ── Export / Import ────────────────────────────────────────────────────────
function exportConfig() {
  if (alarms.length === 0) { showToast('No alarms to export.'); return; }

  const data = alarms.map(({ id, targetTime, repeatMs, loop, active, soundName }) => ({
    id, targetTime, repeatMs, loop, active,
    soundName: soundName || null,
  }));

  const blob = new Blob([JSON.stringify({ alarms: data }, null, 2)], { type: 'application/json' });
  const url  = URL.createObjectURL(blob);
  const a    = document.createElement('a');
  a.href = url;
  a.download = `alarms-${new Date().toISOString().slice(0, 10)}.json`;
  a.click();
  URL.revokeObjectURL(url);
  showToast('Configuration exported!');
}

function importConfig(event) {
  const file = event.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = e => {
    try {
      const parsed = JSON.parse(e.target.result);
      if (!parsed.alarms || !Array.isArray(parsed.alarms)) throw new Error('bad');

      alarms.forEach(a => stopAlarmSound(a));
      alarms = [];

      parsed.alarms.forEach(cfg => {
        if (!cfg.targetTime) return;
        const nextFire = computeNextFire(cfg.targetTime);
        // Support both old (repeatHours) and new (repeatMs) format
        let repeatMs = cfg.repeatMs ?? null;
        if (!repeatMs && cfg.repeatHours) repeatMs = cfg.repeatHours * 3600_000;

        alarms.push({
          id:           cfg.id ?? (Date.now() + Math.random()),
          targetTime:   cfg.targetTime,
          repeatMs,
          loop:         cfg.loop    ?? false,
          active:       cfg.active  ?? true,
          ringing:      false,
          cancelled:    false,
          nextFire,
          totalMs:      nextFire - new Date(),
          soundDataUrl: null,
          soundName:    cfg.soundName ?? null,
          _soundTimer:  null,
          _audioEl:     null,
        });
      });

      renderList();
      const needsSound = alarms.some(a => a.soundName && a.soundName !== 'alarm.wav');
      showToast(`Imported ${alarms.length} alarm(s)!${needsSound ? ' Re-select custom sound files.' : ''}`);
    } catch {
      showToast('Import failed: invalid JSON.');
    }
    event.target.value = '';
  };
  reader.readAsText(file);
}

// ── Toast ──────────────────────────────────────────────────────────────────
function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 3200);
}

// ── Wake Lock ──────────────────────────────────────────────────────────────
let wakeLockSentinel = null;
let wakeLockEnabled  = false;  // user's intent, survives tab hide/show

async function toggleWakeLock() {
  if (wakeLockEnabled) {
    wakeLockEnabled = false;
    if (wakeLockSentinel) {
      await wakeLockSentinel.release();
      wakeLockSentinel = null;
    }
    setWakeLockUI(false);
  } else {
    wakeLockEnabled = true;
    await acquireWakeLock();
  }
}

async function acquireWakeLock() {
  if (!('wakeLock' in navigator)) {
    showToast('Wake Lock is not supported in this browser.');
    wakeLockEnabled = false;
    return;
  }
  try {
    wakeLockSentinel = await navigator.wakeLock.request('screen');
    setWakeLockUI(true);
    wakeLockSentinel.addEventListener('release', () => {
      wakeLockSentinel = null;
      // Only update UI to inactive if the user didn't explicitly keep it on
      if (!wakeLockEnabled) setWakeLockUI(false);
    });
  } catch (err) {
    wakeLockEnabled = false;
    setWakeLockUI(false);
    showToast(`Wake Lock failed: ${err.message}`);
  }
}

function setWakeLockUI(active) {
  const btn    = document.getElementById('wakelock-btn');
  const status = document.getElementById('wakelock-status');
  if (active) {
    btn.classList.add('active');
    btn.innerHTML = '🔆 Disable Wake Lock';
    status.textContent = 'Screen will stay awake';
    status.classList.add('active');
  } else {
    btn.classList.remove('active');
    btn.innerHTML = '☀️ Enable Wake Lock';
    status.textContent = 'Screen may sleep';
    status.classList.remove('active');
  }
}

// Re-acquire when tab becomes visible — browsers release the lock on hide
document.addEventListener('visibilitychange', () => {
  if (document.visibilityState === 'visible' && wakeLockEnabled && !wakeLockSentinel) {
    acquireWakeLock();
  }
});

// ── Mobile tab switcher ────────────────────────────────────────────────────
function switchTab(tab) {
  const left  = document.querySelector('.left-panel');
  const right = document.querySelector('.right-panel');
  const tabs  = document.querySelectorAll('.mobile-tab');

  if (tab === 'left') {
    left.classList.add('mobile-visible');
    right.classList.add('mobile-hidden');
    tabs[0].classList.add('active');
    tabs[1].classList.remove('active');
  } else {
    left.classList.remove('mobile-visible');
    right.classList.remove('mobile-hidden');
    tabs[0].classList.remove('active');
    tabs[1].classList.add('active');
  }
}

// ── Init ───────────────────────────────────────────────────────────────────
startClock();
</script>
</body>
</html>
