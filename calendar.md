---
layout: default
title: Family Calendar
---

<style>
/* ─── RESET & BASE ─────────────────────────────────────────────────────────── */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

#cal-root {
  position: fixed;
  top: 50px;          /* clear the site nav bar */
  left: 0; right: 0; bottom: 0;
  display: flex;
  flex-direction: column;
  background: #0d1117;
  color: #e6edf3;
  font-family: 'Segoe UI', system-ui, sans-serif;
  overflow: hidden;
  touch-action: pan-y;
}

/* ─── TOP TOOLBAR ──────────────────────────────────────────────────────────── */
#cal-toolbar {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 16px;
  background: #161b22;
  border-bottom: 1px solid #30363d;
  flex-shrink: 0;
  flex-wrap: wrap;
}

#cal-title {
  font-size: 1.15rem;
  font-weight: 700;
  flex: 1;
  white-space: nowrap;
  min-width: 160px;
}

.tb-btn {
  background: #21262d;
  border: 1px solid #30363d;
  color: #e6edf3;
  border-radius: 8px;
  padding: 7px 14px;
  font-size: 0.85rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s, border-color 0.15s;
  white-space: nowrap;
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}
.tb-btn:hover, .tb-btn:active { background: #30363d; border-color: #58a6ff; }
.tb-btn.today-btn { color: #58a6ff; border-color: #388bfd55; }

/* view toggle */
#view-toggle { display: flex; gap: 4px; }
.view-btn { padding: 7px 12px; }
.view-btn.active { background: #1f6feb; border-color: #388bfd; color: #fff; }

/* ─── CALENDAR LEGEND ──────────────────────────────────────────────────────── */
#cal-legend {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  padding: 8px 16px;
  background: #161b22;
  border-bottom: 1px solid #30363d;
  flex-shrink: 0;
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 5px;
  font-size: 0.75rem;
  opacity: 0.75;
}
.legend-dot {
  width: 9px; height: 9px;
  border-radius: 50%;
  flex-shrink: 0;
}

/* ─── LOADING / ERROR ──────────────────────────────────────────────────────── */
#cal-message {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex: 1;
  gap: 16px;
  padding: 40px;
  text-align: center;
}

.spinner {
  width: 36px; height: 36px;
  border: 3px solid rgba(88,166,255,0.2);
  border-top-color: #58a6ff;
  border-radius: 50%;
  animation: spin 0.75s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }

#cal-message p { font-size: 0.9rem; opacity: 0.6; line-height: 1.6; max-width: 420px; }
#cal-message code {
  background: #21262d; border: 1px solid #30363d;
  border-radius: 6px; padding: 2px 7px; font-size: 0.8rem;
}

/* setup form inside message */
#setup-form { display: flex; flex-direction: column; gap: 10px; width: 100%; max-width: 480px; }
#setup-form input {
  background: #21262d; border: 1px solid #30363d;
  border-radius: 8px; padding: 10px 14px;
  color: #e6edf3; font-size: 0.9rem; outline: none;
  transition: border-color 0.2s;
}
#setup-form input:focus { border-color: #58a6ff; }
#setup-form input::placeholder { opacity: 0.35; }
#setup-form .row { display: flex; gap: 8px; }
.save-btn {
  background: #1f6feb; border: 1px solid #388bfd;
  color: #fff; border-radius: 8px; padding: 10px 18px;
  font-size: 0.9rem; font-weight: 700; cursor: pointer;
  white-space: nowrap; transition: background 0.15s;
}
.save-btn:hover { background: #388bfd; }

/* ─── WEEK GRID ─────────────────────────────────────────────────────────────── */
#cal-grid {
  display: none;          /* shown when data loads */
  flex-direction: column;
  flex: 1;
  overflow: hidden;
}

/* Day header row */
#day-headers {
  display: grid;
  grid-template-columns: 52px repeat(7, 1fr);
  border-bottom: 1px solid #30363d;
  flex-shrink: 0;
}

