<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Family Display – Home</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&family=Space+Mono:wght@700&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { margin:0; padding:0; box-sizing:border-box; }

  html, body {
    width:100%; height:100%;
    background:#000;
    color:#fff;
    font-family:'Outfit', sans-serif;
    overflow:hidden;
    user-select:none;
    -webkit-user-select:none;
  }

  /* ── SLIDESHOW ── */
  #slideshow {
    position:fixed; inset:0; z-index:0;
    background:#111;
  }

  .slide {
    position:absolute; inset:0;
    opacity:0;
    transition:opacity 1.4s ease;
    background:#111 center/cover no-repeat;
  }
  .slide.active { opacity:1; }

  /* Vignette */
  #vignette {
    position:fixed; inset:0; z-index:1; pointer-events:none;
    background:
      linear-gradient(to top,    rgba(0,0,0,.78)  0%, rgba(0,0,0,.12) 44%, transparent 65%),
      linear-gradient(to bottom, rgba(0,0,0,.48)  0%, transparent 28%),
      linear-gradient(to right,  rgba(0,0,0,.26)  0%, transparent 22%);
  }

  /* ── NAV ARROWS ── */
  .nav-btn {
    position:fixed; top:50%; z-index:5;
    transform:translateY(-50%);
    width:52px; height:52px; border-radius:50%;
    background:rgba(255,255,255,.10);
    border:1.5px solid rgba(255,255,255,.22);
    backdrop-filter:blur(8px);
    display:flex; align-items:center; justify-content:center;
    cursor:pointer;
    transition:background .2s, transform .2s;
    -webkit-tap-highlight-color:transparent;
  }
  .nav-btn:hover { background:rgba(255,255,255,.26); transform:translateY(-50%) scale(1.08); }
  #nav-prev { left:18px; }
  #nav-next { right:18px; }

  /* ── DOTS ── */
  #dots {
    position:fixed; bottom:148px; left:50%;
    transform:translateX(-50%);
    display:flex; gap:7px; z-index:5;
  }
  .dot {
    width:7px; height:7px; border-radius:50%;
    background:rgba(255,255,255,.3);
    cursor:pointer;
    transition:background .3s, transform .3s;
  }
  .dot.active { background:#fff; transform:scale(1.4); }

  /* ── INFO OVERLAY ── */
  #info {
    position:fixed; bottom:0; left:0; right:0;
    z-index:4;
    padding:22px 38px 30px;
    display:flex; align-items:flex-end; justify-content:space-between; gap:24px;
  }

  #clock {
    font-family:'Space Mono', monospace;
    font-size:clamp(52px,8.5vw,100px);
    font-weight:700;
    line-height:.92;
    letter-spacing:-2px;
    text-shadow:0 3px 22px rgba(0,0,0,.7);
  }
  #date {
    font-size:clamp(14px,1.7vw,19px);
    opacity:.80;
    margin-top:7px;
    text-shadow:0 1px 8px rgba(0,0,0,.6);
  }

  /* Weather */
  #weather-block {
    display:flex; flex-direction:column; align-items:flex-end; gap:11px; flex-shrink:0;
  }
  #w-now { display:flex; align-items:center; gap:12px; }
  #w-icon { font-size:48px; line-height:1; }
  #w-temp {
    font-family:'Space Mono', monospace;
    font-size:clamp(44px,6vw,68px);
    font-weight:700; line-height:.92;
    text-shadow:0 2px 18px rgba(0,0,0,.55);
  }
  #w-desc { font-size:13px; opacity:.70; text-align:right; text-shadow:0 1px 5px rgba(0,0,0,.5); }
  #forecast { display:flex; gap:9px; }
  .fc {
    background:rgba(255,255,255,.10);
    backdrop-filter:blur(12px); -webkit-backdrop-filter:blur(12px);
    border:1px solid rgba(255,255,255,.14);
    border-radius:16px; padding:9px 13px; text-align:center; min-width:56px;
  }
  .fc-d { font-size:10px; font-weight:700; opacity:.60; letter-spacing:1px; text-transform:uppercase; }
  .fc-i { font-size:20px; margin:4px 0; }
  .fc-h { font-size:14px; font-weight:700; }
  .fc-l { font-size:11px; opacity:.52; margin-top:2px; }

  /* ── LOADING / STATUS ── */
  #status-bar {
    position:fixed; top:0; left:0; right:0; z-index:10;
    padding:18px 28px;
    display:flex; align-items:center; justify-content:space-between; gap:12px;
  }

  .pill {
    background:rgba(0,0,0,.45);
    backdrop-filter:blur(10px); -webkit-backdrop-filter:blur(10px);
    border:1px solid rgba(255,255,255,.12);
    border-radius:12px; padding:8px 15px;
    font-size:13px; font-weight:600; color:rgba(255,255,255,.82);
    display:flex; align-items:center; gap:7px;
  }

  .spinner {
    width:14px; height:14px;
    border:2px solid rgba(255,255,255,.25);
    border-top-color:rgba(255,255,255,.85);
    border-radius:50%;
    animation:spin .7s linear infinite;
  }
  @keyframes spin { to { transform:rotate(360deg); } }

  #change-btn {
    background:rgba(0,0,0,.45);
    backdrop-filter:blur(10px);
    border:1px solid rgba(255,255,255,.12);
    border-radius:12px; padding:8px 15px;
    font-size:12px; color:rgba(255,255,255,.55);
    font-family:'Outfit', sans-serif;
    cursor:pointer; transition:all .2s;
    display:none;
  }
  #change-btn:hover { color:#fff; border-color:rgba(255,255,255,.3); }

  /* ── SETUP MODAL ── */
  #setup-modal {
    position:fixed; inset:0; z-index:30;
    background:rgba(0,0,0,.88);
    backdrop-filter:blur(16px); -webkit-backdrop-filter:blur(16px);
    display:flex; align-items:center; justify-content:center;
    padding:24px;
  }

  .setup-card {
    background:rgba(22,22,30,.95);
    border:1px solid rgba(255,255,255,.11);
    border-radius:26px; padding:38px 40px;
    max-width:560px; width:100%;
    display:flex; flex-direction:column; gap:18px;
    box-shadow:0 24px 64px rgba(0,0,0,.6);
  }

  .setup-card h2 { font-size:24px; font-weight:700; letter-spacing:-.4px; }
  .setup-card .sub { font-size:14px; opacity:.62; line-height:1.7; }

  .steps { font-size:14px; opacity:.65; line-height:1.85; padding-left:20px; }
  .steps li { padding-left:4px; }

  .input-row { display:flex; gap:9px; }

  #url-input {
    flex:1;
    background:rgba(255,255,255,.07);
    border:1px solid rgba(255,255,255,.18);
    border-radius:12px; padding:13px 16px;
    color:#fff; font-size:14px; font-family:'Outfit', sans-serif;
    outline:none; transition:border-color .2s;
  }
  #url-input:focus { border-color:rgba(255,255,255,.45); }
  #url-input::placeholder { opacity:.30; }

  #load-btn {
    background:rgba(255,255,255,.13);
    border:1px solid rgba(255,255,255,.26);
    color:#fff; border-radius:12px;
    padding:13px 22px; font-size:14px; font-weight:700;
    font-family:'Outfit', sans-serif;
    cursor:pointer; transition:background .2s; white-space:nowrap;
  }
  #load-btn:hover { background:rgba(255,255,255,.24); }

  #prefill-btn {
    align-self:flex-start;
    background:transparent;
    border:1px solid rgba(255,255,255,.11);
    color:rgba(255,255,255,.38);
    border-radius:9px; padding:8px 14px;
    font-size:12px; font-family:'Outfit', sans-serif;
    cursor:pointer; transition:all .2s;
  }
  #prefill-btn:hover { color:rgba(255,255,255,.70); border-color:rgba(255,255,255,.24); }

  #error-msg {
    display:none; font-size:13px;
    color:#ff8a80; background:rgba(255,80,60,.10);
    border:1px solid rgba(255,80,60,.22);
    border-radius:9px; padding:10px 14px; line-height:1.5;
  }
