---
layout: default
title: Home
---

<style>
    /* Full-screen Background */
    #photo-bg { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; }
    
    /* Darkening Vignette */
    .overlay-vignette {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 40%, rgba(0,0,0,0.7) 100%);
        z-index: 2; pointer-events: none;
    }

    /* Layout Containers */
    .bottom-ui {
        position: absolute; bottom: 50px; left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 60px; box-sizing: border-box; z-index: 10;
    }

    /* Weather (Bottom Left) */
    .weather-box {
        width: 380px; background: rgba(0,0,0,0.3); backdrop-filter: blur(8px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
    }

    /* Clock (Bottom Right) */
    .clock-box { text-align: right; text-shadow: 0 4px 15px rgba(0,0,0,0.9); }
    #time-display { font-size: 8.5rem; font-weight: 900; line-height: 0.9; margin: 0; letter-spacing: -5px; }
    #date-display { font-size: 2.2rem; font-weight: 300; text-transform: uppercase; letter-spacing: 5px; opacity: 0.8; }
</style>

<!-- BACKGROUND PHOTOS -->
<div id="photo-bg">
    <script src="https://cdn.jsdelivr.net/npm/publicalbum@latest/embed-ui.min.js" async></script>
    <div class="pa-carousel-widget" 
         data-link="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"
         data-delay="10" data-repeat="true" style="width:100%; height:100%; display:none;">
        <object data="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"></object>
    </div>
</div>

<div class="overlay-vignette"></div>

<!-- LOWER UI -->
<div class="bottom-ui">
    <!-- WEATHER (LEFT) -->
    <div class="weather-box">
        <a class="weatherwidget-io" href="https://forecast7.com/en/47d17n122d53/98498/" 
           data-label_1="LAKEWOOD" data-label_2="WASHINGTON" data-font="Montserrat" 
           data-icons="Climacons Animated" data-theme="pure" data-basecolor="transparent" 
           data-textcolor="#ffffff">LAKEWOOD WA</a>
    </div>

    <!-- CLOCK (RIGHT) -->
    <div class="clock-box">
        <div id="time-display">00:00</div>
        <div id="date-display">LOADING</div>
    </div>
</div>

<script>
    // 1. CLOCK LOGIC
    function updateClock() {
        const now = new Date();
        let h = now.getHours();
        let m = now.getMinutes();
        const ampm = h >= 12 ? 'PM' : 'AM';
        h = h % 12 || 12;
        m = m < 10 ? '0' + m : m;
        document.getElementById('time-display').textContent = `${h}:${m}`; // Removed ampm for cleaner look, add if desired
        
        const options = { weekday: 'long', month: 'short', day: 'numeric' };
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', options);
    }
    setInterval(updateClock, 1000);
    updateClock();

    // 2. WEATHER & PHOTO LOADER (Forces scripts to run after content loads)
    window.onload = function() {
        // Load Weather
        !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
        
        // Refresh PublicAlbum if it's stuck
        if(window.PA && window.PA.carousel) {
            window.PA.carousel.load();
        }
    };
</script>
