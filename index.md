---
layout: default
title: Home
---

<style>
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
    .bottom-ui {
        position: absolute; bottom: 60px; left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 70px; box-sizing: border-box; z-index: 10;
    }
    .weather-box {
        width: 380px; background: rgba(0,0,0,0.4); backdrop-filter: blur(15px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
    }
    .clock-box { text-align: right; }
    #time-display { font-size: 10rem; font-weight: 900; line-height: 0.8; margin: 0; letter-spacing: -6px; }
    #date-display { font-size: 2.4rem; font-weight: 300; text-transform: uppercase; letter-spacing: 6px; opacity: 0.8; }
</style>

<div id="photo-bg-1"></div>
<div id="photo-bg-2"></div>
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
    const albumUrl = 'https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7';
    let photoArray = [];
    let currentIndex = 0;
    let activeBg = 1;

    async function fetchPhotos() {
        // Added a 3rd proxy and changed logic to use .text() to avoid "Not valid JSON" errors
        const proxies = [
            'https://api.allorigins.win/get?url=',
            'https://corsproxy.io/?',
            'https://thingproxy.freeboard.io/fetch/'
        ];

        for (let proxy of proxies) {
            try {
                console.log(`Trying proxy: ${proxy}`);
                const response = await fetch(proxy + encodeURIComponent(albumUrl));
                
                // We use .text() because proxies often return raw HTML which crashes .json()
                let html = "";
                if (proxy.includes("allorigins")) {
                    const data = await response.json();
                    html = data.contents;
                } else {
                    html = await response.text();
                }

                if (!html || html.includes("Oops") || html.includes("Too Large")) {
                    throw new Error("Proxy response invalid or too large");
                }

                // Regex to find the images in the Google Photos page source
                const regex = /"(https:\/\/lh3\.googleusercontent\.com\/pw\/[a-zA-Z0-9\-_]+)"/g;
                let match;
                const seen = new Set();
                
                while ((match = regex.exec(html)) !== null) {
                    const url = match[1] + "=w1920-h1080";
                    if (!seen.has(url)) { photoArray.push(url); seen.add(url); }
                }

                if (photoArray.length > 0) {
                    console.log(`Success! Found ${photoArray.length} photos.`);
                    startSlideshow();
                    return; 
                }
            } catch (e) {
                console.warn(`Proxy failed: ${proxy}`, e);
            }
        }
    }

    function startSlideshow() {
        updateBackground();
        setInterval(updateBackground, 20000); // 20 second rotation
    }

    function updateBackground() {
        if (photoArray.length === 0) return;
        const url = photoArray[currentIndex];
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
            currentIndex = (currentIndex + 1) % photoArray.length;
        };
    }

    // FIXED CLOCK LOGIC
    function updateClock() {
        const now = new Date();
        let h = now.getHours() % 12 || 12;
        let m = now.getMinutes().toString().padStart(2, '0');
        // The parentheses below ensure the full time string is built before being set
        document.getElementById('time-display').textContent = h + ":" + m;
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }

    updateClock();
    setInterval(updateClock, 1000);
    fetchPhotos();

    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
</script>