</style>
</head>
<body>

<div id="slideshow"></div>
<div id="vignette"></div>

<!-- Nav arrows -->
<button class="nav-btn" id="nav-prev" aria-label="Previous">
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"/></svg>
</button>
<button class="nav-btn" id="nav-next" aria-label="Next">
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"/></svg>
</button>

<!-- Dots -->
<div id="dots"></div>

<!-- Top status bar -->
<div id="status-bar">
  <div class="pill" id="status-pill">📷 Family Photos</div>
  <button id="change-btn" onclick="showSetup()">⚙ Change Album</button>
</div>

<!-- Clock + weather -->
<div id="info">
  <div>
    <div id="clock">--:-- --</div>
    <div id="date"></div>
  </div>
  <div id="weather-block">
    <div style="opacity:.45;font-size:14px">Loading weather…</div>
  </div>
</div>

<!-- Setup modal -->
<div id="setup-modal">
  <div class="setup-card">
    <h2>📸 Connect Your Google Photos Album</h2>
    <p class="sub">Paste your Google Photos share link — photos load automatically, no manual URL copying needed.</p>
    <ol class="steps">
      <li>Open your Google Photos album</li>
      <li>Tap the <strong>share button</strong> → <strong>"Create link"</strong></li>
      <li>Copy the link &mdash; looks like <code style="background:rgba(255,255,255,.09);padding:2px 7px;border-radius:5px;font-family:monospace;font-size:11px">https://photos.app.goo.gl/…</code></li>
      <li>Paste it below and tap <strong>Load Album</strong></li>
    </ol>
    <div class="input-row">
      <input id="url-input" type="url"
        placeholder="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"
        autocomplete="off" spellcheck="false">
      <button id="load-btn" onclick="loadAlbum()">Load Album</button>
    </div>
    <button id="prefill-btn" onclick="prefillFamily()">↗ Use family album (R5JTm4dMNEbHEvjm7)</button>
    <div id="error-msg"></div>
  </div>
