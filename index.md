---
layout: default
title: Home
---

<style>
    /* ── BACKGROUND LAYERS ── */
    #photo-bg-1, #photo-bg-2 {
        position: fixed; top: 0; left: 0; width: 100%; height: 100%;
        background-size: cover; background-position: center;
        transition: opacity 2s ease-in-out; z-index: 1; background-color: #000;
    }
    #photo-bg-2 { opacity: 0; }

    .overlay-vignette {
        position: fixed; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 20%, rgba(0,0,0,0.75) 100%);
        z-index: 3; pointer-events: none;
    }

    /* ── CLOCK / WEATHER — bottom left / right ── */
    .bottom-ui {
        position: fixed;
        bottom: 110px;          /* sits above the nav bar */
        left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 70px; z-index: 10;
    }
    .weather-box {
        width: 380px; background: rgba(0,0,0,0.4); backdrop-filter: blur(15px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
    }
    .clock-box { text-align: right; text-shadow: 0 4px 20px rgba(0,0,0,0.5); }
    #time-display {
        font-size: 10rem; font-weight: 900; line-height: 0.8;
        margin: 0; letter-spacing: -6px; color: #fff;
    }
    #date-display {
        font-size: 2.4rem; font-weight: 300; text-transform: uppercase;
        letter-spacing: 6px; opacity: 0.8; color: #fff;
    }

    /* ── BG SELECTOR — floats just above the nav bar, centred ── */
    .bg-selector {
        position: fixed;
        bottom: 96px;           /* snug above the 28px-bottom + ~40px-tall nav */
        left: 50%;
        transform: translateX(-50%);
        z-index: 1001;          /* above nav */
        display: flex;
        flex-direction: row;    /* horizontal pill row */
        gap: 6px;
        background: rgba(0, 0, 0, 0.6);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        padding: 6px 10px;
        border-radius: 50px;
        border: 1px solid rgba(255, 255, 255, 0.1);
        /* Fade in on hover / touch; always visible on touch devices */
        opacity: 0.25;
        transition: opacity 0.4s;
    }
    /* Show fully when hovered or when a mode is active */
    .bg-selector:hover,
    .bg-selector:focus-within { opacity: 1; }

    /* Matches the nav link style exactly */
    .btn-mode {
        background: transparent;
        color: rgba(255, 255, 255, 0.7);
        border: none;
        padding: 6px 18px;
        border-radius: 50px;
        cursor: pointer;
        font-size: 1.2rem;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: 1px;
        transition: color 0.3s, background 0.3s;
        white-space: nowrap;
        -webkit-tap-highlight-color: transparent;
        touch-action: manipulation;
    }
    .btn-mode:hover { color: #fff; }
    .btn-mode.active {
        background: rgba(255, 255, 255, 0.15);
        color: #fff;
    }

    /* ── PHOTO NAV ARROWS — left and right edges, vertically centred ── */
    .photo-nav-arrow {
        position: fixed;
        top: 50%;
        transform: translateY(-50%);
        z-index: 100;
        background: rgba(255, 255, 255, 0.08);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.18);
        color: white;
        width: 64px;
        height: 64px;
        border-radius: 50%;
        cursor: pointer;
        font-size: 1.5rem;
        transition: background 0.3s, transform 0.2s;
        display: flex;
        align-items: center;
        justify-content: center;
        /* Hidden until hover/touch */
        opacity: 0;
        transition: opacity 0.4s, background 0.3s;
        -webkit-tap-highlight-color: transparent;
        touch-action: manipulation;
    }
    body:hover .photo-nav-arrow { opacity: 1; }
    .photo-nav-arrow:hover {
        background: rgba(255, 255, 255, 0.28);
        opacity: 1;
    }
    /* Force visible on touch screens that don't support :hover */
    @media (hover: none) {
        .photo-nav-arrow { opacity: 0.5; }
        .bg-selector { opacity: 1; }
    }

    #arrow-prev { left: 24px; }
    #arrow-next { right: 24px; }
</style>

<!-- Backgrounds -->
<div id="photo-bg-1"></div>
<div id="photo-bg-2"></div>
<div class="overlay-vignette"></div>

<!-- Photo nav arrows -->
<button id="arrow-prev" class="photo-nav-arrow" onclick="prevPhoto()" aria-label="Previous photo">❮</button>
<button id="arrow-next" class="photo-nav-arrow" onclick="nextPhoto()" aria-label="Next photo">❯</button>

<!-- Clock + weather -->
<div class="bottom-ui">
    <div class="weather-box">
        <a class="weatherwidget-io"
           href="https://forecast7.com/en/47d17n122d52/lakewood/?unit=us"
           data-label_1="LAKEWOOD" data-label_2="WASHINGTON"
           data-icons="Climacons Animated" data-theme="pure"
           data-basecolor="transparent" data-textcolor="#ffffff">LAKEWOOD WA</a>
    </div>
    <div class="clock-box">
        <div id="time-display">00:00</div>
        <div id="date-display">LOADING</div>
    </div>
</div>

