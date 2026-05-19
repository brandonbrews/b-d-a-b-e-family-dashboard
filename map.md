---
layout: default
title: World Map
---

<style>
  /* Pull the map flush under the nav bar and fill the rest of the screen */
  #map-wrap {
    position: fixed;
    top: 0px;
    left: 0; right: 0; bottom: 0;
    overflow: hidden;
  }

  #map-wrap iframe {
    width: 100%;
    height: 100%;
    border: none;
    display: block;

    /*
     * THE TOUCH FIX:
     * On Chromium/Pi, the browser intercepts pointer events for its own
     * pinch-to-zoom handling before passing them to the iframe, which breaks
     * the map's ability to handle pinch-zoom-out correctly.
     *
     * touch-action: none  tells the browser "hands off — let the iframe (i.e.
     * Google Maps) handle all touch gestures itself", fixing both directions
     * of pinch zoom and two-finger pan.
     */
    touch-action: none;
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