</div>

<script>
// ═══════════════════════════════════════════════════════════════════════════════
// CONFIGURATION
// ═══════════════════════════════════════════════════════════════════════════════
const FAMILY_ALBUM  = 'https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7';
const STORAGE_URL   = 'familyAlbumUrl';
const STORAGE_URLS  = 'familyPhotoUrls';   // cached extracted URLs
const STORAGE_STAMP = 'familyPhotoStamp';  // when we last fetched
const CACHE_TTL_MS  = 6 * 60 * 60 * 1000; // re-fetch every 6 hours
const SLIDE_DELAY   = 8000;               // ms between auto-advance

// ═══════════════════════════════════════════════════════════════════════════════
// SLIDESHOW ENGINE
// ═══════════════════════════════════════════════════════════════════════════════
let slides     = [];
let current    = 0;
let autoTimer  = null;
let touchX0    = null;

const slideshowEl = document.getElementById('slideshow');
const dotsEl      = document.getElementById('dots');

function buildSlides(urls) {
  slideshowEl.innerHTML = '';
  dotsEl.innerHTML = '';
  slides = [];

  // Shuffle for randomness each session
  const shuffled = [...urls].sort(() => Math.random() - .5);

  shuffled.forEach((url, i) => {
    const div = document.createElement('div');
    div.className = 'slide';
    // Use background-image so object-fit:cover behaviour is natural
    div.style.backgroundImage = `url("${url}")`;
    slideshowEl.appendChild(div);
    slides.push(div);

    const dot = document.createElement('div');
    dot.className = 'dot';
    dot.addEventListener('click', () => goTo(i));
    dotsEl.appendChild(dot);
  });

  if (slides.length) goTo(0, false);
}

