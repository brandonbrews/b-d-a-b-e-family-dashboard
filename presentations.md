---
layout: default
title: Presentations
---

<style>
    .gallery-container {
        padding: 120px 50px 50px; /* Space for the nav bar */
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
        gap: 30px;
    }
    .presentation-card {
        background: rgba(255, 255, 255, 0.1);
        border: 1px solid rgba(255, 255, 255, 0.2);
        border-radius: 15px;
        overflow: hidden;
        transition: transform 0.3s ease, background 0.3s;
        text-decoration: none;
        color: white;
        cursor: pointer;
    }
    .presentation-card:hover {
        transform: translateY(-10px);
        background: rgba(255, 255, 255, 0.2);
    }
    .preview-thumb {
        width: 100%;
        height: 180px;
        background-size: cover;
        background-position: center;
        border-bottom: 1px solid rgba(255, 255, 255, 0.1);
    }
    .card-info { padding: 20px; }
    .card-title { font-weight: 700; font-size: 1.1rem; margin-bottom: 5px; }
    .card-author { font-size: 0.8rem; opacity: 0.7; text-transform: uppercase; letter-spacing: 1px; }
</style>

<div class="gallery-container">
    
    <a href="/b-d-a-b-e-family-dashboard/presentations/deck-builder" class="presentation-card">
        <div class="preview-thumb" style="background-image: url('/assets/img/deck-preview.jpg');"></div>
        <div class="card-info">
            <div class="card-title">Home Deck Build Plan</div>
            <div class="card-author">By Brandon</div>
        </div>
    </a>

    <a href="/b-d-a-b-e-family-dashboard/presentations/dragon-eggs" class="presentation-card">
        <div class="preview-thumb" style="background-image: url('/assets/img/dragon-preview.jpg');"></div>
        <div class="card-info">
            <div class="card-title">All About 3D Dragon Eggs</div>
            <div class="card-author">Kid Project</div>
        </div>
    </a>

</div>