.day-header {
  padding: 8px 4px;
  text-align: center;
  font-size: 0.78rem;
  font-weight: 600;
  border-left: 1px solid #30363d;
  user-select: none;
}
.day-header:first-child { border-left: none; }
.day-header .dow  { opacity: 0.5; text-transform: uppercase; letter-spacing: 0.5px; font-size: 0.7rem; }
.day-header .dom  { font-size: 1.1rem; font-weight: 700; margin-top: 2px; }
.day-header.today .dom {
  background: #1f6feb;
  color: #fff;
  border-radius: 50%;
  width: 30px; height: 30px;
  display: inline-flex; align-items: center; justify-content: center;
}

/* All-day banner */
#allday-row {
  display: grid;
  grid-template-columns: 52px repeat(7, 1fr);
  border-bottom: 2px solid #30363d;
  min-height: 28px;
  flex-shrink: 0;
}
.allday-gutter { border-right: 1px solid #30363d; display: flex; align-items: center; justify-content: center; }
.allday-gutter span { font-size: 0.6rem; opacity: 0.35; writing-mode: vertical-rl; }
.allday-cell {
  border-left: 1px solid #30363d;
  padding: 2px 3px;
  display: flex;
  flex-direction: column;
  gap: 2px;
  min-height: 24px;
}

/* Scrollable time grid */
#time-scroll {
  flex: 1;
  overflow-y: auto;
  overflow-x: hidden;
  scrollbar-width: thin;
  scrollbar-color: #30363d transparent;
  position: relative;
}
#time-scroll::-webkit-scrollbar { width: 6px; }
#time-scroll::-webkit-scrollbar-thumb { background: #30363d; border-radius: 3px; }

#time-grid {
  display: grid;
  grid-template-columns: 52px repeat(7, 1fr);
  position: relative;
}

/* Hour rows */
.hour-label {
  font-size: 0.65rem;
  opacity: 0.35;
  text-align: right;
  padding-right: 8px;
  padding-top: 2px;
  height: 56px;
  border-right: 1px solid #30363d;
  flex-shrink: 0;
  user-select: none;
}

.day-col {
  border-left: 1px solid #21262d;
  position: relative;
  height: calc(56px * 24);  /* 24 hours × 56px per hour */
}
.day-col.today-col { background: rgba(31,111,235,0.04); }

/* Hour lines inside day columns */
.day-col::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: repeating-linear-gradient(
    to bottom,
    transparent,
    transparent calc(56px - 1px),
    #21262d calc(56px - 1px),
    #21262d 56px
  );
  pointer-events: none;
}

/* Half-hour dashed lines */
.day-col::after {
  content: '';
  position: absolute;
  inset: 0;
  background-image: repeating-linear-gradient(
    to bottom,
    transparent,
    transparent calc(28px - 1px),
    #1c2128 calc(28px - 1px),
    #1c2128 28px,
    transparent 28px,
    transparent 56px
  );
  pointer-events: none;
}

/* ─── NOW LINE ──────────────────────────────────────────────────────────────── */
#now-line {
  position: absolute;
  left: 52px; right: 0;
  height: 2px;
  background: #f85149;
  z-index: 10;
  pointer-events: none;
}
#now-line::before {
  content: '';
  position: absolute;
  left: -1px; top: -4px;
  width: 10px; height: 10px;
  border-radius: 50%;
  background: #f85149;
}

/* ─── EVENTS ────────────────────────────────────────────────────────────────── */
.cal-event {
  position: absolute;
  left: 3px; right: 3px;
  border-radius: 5px;
  padding: 3px 6px;
  font-size: 0.72rem;
  font-weight: 600;
  line-height: 1.3;
  overflow: hidden;
  cursor: default;
  z-index: 2;
  border-left: 3px solid rgba(0,0,0,0.25);
  -webkit-tap-highlight-color: transparent;
}
.cal-event .ev-title { white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.cal-event .ev-time  { opacity: 0.75; font-size: 0.65rem; font-weight: 400; }

.allday-event {
  border-radius: 4px;
  padding: 2px 5px;
  font-size: 0.7rem;
  font-weight: 600;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  cursor: default;
}

/* ─── MONTH VIEW ────────────────────────────────────────────────────────────── */
#month-grid {
  display: none;
  flex-direction: column;
  flex: 1;
  overflow: hidden;
}

