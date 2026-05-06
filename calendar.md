---
layout: default
title: Family Calendar
---
<style>
    /* This wrapper forces the calendar into Dark Mode */
    .calendar-wrapper {
        width: 100%;
        height: 100vh;
        background-color: #000; /* Keeps the background black while loading */
        overflow: hidden;
    }

    .calendar-wrapper iframe {
        width: 100%;
        height: 100%;
        border: none;
        
        /* THE DARK MODE MAGIC */
        /* Invert flips white to black, hue-rotate fixes the colors so they aren't 'neon' */
        filter: invert(60%) hue-rotate(180deg) brightness(1.1);
        
        /* Smooths out the edges on high-res displays */
        -webkit-font-smoothing: antialiased;
    }

    /* Optional: A dark overlay for the top bar specifically if needed */
    .calendar-container {
        position: relative;
    }
</style>

<div class="calendar-wrapper">
    <!-- PASTE YOUR CUSTOMIZED BUNDLE CODE FROM GOOGLE BELOW -->
    <!-- Ensure you change "showPrint=1" to "showPrint=0" for a cleaner look -->
    <iframe src="https://calendar.google.com/calendar/embed?height=600&wkst=1&ctz=America%2FLos_Angeles&showPrint=0&src=YnJhbmRvbmhvcm5AZ21haWwuY29t&src=aWF0bG1zZDUyc2plMm9vcWRsbTU5bjRpM29AZ3JvdXAuY2FsZW5kYXIuZ29vZ2xlLmNvbQ&src=a2cxbTB0ZWsxNWZrMnZmM2QzZTY0aWdkcnNAZ3JvdXAuY2FsZW5kYXIuZ29vZ2xlLmNvbQ&src=bjRuaXM4MnFoam4xMWEzOWtuOWdpYWJnMjhAZ3JvdXAuY2FsZW5kYXIuZ29vZ2xlLmNvbQ&src=ZW4udXNhI2hvbGlkYXlAZ3JvdXAudi5jYWxlbmRhci5nb29nbGUuY29t&color=%237986cb&color=%23e4c441&color=%237cb342&color=%23ad1457&color=%23d81b60" style="border:solid 1px #777" width="800" height="600" frameborder="0" scrolling="no"></iframe>
</div>
