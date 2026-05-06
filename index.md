---
layout: default
title: Home Dashboard
---
<!-- Refined geometic font for numbers/weather -->
<link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;700;900&display=swap" rel="stylesheet">

<style>
    /* Full-screen Background Slider */
    #background-slider {
        position: absolute; top: 0; left: 0;
        width: 100vw; height: 100vh;
        z-index: 1; /* Lowest layer */
        background-color: #000;
    }

    /* Subtle gradient overlay to ensure text is always readable */
    .vignette {
        position: absolute; bottom: 0; left: 0;
        width: 100%; height: 50%;
        background: linear-gradient(to top, rgba(0,0,0,0.8), transparent);
        z-index: 2;
        pointer-events: none;
    }

    /* Information Overlay Wrapper */
    .dashboard-ui {
        position: absolute;
        top: 0; left: 0;
        width: 100%; height: 100%;
        display: flex; justify-content: space-between; align-items: flex-start;
        padding: 50px; box-sizing: border-box;
        z-index: 3;
        pointer-events: none; /* Let touches pass to the photos */
    }

    /* Refined Clock Style (Top Left) */
    .clock-section { text-align: left; text-shadow: 2px 4px 10px rgba(0,0,0,0.8); }
    #time { font-size: 8rem; font-weight: 900; line-height: 1; letter-spacing: -4px; margin: 0; }
    #date { font-size: 2rem; font-weight: 300; text-transform: uppercase; letter-spacing: 4px; opacity: 0.9; margin-top: 5px; }

    /* Modern Weather Widget (Top Right) */
    .weather-section {
        width: 400px; text-align: right; pointer-events: auto; /* Allow interacting with weather */
        background: rgba(0,0,0,0.4); backdrop-filter: blur(10px);
        padding: 15px; border-radius: 15px;
        border: 1px solid rgba(255,255,255,0.15);
        box-shadow: 0 10px 30px rgba(0,0,0,0.4);
    }
</style>

<!-- PHOTO BACKGROUND (Layer 1) -->
<div id="background-slider">
    <!-- Using the robust PublicAlbum embed -->
    <script src="https://cdn.jsdelivr.net/npm/publicalbum@latest/embed-ui.min.js" async></script>
    <div class="pa-carousel-widget" 
         data-link="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"
         data-delay="8" 
         data-repeat="true"
         style="width:100%; height:100%; display:none;">
        <object data="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"></object>
    </div>
</div>

<!-- VIGNETTE (Layer 2) -->
<div class="vignette"></div>

<!-- UI OVERLAY (Layer 3) -->
<div class="dashboard-ui">
    
    <!-- Clock: Montserrat Font -->
    <div class="clock-section">
        <h1 id="time">00:00</h1>
        <h2 id="date">Loading...</h2>
    </div>

    <!-- Weather: Refined Animated Icons -->
    <div class="weather-section">
        <a class="weatherwidget-io" 
           href="https://forecast7.com/en/47d17n122d53/98498/" 
           data-label_1="LAKEWOOD, WA" 
           data-label_2="5-Day Forecast" 
           data-font="Montserrat" 
           data-icons="Climacons Animated" 
           data-theme="pure" 
           data-basecolor="transparent" 
           data-textcolor="#ffffff">
           LAKEWOOD, WA 5-Day Forecast
        }</a>
    </div>

</div>

<!-- Logic Scripts -->
<script>
    // Clock Update
    function updateClock() {
        const now = new Date();
        
        // Time: HH:MM AM/PM
        let h = now.getHours();
        let m = now.getMinutes();
        const ampm = h >= 12 ? 'PM' : 'AM';
        h = h % 12 || 12;
        m = m < 10 ? '0' + m : m;
        document.getElementById('time').textContent = `${h}:${m} ${ampm}`;
        
        // Date: Wkdy, Mon Day
        const options = { weekday: 'long', month: 'short', day: 'numeric' };
        document.getElementById('date').textContent = now.toLocaleDateString('en-US', options);
    }

    // Weather Loader
    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');

    // Interval settings
    setInterval(updateClock, 1000);
    updateClock(); // Initial run
</script>