function goTo(idx, animate = true) {
  if (!slides.length) return;
  slides[current].classList.remove('active');
  dotsEl.children[current]?.classList.remove('active');
  current = ((idx % slides.length) + slides.length) % slides.length;
  slides[current].classList.add('active');
  dotsEl.children[current]?.classList.add('active');
  scheduleNext();
}

function scheduleNext() {
  clearTimeout(autoTimer);
  autoTimer = setTimeout(() => goTo(current + 1), SLIDE_DELAY);
}

// Arrow buttons
document.getElementById('nav-prev').addEventListener('click', () => goTo(current - 1));
document.getElementById('nav-next').addEventListener('click', () => goTo(current + 1));

// Touch / swipe
document.addEventListener('touchstart', e => { touchX0 = e.touches[0].clientX; }, {passive:true});
document.addEventListener('touchend',   e => {
  if (touchX0 === null) return;
  const dx = e.changedTouches[0].clientX - touchX0;
  if (Math.abs(dx) > 48) goTo(current + (dx < 0 ? 1 : -1));
  touchX0 = null;
}, {passive:true});

// Keyboard
document.addEventListener('keydown', e => {
  if (e.key === 'ArrowLeft')  goTo(current - 1);
  if (e.key === 'ArrowRight') goTo(current + 1);
});

// ═══════════════════════════════════════════════════════════════════════════════
// ALBUM LOADING — uses Claude API to fetch & extract photo URLs
// ═══════════════════════════════════════════════════════════════════════════════
function setStatus(text, loading = false) {
  const pill = document.getElementById('status-pill');
  pill.innerHTML = loading
    ? `<div class="spinner"></div>${text}`
    : `📷 ${text}`;
}

function showError(msg) {
  const el = document.getElementById('error-msg');
  el.textContent = msg;
  el.style.display = 'block';
}

function hideError() {
  document.getElementById('error-msg').style.display = 'none';
}

function showSetup() {
  const saved = localStorage.getItem(STORAGE_URL);
  if (saved) document.getElementById('url-input').value = saved;
  hideError();
  document.getElementById('setup-modal').style.display = 'flex';
}

function hideSetup() {
  document.getElementById('setup-modal').style.display = 'none';
}

function prefillFamily() {
  document.getElementById('url-input').value = FAMILY_ALBUM;
  loadAlbum();
}

async function loadAlbum() {
  const raw = document.getElementById('url-input').value.trim();
  if (!raw) return;

  if (!raw.includes('photos.app.goo.gl') && !raw.includes('photos.google.com')) {
    showError('Please enter a valid Google Photos share link (photos.app.goo.gl/… or photos.google.com/share/…)');
    return;
  }

  hideError();
  document.getElementById('load-btn').disabled = true;
  document.getElementById('load-btn').textContent = 'Loading…';
  setStatus('Fetching album…', true);

  localStorage.setItem(STORAGE_URL, raw);

  try {
    const urls = await fetchPhotoUrls(raw);
    if (!urls || urls.length === 0) {
      throw new Error('No photos found in this album. Make sure the album is shared publicly ("Anyone with the link").');
    }

    // Cache
    localStorage.setItem(STORAGE_URLS,  JSON.stringify(urls));
    localStorage.setItem(STORAGE_STAMP, Date.now().toString());

    hideSetup();
    document.getElementById('change-btn').style.display = 'block';
    setStatus(`${urls.length} photos`);
    buildSlides(urls);

  } catch(err) {
    showError(err.message || 'Failed to load album. Check the link and try again.');
    setStatus('Error loading album');
  } finally {
    document.getElementById('load-btn').disabled = false;
    document.getElementById('load-btn').textContent = 'Load Album';
  }
}

/*
 * Uses the Anthropic API (claude-sonnet) to fetch the Google Photos album page
 * via its web_search / browse capability and extract all lh3.googleusercontent.com
 * image URLs from the HTML source.
 */
