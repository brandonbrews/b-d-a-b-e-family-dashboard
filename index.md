---
layout: default
title: Home
---

<style>
    /* Layer 1: Google Photos Native Embed */
    #photo-bg { 
        position: absolute; top: 0; left: 0; 
        width: 100%; height: 100%; 
        z-index: 1; background: #000;
    }
    #photo-bg iframe { width: 100%; height: 100%; border: none; }

    .overlay-vignette {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 20%, rgba(0,0,0,0.8) 100%);
        z-index: 2; pointer-events: none;
    }

    .bottom-ui {
        position: absolute; bottom: 50px; left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 60px; box-sizing: border-box; z-index: 10;
    }

    .weather-box {
        width: 380px; background: rgba(0,0,0,0.5); backdrop-filter: blur(15px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
    }

    .clock-box { text-align: right; text-shadow: 0 4px 20px rgba(0,0,0,1); }
    #time-display { font-size: 9.5rem; font-weight: 900; line-height: 0.8; margin: 0; letter-spacing: -6px; }
    #date-display { font-size: 2.2rem; font-weight: 300; text-transform: uppercase; letter-spacing: 5px; opacity: 0.9; }
</style>

<div id="photo-bg">
    <iframe src="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7?embed=true"></iframe>
</div>

<div class="overlay-vignette"></div>

<div class="bottom-ui">
    <div class="weather-box" id="weather-container">
        <a class="weatherwidget-io" 
           href="https://forecast7.com/en/47d17n122d52/lakewood/?unit=us" 
           data-label_1="LAKEWOOD" 
           data-label_2="WASHINGTON" 
           data-font="Montserrat" 
           data-icons="Climacons Animated" 
           data-theme="pure" 
           data-basecolor="transparent" 
           data-textcolor="#ffffff">LAKEWOOD WA</a>
    </div>

    <div class="clock-box">
        <div id="time-display">00:00</div>
        <div id="date-display">LOADING</div>
    </div>
</div>

<script>
    function updateClock() {
        const now = new Date();
        let h = now.getHours() % 12 || 12;
        let m = now.getMinutes().toString().padStart(2, '0');
        document.getElementById('time-display').textContent = `${h}:${m}`;
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }
    setInterval(updateClock, 1000);
    updateClock();

    // Weather Loader with a small delay to ensure the container exists
    setTimeout(function() {
        !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
    }, 1000);
</script>
