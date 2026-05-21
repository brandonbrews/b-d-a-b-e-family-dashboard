---
layout: default
title: Upload Photos
---

<style>
    #upload-screen {
        position: fixed; inset: 0;
        display: flex; align-items: center; justify-content: center;
        z-index: 50; padding: 20px;
        background: rgba(0,0,0,0.85);
        backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
    }

    .upload-card {
        background: #1a1f2e;
        border: 1px solid rgba(255,255,255,0.12);
        border-radius: 24px; padding: 36px 32px;
        width: 100%; max-width: 440px;
        text-align: center;
        box-shadow: 0 20px 60px rgba(0,0,0,0.5);
    }
    .upload-card h1 {
        font-size: 1.6rem; font-weight: 900; color: #fff;
        letter-spacing: 1px; margin-bottom: 6px;
    }
    .upload-card .subtitle {
        font-size: 0.9rem; color: rgba(255,255,255,0.45); margin-bottom: 28px;
    }

    .upload-input {
        width: 100%; background: rgba(255,255,255,0.07);
        border: 1px solid rgba(255,255,255,0.15); border-radius: 14px;
        padding: 16px 18px; font-size: 1.1rem; color: #fff;
        outline: none; transition: border-color 0.2s; margin-bottom: 14px;
        -webkit-appearance: none;
    }
    .upload-input:focus { border-color: rgba(102,252,241,0.6); }
    .upload-input::placeholder { color: rgba(255,255,255,0.25); }

    /* ── Album selector ── */
    .album-selector { margin-bottom: 20px; text-align: left; }
    .album-selector-label {
        font-size: 0.75rem; text-transform: uppercase; letter-spacing: 2px;
        color: rgba(255,255,255,0.4); margin-bottom: 10px; display: block;
    }
    .album-options { display: flex; flex-direction: column; gap: 10px; }
    .album-option {
        display: flex; align-items: center; gap: 14px;
        background: rgba(255,255,255,0.05);
        border: 2px solid rgba(255,255,255,0.1);
        border-radius: 14px; padding: 14px 18px;
        cursor: pointer; transition: border-color 0.2s, background 0.2s;
        -webkit-tap-highlight-color: transparent; touch-action: manipulation;
    }
    .album-option:hover { background: rgba(255,255,255,0.08); border-color: rgba(102,252,241,0.3); }
    .album-option.selected { border-color: #66FCF1; background: rgba(102,252,241,0.08); }
    .album-option input[type="radio"] { display: none; }
    .album-icon { font-size: 1.8rem; flex-shrink: 0; }
    .album-text { text-align: left; }
    .album-name { font-size: 1rem; font-weight: 700; color: #fff; }
    .album-desc { font-size: 0.78rem; color: rgba(255,255,255,0.4); margin-top: 2px; }
    /* Checkmark badge when selected */
    .album-check {
        margin-left: auto; width: 22px; height: 22px; border-radius: 50%;
        border: 2px solid rgba(255,255,255,0.2);
        display: flex; align-items: center; justify-content: center;
        flex-shrink: 0; font-size: 0.75rem; transition: background 0.2s, border-color 0.2s;
    }
    .album-option.selected .album-check { background: #66FCF1; border-color: #66FCF1; color: #000; }

    .btn-primary {
        width: 100%; background: linear-gradient(135deg, #45A29E, #66FCF1);
        color: #0B0C10; border: none; border-radius: 14px; padding: 16px;
        font-size: 1.1rem; font-weight: 900; letter-spacing: 0.5px;
        cursor: pointer; transition: opacity 0.2s, transform 0.15s;
        -webkit-tap-highlight-color: transparent; touch-action: manipulation;
    }
    .btn-primary:hover  { opacity: 0.9; }
    .btn-primary:active { transform: scale(0.98); }
    .btn-primary:disabled { opacity: 0.4; cursor: not-allowed; }

    #drop-zone {
        border: 2px dashed rgba(102,252,241,0.35); border-radius: 16px;
        padding: 28px 20px; margin-bottom: 18px; cursor: pointer;
        transition: border-color 0.2s, background 0.2s;
        -webkit-tap-highlight-color: transparent;
    }
    #drop-zone:hover, #drop-zone.drag-over {
        border-color: rgba(102,252,241,0.8); background: rgba(102,252,241,0.04);
    }
    #drop-zone .dz-icon  { font-size: 2.4rem; margin-bottom: 8px; }
    #drop-zone .dz-label { color: rgba(255,255,255,0.6); font-size: 0.9rem; line-height: 1.5; }
    #drop-zone .dz-label strong { color: #66FCF1; }

    #file-list {
        text-align: left; margin-bottom: 16px; display: none;
        max-height: 160px; overflow-y: auto;
        scrollbar-width: thin; scrollbar-color: #45A29E rgba(255,255,255,0.06);
    }
    #file-list::-webkit-scrollbar { width: 6px; }
    #file-list::-webkit-scrollbar-thumb { background: #45A29E; border-radius: 3px; }
    .file-row {
        display: flex; align-items: center; gap: 10px;
        padding: 7px 4px; border-bottom: 1px solid rgba(255,255,255,0.06);
        font-size: 0.82rem; color: rgba(255,255,255,0.7);
    }
    .file-row:last-child { border-bottom: none; }
    .file-row .fname { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
    .file-row .fsize { opacity: 0.4; flex-shrink: 0; }
    .file-row .fstatus { flex-shrink: 0; }

    #progress-wrap {
        display: none; margin-bottom: 14px;
        background: rgba(255,255,255,0.07); border-radius: 8px; height: 8px; overflow: hidden;
    }
    #progress-bar {
        height: 100%; background: linear-gradient(90deg, #45A29E, #66FCF1);
        border-radius: 8px; width: 0%; transition: width 0.3s;
    }

    #status-msg {
        font-size: 0.9rem; min-height: 1.4em; margin-bottom: 14px; color: rgba(255,255,255,0.55);
    }
    #status-msg.success { color: #66FCF1; }
    #status-msg.error   { color: #ff6b6b; }

    #file-input { display: none; }
    #auth-screen { display: flex; }
    #main-screen { display: none; }
