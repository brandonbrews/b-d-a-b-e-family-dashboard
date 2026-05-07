---
layout: default
title: Presentations
---

<style>
    .gallery-container {
        padding: 120px 50px 50px;
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
        gap: 30px;
    }
    .presentation-card {
        background: rgba(255, 255, 255, 0.1);
        border: 1px solid rgba(255, 255, 255, 0.2);
        border-radius: 15px;
        overflow: hidden;
        transition: 0.3s ease;
        text-decoration: none;
        color: white;
    }
    .presentation-card:hover { transform: translateY(-10px); background: rgba(255, 255, 255, 0.2); }
    .preview-thumb {
        width: 100%; height: 160px;
        background-color: #333; background-size: cover; background-position: center;
    }
    .card-info { padding: 15px; }
    .card-title { font-weight: 700; margin-bottom: 5px; }
    .card-author { font-size: 0.75rem; opacity: 0.6; text-transform: uppercase; }
</style>

<div class="gallery-container">
    {% for p in site.pages %}
        {% if p.path contains 'presentations/' and p.path != 'presentations.md' %}
            <a href="{{ site.baseurl }}{{ p.url }}" class="presentation-card">
                <div class="preview-thumb" style="background-image: url('{{ p.thumbnail | default: "/assets/img/default-slide.jpg" }}');"></div>
                <div class="card-info">
                    <div class="card-title">{{ p.title }}</div>
                    <div class="card-author">By {{ p.author | default: "Family Member" }}</div>
                </div>
            </a>
        {% endif %}
    {% endfor %}
</div>