#month-dow-headers {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  border-bottom: 1px solid #30363d;
  flex-shrink: 0;
}
.month-dow {
  text-align: center;
  font-size: 0.72rem;
  font-weight: 700;
  opacity: 0.45;
  padding: 7px 0;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

#month-cells {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  flex: 1;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: #30363d transparent;
}
#month-cells::-webkit-scrollbar { width: 6px; }
#month-cells::-webkit-scrollbar-thumb { background: #30363d; border-radius: 3px; }

.month-cell {
  border-right: 1px solid #21262d;
  border-bottom: 1px solid #21262d;
  padding: 5px 5px 4px;
  min-height: 80px;
  position: relative;
  overflow: hidden;
}
.month-cell:nth-child(7n) { border-right: none; }
.month-cell .mc-num {
  font-size: 0.8rem;
  font-weight: 700;
  opacity: 0.5;
  margin-bottom: 3px;
  display: inline-block;
  width: 24px; height: 24px;
  line-height: 24px;
  text-align: center;
  border-radius: 50%;
}
.month-cell.today .mc-num { background: #1f6feb; color: #fff; opacity: 1; }
.month-cell.other-month { opacity: 0.35; }
.month-cell .mc-events { display: flex; flex-direction: column; gap: 2px; }
.mc-event-pill {
  font-size: 0.66rem;
  font-weight: 600;
  padding: 1px 5px;
  border-radius: 3px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  cursor: default;
}
.mc-more { font-size: 0.65rem; opacity: 0.45; padding-left: 2px; }

/* ─── SWIPE HINT ──────────────────────────────────────────────────────────── */
#swipe-hint {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0,0,0,0.6);
  border: 1px solid #30363d;
  border-radius: 20px;
  padding: 6px 14px;
  font-size: 0.75rem;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.4s;
  z-index: 50;
}
</style>

<div id="cal-root">

  <div id="cal-toolbar">
    <div id="cal-title">Loading…</div>
    <button class="tb-btn today-btn" onclick="goToToday()">Today</button>
    <button class="tb-btn" onclick="navWeek(-1)">&#8249;</button>
    <button class="tb-btn" onclick="navWeek(1)">&#8250;</button>
    <div id="view-toggle">
      <button class="view-btn tb-btn active" onclick="setView('week')">Week</button>
      <button class="view-btn tb-btn" onclick="setView('month')">Month</button>
    </div>
  </div>

  <div id="cal-legend"></div>

  <!-- Loading / setup state -->
  <div id="cal-message">
    <div class="spinner"></div>
    <p>Loading calendar…</p>
  </div>

  <!-- Week view -->
  <div id="cal-grid">
    <div id="day-headers">
      <div style="border-right:1px solid #30363d"></div>
      <!-- 7 day headers injected by JS -->
    </div>
    <div id="allday-row">
      <div class="allday-gutter"><span>ALL DAY</span></div>
      <!-- 7 all-day cells injected by JS -->
    </div>
    <div id="time-scroll">
      <div id="now-line" style="display:none"></div>
      <div id="time-grid">
        <!-- hour labels + day columns injected by JS -->
      </div>
    </div>
  </div>

  <!-- Month view -->
  <div id="month-grid">
    <div id="month-dow-headers">
      <div class="month-dow">Sun</div><div class="month-dow">Mon</div>
      <div class="month-dow">Tue</div><div class="month-dow">Wed</div>
      <div class="month-dow">Thu</div><div class="month-dow">Fri</div>
      <div class="month-dow">Sat</div>
    </div>
    <div id="month-cells"></div>
  </div>

</div>

<div id="swipe-hint">← swipe to change week →</div>

