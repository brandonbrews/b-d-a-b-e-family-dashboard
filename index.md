---
layout: default
title: Home
---

<style>
    /* Dual-layer background for seamless cross-fading */
    #photo-bg-1, #photo-bg-2 {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background-size: cover; background-position: center;
        transition: opacity 2.5s ease-in-out; z-index: 1;
    }
    #photo-bg-2 { opacity: 0; }

    .overlay-vignette {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 20%, rgba(0,0,0,0.85) 100%);
        z-index: 3; pointer-events: none;
    }

    .bottom-ui {
        position: absolute; bottom: 60px; left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 70px; box-sizing: border-box; z-index: 10;
    }

    .weather-box {
        width: 380px; background: rgba(0,0,0,0.4); backdrop-filter: blur(15px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
    }

    .clock-box { text-align: right; text-shadow: 0 4px 30px rgba(0,0,0,1); }
    #time-display { font-size: 10rem; font-weight: 900; line-height: 0.8; margin: 0; letter-spacing: -6px; }
    #date-display { font-size: 2.4rem; font-weight: 300; text-transform: uppercase; letter-spacing: 6px; opacity: 0.8; }
</style>

<div id="photo-bg-container">
    <div id="photo-bg-1"></div>
    <div id="photo-bg-2"></div>
</div>

<div class="overlay-vignette"></div>

<div class="bottom-ui">
    <div class="weather-box">
        <a class="weatherwidget-io" href="https://forecast7.com/en/47d17n122d52/lakewood/?unit=us" 
           data-label_1="LAKEWOOD" data-label_2="WASHINGTON" data-font="Montserrat" 
           data-icons="Climacons Animated" data-theme="pure" data-basecolor="transparent" 
           data-textcolor="#ffffff">LAKEWOOD WA</a>
    </div>

    <div class="clock-box">
        <div id="time-display">00:00</div>
        <div id="date-display">LOADING</div>
    </div>
</div>

<script>
    // 1. AUTOMATED PHOTO SCRAPER
    const albumUrl = 'https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7';
    let photoArray = [];
    let currentIndex = 0;
    let activeBg = 1;

    async function fetchPhotos() {
        try {
            // Using a CORS proxy to read the public album's HTML content
            const proxyUrl = 'https://api.allorigins.win/get?url=' + encodeURIComponent(albumUrl);
            const response = await fetch(proxyUrl);
            const data = await response.json();
            const html = data.contents;

            // Regex to extract direct image URLs from the Google Photos page source
            const regex = /"(https:\/\/lh3\.googleusercontent\.com\/pw\/[a-zA-Z0-9\-_]+)"/g;
            let match;
            const seen = new Set();
            
            while ((match = regex.exec(html)) !== null) {
                const fullUrl = match[1] + "=w1920-h1080"; // Force HD resolution
                if (!seen.has(fullUrl)) {
                    photoArray.push(fullUrl);
                    seen.add(fullUrl);
                }
            }

            if (photoArray.length > 0) {
                shuffle(photoArray);
                updateBackground();
                setInterval(updateBackground, 15000); // Rotate every 15 seconds
            }
        } catch (e) {
            console.error("Photo Scraper failed:", e);
        }
    }

    function shuffle(array) {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
    }

    function updateBackground() {
        const url = photoArray[currentIndex];
        const nextBg = activeBg === 1 ? 2 : 1;
        const currentEl = document.getElementById(`photo-bg-${activeBg}`);
        const nextEl = document.getElementById(`photo-bg-${nextBg}`);

        // Preload image in memory to ensure a smooth transition
        const img = new Image();
        img.src = url;
        img.onload = () => {
            nextEl.style.backgroundImage = `url('${url}')`;
            nextEl.style.opacity = 1;
            currentEl.style.opacity = 0;
            activeBg = nextBg;
            currentIndex = (currentIndex + 1) % photoArray.length;
        };
    }

    // 2. CLOCK LOGIC
    function updateClock() {
        const now = new Date();
        let h = now.getHours() % 12 || 12;
        let m = now.getMinutes().toString().padStart(2, '0');
        document.getElementById('time-display').textContent = `${h}:${m}`;
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }

    // Initialize all components
    setInterval(updateClock, 1000);
    updateClock();
    fetchPhotos();

    // 3. WEATHER WIDGET INJECTOR
    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
</script>