async function fetchPhotoUrls(albumUrl) {
  const prompt = `Fetch this Google Photos shared album URL and extract ALL photo/image URLs from it:
${albumUrl}

The page HTML contains many URLs in the format:
  https://lh3.googleusercontent.com/...=w[WIDTH]-h[HEIGHT]  or similar

Instructions:
1. Fetch the page at the URL above
2. Find every unique URL that starts with https://lh3.googleusercontent.com/
3. For each URL found, replace any size suffix like =w800-h600 or =w2048 at the end with =w1920-h1080 to get full-quality images suitable for a fullscreen display
4. Return ONLY a raw JSON array of strings (the URLs), with no explanation, no markdown fences, no other text whatsoever.
   Example output format: ["https://lh3.googleusercontent.com/abc=w1920-h1080","https://lh3.googleusercontent.com/xyz=w1920-h1080"]

Return at least 5 URLs if available. If you cannot access the page or find no lh3 URLs, return an empty array: []`;

  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 4000,
      tools: [{ type: 'web_search_20250305', name: 'web_search' }],
      messages: [{ role: 'user', content: prompt }]
    })
  });

  if (!response.ok) {
    const err = await response.json().catch(() => ({}));
    throw new Error(`API error ${response.status}: ${err.error?.message || response.statusText}`);
  }

  const data = await response.json();

  // Extract text from response content blocks
  const textBlocks = (data.content || []).filter(b => b.type === 'text');
  const combined   = textBlocks.map(b => b.text).join('\n');

  // Pull out JSON array
  const match = combined.match(/\[[\s\S]*?\]/);
  if (!match) {
    console.warn('Claude response:', combined);
    throw new Error('Could not parse photo URLs from response. The album may be private or empty.');
  }

  let urls;
  try {
    urls = JSON.parse(match[0]);
  } catch(e) {
    throw new Error('Failed to parse photo URL list from response.');
  }

  // Deduplicate and validate
  return [...new Set(urls)].filter(u => typeof u === 'string' && u.includes('googleusercontent.com'));
}

// ═══════════════════════════════════════════════════════════════════════════════
// INIT — load from cache or show setup
// ═══════════════════════════════════════════════════════════════════════════════
async function init() {
  const savedUrl   = localStorage.getItem(STORAGE_URL);
  const cachedUrls = localStorage.getItem(STORAGE_URLS);
  const stamp      = parseInt(localStorage.getItem(STORAGE_STAMP) || '0', 10);
  const stale      = Date.now() - stamp > CACHE_TTL_MS;

  if (cachedUrls && !stale) {
    // Use cached photo URLs immediately — no network wait
    try {
      const urls = JSON.parse(cachedUrls);
      if (urls.length) {
        hideSetup();
        document.getElementById('change-btn').style.display = 'block';
        setStatus(`${urls.length} photos`);
        buildSlides(urls);

        // Silently refresh in the background if url is saved
        if (savedUrl) refreshInBackground(savedUrl);
        return;
      }
    } catch(e) {}
  }

  if (savedUrl) {
    // Have a URL but no valid cache — fetch now
    hideSetup();
    setStatus('Loading photos…', true);
    document.getElementById('url-input').value = savedUrl;
    try {
      const urls = await fetchPhotoUrls(savedUrl);
      if (urls.length) {
        localStorage.setItem(STORAGE_URLS,  JSON.stringify(urls));
        localStorage.setItem(STORAGE_STAMP, Date.now().toString());
        document.getElementById('change-btn').style.display = 'block';
        setStatus(`${urls.length} photos`);
        buildSlides(urls);
      } else {
        setStatus('No photos found');
        showSetup();
      }
    } catch(e) {
      setStatus('Could not load album');
      showSetup();
    }
  } else {
    // First visit — show setup
    // (setup modal already visible from HTML)
  }
}

async function refreshInBackground(albumUrl) {
  try {
    const urls = await fetchPhotoUrls(albumUrl);
    if (urls.length) {
      localStorage.setItem(STORAGE_URLS,  JSON.stringify(urls));
      localStorage.setItem(STORAGE_STAMP, Date.now().toString());
    }
  } catch(e) { /* silent */ }
}