<script>
// ═══════════════════════════════════════════════════════════════════════════════
//  CONFIG  — paste your Apps Script web app URL here after deploying
// ═══════════════════════════════════════════════════════════════════════════════
const SCRIPT_URL_KEY = 'calendarScriptUrl';
let SCRIPT_URL = localStorage.getItem(SCRIPT_URL_KEY) || '';

// ─── Calendar colors (fallback if not returned by API) ────────────────────────
const CAL_COLORS = {
  'piguy314piguy@gmail.com':                                '#039be5',
  'en.usa#holiday@group.v.calendar.google.com':            '#0b8043',
  'brandonhorn@gmail.com':                                  '#c0ca33',
  'family13948944454896787216@group.calendar.google.com':  '#f6bf26',
  'kg1m0tek15fk2vf3d3e64igdrs@group.calendar.google.com': '#7cb342',
  'n4nis82qhjn11a39kn9giabg28@group.calendar.google.com': '#ad1457',
};

// ═══════════════════════════════════════════════════════════════════════════════
//  STATE
// ═══════════════════════════════════════════════════════════════════════════════
const PX_PER_HOUR = 56;
let viewMode    = 'month';  // 'week' | 'month'
let weekOffset  = 0;       // weeks relative to current
let monthOffset = 0;       // months relative to current
let allEvents   = [];
let calMeta     = [];

// ─── Touch swipe tracking ─────────────────────────────────────────────────────
let touchStartX = null;
document.addEventListener('touchstart', e => { touchStartX = e.touches[0].clientX; }, { passive: true });
document.addEventListener('touchend', e => {
  if (touchStartX === null) return;
  const dx = e.changedTouches[0].clientX - touchStartX;
  touchStartX = null;
  if (Math.abs(dx) < 60) return;
  if (viewMode === 'week') navWeek(dx < 0 ? 1 : -1);
  else navMonth(dx < 0 ? 1 : -1);
}, { passive: true });

// ═══════════════════════════════════════════════════════════════════════════════
//  DATE HELPERS
// ═══════════════════════════════════════════════════════════════════════════════
function startOfWeek(date) {
  const d = new Date(date);
  d.setHours(0, 0, 0, 0);
  d.setDate(d.getDate() - d.getDay()); // Sunday
  return d;
}

function addDays(date, n) {
  const d = new Date(date);
  d.setDate(d.getDate() + n);
  return d;
}

function sameDay(a, b) {
  return a.getFullYear() === b.getFullYear() &&
         a.getMonth()    === b.getMonth()    &&
         a.getDate()     === b.getDate();
}

function fmtTime(date) {
  let h = date.getHours(), m = date.getMinutes();
  const ampm = h >= 12 ? 'pm' : 'am';
  h = h % 12 || 12;
  return m === 0 ? `${h}${ampm}` : `${h}:${String(m).padStart(2,'0')}${ampm}`;
}

function fmtDateRange(start, days) {
  const end = addDays(start, days - 1);
  const mo  = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  if (start.getMonth() === end.getMonth())
    return `${mo[start.getMonth()]} ${start.getDate()}–${end.getDate()}, ${start.getFullYear()}`;
  return `${mo[start.getMonth()]} ${start.getDate()} – ${mo[end.getMonth()]} ${end.getDate()}, ${end.getFullYear()}`;
}

// ═══════════════════════════════════════════════════════════════════════════════
//  DATA FETCHING
// ═══════════════════════════════════════════════════════════════════════════════
async function fetchEvents() {
  if (!SCRIPT_URL) {
    showSetupUI();
    return;
  }

  showLoading('Fetching your calendars…');

  // Fetch a wide window (3 months) and cache client-side
  const now      = new Date();
  const timeMin  = addDays(now, -30).toISOString();
  const timeMax  = addDays(now,  90).toISOString();
  const url      = `${SCRIPT_URL}?timeMin=${encodeURIComponent(timeMin)}&timeMax=${encodeURIComponent(timeMax)}`;

  try {
    const res  = await fetch(url);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();

    if (data.error) throw new Error(data.error);

    allEvents = data.events  || [];
    calMeta   = data.calendars || [];

    buildLegend();
    renderCurrentView();
    hideMessage();
    startNowLineTicker();

  } catch (err) {
    showError(`Could not load calendar data.<br><br>
      <strong>Error:</strong> ${err.message}<br><br>
      Check that your Apps Script is deployed as a web app with access set to <code>Anyone</code>,
      and that the URL below is correct.`);
  }
}