<!-- BG selector — sits above the bottom nav -->
<div class="bg-selector">
    <button class="btn-mode" id="btn-family" onclick="setMode('family')">Family Photos</button>
    <button class="btn-mode" id="btn-nature" onclick="setMode('nature')">Daily Nature</button>
    <button class="btn-mode" id="btn-crest"  onclick="setMode('crest')">Family Crest</button>
</div>

<script>
    var cloudName = 'dybmaxwvb';
    var tagName   = 'dashboard';
    var crestPath = '/b-d-a-b-e-family-dashboard/assets/img/family-crest-metal-on-wood.png';

    var photoUrls        = [];
    var activeBg         = 1;
    var currentIndex     = 0;
    var slideshowInterval = null;

    /* ── Clock ── */
    function updateClock() {
        var now = new Date();
        var h   = now.getHours() % 12 || 12;
        var m   = now.getMinutes().toString().padStart(2, '0');
        document.getElementById('time-display').textContent = h + ':' + m;
        document.getElementById('date-display').textContent =
            now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }
    updateClock();
    setInterval(updateClock, 1000);

    /* ── Background cross-fade ── */
    function applyBackground(url) {
        var nextBg  = activeBg === 1 ? 2 : 1;
        var current = document.getElementById('photo-bg-' + activeBg);
        var next    = document.getElementById('photo-bg-' + nextBg);
        var img     = new Image();
        img.onload  = function() {
            next.style.backgroundImage = "url('" + url + "')";
            next.style.opacity = 1;
            current.style.opacity = 0;
            activeBg = nextBg;
        };
        img.onerror = function() { console.error('Failed to load: ' + url); };
        img.src = url;
    }

    /* ── Cloudinary fetch ── */
    async function fetchCloudinary() {
        try {
            var listUrl  = 'https://res.cloudinary.com/' + cloudName + '/image/list/' + tagName + '.json?cb=' + Date.now();
            var response = await fetch(listUrl);
            if (!response.ok) throw new Error('Cloudinary fetch failed');
            var data = await response.json();
            // Shuffle so it's a different order each session
            photoUrls = data.resources
                .map(function(r) { return { r: r, sort: Math.random() }; })
                .sort(function(a, b) { return a.sort - b.sort; })
                .map(function(o) {
                    var r = o.r;
                    return 'https://res.cloudinary.com/' + cloudName +
                           '/image/upload/q_auto,f_auto,w_2560,c_limit/' +
                           r.public_id + '.' + r.format;
                });
            if (photoUrls.length > 0) {
                currentIndex = 0;
                applyBackground(photoUrls[currentIndex]);
                startSlideshow();
            }
        } catch(e) {
            console.warn('Cloudinary error, using fallback:', e);
            applyBackground('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?q=80&w=2560');
        }
    }

    function startSlideshow() {
        if (slideshowInterval) clearInterval(slideshowInterval);
        slideshowInterval = setInterval(function() {
            currentIndex = (currentIndex + 1) % photoUrls.length;
            applyBackground(photoUrls[currentIndex]);
        }, 30000);
    }

    function resetSlideshow() {
        if (slideshowInterval) {
            clearInterval(slideshowInterval);
            startSlideshow();
        }
    }

    /* ── Manual nav ── */
    function nextPhoto() {
        if (photoUrls.length === 0) return;
        currentIndex = (currentIndex + 1) % photoUrls.length;
        applyBackground(photoUrls[currentIndex]);
        resetSlideshow();
    }

    function prevPhoto() {
        if (photoUrls.length === 0) return;
        currentIndex = (currentIndex - 1 + photoUrls.length) % photoUrls.length;
        applyBackground(photoUrls[currentIndex]);
        resetSlideshow();
    }

    /* ── Mode selector ── */
    function setMode(mode) {
        localStorage.setItem('dashboard-bg-mode', mode);

        document.querySelectorAll('.btn-mode').forEach(function(b) {
            b.classList.remove('active');
        });
        document.getElementById('btn-' + mode).classList.add('active');

        // Show/hide arrows — only relevant in family mode
        var arrowVis = (mode === 'family') ? '' : 'none';
        document.getElementById('arrow-prev').style.display = arrowVis;
        document.getElementById('arrow-next').style.display = arrowVis;

        if (slideshowInterval) { clearInterval(slideshowInterval); slideshowInterval = null; }

        if (mode === 'family') {
            fetchCloudinary();
        } else if (mode === 'nature') {
            applyBackground('https://images.unsplash.com/photo-1470770841072-f978cf4d019e?q=80&w=2560&auto=format&fit=crop');
        } else if (mode === 'crest') {
            applyBackground(crestPath);
        }
    }

    // Restore last-used mode on load
    var savedMode = localStorage.getItem('dashboard-bg-mode') || 'family';
    setMode(savedMode);

    // Weather widget
    !function(d,s,id){
        var js,fjs=d.getElementsByTagName(s)[0];
        if(!d.getElementById(id)){
            js=d.createElement(s); js.id=id;
            js.src='https://weatherwidget.io/js/widget.min.js';
            fjs.parentNode.insertBefore(js,fjs);
        }
    }(document,'script','weatherwidget-io-js');
</script>