</style>

<div id="upload-screen">

    <!-- AUTH -->
    <div class="upload-card" id="auth-screen">
        <div style="font-size:3rem;margin-bottom:12px">📸</div>
        <h1>{{ site.family_name }}</h1>
        <div class="subtitle">Photo Upload</div>
        <input class="upload-input" type="password" id="pw-input"
               placeholder="Enter password"
               onkeydown="if(event.key==='Enter') checkPassword()">
        <button class="btn-primary" onclick="checkPassword()">Unlock</button>
        <div id="auth-error" style="color:#ff6b6b;font-size:0.85rem;margin-top:12px;min-height:1.2em"></div>
    </div>

    <!-- UPLOAD -->
    <div class="upload-card" id="main-screen">
        <div style="font-size:2.2rem;margin-bottom:6px">📸</div>
        <h1>Add Photos</h1>
        <div class="subtitle">Photos go straight to the dashboard</div>

        <!-- Album selector — only shown when multiple presets are configured -->
        <div class="album-selector" id="album-selector" style="display:none">
            <span class="album-selector-label">Choose album</span>
            <div class="album-options" id="album-options"></div>
        </div>

        <div id="drop-zone"
             onclick="document.getElementById('file-input').click()"
             ondragover="handleDragOver(event)"
             ondragleave="handleDragLeave(event)"
             ondrop="handleDrop(event)">
            <div class="dz-icon">🖼️</div>
            <div class="dz-label">
                <strong>Tap to choose photos</strong><br>
                or drag &amp; drop here<br>
                <span style="font-size:0.78rem;opacity:0.6">JPG, PNG, HEIC · multiple OK</span>
            </div>
        </div>

        <input type="file" id="file-input" accept="image/*" multiple onchange="handleFiles(this.files)">

        <div id="file-list"></div>
        <div id="progress-wrap"><div id="progress-bar"></div></div>
        <div id="status-msg"></div>

        <button class="btn-primary" id="upload-btn" onclick="startUpload()" disabled>Upload Photos</button>

        <div style="margin-top:16px">
            <button onclick="resetUpload()"
                    style="background:none;border:none;color:rgba(255,255,255,0.3);
                           font-size:0.85rem;cursor:pointer;text-decoration:underline">
                Clear &amp; start over
            </button>
        </div>
    </div>
