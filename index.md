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
    
    /* Overlay for readability */
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
    #time-display { font-size: 10rem; font-weight: 900; line-height: 0.8; margin: 0; letter-spacing: -6px; }
    #date-display { font-size: 2.4rem; font-weight: 300; text-transform: uppercase; letter-spacing: 6px; opacity: 0.8; }

    /* The Selector Toggle */
    .bg-selector {
        position: fixed; top: 100px; right: 30px; z-index: 1000;
        display: flex; flex-direction: column; gap: 10px;
        background: rgba(0,0,0,0.5); backdrop-filter: blur(10px);
        padding: 15px; border-radius: 15px; border: 1px solid rgba(255,255,255,0.1);
        opacity: 0; transition: opacity 0.5s;
    }
    body:hover .bg-selector { opacity: 1; } /* Only shows when you move the mouse/touch */
    .btn-mode {
        background: rgba(255,255,255,0.1); color: white; border: none;
        padding: 8px 15px; border-radius: 8px; cursor: pointer;
        font-size: 0.7rem; text-transform: uppercase; letter-spacing: 1px;
        transition: 0.3s;
    }
    .btn-mode.active { background: #fff; color: #000; font-weight: 900; }
</style>

<div id="photo-bg-1"></div>
<div id="photo-bg-2"></div>
<div class="overlay-vignette"></div>

<div class="bg-selector">
    <button class="btn-mode" onclick="setMode('family')">Family Photos</button>
    <button class="btn-mode" onclick="setMode('nature')">Daily Nature</button>
    <button class="btn-mode" onclick="setMode('crest')">Family Crest</button>
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
    const cloudName = 'YOUR_CLOUD_NAME'; 
    const tagName = 'dashboard';
    const crestPath = 'assets/img/family-crest.png'; // Path to your crest
    
    let photoUrls = [];
    let activeBg = 1;
    let slideshowInterval = null;

    // 1. Core Mode Logic
    function setMode(mode) {
        localStorage.setItem('dashboard-bg-mode', mode);
        
        // Update Button UI
        document.querySelectorAll('.btn-mode').forEach(btn => {
            btn.classList.remove('active');
            if(btn.innerText.toLowerCase().includes(mode)) btn.classList.add('active');
        });

        // Clear existing intervals
        if (slideshowInterval) clearInterval(slideshowInterval);

        if (mode === 'family') {
            fetchCloudinary();
        } else if (mode === 'nature') {
            const natureUrl = `https://source.unsplash.com/featured/1920x1080/?nature,landscape,mountains&sig=${new Date().getDay()}`;
            applyBackground(natureUrl);
        } else if (mode === 'crest') {
            applyBackground(crestPath);
        }
    }

    // 2. Background Application (with crossfade)
    function applyBackground(url) {
        const nextBg = activeBg === 1 ? 2 : 1;
        const currentEl = document.getElementById(`photo-bg-${activeBg}`);
        const nextEl = document.getElementById(`photo-bg-${nextBg}`);

        const img = new Image();
        img.src = url;
        img.onload = () => {
            nextEl.style.backgroundImage = `url('${url}')`;
            nextEl.style.opacity = 1;
            currentEl.style.opacity = 0;
            activeBg = nextBg;
        };
    }

    // 3. Cloudinary Fetch
    async function fetchCloudinary() {
        try {
            const response = await fetch(`https://res.cloudinary.com/${cloudName}/image/list/${tagName}.json?cb=${Date.now()}`);
            const data = await response.json();
            photoUrls = data.resources.map(res => 
                `https://res.cloudinary.com/${cloudName}/image/upload/q_auto,f_auto,w_1920,c_limit/${res.public_id}.${res.format}`
            );
            if (photoUrls.length > 0) {
                rotateFamilyPhoto();
                slideshowInterval = setInterval(rotateFamilyPhoto, 30000);
            }
        } catch (e) { console.error("Cloudinary failed", e); }
    }

    function rotateFamilyPhoto() {
        const url = photoUrls[Math.floor(Math.random() * photoUrls.length)];
        applyBackground(url);
    }

    // 4. Clock Logic
    function updateClock() {
        const now = new Date();
        let h = now.getHours() % 12 || 12;
        let m = now.getMinutes().toString().padStart(2, '0');
        document.getElementById('time-display').textContent = h + ":" + m;
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }

    // Initialize
    updateClock();
    setInterval(updateClock, 1000);
    
    // Load saved mode or default to family
    const savedMode = localStorage.getItem('dashboard-bg-mode') || 'family';
    setMode(savedMode);

    // Weather Widget
    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
</script>
