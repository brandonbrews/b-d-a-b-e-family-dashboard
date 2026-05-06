---
layout: default
title: Home
---

<style>
    /* Ensure background is truly full-screen and black while loading */
    #photo-bg { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; background: #000; overflow: hidden; }
    
    .overlay-vignette {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        background: radial-gradient(circle, transparent 20%, rgba(0,0,0,0.85) 100%);
        z-index: 2; pointer-events: none;
    }

    .bottom-ui {
        position: absolute; bottom: 60px; left: 0; width: 100%;
        display: flex; justify-content: space-between; align-items: flex-end;
        padding: 0 70px; box-sizing: border-box; z-index: 10;
    }

    /* Weather: Bottom Left */
    .weather-box {
        width: 380px; background: rgba(0,0,0,0.4); backdrop-filter: blur(15px);
        padding: 20px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);
        font-family: 'Montserrat', sans-serif !important;
    }

    /* Clock: Bottom Right */
    .clock-box { text-align: right; text-shadow: 0 4px 30px rgba(0,0,0,1); }
    #time-display { font-size: 10rem; font-weight: 900; line-height: 0.8; margin: 0; letter-spacing: -6px; }
    #date-display { font-size: 2.4rem; font-weight: 300; text-transform: uppercase; letter-spacing: 6px; opacity: 0.8; }
</style>

<div id="photo-bg">
    <div class="pa-carousel-widget" 
         data-link="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"
         data-delay="10" 
         data-repeat="true" 
         style="width:100%; height:100%; display:none;">
    </div>
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

<script src="https://cdn.jsdelivr.net/npm/publicalbum@latest/embed-ui.min.js" async></script>

<script>
    // Clock Update
    function updateClock() {
        const now = new Date();
        let h = now.getHours() % 12 || 12;
        let m = now.getMinutes().toString().padStart(2, '0');
        document.getElementById('time-display').textContent = `${h}:${m}`;
        document.getElementById('date-display').textContent = now.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    }
    setInterval(updateClock, 1000);
    updateClock();

    // Weather Loader
    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');
</script>
