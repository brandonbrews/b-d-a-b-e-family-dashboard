---
layout: default
title: Home
---

<style>
    #photo-bg-1, #photo-bg-2 {
        position: fixed; top: 0; left: 0; width: 100%; height: 100%;
        transition: opacity 2s ease-in-out; z-index: 1; background-color: #000;
        display: flex; align-items: center; justify-content: center;
        overflow: hidden;
    }
    #photo-bg-2 { opacity: 0; }

    /* The actual image inside each bg layer */
    .bg-img {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        object-fit: cover;       /* default: fill screen, crop if needed */
        object-position: center;
        transition: none;
    }
    /* Portrait photos: contain so nothing is cropped */
    .bg-img.portrait {
        object-fit: contain;
        background-color: #000;  /* letterbox bars */
    }

    .overlay-vignette {
        position: fixed; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 20%, rgba(0,0,0,0.75) 100%);
        z-index: 3; pointer-events: none;
    }

    /* ── NATURE CAPTION ── */
    #nature-caption {
        position: fixed; top: 28px; right: 28px;
        z-index: 20; text-align: right;
        opacity: 0; transition: opacity 0.8s ease; pointer-events: none;
    }
    #nature-caption.visible { opacity: 1; pointer-events: auto; }
    .caption-location {
        font-size: 1.5rem; font-weight: 700; color: #fff;
        text-transform: uppercase; letter-spacing: 3px;
        text-shadow: 0 2px 12px rgba(0,0,0,0.7); line-height: 1.2;
    }
    .caption-desc {
        font-size: 1rem; color: rgba(255,255,255,0.75);
        text-shadow: 0 2px 8px rgba(0,0,0,0.7);
        margin-top: 4px; max-width: 320px; margin-left: auto;
    }
    .caption-credit {
        font-size: 0.8rem; color: rgba(255,255,255,0.45);
        margin-top: 6px; text-shadow: 0 1px 6px rgba(0,0,0,0.6);
    }
    .caption-credit a { color: rgba(255,255,255,0.55); text-decoration: underline; }

    /* ── CREST INDICATOR — dots below crest showing position ── */
    #crest-indicator {
        position: fixed; bottom: 160px; left: 50%;
        transform: translateX(-50%);
        z-index: 20; display: none;
        gap: 8px; align-items: center;
    }
    #crest-indicator.visible { display: flex; }
    .crest-dot {
        width: 8px; height: 8px; border-radius: 50%;
        background: rgba(255,255,255,0.3);
        transition: background 0.3s;
    }
    .crest-dot.active { background: rgba(255,255,255,0.9); }

    /* ── CLOCK / WEATHER ── */
    .bottom-ui {
        position: fixed; bottom: 28px; left: 0; width: 100%;
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

    /* ── BG SELECTOR — intentionally smaller/subtler than the main nav ── */
    .bg-selector {
        position: fixed; bottom: 112px; left: 50%;
        transform: translateX(-50%);
        z-index: 1001; display: flex; gap: 4px;
        background: rgba(0,0,0,0.45); backdrop-filter: blur(8px);
        -webkit-backdrop-filter: blur(8px);
        padding: 4px 8px; border-radius: 40px;
        border: 1px solid rgba(255,255,255,0.08);
        opacity: 0.2; transition: opacity 0.4s;
    }
    .bg-selector:hover, .bg-selector:focus-within { opacity: 1; }
    .btn-mode {
        background: transparent; color: rgba(255,255,255,0.55);
        border: none; padding: 4px 14px; border-radius: 40px;
        cursor: pointer; font-size: 0.85rem; font-weight: 600;
        text-transform: uppercase; letter-spacing: 1px;
        transition: color 0.3s, background 0.3s; white-space: nowrap;
        -webkit-tap-highlight-color: transparent; touch-action: manipulation;
    }
    .btn-mode:hover { color: rgba(255,255,255,0.85); }
    .btn-mode.active { background: rgba(255,255,255,0.12); color: rgba(255,255,255,0.9); }

    /* ── PHOTO NAV ARROWS ── */
    .photo-nav-arrow {
        position: fixed; top: 50%; transform: translateY(-50%);
        z-index: 100;
        background: rgba(255,255,255,0.08); backdrop-filter: blur(10px);
        border: 1px solid rgba(255,255,255,0.18);
        color: white; width: 64px; height: 64px; border-radius: 50%;
        cursor: pointer; font-size: 1.5rem;
        display: flex; align-items: center; justify-content: center;
        opacity: 0; transition: opacity 0.4s, background 0.3s;
        -webkit-tap-highlight-color: transparent; touch-action: manipulation;
    }
    body:hover .photo-nav-arrow { opacity: 1; }
    .photo-nav-arrow:hover { background: rgba(255,255,255,0.28); opacity: 1; }
    @media (hover: none) {
        .photo-nav-arrow { opacity: 0.5; }
        .bg-selector { opacity: 1; }
    }
    #arrow-prev { left: 24px; }
    #arrow-next { right: 24px; }

    /* ── UPLOAD BUTTON — subtle camera, top-left, appears on hover ── */
    #upload-btn {
        position: fixed; top: 20px; left: 24px; z-index: 500;
        background: rgba(0,0,0,0.35); backdrop-filter: blur(10px);
        border: 1px solid rgba(255,255,255,0.15); border-radius: 50%;
        width: 44px; height: 44px;
        display: flex; align-items: center; justify-content: center;
        font-size: 1.2rem; cursor: pointer; text-decoration: none;
        opacity: 0; transition: opacity 0.4s;
        -webkit-tap-highlight-color: transparent; touch-action: manipulation;
    }
    body:hover #upload-btn { opacity: 1; }
    #upload-btn:hover { background: rgba(0,0,0,0.6); opacity: 1; }
    @media (hover: none) { #upload-btn { opacity: 0.6; } }
</style>

<div id="photo-bg-1"></div>
<div id="photo-bg-2"></div>
<div class="overlay-vignette"></div>

<div id="nature-caption">
    <div class="caption-location" id="caption-location"></div>
    <div class="caption-desc"     id="caption-desc"></div>
    <div class="caption-credit"   id="caption-credit"></div>
</div>

<!-- Crest position indicator dots -->
<div id="crest-indicator"></div>

<button id="arrow-prev" class="photo-nav-arrow" onclick="handlePrev()" aria-label="Previous">&#10094;</button>
<button id="arrow-next" class="photo-nav-arrow" onclick="handleNext()" aria-label="Next">&#10095;</button>

<a id="upload-btn" href="{{ site.baseurl }}/upload" title="Upload photos" aria-label="Upload photos">📷</a>

<div class="bottom-ui">
    <div class="weather-box">
        <a class="weatherwidget-io"
           href="https://forecast7.com/en/47d17n122d52/lakewood/?unit=us"
           data-label_1="LAKEWOOD" data-label_2="WASHINGTON"
           data-icons="Climacons Animated" data-theme="pure"
           data-basecolor="transparent" data-textcolor="#ffffff" 
           data-highcolor="#e7eb1c" data-lowcolor="#9f21dd" >LAKEWOOD WEATHER>LAKEWOOD WA
            {{ site.weather_label_1 }} {{ site.weather_label_2 }}
        </a>
    </div>
    <div class="clock-box">
        <div id="time-display">00:00</div>
        <div id="date-display">LOADING</div>
    </div>
</div>

<div class="bg-selector">
    {% for mode in site.bg_modes %}
        {% if mode == 'family' %}
        <button class="btn-mode" id="btn-family" onclick="setMode('family')">Family Photos</button>
        {% endif %}
        {% if mode == 'bonus' %}
        <button class="btn-mode" id="btn-bonus"  onclick="setMode('bonus')">Bonus Photos</button>
        {% endif %}
        {% if mode == 'nature' %}
        <button class="btn-mode" id="btn-nature" onclick="setMode('nature')">Daily Nature</button>
        {% endif %}
        {% if mode == 'crest' %}
        <button class="btn-mode" id="btn-crest"  onclick="setMode('crest')">Family Crest</button>
        {% endif %}
    {% endfor %}
</div>

<script>
    /* ── Config from _config.yml ─────────────────────────────────── */
    var CLOUD        = '{{ site.cloudinary_cloud }}';
    var FOLDER       = '{{ site.cloudinary_folder }}';
    var FOLDER_BONUS = '{{ site.cloudinary_folder_bonus }}';
    var TAG          = '{{ site.cloudinary_tag }}';
    var UNSPLASH_KEY = '{{ site.unsplash_key }}';
    var BG_MODES     = {{ site.bg_modes | jsonify }};
    var BASE_URL     = '{{ site.baseurl }}';

    /* Crest images — array injected from _config.yml crest_images list */
    var CREST_IMAGES = [
        {% for img in site.crest_images %}
        '{{ site.baseurl }}{{ img }}'{% unless forloop.last %},{% endunless %}
        {% endfor %}
    ];
    /* ─────────────────────────────────────────────────────────────── */

    var photoUrls         = [];
    var activeBg          = 1;
    var currentIndex      = 0;
    var crestIndex        = 0;
    var slideshowInterval = null;
    var currentMode       = null;

    /* ── Clock ── */
    function updateClock() {
        var now = new Date();
        var h   = now.getHours() % 12 || 12;
        var m   = now.getMinutes().toString().padStart(2,'0');
        document.getElementById('time-display').textContent = h + ':' + m;
        document.getElementById('date-display').textContent =
            now.toLocaleDateString('en-US',{weekday:'long',month:'short',day:'numeric'});
    }
    updateClock();
    setInterval(updateClock, 1000);

    /* ── Cross-fade ── */
    function applyBackground(url) {
        var nextBg  = activeBg === 1 ? 2 : 1;
        var current = document.getElementById('photo-bg-' + activeBg);
        var next    = document.getElementById('photo-bg-' + nextBg);

        var img = new Image();
        img.onload = function() {
            /* Detect portrait photos (taller than wide) */
            var isPortrait = img.naturalHeight > img.naturalWidth * 1.1;

            /* Build or update the <img> inside the next layer */
            var existing = next.querySelector('.bg-img');
            if (!existing) {
                existing = document.createElement('img');
                existing.className = 'bg-img';
                existing.alt = '';
                next.appendChild(existing);
            }
            existing.src = url;
            existing.classList.toggle('portrait', isPortrait);

            next.style.opacity    = 1;
            current.style.opacity = 0;
            activeBg = nextBg;
        };
        img.onerror = function() { console.error('Failed to load: ' + url); };
        img.src = url;
    }

    /* ── Cloudinary fetch ── */
    function shuffle(arr) {
        return arr.map(function(r){ return {r:r,s:Math.random()}; })
                  .sort(function(a,b){ return a.s-b.s; })
                  .map(function(o){ return o.r; });
    }

    async function fetchByFolder(folder) {
        var url  = 'https://res.cloudinary.com/'+CLOUD+'/image/list/'+folder+'.json?cb='+Date.now();
        var resp = await fetch(url);
        if (!resp.ok) throw new Error('Cloudinary '+resp.status);
        var data = await resp.json();
        return shuffle(data.resources||[]).map(function(r){
            return 'https://res.cloudinary.com/'+CLOUD
                 + '/image/upload/q_auto,f_auto,w_2560,c_limit/'
                 + r.public_id+'.'+r.format;
        });
    }

    async function loadPhotos(folder) {
        try {
            var urls = await fetchByFolder(folder);
            if (!urls.length) throw new Error('No photos in '+folder);
            photoUrls    = urls;
            currentIndex = 0;
            applyBackground(photoUrls[0]);
            startSlideshow();
        } catch(e) {
            console.warn('Photo load failed:', e);
            applyBackground('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?q=80&w=2560');
        }
    }

    function startSlideshow() {
        if (slideshowInterval) clearInterval(slideshowInterval);
        slideshowInterval = setInterval(function(){
            currentIndex = (currentIndex+1) % photoUrls.length;
            applyBackground(photoUrls[currentIndex]);
        }, 30000);
    }
    function resetSlideshow() {
        if (slideshowInterval) { clearInterval(slideshowInterval); startSlideshow(); }
    }

    /* ── Photo nav (family / bonus modes) ── */
    function nextPhoto() {
        if (!photoUrls.length) return;
        currentIndex = (currentIndex+1) % photoUrls.length;
        applyBackground(photoUrls[currentIndex]);
        resetSlideshow();
    }
    function prevPhoto() {
        if (!photoUrls.length) return;
        currentIndex = (currentIndex-1+photoUrls.length) % photoUrls.length;
        applyBackground(photoUrls[currentIndex]);
        resetSlideshow();
    }

    /* ── Crest cycling ── */
    function buildCrestDots() {
        var el = document.getElementById('crest-indicator');
        el.innerHTML = '';
        if (CREST_IMAGES.length <= 1) return; // no dots needed for single crest
        CREST_IMAGES.forEach(function(_, i) {
            var dot = document.createElement('div');
            dot.className = 'crest-dot' + (i === crestIndex ? ' active' : '');
            dot.id = 'crest-dot-' + i;
            el.appendChild(dot);
        });
    }

    function updateCrestDots() {
        CREST_IMAGES.forEach(function(_, i) {
            var dot = document.getElementById('crest-dot-' + i);
            if (dot) dot.className = 'crest-dot' + (i === crestIndex ? ' active' : '');
        });
    }

    function showCrest(index) {
        crestIndex = (index + CREST_IMAGES.length) % CREST_IMAGES.length;
        applyBackground(CREST_IMAGES[crestIndex]);
        updateCrestDots();
    }

    function nextCrest() { showCrest(crestIndex + 1); }
    function prevCrest() { showCrest(crestIndex - 1); }

    /* ── Unified arrow handlers — behaviour depends on current mode ── */
    function handleNext() {
        if (currentMode === 'crest')  nextCrest();
        else                           nextPhoto();
    }
    function handlePrev() {
        if (currentMode === 'crest')  prevCrest();
        else                           prevPhoto();
    }

    /* ── Nature caption ── */
    function showCaption(loc, desc, name, userUrl, photoUrl) {
        document.getElementById('caption-location').textContent = loc || 'Daily Nature';
        document.getElementById('caption-desc').textContent     = desc || '';
        var el = document.getElementById('caption-credit');
        el.innerHTML = name
            ? 'Photo by <a href="'+userUrl+'" target="_blank" rel="noopener">'+name+'</a>'
            + ' on <a href="'+photoUrl+'" target="_blank" rel="noopener">Unsplash</a>'
            : '';
        document.getElementById('nature-caption').classList.add('visible');
    }
    function hideCaption() {
        document.getElementById('nature-caption').classList.remove('visible');
    }

    /* ── Unsplash daily nature ── */
    async function fetchNature() {
        var today    = new Date();
        var cacheKey = 'nature-'+today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate();
        var cached   = sessionStorage.getItem(cacheKey);
        if (cached) {
            var d = JSON.parse(cached);
            applyBackground(d.url);
            showCaption(d.loc,d.desc,d.name,d.userUrl,d.photoUrl);
            return;
        }
        if (!UNSPLASH_KEY || UNSPLASH_KEY === 'uVlnR0MpCzzax-Zw8ViI6VOCJuo4z6H0G41lnZUgrWI') {
            applyBackground('https://images.unsplash.com/photo-1470770841072-f978cf4d019e?q=80&w=2560&auto=format&fit=crop');
            showCaption('Daily Nature','Add unsplash_key to _config.yml','','','');
            return;
        }
        try {
            var resp = await fetch('https://api.unsplash.com/photos/random'
                +'?query=nature+landscape+scenic&orientation=landscape'
                +'&content_filter=high&client_id='+UNSPLASH_KEY);
            if (!resp.ok) throw new Error(resp.status);
            var p = await resp.json();
            var d = {
                url:     p.urls.raw+'&w=2560&q=85&fit=crop&auto=format',
                loc:     (p.location&&(p.location.name||p.location.city||p.location.country))||'',
                desc:    (p.description||p.alt_description||'').substring(0,80),
                name:    p.user.name,
                userUrl: p.user.links.html+'?utm_source=family_dashboard&utm_medium=referral',
                photoUrl:p.links.html+'?utm_source=family_dashboard&utm_medium=referral'
            };
            sessionStorage.setItem(cacheKey, JSON.stringify(d));
            applyBackground(d.url);
            showCaption(d.loc,d.desc,d.name,d.userUrl,d.photoUrl);
        } catch(e) {
            console.warn('Unsplash error:',e);
            applyBackground('https://images.unsplash.com/photo-1470770841072-f978cf4d019e?q=80&w=2560&auto=format&fit=crop');
        }
    }

    /* ── Mode selector ── */
    function setMode(mode) {
        if (BG_MODES.indexOf(mode) === -1) mode = BG_MODES[0];
        currentMode = mode;
        localStorage.setItem(BASE_URL+'-bg-mode', mode);

        document.querySelectorAll('.btn-mode').forEach(function(b){ b.classList.remove('active'); });
        var btn = document.getElementById('btn-'+mode);
        if (btn) btn.classList.add('active');

        if (slideshowInterval) { clearInterval(slideshowInterval); slideshowInterval = null; }
        photoUrls = [];
        hideCaption();

        /* Arrows: visible for photo modes AND crest (when >1 crest) */
        var showArrows = (mode === 'family' || mode === 'bonus')
                      || (mode === 'crest' && CREST_IMAGES.length > 1);
        document.getElementById('arrow-prev').style.display = showArrows ? '' : 'none';
        document.getElementById('arrow-next').style.display = showArrows ? '' : 'none';

        /* Crest dots: only in crest mode */
        var indicator = document.getElementById('crest-indicator');
        if (mode === 'crest' && CREST_IMAGES.length > 1) {
            indicator.classList.add('visible');
        } else {
            indicator.classList.remove('visible');
        }

        if      (mode === 'family') { loadPhotos(FOLDER); }
        else if (mode === 'bonus')  { loadPhotos(FOLDER_BONUS); }
        else if (mode === 'nature') { fetchNature(); }
        else if (mode === 'crest')  {
            buildCrestDots();
            showCrest(crestIndex); // resume from last viewed crest
        }
    }

    var saved = localStorage.getItem(BASE_URL+'-bg-mode') || BG_MODES[0];
    setMode(saved);

    !function(d,s,id){
        var js,fjs=d.getElementsByTagName(s)[0];
        if(!d.getElementById(id)){js=d.createElement(s);js.id=id;
        js.src='https://weatherwidget.io/js/widget.min.js';
        fjs.parentNode.insertBefore(js,fjs);}
    }(document,'script','weatherwidget-io-js');
</script>