// ═══════════════════════════════════════════════════════════════════════════════
// CLOCK
// ═══════════════════════════════════════════════════════════════════════════════
const DAYS   = ['Sunday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'];
const MONTHS = ['January','February','March','April','May','June','July','August','September','October','November','December'];
const DAYS_S = ['Sun','Mon','Tue','Wed','Thu','Fri','Sat'];

function tick() {
  const n = new Date();
  const h = n.getHours(), m = String(n.getMinutes()).padStart(2,'0');
  document.getElementById('clock').textContent = `${h%12||12}:${m} ${h>=12?'PM':'AM'}`;
  document.getElementById('date').textContent  =
    `${DAYS[n.getDay()]}, ${MONTHS[n.getMonth()]} ${n.getDate()}, ${n.getFullYear()}`;
}
tick();
setInterval(tick, 1000);

// ═══════════════════════════════════════════════════════════════════════════════
// WEATHER — Open-Meteo, free, no key, Lakewood WA 98498
// ═══════════════════════════════════════════════════════════════════════════════
const WMO = {
  0:['☀️','Clear'],      1:['🌤️','Mostly Clear'],  2:['⛅','Partly Cloudy'], 3:['☁️','Overcast'],
  45:['🌫️','Fog'],       48:['🌫️','Icy Fog'],
  51:['🌦️','Drizzle'],   53:['🌦️','Drizzle'],      55:['🌧️','Heavy Drizzle'],
  61:['🌧️','Light Rain'],63:['🌧️','Rain'],          65:['🌧️','Heavy Rain'],
  71:['🌨️','Light Snow'],73:['🌨️','Snow'],          75:['❄️','Heavy Snow'],  77:['🌨️','Snow Grains'],
  80:['🌦️','Showers'],   81:['🌧️','Heavy Showers'], 82:['⛈️','Violent Showers'],
  85:['🌨️','Snow Showers'],86:['❄️','Heavy Snow Showers'],
  95:['⛈️','Thunderstorm'],96:['⛈️','Thunder+Hail'], 99:['⛈️','Heavy Thunder+Hail'],
};

async function loadWeather() {
  try {
    const d = await fetch(
      `https://api.open-meteo.com/v1/forecast?latitude=47.1731&longitude=-122.5185` +
      `&current=temperature_2m,weathercode,apparent_temperature` +
      `&daily=weathercode,temperature_2m_max,temperature_2m_min` +
      `&temperature_unit=fahrenheit&timezone=America%2FLos_Angeles&forecast_days=6`
    ).then(r=>r.json());

    const [icon,desc] = WMO[d.current.weathercode] || ['🌡️','Unknown'];
    const temp  = Math.round(d.current.temperature_2m);
    const feels = Math.round(d.current.apparent_temperature);

    let fc = '';
    for (let i=1;i<=5;i++) {
      const day = new Date(d.daily.time[i]+'T12:00:00');
      const [fi] = WMO[d.daily.weathercode[i]] || ['🌡️'];
      fc += `<div class="fc">
        <div class="fc-d">${DAYS_S[day.getDay()]}</div>
        <div class="fc-i">${fi}</div>
        <div class="fc-h">${Math.round(d.daily.temperature_2m_max[i])}°</div>
        <div class="fc-l">${Math.round(d.daily.temperature_2m_min[i])}°</div>
      </div>`;
    }

    document.getElementById('weather-block').innerHTML = `
      <div id="w-now">
        <div id="w-icon">${icon}</div>
        <div id="w-temp">${temp}°</div>
      </div>
      <div id="w-desc">${desc} · Feels like ${feels}°</div>
      <div id="forecast">${fc}</div>`;
  } catch {
    document.getElementById('weather-block').innerHTML =
      '<div style="opacity:.35;font-size:13px">Weather unavailable</div>';
  }
}

loadWeather();
setInterval(loadWeather, 10*60*1000);

// ═══════════════════════════════════════════════════════════════════════════════
// KICK OFF
// ═══════════════════════════════════════════════════════════════════════════════
init();
</script>
</body>
</html>