// ─── Legend ───────────────────────────────────────────────────────────────────
function buildLegend() {
  const el = document.getElementById('cal-legend');
  el.innerHTML = '';
  const list = calMeta.length ? calMeta : Object.entries(CAL_COLORS).map(([id,color]) => ({ id, name: id.split('@')[0], color, accessible: true }));
  list.filter(c => c.accessible !== false).forEach(cal => {
    el.innerHTML += `<div class="legend-item">
      <div class="legend-dot" style="background:${cal.color}"></div>
      <span>${cal.name}</span>
    </div>`;
  });
}

// ═══════════════════════════════════════════════════════════════════════════════
//  NAVIGATION
// ═══════════════════════════════════════════════════════════════════════════════
function navWeek(delta)  { weekOffset  += delta; renderWeekView(); }
function navMonth(delta) { monthOffset += delta; renderMonthView(); }

function goToToday() {
  weekOffset  = 0;
  monthOffset = 0;
  renderCurrentView();
}

function setView(mode) {
  viewMode = mode;
  document.querySelectorAll('.view-btn').forEach(b => b.classList.remove('active'));
  document.querySelector(`.view-btn[onclick="setView('${mode}')"]`).classList.add('active');
  renderCurrentView();
}

function renderCurrentView() {
  if (viewMode === 'week') renderWeekView();
  else renderMonthView();
}

// ═══════════════════════════════════════════════════════════════════════════════
//  WEEK VIEW
// ═══════════════════════════════════════════════════════════════════════════════
function renderWeekView() {
  document.getElementById('cal-grid').style.display   = 'flex';
  document.getElementById('month-grid').style.display = 'none';

  const today     = new Date();
  today.setHours(0,0,0,0);
  const weekStart = addDays(startOfWeek(today), weekOffset * 7);
  const days      = Array.from({ length: 7 }, (_, i) => addDays(weekStart, i));

  // ── Title
  document.getElementById('cal-title').textContent = fmtDateRange(weekStart, 7);

  // ── Day headers
  const daysOfWeek = ['Sun','Mon','Tue','Wed','Thu','Fri','Sat'];
  const hdrs = document.getElementById('day-headers');
  hdrs.innerHTML = '<div style="border-right:1px solid #30363d"></div>';
  days.forEach(day => {
    const isToday = sameDay(day, today);
    hdrs.innerHTML += `<div class="day-header${isToday ? ' today' : ''}">
      <div class="dow">${daysOfWeek[day.getDay()]}</div>
      <div class="dom">${day.getDate()}</div>
    </div>`;
  });

  // ── All-day row
  const alldayRow = document.getElementById('allday-row');
  alldayRow.innerHTML = '<div class="allday-gutter"><span>ALL DAY</span></div>';
  days.forEach(day => {
    const cell = document.createElement('div');
    cell.className = 'allday-cell';
    const dayEvents = allEvents.filter(ev => ev.allDay && sameDay(new Date(ev.start), day));
    dayEvents.forEach(ev => {
      const pill = document.createElement('div');
      pill.className = 'allday-event';
      pill.textContent = ev.title;
      pill.style.background = ev.color + '33';
      pill.style.color       = ev.color;
      pill.style.borderLeft  = `3px solid ${ev.color}`;
      cell.appendChild(pill);
    });
    alldayRow.appendChild(cell);
  });

  // ── Time grid
  const grid = document.getElementById('time-grid');
  grid.innerHTML = '';

  // Hour labels column
  const HOUR_LABELS = ['12am','1am','2am','3am','4am','5am','6am','7am','8am','9am','10am','11am',
                        '12pm','1pm','2pm','3pm','4pm','5pm','6pm','7pm','8pm','9pm','10pm','11pm'];
  const labelCol = document.createElement('div');
  labelCol.style.cssText = 'grid-column:1; display:flex; flex-direction:column;';
  HOUR_LABELS.forEach(lbl => {
    const el = document.createElement('div');
    el.className = 'hour-label';
    el.textContent = lbl;
    labelCol.appendChild(el);
  });
  grid.appendChild(labelCol);

  // Day columns
  days.forEach((day, colIdx) => {
    const col = document.createElement('div');
    col.className = 'day-col' + (sameDay(day, today) ? ' today-col' : '');
    col.style.gridColumn = String(colIdx + 2);

    // Place timed events
    const timedEvents = allEvents.filter(ev =>
      !ev.allDay &&
      sameDay(new Date(ev.start), day)
    );

    timedEvents.forEach(ev => {
      const start   = new Date(ev.start);
      const end     = new Date(ev.end);
      const startMin = start.getHours() * 60 + start.getMinutes();
      const durMin   = Math.max(30, (end - start) / 60000); // min 30px height
      const top      = (startMin / 60) * PX_PER_HOUR;
      const height   = (durMin   / 60) * PX_PER_HOUR - 2;

      const el = document.createElement('div');
      el.className = 'cal-event';
      el.style.cssText = `top:${top}px; height:${height}px; background:${ev.color}22; color:${ev.color}; border-left-color:${ev.color};`;
      el.innerHTML = `<div class="ev-title">${ev.title}</div>
                      <div class="ev-time">${fmtTime(start)}${durMin > 45 ? ' – ' + fmtTime(end) : ''}</div>`;
      col.appendChild(el);
    });

    grid.appendChild(col);
  });

  // ── Now line
  positionNowLine(today, weekStart);

  // ── Scroll to business hours (8am)
  const scroll = document.getElementById('time-scroll');
  if (weekOffset === 0) {
    const now = new Date();
    const scrollTo = Math.max(0, ((now.getHours() - 1) * PX_PER_HOUR));
    scroll.scrollTop = scrollTo;
  } else {
    scroll.scrollTop = 8 * PX_PER_HOUR;
  }
}