</div>

<script>
    /* ── Jekyll config injection ───────────────────────────────── */
    var CORRECT_PASSWORD = '{{ site.upload_password }}';
    var CLOUDINARY_CLOUD = '{{ site.cloudinary_cloud }}';

    /*
     * ALBUMS: each entry has a display name, description, emoji, and
     * the Cloudinary upload preset that targets that folder.
     * Single-entry = selector hidden, behaves exactly like before.
     */
    var ALBUMS = [
        {% if site.cloudinary_upload_preset and site.cloudinary_upload_preset != '' %}
        {
            name:    "{{ site.cloudinary_folder | replace: '-', ' ' | capitalize }}",
            desc:    "Main family photos",
            icon:    "🏠",
            preset:  "{{ site.cloudinary_upload_preset }}"
        }{% if site.cloudinary_upload_preset_bonus and site.cloudinary_upload_preset_bonus != '' %},{% endif %}
        {% endif %}
        {% if site.cloudinary_upload_preset_bonus and site.cloudinary_upload_preset_bonus != '' %}
        {
            name:    "{{ site.cloudinary_folder_bonus | replace: '-', ' ' | capitalize }}",
            desc:    "Bonus album",
            icon:    "⭐",
            preset:  "{{ site.cloudinary_upload_preset_bonus }}"
        }
        {% endif %}
    ];
    /* ─────────────────────────────────────────────────────────── */

    var selectedFiles  = [];
    var selectedPreset = ALBUMS.length > 0 ? ALBUMS[0].preset : '';

    /* ── Auth ── */
    function checkPassword() {
        if (document.getElementById('pw-input').value === CORRECT_PASSWORD) {
            document.getElementById('auth-screen').style.display = 'none';
            document.getElementById('main-screen').style.display = 'block';
            sessionStorage.setItem('upload-auth', '1');
            buildAlbumSelector();
        } else {
            document.getElementById('auth-error').textContent = 'Incorrect password — try again.';
            document.getElementById('pw-input').value = '';
            document.getElementById('pw-input').focus();
        }
    }
    if (sessionStorage.getItem('upload-auth') === '1') {
        document.getElementById('auth-screen').style.display = 'none';
        document.getElementById('main-screen').style.display = 'block';
        buildAlbumSelector();
    }

    /* ── Album selector ── */
    function buildAlbumSelector() {
        if (ALBUMS.length <= 1) return; // hide if only one album
        var wrap = document.getElementById('album-selector');
        var opts = document.getElementById('album-options');
        wrap.style.display = 'block';
        opts.innerHTML = '';
        ALBUMS.forEach(function(album, i) {
            var div = document.createElement('div');
            div.className = 'album-option' + (i === 0 ? ' selected' : '');
            div.dataset.preset = album.preset;
            div.innerHTML =
                '<div class="album-icon">' + album.icon + '</div>'
              + '<div class="album-text">'
              +   '<div class="album-name">' + album.name + '</div>'
              +   '<div class="album-desc">' + album.desc + '</div>'
              + '</div>'
              + '<div class="album-check">' + (i === 0 ? '✓' : '') + '</div>';
            div.addEventListener('click', function() { selectAlbum(div, album.preset); });
            opts.appendChild(div);
        });
    }

    function selectAlbum(el, preset) {
        document.querySelectorAll('.album-option').forEach(function(o) {
            o.classList.remove('selected');
            o.querySelector('.album-check').textContent = '';
        });
        el.classList.add('selected');
        el.querySelector('.album-check').textContent = '✓';
        selectedPreset = preset;
    }

    /* ── File handling ── */
    function handleFiles(files) {
        selectedFiles = Array.from(files);
        renderFileList();
    }
    function handleDragOver(e) { e.preventDefault(); document.getElementById('drop-zone').classList.add('drag-over'); }
    function handleDragLeave() { document.getElementById('drop-zone').classList.remove('drag-over'); }
    function handleDrop(e) {
        e.preventDefault();
        document.getElementById('drop-zone').classList.remove('drag-over');
        handleFiles(e.dataTransfer.files);
    }

    function renderFileList() {
        var list = document.getElementById('file-list');
        if (selectedFiles.length === 0) {
            list.style.display = 'none';
            document.getElementById('upload-btn').disabled = true;
            return;
        }
        list.style.display = 'block';
        list.innerHTML = selectedFiles.map(function(f, i) {
            return '<div class="file-row" id="frow-' + i + '">'
                 + '<span class="fstatus" id="fstatus-' + i + '">📷</span>'
                 + '<span class="fname">' + f.name + '</span>'
                 + '<span class="fsize">' + (f.size/1024/1024).toFixed(1) + ' MB</span>'
                 + '</div>';
        }).join('');
        document.getElementById('upload-btn').disabled = false;
        setStatus('');
    }

    /* ── Upload ── */
    async function startUpload() {
        if (!selectedFiles.length || !selectedPreset) return;
        var btn = document.getElementById('upload-btn');
        btn.disabled = true; btn.textContent = 'Uploading…';
        document.getElementById('progress-wrap').style.display = 'block';

        var uploaded = 0, failed = 0;
        for (var i = 0; i < selectedFiles.length; i++) {
            setStatus('Uploading ' + (i+1) + ' of ' + selectedFiles.length + '…');
            setFileStatus(i, '⏳');
            var ok = await uploadOne(selectedFiles[i]);
            if (ok) { uploaded++; setFileStatus(i, '✅'); }
            else    { failed++;   setFileStatus(i, '❌'); }
            document.getElementById('progress-bar').style.width =
                Math.round(((i+1) / selectedFiles.length) * 100) + '%';
        }

        btn.textContent = 'Upload Photos';
        if (failed === 0) {
            setStatus('🎉 ' + uploaded + ' photo' + (uploaded > 1 ? 's' : '') + ' uploaded!', 'success');
        } else {
            setStatus(uploaded + ' uploaded, ' + failed + ' failed.', 'error');
            btn.disabled = false;
        }
    }

    async function uploadOne(file) {
        var fd = new FormData();
        fd.append('file', file);
        fd.append('upload_preset', selectedPreset);
        try {
            var resp = await fetch(
                'https://api.cloudinary.com/v1_1/' + CLOUDINARY_CLOUD + '/image/upload',
                { method: 'POST', body: fd }
            );
            if (!resp.ok) throw new Error('HTTP ' + resp.status);
            var data = await resp.json();
            return !!data.public_id;
        } catch(e) {
            console.error('Upload failed:', file.name, e);
            return false;
        }
    }

    /* ── Helpers ── */
    function setStatus(msg, type) {
        var el = document.getElementById('status-msg');
        el.textContent = msg; el.className = type || '';
    }
    function setFileStatus(i, icon) {
        var el = document.getElementById('fstatus-' + i);
        if (el) el.textContent = icon;
    }
    function resetUpload() {
        selectedFiles = [];
        document.getElementById('file-input').value = '';
        document.getElementById('file-list').style.display = 'none';
        document.getElementById('progress-wrap').style.display = 'none';
        document.getElementById('progress-bar').style.width = '0%';
        document.getElementById('upload-btn').disabled = true;
        document.getElementById('upload-btn').textContent = 'Upload Photos';
        setStatus('');
    }
</script>