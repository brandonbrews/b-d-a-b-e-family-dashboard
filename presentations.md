---
layout: default
title: Presentations
---

<style>
    /* Ensure the page background is dark to match the nav */
    body {
        background-color: #121212;
        margin: 0;
        color: white;
        font-family: 'Inter', sans-serif;
    }

    .gallery-container {
        padding: 140px 50px 50px; /* Extra top padding to clear the nav bar */
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
        gap: 40px;
        max-width: 1400px;
        margin: 0 auto;
    }

    .presentation-card {
        background: #1e1e1e;
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 20px;
        overflow: hidden;
        transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        text-decoration: none;
        display: block;
        box-shadow: 0 10px 30px rgba(0,0,0,0.5);
    }

    .presentation-card:hover {
        transform: translateY(-12px);
        border-color: rgba(255, 255, 255, 0.4);
        box-shadow: 0 20px 40px rgba(0,0,0,0.7);
    }

    .preview-thumb {
        width: 100%;
        height: 200px;
        background-color: #2a2a2a;
        background-size: cover;
        background-position: center;
        position: relative;
    }

    /* Adds a "Play" icon overlay on hover */
    .presentation-card:hover .preview-thumb::after {
        content: '▶';
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-size: 3rem;
        color: white;
        text-shadow: 0 0 20px rgba(0,0,0,0.5);
    }

    .card-info {
        padding: 25px;
        background: linear-gradient(to bottom, #222, #1a1a1a);
    }

    .card-title {
        color: #ffffff;
        font-weight: 800;
        font-size: 1.2rem;
        margin-bottom: 8px;
        display: block;
    }

    .card-author {
        color: #aaa;
        font-size: 0.8rem;
        text-transform: uppercase;
        letter-spacing: 2px;
        font-weight: 600;
    }
</style>

<div class="gallery-container">
    {% for p in site.pages %}
        {% if p.path contains 'presentations/' and p.path != 'presentations.md' %}
            <a href="{{ site.baseurl }}{{ p.url }}" class="presentation-card">
                <div class="preview-thumb" style="background-image: url('{{ p.thumbnail | default: "/assets/img/default-slide.jpg" }}');"></div>
                <div class="card-info">
                    <span class="card-title">{{ p.title }}</span>
                    <span class="card-author">By {{ p.author | default: "Family Member" }}</span>
                </div>
            </a>
        {% endif %}
    {% endfor %}
</div>
