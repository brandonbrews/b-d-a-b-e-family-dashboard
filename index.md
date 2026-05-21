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

    /* ── NATURE PHOTO CAPTION — top-right corner ── */
    #nature-caption {
        position: fixed;
        top: 28px;
        right: 28px;
        z-index: 20;
        text-align: right;
        opacity: 0;
        transition: opacity 0.8s ease;
        pointer-events: none;
    }
    #nature-caption.visible { opacity: 1; pointer-events: auto; }
    #nature-caption .caption-location {
        font-size: 1.5rem;
        font-weight: 700;
        color: #fff;
        text-transform: uppercase;
        letter-spacing: 3px;
        text-shadow: 0 2px 12px rgba(0,0,0,0.7);
        line-height: 1.2;
    }
    #nature-caption .caption-desc {
        font-size: 1rem;
        font-weight: 400;
        color: rgba(255,255,255,0.75);
        text-shadow: 0 2px 8px rgba(0,0,0,0.7);
        margin-top: 4px;
        max-width: 320px;
        margin-left: auto;
    }
    #nature-caption .caption-credit {
        font-size: 0.8rem;
        color: rgba(255,255,255,0.45);
        margin-top: 6px;
        text-shadow: 0 1px 6px rgba(0,0,0,0.6);
    }
    #nature-caption .caption-credit a {
        color: rgba(255,255,255,0.55);
        text-decoration: underline;
    }

    /* ── CLOCK / WEATHER — bottom left / right ── */
    .bottom-ui {
        position: fixed;
        bottom: 30px;
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

    /* ── BG SELECTOR ── */
    .bg-selector {
        position: fixed;
        bottom: 96px;
        left: 50%;
        transform: translateX(-50%);
        z-index: 1001;
        display: flex;
        flex-direction: row;
        gap: 6px;
        background: rgba(0, 0, 0, 0.6);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        padding: 6px 10px;
        border-radius: 50px;
        border: 1px solid rgba(255, 255, 255, 0.1);
        opacity: 0.25;
        transition: opacity 0.4s;
    }
    .bg-selector:hover,
    .bg-selector:focus-within { opacity: 1; }

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

    /* ── PHOTO NAV ARROWS ── */
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
        display: flex;
        align-items: center;
        justify-content: center;
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

<!-- Nature photo caption (top-right, only shown in nature mode) -->
<div id="nature-caption">
    <div class="caption-location" id="caption-location"></div>
    <div class="caption-desc"     id="caption-desc"></div>
    <div class="caption-credit"   id="caption-credit"></div>
</div>

<!-- Photo nav arrows (family mode only) -->
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

<!-- BG selector -->
<div class="bg-selector">
    <button class="btn-mode" id="btn-family" onclick="setMode('family')">Family Photos</button>
    <button class="btn-mode" id="btn-nature" onclick="setMode('nature')">Daily Nature</button>
    <button class="btn-mode" id="btn-crest"  onclick="setMode('crest')">Family Crest</button>
</div>

<script>
    /* ── CONFIG — edit these ── */
    var cloudName = 'dybmaxwvb';
    var tagName   = 'DBABE';          // tag used in Cloudinary for family photos
    var crestPath = '/b-d-a-b-e-family-dashboard/assets/img/family-crest-metal-on-wood.png';

    // ── Unsplash Access Key ──────────────────────────────────────────────────
    // 1. Sign up free at https://unsplash.com/developers
    // 2. Create an app → copy the "Access Key"
    // 3. Paste it below (the free tier allows 50 requests/hour — plenty for daily use)
    var unsplashAccessKey = 'uVlnR0MpCzzax-Zw8ViI6VOCJuo4z6H0G41lnZUgrWI';
    // ─────────────────────────────────────────────────────────────────────────

    var photoUrls         = [];
    var activeBg          = 1;
    var currentIndex      = 0;
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

    /* ── Cloudinary fetch (family photos) ── */
    async function fetchCloudinary() {
        try {
            var listUrl  = 'https://res.cloudinary.com/' + cloudName + '/image/list/' + tagName + '.json?cb=' + Date.now();
            var response = await fetch(listUrl);
            if (!response.ok) throw new Error('Cloudinary fetch failed');
            var data = await response.json();
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
        if (slideshowInterval) { clearInterval(slideshowInterval); startSlideshow(); }
    }

    /* ── Manual nav (family mode) ── */
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

    /* ── Nature caption helpers ── */
    function showNatureCaption(location, description, photographerName, photographerUrl, photoPageUrl) {
        document.getElementById('caption-location').textContent = location || 'Daily Nature';
        document.getElementById('caption-desc').textContent     = description || '';
        // Credit line with link to photo page (required by Unsplash guidelines)
        var credit = document.getElementById('caption-credit');
        if (photographerName) {
            credit.innerHTML = 'Photo by <a href="' + (photographerUrl || '#') + '" target="_blank" rel="noopener">'
                             + photographerName + '</a> on '
                             + '<a href="' + (photoPageUrl || 'https://unsplash.com') + '" target="_blank" rel="noopener">Unsplash</a>';
        } else {
            credit.textContent = '';
        }
        document.getElementById('nature-caption').classList.add('visible');
    }

    function hideNatureCaption() {
        document.getElementById('nature-caption').classList.remove('visible');
    }

    /* ── Unsplash daily nature photo ── */
    async function fetchNaturePhoto() {
        // Use today's date as a stable seed so the photo stays the same all day
        var today = new Date();
        var seed  = today.getFullYear() + '-' + (today.getMonth() + 1) + '-' + today.getDate();

        // Cache in sessionStorage so we don't burn API calls on every page load
        var cacheKey  = 'nature-photo-' + seed;
        var cached    = sessionStorage.getItem(cacheKey);

        if (cached) {
            var data = JSON.parse(cached);
            applyNaturePhoto(data);
            return;
        }

        // Fallback if no API key is configured yet
        if (!unsplashAccessKey || unsplashAccessKey === 'YOUR_UNSPLASH_ACCESS_KEY') {
            applyBackground('https://images.unsplash.com/photo-1470770841072-f978cf4d019e?q=80&w=2560&auto=format&fit=crop');
            showNatureCaption('Daily Nature', 'Add your Unsplash Access Key to see location info.', '', '', '');
            return;
        }

        try {
            // Query: stunning landscape/nature, oriented landscape, high quality
            var url = 'https://api.unsplash.com/photos/random'
                    + '?query=nature+landscape+scenic'
                    + '&orientation=landscape'
                    + '&content_filter=high'
                    + '&client_id=' + unsplashAccessKey;

            var resp = await fetch(url);
            if (!resp.ok) throw new Error('Unsplash error: ' + resp.status);
            var photo = await resp.json();

            var photoData = {
                url:             photo.urls.raw + '&w=2560&q=85&fit=crop&auto=format',
                location:        (photo.location && (photo.location.name || photo.location.city || photo.location.country)) || '',
                description:     photo.description || photo.alt_description || '',
                photographerName: photo.user.name,
                photographerUrl:  photo.user.links.html + '?utm_source=horn_family_dashboard&utm_medium=referral',
                photoPageUrl:     photo.links.html + '?utm_source=horn_family_dashboard&utm_medium=referral'
            };

            // Cap description length
            if (photoData.description && photoData.description.length > 80) {
                photoData.description = photoData.description.substring(0, 77) + '…';
            }

            sessionStorage.setItem(cacheKey, JSON.stringify(photoData));
            applyNaturePhoto(photoData);

        } catch(e) {
            console.warn('Unsplash fetch failed, using static fallback:', e);
            applyBackground('https://images.unsplash.com/photo-1470770841072-f978cf4d019e?q=80&w=2560&auto=format&fit=crop');
            showNatureCaption('Scenic Landscape', '', '', '', '');
        }
    }

    function applyNaturePhoto(data) {
        applyBackground(data.url);
        showNatureCaption(data.location, data.description, data.photographerName, data.photographerUrl, data.photoPageUrl);
    }

    /* ── Mode selector ── */
    function setMode(mode) {
        localStorage.setItem('dashboard-bg-mode', mode);

        document.querySelectorAll('.btn-mode').forEach(function(b) {
            b.classList.remove('active');
        });
        document.getElementById('btn-' + mode).classList.add('active');

        var arrowVis = (mode === 'family') ? '' : 'none';
        document.getElementById('arrow-prev').style.display = arrowVis;
        document.getElementById('arrow-next').style.display = arrowVis;

        if (slideshowInterval) { clearInterval(slideshowInterval); slideshowInterval = null; }
        hideNatureCaption();

        if (mode === 'family') {
            fetchCloudinary();
        } else if (mode === 'nature') {
            fetchNaturePhoto();
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