function positionNowLine(today, weekStart) {
  const line    = document.getElementById('now-line');
  const now     = new Date();
  const dayIdx  = days => days.findIndex(d => sameDay(d, today));
  const weekEnd = addDays(weekStart, 6);

  if (now < weekStart || now > weekEnd) {
    line.style.display = 'none';
    return;
  }

  const col = (now.getDay() - weekStart.getDay() + 7) % 7; // 0-6 within week
  const min = now.getHours() * 60 + now.getMinutes();
  const top = (min / 60) * PX_PER_HOUR;

  // Position the line over the correct day column
  // Each col is 1fr; we approximate using percentage offset
  const colWidth = 100 / 7;
  line.style.display = 'block';
  line.style.top     = top + 'px';
  line.style.left    = `calc(52px + ${col * colWidth}%)`;
  line.style.right   = `calc(${(6 - col) * colWidth}%)`;
}

function startNowLineTicker() {
  setInterval(() => {
    const today    = new Date(); today.setHours(0,0,0,0);
    const weekStart = addDays(startOfWeek(today), weekOffset * 7);
    positionNowLine(today, weekStart);
  }, 60 * 1000);
}

// ═══════════════════════════════════════════════════════════════════════════════
//  MONTH VIEW
// ═══════════════════════════════════════════════════════════════════════════════
function renderMonthView() {
  document.getElementById('cal-grid').style.display   = 'none';
  document.getElementById('month-grid').style.display = 'flex';

  const today   = new Date();
  const refDate = new Date(today.getFullYear(), today.getMonth() + monthOffset, 1);
  const year    = refDate.getFullYear();
  const month   = refDate.getMonth();

  const mo = ['January','February','March','April','May','June',
               'July','August','September','October','November','December'];
  document.getElementById('cal-title').textContent = `${mo[month]} ${year}`;

  const firstDay   = new Date(year, month, 1);
  const lastDay    = new Date(year, month + 1, 0);
  const startDate  = addDays(firstDay, -firstDay.getDay()); // back to Sunday
  const totalCells = Math.ceil((firstDay.getDay() + lastDay.getDate()) / 7) * 7;

  const cells = document.getElementById('month-cells');
  cells.innerHTML = '';
  // Set grid rows dynamically
  cells.style.gridTemplateRows = `repeat(${totalCells / 7}, 1fr)`;

  for (let i = 0; i < totalCells; i++) {
    const day      = addDays(startDate, i);
    const isToday  = sameDay(day, today);
    const otherMo  = day.getMonth() !== month;

    const cell = document.createElement('div');
    cell.className = 'month-cell' + (isToday ? ' today' : '') + (otherMo ? ' other-month' : '');

    const dayEvs = allEvents.filter(ev => sameDay(new Date(ev.start), day));
    const maxShow = 3;
    const more    = Math.max(0, dayEvs.length - maxShow);

    cell.innerHTML = `<div class="mc-num">${day.getDate()}</div>
      <div class="mc-events">
        ${dayEvs.slice(0, maxShow).map(ev => `
          <div class="mc-event-pill" style="background:${ev.color}25;color:${ev.color};border-left:2px solid ${ev.color}">
            ${ev.allDay ? '' : fmtTime(new Date(ev.start)) + ' '}${ev.title}
          </div>`).join('')}
        ${more ? `<div class="mc-more">+${more} more</div>` : ''}
      </div>`;

    cells.appendChild(cell);
  }
}

