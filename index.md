---
layout: default
title: Home
---

<style>
    /* Background Layers */
    #photo-bg-1, #photo-bg-2 {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background-size: cover; background-position: center;
        transition: opacity 2s ease-in-out; z-index: 1; background-color: #000;
    }
    #photo-bg-2 { opacity: 0; }
    
    .overlay-vignette {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 20%, rgba(0,0,0,0.8) 100%);
        z-index: 3; pointer-events: none;
    }

    /* UI Placement */
    .bottom-ui {
        position: absolute; bottom: 60px; left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 70px; box-sizing: border-box; z-index: 10;
    }
    .weather-box {
        width: 380px; background: rgba(0,0,0,0.4); backdrop-filter: blur(15px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
    }
    .clock-box { text-align: right; text-shadow: 0 4px 20px rgba(0,0,0,0.5); }
    #time-display { font-size: 10rem; font-weight: 900; line-height: 0.8; margin: 0; letter-spacing: -6px; color: #fff; }
    #date-display { font-size: 2.4rem; font-weight: 300; text-transform: uppercase; letter-spacing: 6px; opacity: 0.8; color: #fff; }

    /* The Selector Toggle - Top Right */
    .bg-selector {
        position: fixed; top: 30px; right: 30px; z-index: 1000;
        display: flex; flex-direction: column; gap: 8px;
        background: rgba(0,0,0,0.5); backdrop-filter: blur(10px);
        padding: 12px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1);
        opacity: 0; transition: opacity 0.5s;
    }
    body:hover .bg-selector { opacity: 1; }

    /* Manual Navigation Controls */
    <div class="photo-nav">
        <button onclick="prevPhoto()" class="nav-arrow">❮</button>
        <button onclick="nextPhoto()" class="nav-arrow">❯</button>
    </div>
    
    .btn-mode {
        background: rgba(255,255,255,0.1); color: white; border: none;
        padding: 8px 15px; border-radius: 6px; cursor: pointer;
        font-size: 1rem; text-transform: uppercase; letter-spacing: 1px;
        transition: 0.3s; text-align: left; width: 140px;
    }
    .btn-mode.active { background: #fff !important; color: #000 !important; font-weight: 900; }
</style>

<div id="photo-bg-1"></div>
<div id="photo-bg-2"></div>
<div class="overlay-vignette"></div>

<div class="bg-selector">
    <button class="btn-mode" id="btn-family" onclick="setMode('family')">Family Photos</button>
    <button class="btn-mode" id="btn-nature" onclick="setMode('nature')">Daily Nature</button>
    <button class="btn-mode" id="btn-crest" onclick="setMode('crest')">Family Crest</button>
</div>

<div class="bottom-ui">
    <div class="weather-box">
        <a class="weatherwidget-io" href="https://forecast7.com/en/47d17n122d52/lakewood/?unit=us" 
           data-label_1="LAKEWOOD" data-label_2="WASHINGTON" 
           data-icons="Climacons Animated" data-theme="pure" data-basecolor="transparent" 
           data-textcolor="#ffffff">LAKEWOOD WA</a>
    </div>
    <div class="clock-box">
        <div id="time-display">00:00</div>
        <div id="date-display">LOADING</div>
    </div>
</div>

<script>
    var cloudName = 'dybmaxwvb'; 
    var tagName = 'dashboard';
    // Path check: ensure this file exists in assets/img/ and matches case exactly
    var crestPath = 'assets/img/family-crest-metal-on-wood.png'; 
    
    var photoUrls = [];
    var activeBg = 1;
    var slideshowInterval = null;

    function updateClock() {
        var now = new Date();
        var h = now.getHours() % 12 || 12;
        var m = now.getMinutes().toString().padStart(2, '0');
        document.getElementById('time-display').textContent = h + ":" + m;
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }
    updateClock();
    setInterval(updateClock, 1000);

    function applyBackground(url) {
        var nextBg = activeBg === 1 ? 2 : 1;
        var currentEl = document.getElementById('photo-bg-' + activeBg);
        var nextEl = document.getElementById('photo-bg-' + nextBg);

        var img = new Image();
        img.src = url;
        img.onload = function() {
            nextEl.style.backgroundImage = "url('" + url + "')";
            nextEl.style.opacity = 1;
            currentEl.style.opacity = 0;
            activeBg = nextBg;
        };
        img.onerror = function() {
            console.error("Failed to load background: " + url);
        };
    }

    async function fetchCloudinary() {
        try {
            var listUrl = 'https://res.cloudinary.com/' + cloudName + '/image/list/' + tagName + '.json?cb=' + Date.now();
            var response = await fetch(listUrl);
            if (!response.ok) throw new Error("Cloudinary fetch failed.");
            var data = await response.json();
            photoUrls = data.resources.map(function(res) {
                return 'https://res.cloudinary.com/' + cloudName + '/image/upload/q_auto,f_auto,w_1920,c_limit/' + res.public_id + '.' + res.format;
            });
            if (photoUrls.length > 0) {
                rotateFamilyPhoto();
                slideshowInterval = setInterval(rotateFamilyPhoto, 30000);
            }
        } catch (e) {
            applyBackground('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?q=80&w=1920');
        }
    }

    function rotateFamilyPhoto() {
        if (photoUrls.length === 0) return;
        var url = photoUrls[Math.floor(Math.random() * photoUrls.length)];
        applyBackground(url);
    }

    function setMode(mode) {
        localStorage.setItem('dashboard-bg-mode', mode);
        var buttons = document.querySelectorAll('.btn-mode');
        for (var i = 0; i < buttons.length; i++) { buttons[i].classList.remove('active'); }
        document.getElementById('btn-' + mode).classList.add('active');

        if (slideshowInterval) clearInterval(slideshowInterval);

        if (mode === 'family') {
            fetchCloudinary();
        } else if (mode === 'nature') {
            applyBackground('https://images.unsplash.com/photo-1470770841072-f978cf4d019e?q=80&w=1920&auto=format&fit=crop');
        } else if (mode === 'crest') {
            // Note: If this fails, check your console (F12) for the specific error path
            applyBackground(crestPath);
        }
    }

    var savedMode = localStorage.getItem('dashboard-bg-mode') || 'family';
    setMode(savedMode);

    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
</script>
