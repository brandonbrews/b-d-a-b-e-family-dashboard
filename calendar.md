---
layout: default
title: Family Calendar
---

<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

#cal-root {
  position: fixed;
  top: 0px;
  left: 0; right: 0; bottom: 0;
  display: flex;
  flex-direction: column;
  background: #0d1117;
  color: #e6edf3;
  font-family: 'Segoe UI', system-ui, sans-serif;
  overflow: hidden;
  touch-action: pan-y;
}

/* ─── TOOLBAR ── */
#cal-toolbar {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 20px;
  background: #161b22;
  border-bottom: 1px solid #30363d;
  flex-shrink: 0;
  flex-wrap: wrap;
}
#cal-title {
  font-size: 1.25rem;
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
  padding: 9px 18px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s, border-color 0.15s;
  white-space: nowrap;
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}
.tb-btn:hover, .tb-btn:active { background: #30363d; border-color: #58a6ff; }
.tb-btn.today-btn { color: #58a6ff; border-color: #388bfd55; }
#view-toggle { display: flex; gap: 4px; }
.view-btn { padding: 9px 16px; }
.view-btn.active { background: #1f6feb; border-color: #388bfd; color: #fff; }

/* ─── LEGEND ── */
#cal-legend {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  padding: 10px 20px;
  background: #161b22;
  border-bottom: 1px solid #30363d;
  flex-shrink: 0;
}
.legend-item { display: flex; align-items: center; gap: 6px; font-size: 0.82rem; opacity: 0.8; }
.legend-dot  { width: 10px; height: 10px; border-radius: 50%; flex-shrink: 0; }

/* ─── LOADING / SETUP ── */
#cal-message {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex: 1;
  gap: 18px;
  padding: 40px;
  text-align: center;
}
.spinner {
  width: 40px; height: 40px;
  border: 3px solid rgba(88,166,255,0.2);
  border-top-color: #58a6ff;
  border-radius: 50%;
  animation: spin 0.75s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
#cal-message p   { font-size: 0.95rem; opacity: 0.65; line-height: 1.7; max-width: 460px; }
#cal-message code { background: #21262d; border: 1px solid #30363d; border-radius: 6px; padding: 2px 7px; font-size: 0.82rem; }
#setup-form { display: flex; flex-direction: column; gap: 10px; width: 100%; max-width: 500px; }
#setup-form input {
  background: #21262d; border: 1px solid #30363d; border-radius: 8px;
  padding: 12px 16px; color: #e6edf3; font-size: 0.9rem; outline: none; transition: border-color 0.2s;
}
#setup-form input:focus { border-color: #58a6ff; }
#setup-form input::placeholder { opacity: 0.35; }
#setup-form .row { display: flex; gap: 8px; }
.save-btn {
  background: #1f6feb; border: 1px solid #388bfd; color: #fff;
  border-radius: 8px; padding: 12px 20px; font-size: 0.9rem; font-weight: 700;
  cursor: pointer; white-space: nowrap; touch-action: manipulation;
}
.save-btn:hover { background: #388bfd; }

/* ─── WEEK VIEW ── */
#cal-grid { display: none; flex-direction: column; flex: 1; overflow: hidden; }
#day-headers { display: grid; grid-template-columns: 56px repeat(7, 1fr); border-bottom: 1px solid #30363d; flex-shrink: 0; }
.day-header { padding: 10px 4px; text-align: center; font-size: 0.82rem; font-weight: 600; border-left: 1px solid #30363d; user-select: none; }
.day-header:first-child { border-left: none; }
.day-header .dow { opacity: 0.5; text-transform: uppercase; letter-spacing: 0.5px; font-size: 0.72rem; }
.day-header .dom { font-size: 1.2rem; font-weight: 700; margin-top: 3px; }
.day-header.today .dom { background: #1f6feb; color: #fff; border-radius: 50%; width: 34px; height: 34px; display: inline-flex; align-items: center; justify-content: center; }
#allday-row { display: grid; grid-template-columns: 56px repeat(7, 1fr); border-bottom: 2px solid #30363d; min-height: 30px; flex-shrink: 0; }
.allday-gutter { border-right: 1px solid #30363d; display: flex; align-items: center; justify-content: center; }
.allday-gutter span { font-size: 0.6rem; opacity: 0.35; writing-mode: vertical-rl; }
.allday-cell { border-left: 1px solid #30363d; padding: 2px 3px; display: flex; flex-direction: column; gap: 2px; min-height: 26px; }
#time-scroll { flex: 1; overflow-y: auto; overflow-x: hidden; scrollbar-width: thin; scrollbar-color: #30363d transparent; position: relative; }
#time-scroll::-webkit-scrollbar { width: 6px; }
#time-scroll::-webkit-scrollbar-thumb { background: #30363d; border-radius: 3px; }
#time-grid { display: grid; grid-template-columns: 56px repeat(7, 1fr); position: relative; }
.hour-label { font-size: 0.68rem; opacity: 0.35; text-align: right; padding-right: 9px; padding-top: 2px; height: 60px; border-right: 1px solid #30363d; flex-shrink: 0; user-select: none; }
.day-col { border-left: 1px solid #21262d; position: relative; height: calc(60px * 24); }
.day-col.today-col { background: rgba(31,111,235,0.04); }
.day-col::before { content: ''; position: absolute; inset: 0; background-image: repeating-linear-gradient(to bottom, transparent, transparent calc(60px - 1px), #21262d calc(60px - 1px), #21262d 60px); pointer-events: none; }
.day-col::after  { content: ''; position: absolute; inset: 0; background-image: repeating-linear-gradient(to bottom, transparent, transparent calc(30px - 1px), #1c2128 calc(30px - 1px), #1c2128 30px, transparent 30px, transparent 60px); pointer-events: none; }
#now-line { position: absolute; left: 56px; right: 0; height: 2px; background: #f85149; z-index: 10; pointer-events: none; }
#now-line::before { content: ''; position: absolute; left: -1px; top: -4px; width: 10px; height: 10px; border-radius: 50%; background: #f85149; }
.cal-event { position: absolute; left: 3px; right: 3px; border-radius: 5px; padding: 3px 7px; font-size: 0.75rem; font-weight: 600; line-height: 1.3; overflow: hidden; cursor: default; z-index: 2; border-left: 3px solid rgba(0,0,0,0.25); -webkit-tap-highlight-color: transparent; }
.cal-event .ev-title { white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.cal-event .ev-time  { opacity: 0.75; font-size: 0.68rem; font-weight: 400; }
.allday-event { border-radius: 4px; padding: 2px 6px; font-size: 0.72rem; font-weight: 600; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; cursor: default; }

/* ─── MONTH VIEW ── */
#month-grid { display: none; flex-direction: column; flex: 1; overflow: hidden; }
#month-dow-headers { display: grid; grid-template-columns: repeat(7, 1fr); border-bottom: 1px solid #30363d; flex-shrink: 0; }
.month-dow { text-align: center; font-size: 0.78rem; font-weight: 700; opacity: 0.45; padding: 10px 0; text-transform: uppercase; letter-spacing: 0.5px; }
#month-cells { display: grid; grid-template-columns: repeat(7, 1fr); flex: 1; overflow-y: auto; align-content: start; scrollbar-width: thin; scrollbar-color: #30363d transparent; }
#month-cells::-webkit-scrollbar { width: 6px; }
#month-cells::-webkit-scrollbar-thumb { background: #30363d; border-radius: 3px; }
.month-cell { border-right: 1px solid #21262d; border-bottom: 1px solid #21262d; padding: 7px 7px 6px; min-height: 130px; position: relative; overflow: hidden; }
.month-cell:nth-child(7n) { border-right: none; }
.month-cell .mc-num { font-size: 0.9rem; font-weight: 700; opacity: 0.5; margin-bottom: 5px; display: inline-flex; align-items: center; justify-content: center; width: 28px; height: 28px; border-radius: 50%; }
.month-cell.today .mc-num { background: #1f6feb; color: #fff; opacity: 1; }
.month-cell.other-month   { opacity: 0.35; }
.month-cell .mc-events    { display: flex; flex-direction: column; gap: 3px; }
.mc-event-pill { font-size: 0.72rem; font-weight: 600; padding: 2px 7px; border-radius: 4px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; cursor: default; line-height: 1.4; }
.mc-more { font-size: 0.7rem; opacity: 0.45; padding-left: 3px; margin-top: 1px; }
</style>

<div id="cal-root">

  <div id="cal-toolbar">
    <div id="cal-title">Loading…</div>
    <button class="tb-btn today-btn" onclick="goToToday()">Today</button>
    <button class="tb-btn" onclick="navBack()">&#8249;</button>
    <button class="tb-btn" onclick="navForward()">&#8250;</button>
    <div id="view-toggle">
      <button class="view-btn tb-btn" onclick="setView('week')">Week</button>
      <button class="view-btn tb-btn active" onclick="setView('month')">Month</button>
    </div>
  </div>

  <div id="cal-legend"></div>

  <div id="cal-message">
    <div class="spinner"></div>
    <p>Loading calendar…</p>
  </div>

  <div id="cal-grid">
    <div id="day-headers"><div style="border-right:1px solid #30363d"></div></div>
    <div id="allday-row"><div class="allday-gutter"><span>ALL DAY</span></div></div>
    <div id="time-scroll">
      <div id="now-line" style="display:none"></div>
      <div id="time-grid"></div>
    </div>
  </div>

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

<script>
const SCRIPT_URL_KEY = 'calendarScriptUrl';
let SCRIPT_URL = localStorage.getItem(SCRIPT_URL_KEY) || '';

const PX_PER_HOUR      = 60;
const MAX_MONTH_EVENTS = 5;   // fits the larger 27" cells

let viewMode    = 'month';    // ← default: month view
let weekOffset  = 0;
let monthOffset = 0;
let allEvents   = [];
let calMeta     = [];

// ─── Swipe ───────────────────────────────────────────────────────────────────
let touchStartX = null;
document.addEventListener('touchstart', e => { touchStartX = e.touches[0].clientX; }, { passive: true });
document.addEventListener('touchend', e => {
  if (touchStartX === null) return;
  const dx = e.changedTouches[0].clientX - touchStartX;
  touchStartX = null;
  if (Math.abs(dx) < 60) return;
  viewMode === 'week' ? navWeek(dx < 0 ? 1 : -1) : navMonth(dx < 0 ? 1 : -1);
}, { passive: true });

// ─── Date helpers ─────────────────────────────────────────────────────────────
function startOfWeek(d) { const r = new Date(d); r.setHours(0,0,0,0); r.setDate(r.getDate()-r.getDay()); return r; }
function addDays(d, n)  { const r = new Date(d); r.setDate(r.getDate()+n); return r; }
function sameDay(a, b)  { return a.getFullYear()===b.getFullYear() && a.getMonth()===b.getMonth() && a.getDate()===b.getDate(); }
function fmtTime(d)     { let h=d.getHours(), m=d.getMinutes(), ap=h>=12?'pm':'am'; h=h%12||12; return m===0?`${h}${ap}`:`${h}:${String(m).padStart(2,'0')}${ap}`; }
function fmtWeekRange(start) {
  const end=addDays(start,6), mo=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  return start.getMonth()===end.getMonth()
    ? `${mo[start.getMonth()]} ${start.getDate()}–${end.getDate()}, ${start.getFullYear()}`
    : `${mo[start.getMonth()]} ${start.getDate()} – ${mo[end.getMonth()]} ${end.getDate()}, ${end.getFullYear()}`;
}

// ─── Navigation ───────────────────────────────────────────────────────────────
function navBack()    { viewMode==='week' ? navWeek(-1)  : navMonth(-1); }
function navForward() { viewMode==='week' ? navWeek(1)   : navMonth(1);  }
function navWeek(d)   { weekOffset  += d; renderWeekView(); }
function navMonth(d)  { monthOffset += d; renderMonthView(); }
function goToToday()  { weekOffset=0; monthOffset=0; renderCurrentView(); }
function setView(mode) {
  viewMode = mode;
  document.querySelectorAll('.view-btn').forEach(b => b.classList.remove('active'));
  document.querySelector(`.view-btn[onclick="setView('${mode}')"]`).classList.add('active');
  renderCurrentView();
}
function renderCurrentView() { viewMode==='week' ? renderWeekView() : renderMonthView(); }

// ─── Fetch ────────────────────────────────────────────────────────────────────
async function fetchEvents() {
  if (!SCRIPT_URL) { showSetupUI(); return; }
  showLoading('Fetching your calendars…');
  const now=new Date(), timeMin=addDays(now,-30).toISOString(), timeMax=addDays(now,90).toISOString();
  const url=`${SCRIPT_URL}?timeMin=${encodeURIComponent(timeMin)}&timeMax=${encodeURIComponent(timeMax)}`;
  try {
    const res=await fetch(url);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data=await res.json();
    if (data.error) throw new Error(data.error);
    allEvents=data.events||[]; calMeta=data.calendars||[];
    buildLegend(); renderCurrentView(); hideMessage(); startNowLineTicker();
  } catch(err) {
    showError(`Could not load calendar data.<br><br><strong>Error:</strong> ${err.message}<br><br>
      Check the Apps Script is deployed with access set to <code>Anyone</code> and the URL is correct.`);
  }
}

function buildLegend() {
  const el=document.getElementById('cal-legend'); el.innerHTML='';
  calMeta.filter(c=>c.accessible!==false).forEach(cal => {
    el.innerHTML+=`<div class="legend-item"><div class="legend-dot" style="background:${cal.color}"></div><span>${cal.name}</span></div>`;
  });
}

// ─── Week view ────────────────────────────────────────────────────────────────
function renderWeekView() {
  document.getElementById('cal-grid').style.display='flex';
  document.getElementById('month-grid').style.display='none';
  const today=new Date(); today.setHours(0,0,0,0);
  const weekStart=addDays(startOfWeek(today),weekOffset*7);
  const days=Array.from({length:7},(_,i)=>addDays(weekStart,i));
  document.getElementById('cal-title').textContent=fmtWeekRange(weekStart);

  const DOW=['Sun','Mon','Tue','Wed','Thu','Fri','Sat'];
  const hdrs=document.getElementById('day-headers');
  hdrs.innerHTML='<div style="border-right:1px solid #30363d"></div>';
  days.forEach(day=>{
    const isToday=sameDay(day,today);
    hdrs.innerHTML+=`<div class="day-header${isToday?' today':''}"><div class="dow">${DOW[day.getDay()]}</div><div class="dom">${day.getDate()}</div></div>`;
  });

  const alldayRow=document.getElementById('allday-row');
  alldayRow.innerHTML='<div class="allday-gutter"><span>ALL DAY</span></div>';
  days.forEach(day=>{
    const cell=document.createElement('div'); cell.className='allday-cell';
    allEvents.filter(ev=>ev.allDay&&sameDay(new Date(ev.start),day)).forEach(ev=>{
      const p=document.createElement('div'); p.className='allday-event'; p.textContent=ev.title;
      p.style.cssText=`background:${ev.color}33;color:${ev.color};border-left:3px solid ${ev.color}`;
      cell.appendChild(p);
    });
    alldayRow.appendChild(cell);
  });

  const grid=document.getElementById('time-grid'); grid.innerHTML='';
  const LABELS=['12am','1am','2am','3am','4am','5am','6am','7am','8am','9am','10am','11am','12pm','1pm','2pm','3pm','4pm','5pm','6pm','7pm','8pm','9pm','10pm','11pm'];
  const labelCol=document.createElement('div'); labelCol.style.cssText='grid-column:1;display:flex;flex-direction:column;';
  LABELS.forEach(lbl=>{ const el=document.createElement('div'); el.className='hour-label'; el.textContent=lbl; labelCol.appendChild(el); });
  grid.appendChild(labelCol);

  days.forEach((day,ci)=>{
    const col=document.createElement('div');
    col.className='day-col'+(sameDay(day,today)?' today-col':'');
    col.style.gridColumn=String(ci+2);
    allEvents.filter(ev=>!ev.allDay&&sameDay(new Date(ev.start),day)).forEach(ev=>{
      const s=new Date(ev.start), e=new Date(ev.end);
      const sm=s.getHours()*60+s.getMinutes(), dur=Math.max(30,(e-s)/60000);
      const el=document.createElement('div'); el.className='cal-event';
      el.style.cssText=`top:${(sm/60)*PX_PER_HOUR}px;height:${(dur/60)*PX_PER_HOUR-2}px;background:${ev.color}22;color:${ev.color};border-left-color:${ev.color}`;
      el.innerHTML=`<div class="ev-title">${ev.title}</div><div class="ev-time">${fmtTime(s)}${dur>45?' – '+fmtTime(e):''}</div>`;
      col.appendChild(el);
    });
    grid.appendChild(col);
  });

  positionNowLine(today,weekStart);
  const scroll=document.getElementById('time-scroll');
  scroll.scrollTop=weekOffset===0 ? Math.max(0,(new Date().getHours()-1)*PX_PER_HOUR) : 8*PX_PER_HOUR;
}

function positionNowLine(today,weekStart) {
  const line=document.getElementById('now-line'), now=new Date(), weekEnd=addDays(weekStart,6);
  if (now<weekStart||now>weekEnd) { line.style.display='none'; return; }
  const col=(now.getDay()-weekStart.getDay()+7)%7, pct=100/7;
  line.style.display='block';
  line.style.top=`${(now.getHours()*60+now.getMinutes())/60*PX_PER_HOUR}px`;
  line.style.left=`calc(56px + ${col*pct}%)`;
  line.style.right=`calc(${(6-col)*pct}%)`;
}
function startNowLineTicker() {
  setInterval(()=>{ const t=new Date(); t.setHours(0,0,0,0); positionNowLine(t,addDays(startOfWeek(t),weekOffset*7)); }, 60000);
}

// ─── Month view ───────────────────────────────────────────────────────────────
function renderMonthView() {
  document.getElementById('cal-grid').style.display='none';
  document.getElementById('month-grid').style.display='flex';
  const today=new Date(), ref=new Date(today.getFullYear(),today.getMonth()+monthOffset,1);
  const year=ref.getFullYear(), month=ref.getMonth();
  const MO=['January','February','March','April','May','June','July','August','September','October','November','December'];
  document.getElementById('cal-title').textContent=`${MO[month]} ${year}`;

  const firstDay=new Date(year,month,1), lastDay=new Date(year,month+1,0);
  const startDate=addDays(firstDay,-firstDay.getDay());
  const totalCells=Math.ceil((firstDay.getDay()+lastDay.getDate())/7)*7;

  const cells=document.getElementById('month-cells');
  cells.innerHTML='';
  cells.style.gridTemplateRows=`repeat(${totalCells/7}, 1fr)`;

  for (let i=0;i<totalCells;i++) {
    const day=addDays(startDate,i), isToday=sameDay(day,today), otherMo=day.getMonth()!==month;
    const dayEvs=allEvents.filter(ev=>sameDay(new Date(ev.start),day));
    const visible=dayEvs.slice(0,MAX_MONTH_EVENTS), overflow=Math.max(0,dayEvs.length-MAX_MONTH_EVENTS);
    const cell=document.createElement('div');
    cell.className='month-cell'+(isToday?' today':'')+(otherMo?' other-month':'');
    cell.innerHTML=`<div class="mc-num">${day.getDate()}</div>
      <div class="mc-events">
        ${visible.map(ev=>`<div class="mc-event-pill" style="background:${ev.color}25;color:${ev.color};border-left:2px solid ${ev.color}">${ev.allDay?'':fmtTime(new Date(ev.start))+' '}${ev.title}</div>`).join('')}
        ${overflow?`<div class="mc-more">+${overflow} more</div>`:''}
      </div>`;
    cells.appendChild(cell);
  }
}

// ─── UI helpers ───────────────────────────────────────────────────────────────
function showLoading(msg) {
  document.getElementById('cal-message').style.display='flex';
  document.getElementById('cal-message').innerHTML=`<div class="spinner"></div><p>${msg}</p>`;
  document.getElementById('cal-grid').style.display='none';
  document.getElementById('month-grid').style.display='none';
}
function hideMessage() { document.getElementById('cal-message').style.display='none'; }
function showError(html) {
  document.getElementById('cal-grid').style.display='none';
  document.getElementById('month-grid').style.display='none';
  document.getElementById('cal-message').style.display='flex';
  document.getElementById('cal-message').innerHTML=`<p>⚠️ ${html}</p>
    <div id="setup-form">
      <input id="url-input" type="url" value="${SCRIPT_URL}" placeholder="https://script.google.com/macros/s/…/exec">
      <div class="row"><button class="save-btn" onclick="saveUrl()">Save & Retry</button></div>
    </div>`;
}
function showSetupUI() {
  document.getElementById('cal-message').style.display='flex';
  document.getElementById('cal-message').innerHTML=`<p>📅 <strong>Connect your Google Calendars</strong><br><br>
    Deploy <code>CalendarProxy.gs</code> as an Apps Script web app, then paste the URL here.</p>
    <div id="setup-form">
      <input id="url-input" type="url" placeholder="https://script.google.com/macros/s/…/exec">
      <div class="row"><button class="save-btn" onclick="saveUrl()">Connect</button></div>
    </div>`;
}
function saveUrl() {
  const val=document.getElementById('url-input')?.value.trim();
  if (!val||!val.startsWith('https://script.google.com')) { alert('Please enter a valid Apps Script URL'); return; }
  SCRIPT_URL=val; localStorage.setItem(SCRIPT_URL_KEY,val); fetchEvents();
}

fetchEvents();
setInterval(()=>{ if(SCRIPT_URL) fetchEvents(); }, 15*60*1000);
</script>