// ═══════════════════════════════════════════════════════════════════════════════
//  UI STATE HELPERS
// ═══════════════════════════════════════════════════════════════════════════════
function showLoading(msg = 'Loading…') {
  document.getElementById('cal-message').style.display = 'flex';
  document.getElementById('cal-message').innerHTML = `<div class="spinner"></div><p>${msg}</p>`;
  document.getElementById('cal-grid').style.display   = 'none';
  document.getElementById('month-grid').style.display = 'none';
}

function hideMessage() {
  document.getElementById('cal-message').style.display = 'none';
}

function showError(html) {
  document.getElementById('cal-grid').style.display   = 'none';
  document.getElementById('month-grid').style.display = 'none';
  document.getElementById('cal-message').style.display = 'flex';
  document.getElementById('cal-message').innerHTML = `
    <p>⚠️ ${html}</p>
    <div id="setup-form">
      <input id="url-input" type="url" value="${SCRIPT_URL}" placeholder="https://script.google.com/macros/s/…/exec">
      <div class="row">
        <button class="save-btn" onclick="saveUrl()">Save & Retry</button>
      </div>
    </div>`;
}

function showSetupUI() {
  document.getElementById('cal-message').style.display = 'flex';
  document.getElementById('cal-message').innerHTML = `
    <p>📅 <strong>Connect your Google Calendars</strong><br><br>
    Deploy the <code>CalendarProxy.gs</code> Apps Script as a web app, then paste the URL here.
    Your calendars stay private — the script runs authenticated as you.</p>
    <div id="setup-form">
      <input id="url-input" type="url" placeholder="https://script.google.com/macros/s/…/exec">
      <div class="row">
        <button class="save-btn" onclick="saveUrl()">Connect</button>
      </div>
    </div>`;
}

function saveUrl() {
  const val = document.getElementById('url-input')?.value.trim();
  if (!val || !val.startsWith('https://script.google.com')) {
    alert('Please enter a valid Apps Script web app URL\n(starts with https://script.google.com/macros/s/…)');
    return;
  }
  SCRIPT_URL = val;
  localStorage.setItem(SCRIPT_URL_KEY, val);
  fetchEvents();
}

// ═══════════════════════════════════════════════════════════════════════════════
//  INIT
// ═══════════════════════════════════════════════════════════════════════════════
fetchEvents();

// Refresh data every 15 minutes quietly in the background
setInterval(() => {
  if (SCRIPT_URL) fetchEvents();
}, 15 * 60 * 1000);
</script>
