---
layout: default
title: World Map
---

<style>
    #map-wrap {
        position: fixed;
        top: 0;
        left: 0; right: 0;
        bottom: 0;
        overflow: hidden;
        z-index: 0;
    }

    #map-wrap iframe {
        width: 100%;
        height: 100%;
        border: none;
        display: block;

        /*
         * TOUCH FIX for Chromium on Raspberry Pi:
         * 'manipulation' allows pan + pinch-zoom but prevents
         * the browser from claiming the gesture before the iframe
         * sees it, which was causing zoom-in-only behaviour.
         * (touch-action:none blocks the browser entirely but can
         * prevent the iframe from receiving pinch events at all.)
         */
        touch-action: manipulation;
    }
</style>

<div id="map-wrap">
    <iframe
        src="https://www.google.com/maps/embed?pb=!1m14!1m12!1m3!1d21710.09442753998!2d-122.54391445!3d47.1429325!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sus!4v1778096895550!5m2!1sen!2sus"
        allowfullscreen=""
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade"
        title="Horn Family Map">
    </iframe>
</div>
