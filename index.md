---
layout: default
title: Home
---
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Refined Family Dashboard</title>
    <!-- Refined, Professional Font: Montserrat -->
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;700;900&display=swap" rel="stylesheet">
    
    <style>
        body, html {
            margin: 0; padding: 0;
            width: 100%; height: 100%;
            font-family: 'Montserrat', sans-serif;
            background-color: #000;
            overflow: hidden;
            color: white;
        }

        /* Full-screen Background Slider */
        #background-container {
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            z-index: 1;
        }

        /* Top-level Header UI */
        .header-ui {
            position: absolute;
            top: 0; left: 0;
            width: 100%;
            display: flex;
            justify-content: space-between;
            padding: 40px;
            box-sizing: border-box;
            z-index: 10;
            /* Gradient to ensure text pops against bright photos */
            background: linear-gradient(to bottom, rgba(0,0,0,0.7) 0%, transparent 100%);
        }

        /* Clock Styling (Top Left) */
        .clock-section {
            text-align: left;
        }
        #time {
            font-size: 6rem;
            font-weight: 900;
            line-height: 1;
            letter-spacing: -3px;
        }
        #date {
            font-size: 1.5rem;
            font-weight: 300;
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-top: 5px;
            opacity: 0.9;
        }

        /* Weather Styling (Top Right) */
        .weather-section {
            text-align: right;
            width: 350px;
        }
        /* Iframe wrapper to ensure display */
        .weather-box {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 15px;
            border: 1px solid rgba(255,255,255,0.2);
        }
    </style>
</head>
<body>

    <!-- PHOTO BACKGROUND -->
    <div id="background-container">
        <!-- Using the PublicAlbum embed - Note: Album must be PUBLICly viewable -->
        <script src="https://cdn.jsdelivr.net/npm/publicalbum@latest/embed-ui.min.js" async></script>
        <div class="pa-carousel-widget" 
             data-link="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"
             data-delay="10" 
             data-repeat="true"
             style="width:100%; height:100%;">
            <object data="https://photos.app.goo.gl/R5JTm4dMNEbHEvjm7"></object>
        </div>
    </div>

    <!-- UI OVERLAY -->
    <div class="header-ui">
        <!-- TOP LEFT: CLOCK -->
        <div class="clock-section">
            <div id="time">00:00</div>
            <div id="date">Loading...</div>
        </div>

        <!-- TOP RIGHT: WEATHER -->
        <div class="weather-section">
            <div class="weather-box">
                <!-- Forecast7 Widget with 'Static' Fallback for Iframe safety -->
                <a class="weatherwidget-io" 
                   href="https://forecast7.com/en/47d17n122d53/98498/" 
                   data-label_1="LAKEWOOD" 
                   data-label_2="5-DAY" 
                   data-font="Montserrat" 
                   data-icons="Climacons Animated" 
                   data-theme="pure" 
                   data-basecolor="transparent" 
                   data-textcolor="#ffffff">LAKEWOOD</a>
            </div>
        </div>
    </div>

    <script>
        // Clock Logic
        function updateDisplay() {
            const now = new Date();
            
            // Time format
            let h = now.getHours();
            let m = now.getMinutes();
            const ampm = h >= 12 ? 'PM' : 'AM';
            h = h % 12 || 12;
            m = m < 10 ? '0' + m : m;
            document.getElementById('time').textContent = `${h}:${m} ${ampm}`;
            
            // Date format
            const options = { weekday: 'long', month: 'short', day: 'numeric' };
            document.getElementById('date').textContent = now.toLocaleDateString('en-US', options);
        }

        // Weather Loader (Ensures the widget script runs inside the embed)
        !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src='https://weatherwidget.io/js/widget.min.js';fjs.parentNode.insertBefore(js,fjs);}}(document,'script','weatherwidget-io-js');

        setInterval(updateDisplay, 1000);
        updateDisplay();
    </script>
</body>
</html>
