---
layout: default
title: Chores
---

<style>
  #chores-root {
    position: fixed;
    top: 0; left: 0; right: 0; bottom: 88px;
    overflow: hidden; display: flex; flex-direction: column;
    background: #0B0C10;
    background-image: radial-gradient(ellipse at 50% 0%, #1a1a2e 0%, #0B0C10 70%);
    color: #C5C6C7; font-family: 'Segoe UI', system-ui, sans-serif;
  }

  #chores-header { text-align: center; padding: 16px 24px 8px; flex-shrink: 0; }
  #chores-header h1 {
    color: #66FCF1; font-size: 1.8rem; text-transform: uppercase;
    letter-spacing: 3px; text-shadow: 0 0 20px rgba(102,252,241,0.4);
  }

  #page-tabs {
    display: flex; justify-content: center; gap: 8px;
    padding: 0 24px 12px; flex-shrink: 0;
  }
  .page-tab {
    background: rgba(255,255,255,0.06); border: 1px solid rgba(255,255,255,0.1);
    border-radius: 30px; padding: 8px 28px; color: rgba(255,255,255,0.5);
    font-size: 1rem; font-weight: 700; text-transform: uppercase; letter-spacing: 1.5px;
    cursor: pointer; transition: all 0.2s; -webkit-tap-highlight-color: transparent;
  }
  .page-tab.active { background: rgba(102,252,241,0.12); border-color: #66FCF1; color: #66FCF1; }

  #kids-row {
    display: grid; grid-template-columns: repeat(3, 1fr);
    gap: 16px; flex: 1; min-height: 0; padding: 0 16px 12px;
  }

  .kid-card {
    background: #1F2833; border: 1px solid #45A29E; border-radius: 16px;
    display: flex; flex-direction: column; overflow: hidden;
    box-shadow: 0 0 20px rgba(69,162,158,0.15);
  }
  .card-header {
    padding: 14px 18px 10px; border-bottom: 1px solid rgba(69,162,158,0.3); flex-shrink: 0;
    display: flex; align-items: center; justify-content: space-between;
  }
  .card-header-left {}
  .kid-name  { font-size: 1.4rem; color: #fff; font-weight: 700; }
  .bucks-total { font-size: 2.4rem; color: #66FCF1; font-weight: 900; line-height: 1; margin-top: 4px; }
  .bucks-label { font-size: 0.72rem; text-transform: uppercase; letter-spacing: 1px; opacity: 0.5; margin-top: 2px; }

  /* Undo button in card header */
  .btn-undo {
    background: rgba(255,100,100,0.12); border: 1px solid rgba(255,100,100,0.3);
    color: rgba(255,150,150,0.8); border-radius: 20px; padding: 6px 14px;
    font-size: 0.78rem; font-weight: 700; cursor: pointer; letter-spacing: 0.5px;
    transition: all 0.2s; -webkit-tap-highlight-color: transparent;
    display: none; /* shown only when there's something to undo */
  }
  .btn-undo:hover { background: rgba(255,100,100,0.25); color: #fff; }
  .btn-undo.visible { display: block; }

  .card-body {
    flex: 1; overflow-y: auto; padding: 12px 14px 14px;
    scrollbar-width: auto; scrollbar-color: #45A29E rgba(255,255,255,0.06);
  }
  .card-body::-webkit-scrollbar       { width: 12px; }
  .card-body::-webkit-scrollbar-track { background: rgba(255,255,255,0.06); border-radius: 6px; margin: 4px 0; }
  .card-body::-webkit-scrollbar-thumb { background: #45A29E; border-radius: 6px; border: 2px solid #1F2833; }
  .card-body::-webkit-scrollbar-thumb:hover { background: #66FCF1; }

  .chore-item {
    background: #16202a; border-left: 3px solid #66FCF1; border-radius: 8px;
    padding: 10px 12px; margin-bottom: 8px;
    display: flex; align-items: center; gap: 10px; cursor: pointer;
    transition: background 0.2s, transform 0.15s, opacity 0.3s;
    -webkit-tap-highlight-color: transparent;
  }
  .chore-item:hover  { background: #1e303d; transform: translateX(3px); }
  .chore-item:active { transform: scale(0.98); }
  .chore-item.done   { opacity: 0.35; pointer-events: none; }

  .chore-check {
    width: 36px; height: 36px; border-radius: 50%; border: 2px solid #45A29E;
    flex-shrink: 0; display: flex; align-items: center; justify-content: center;
    font-size: 1rem; transition: background 0.2s;
  }
  .chore-item.done .chore-check { background: #4CAF50; border-color: #4CAF50; }
  .chore-info { flex: 1; min-width: 0; }
  .chore-name { font-size: 0.95rem; color: #fff; font-weight: 600; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .chore-freq { font-size: 0.75rem; opacity: 0.45; margin-top: 2px; }
  .chore-value { color: #FFD700; font-weight: 900; font-size: 0.95rem; flex-shrink: 0; }

  .reward-item {
    background: #16202a; border-radius: 8px; padding: 10px 12px; margin-bottom: 8px;
    display: flex; align-items: center; gap: 10px;
  }
  .reward-item.can-afford { border-left: 3px solid #4CAF50; cursor: pointer; -webkit-tap-highlight-color: transparent; }
  .reward-item.can-afford:hover { background: #1a2e1a; }
  .reward-item.locked { border-left: 3px solid rgba(244,67,54,0.4); opacity: 0.5; }
  .reward-icon { font-size: 1.4rem; flex-shrink: 0; }
  .reward-info { flex: 1; min-width: 0; }
  .reward-name { font-size: 0.9rem; color: #fff; font-weight: 600; }
  .reward-desc { font-size: 0.72rem; opacity: 0.45; margin-top: 2px; }
  .reward-cost { font-weight: 900; font-size: 0.9rem; flex-shrink: 0; }
  .reward-item.can-afford .reward-cost { color: #FFD700; }
  .reward-item.locked     .reward-cost { color: #f44336; opacity: 0.7; }

  .empty-note { font-size: 0.82rem; opacity: 0.35; font-style: italic; padding: 6px 2px; }

  #chores-loading {
    position: absolute; inset: 0; display: flex; align-items: center;
    justify-content: center; flex-direction: column; gap: 16px;
    background: #0B0C10; z-index: 50;
  }
  .spinner {
    width: 40px; height: 40px; border: 3px solid rgba(102,252,241,0.2);
    border-top-color: #66FCF1; border-radius: 50%; animation: spin 0.8s linear infinite;
  }
  @keyframes spin { to { transform: rotate(360deg); } }
  #chores-loading p { color: #66FCF1; font-size: 1rem; letter-spacing: 1px; text-align: center; padding: 0 24px; }

  /* ── Confirm modal ── */
  #modal-overlay {
    display: none; position: fixed; inset: 0;
    background: rgba(0,0,0,0.75); backdrop-filter: blur(6px);
    z-index: 200; align-items: center; justify-content: center;
  }
  #modal-overlay.open { display: flex; }
  .modal {
    background: #1F2833; border: 1px solid #45A29E; border-radius: 20px;
    padding: 28px 32px; max-width: 400px; width: 90%; text-align: center;
    box-shadow: 0 0 40px rgba(69,162,158,0.3);
  }
  .modal-icon { font-size: 2.6rem; margin-bottom: 10px; }
  .modal h2   { color: #fff; font-size: 1.3rem; margin-bottom: 8px; }
  .modal p    { font-size: 0.9rem; opacity: 0.7; margin-bottom: 22px; line-height: 1.6; }
  .modal-actions { display: flex; gap: 12px; justify-content: center; }
  .btn-confirm {
    background: #4CAF50; color: #000; border: none; border-radius: 30px;
    padding: 11px 26px; font-size: 0.95rem; font-weight: 700; cursor: pointer;
    -webkit-tap-highlight-color: transparent;
  }
  .btn-confirm:hover { background: #6fcf6f; }
  .btn-cancel {
    background: rgba(255,255,255,0.08); color: #C5C6C7;
    border: 1px solid rgba(255,255,255,0.15); border-radius: 30px;
    padding: 11px 26px; font-size: 0.95rem; cursor: pointer;
    -webkit-tap-highlight-color: transparent;
  }
  .btn-cancel:hover { background: rgba(255,255,255,0.15); }

  /* ── Undo confirm modal variant ── */
  .btn-undo-confirm {
    background: #e53935; color: #fff; border: none; border-radius: 30px;
    padding: 11px 26px; font-size: 0.95rem; font-weight: 700; cursor: pointer;
    -webkit-tap-highlight-color: transparent;
  }
  .btn-undo-confirm:hover { background: #ff5252; }

  #toast {
    position: fixed; bottom: 100px; left: 50%;
    transform: translateX(-50%) translateY(20px);
    background: #1F2833; border: 1px solid #45A29E; border-radius: 30px;
    padding: 9px 22px; font-size: 0.88rem; color: #66FCF1;
    opacity: 0; transition: opacity 0.3s, transform 0.3s;
    pointer-events: none; z-index: 300; white-space: nowrap;
  }
  #toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }

  #sync-status {
    position: fixed; top: 16px; right: 20px; font-size: 0.75rem;
    color: rgba(255,255,255,0.3); z-index: 100; transition: color 0.3s;
  }
  #sync-status.syncing { color: #66FCF1; }
  #sync-status.error   { color: #ff6b6b; }
</style>

<script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

<div id="chores-root">
  <div id="chores-loading">
    <div class="spinner"></div>
    <p>🚀 Connecting to the Mothership…</p>
  </div>

  <div id="chores-header" style="display:none">
    <h1>🚀 Space Bucks Command Center 🚀</h1>
  </div>

  <div id="page-tabs" style="display:none">
    <button class="page-tab active" id="tab-missions" onclick="switchPageTab('missions')">🚀 Missions</button>
    <button class="page-tab"        id="tab-rewards"  onclick="switchPageTab('rewards')">🎁 Rewards</button>
  </div>

  <div id="kids-row"></div>
</div>

<!-- Shared modal for chore confirm, reward redeem, and undo -->
<div id="modal-overlay">
  <div class="modal">
    <div class="modal-icon" id="modal-icon">⭐</div>
    <h2 id="modal-title"></h2>
    <p  id="modal-body"></p>
    <div class="modal-actions" id="modal-actions"></div>
  </div>
</div>

<div id="toast"></div>
<div id="sync-status"></div>

<script>
  /* ── CONFIG ── */
  var SHEET_ID    = '1Sj_yeVYQO_UVwXaXLLdZU0SxiW9Qd2EHCQH_vwhhQHU';
  var CSV_KIDS    = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRCxQfzCvX23raHww47T93p_2KFRS7tbg-jkw6OJd11ZAruY1zSOhp40oAVJfCwTDjlImnDv1fxHGXD/pub?gid=0&single=true&output=csv';
  var CSV_CHORES  = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRCxQfzCvX23raHww47T93p_2KFRS7tbg-jkw6OJd11ZAruY1zSOhp40oAVJfCwTDjlImnDv1fxHGXD/pub?gid=366572312&single=true&output=csv';
  var CSV_REWARDS = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRCxQfzCvX23raHww47T93p_2KFRS7tbg-jkw6OJd11ZAruY1zSOhp40oAVJfCwTDjlImnDv1fxHGXD/pub?gid=1460852569&single=true&output=csv';

  /*
   * PROXY_BASE: your Cloudflare Worker URL — no trailing slash.
   * The API key lives in the Worker as an encrypted secret; it never
   * appears in this file or your public repo.
   * Set this after deploying sheets-worker.js to Cloudflare Workers.
   * Example: 'https://sheets-proxy.YOUR-SUBDOMAIN.workers.dev'
   */
  var PROXY_BASE  = 'https://sheets-proxy.brandonhorn.workers.dev';

  /* ── State ── */
  var appData       = { kids: [], chores: [], rewards: [] };
  var pageTab       = 'missions';
  var pendingAction = null;

  /*
   * Per-kid undo stack — stores the last completed action so it can be reversed.
   * Structure: { type: 'chore'|'reward', kidName, label, bucks, choreRow, timestamp }
   * Only one undo level per kid — the Undo button replaces itself each completion.
   */
  var undoStacks = {};  // kidName → action object

  /* ── CSV parser ── */
  function parseCSV(text) {
    var lines = text.trim().split('\n');
    if (lines.length < 2) return [];
    var headers = splitCSVLine(lines[0]);
    return lines.slice(1).map(function(line) {
      var vals = splitCSVLine(line);
      var obj  = {};
      headers.forEach(function(h, i) { obj[h.trim()] = (vals[i]||'').trim(); });
      return obj;
    }).filter(function(row) {
      return Object.values(row).some(function(v){ return v !== ''; });
    });
  }
  function splitCSVLine(line) {
    var result=[], current='', inQ=false;
    for (var i=0; i<line.length; i++) {
      var ch=line[i];
      if (ch==='"') { inQ=!inQ; }
      else if (ch===',' && !inQ) { result.push(current); current=''; }
      else { current+=ch; }
    }
    result.push(current); return result;
  }

  /* ── Load ── */
  async function loadData() {
    try {
      var [kT, cT, rT] = await Promise.all([
        fetch(CSV_KIDS   +'&t='+Date.now()).then(function(r){return r.text();}),
        fetch(CSV_CHORES +'&t='+Date.now()).then(function(r){return r.text();}),
        fetch(CSV_REWARDS+'&t='+Date.now()).then(function(r){return r.text();})
      ]);
      var allChores = parseCSV(cT);
      appData = {
        kids:    parseCSV(kT),
        rewards: parseCSV(rT),
        chores:  allChores.filter(function(c){
          return String(c['Status']||'').trim().toUpperCase() !== 'TRUE';
        })
      };
      renderApp();
      document.getElementById('chores-loading').style.display = 'none';
      document.getElementById('chores-header').style.display  = 'block';
      document.getElementById('page-tabs').style.display      = 'flex';
    } catch(e) {
      document.getElementById('chores-loading').querySelector('p').innerHTML =
        '⚠️ Failed to load:<br><small style="opacity:0.7">'+e.message+'</small><br><br>'
        +'<button onclick="loadData()" style="margin-top:8px;padding:8px 20px;'
        +'background:#45A29E;border:none;border-radius:20px;color:#000;font-weight:700;cursor:pointer;">Retry</button>';
    }
  }

  /* ── Render ── */
  function renderApp() {
    var row = document.getElementById('kids-row');
    row.innerHTML = '';
    if (!appData.kids||!appData.kids.length) {
      row.innerHTML='<p style="color:#66FCF1;padding:20px">No astronauts found.</p>'; return;
    }
    appData.kids.forEach(function(kid) {
      var name  = String(kid['Name']||'').trim();
      var bucks = parseInt(kid['Total Bucks'])||0;
      var myChores = appData.chores.filter(function(c){
        return String(c['Assigned To']||'').trim().toLowerCase()===name.toLowerCase();
      });

      var card = document.createElement('div');
      card.className='kid-card'; card.id='card-'+name;

      /* Header with undo button */
      var hdr = document.createElement('div');
      hdr.className = 'card-header';
      hdr.innerHTML =
        '<div class="card-header-left">'
        +'  <div class="kid-name">🧑‍🚀 '+name+'</div>'
        +'  <div class="bucks-total" id="bucks-'+name+'">'+bucks+'</div>'
        +'  <div class="bucks-label">Space Bucks</div>'
        +'</div>'
        +'<button class="btn-undo'+(undoStacks[name]?' visible':'')+'" '
        +'id="undo-btn-'+name+'" onclick="promptUndo(\''+name+'\')">↩ Undo</button>';
      card.appendChild(hdr);

      var body = document.createElement('div');
      body.className='card-body'; body.id='body-'+name;
      if (pageTab==='missions') renderMissions(body,name,myChores);
      else                      renderRewards(body,name,bucks);
      card.appendChild(body);
      row.appendChild(card);
    });
  }

  function renderMissions(container, kidName, myChores) {
    container.innerHTML='';
    if (!myChores.length) {
      var n=document.createElement('div'); n.className='empty-note';
      n.textContent='All missions complete! 🎉'; container.appendChild(n); return;
    }
    myChores.forEach(function(chore) {
      var choreName=String(chore['Chore Name']||'').trim();
      var bv=parseInt(chore['Space Bucks'])||0;
      var freq=String(chore['Frequency']||'').trim();
      var item=document.createElement('div'); item.className='chore-item';
      item.innerHTML='<div class="chore-check"></div>'
        +'<div class="chore-info"><div class="chore-name">'+choreName+'</div>'
        +'<div class="chore-freq">'+(freq||'Task')+'</div></div>'
        +'<div class="chore-value">+'+bv+' ⭐</div>';
      item.addEventListener('click', function(){
        if (item.classList.contains('done')) return;
        promptChore(item, choreName, kidName, bv, freq);
      });
      container.appendChild(item);
    });
  }

  function renderRewards(container, kidName, bucks) {
    container.innerHTML='';
    if (!appData.rewards||!appData.rewards.length) {
      var n=document.createElement('div'); n.className='empty-note';
      n.textContent='No rewards configured yet.'; container.appendChild(n); return;
    }
    appData.rewards.forEach(function(reward) {
      var rName=String(reward['Reward Name']||'').trim();
      var cost=parseInt(reward['Cost'])||0;
      var desc=String(reward['Description']||'').trim();
      var icon=String(reward['Icon']||'🎁').trim();
      var canAfford=bucks>=cost;
      var el=document.createElement('div');
      el.className='reward-item '+(canAfford?'can-afford':'locked');
      el.innerHTML='<div class="reward-icon">'+icon+'</div>'
        +'<div class="reward-info"><div class="reward-name">'+rName+'</div>'
        +(desc?'<div class="reward-desc">'+desc+'</div>':'')+'</div>'
        +'<div class="reward-cost">'+cost+' ⭐</div>';
      if (canAfford) el.addEventListener('click', function(){ promptRedeem(kidName,rName,cost,icon); });
      container.appendChild(el);
    });
  }

  function switchPageTab(tab) {
    pageTab=tab;
    document.getElementById('tab-missions').classList.toggle('active',tab==='missions');
    document.getElementById('tab-rewards').classList.toggle('active',tab==='rewards');
    renderApp();
  }

  /* ── Confirm chore ── */
  function promptChore(itemEl, choreName, kidName, spaceBucks, freq) {
    showModal('⭐', 'Complete Mission?',
      kidName + ' completed "' + choreName + '" for +' + spaceBucks + ' Space Bucks?',
      [
        { label: 'Yes! +'+spaceBucks+' ⭐', cls: 'btn-confirm', action: function(){
            closeModal();
            completeChore(itemEl, choreName, kidName, spaceBucks, freq);
        }},
        { label: 'Cancel', cls: 'btn-cancel', action: closeModal }
      ]
    );
  }

  /* ── Complete a chore ── */
  function completeChore(itemEl, choreName, kidName, spaceBucks, freq) {
    itemEl.classList.add('done');
    itemEl.querySelector('.chore-check').textContent='✓';

    var kid=appData.kids.find(function(k){return String(k['Name']||'').trim().toLowerCase()===kidName.toLowerCase();});
    var oldBucks=parseInt(kid&&kid['Total Bucks'])||0;
    if (kid) {
      kid['Total Bucks']=oldBucks+spaceBucks;
      var el=document.getElementById('bucks-'+kidName);
      if (el){el.textContent=kid['Total Bucks']; animateBucks(el);}
    }
    appData.chores=appData.chores.filter(function(c){
      return !(String(c['Chore Name']||'').trim().toLowerCase()===choreName.toLowerCase()
            && String(c['Assigned To']||'').trim().toLowerCase()===kidName.toLowerCase());
    });

    /* Save undo state */
    var timestamp=friendlyTimestamp();
    undoStacks[kidName]={type:'chore', kidName, choreName, spaceBucks, freq, timestamp, oldBucks};
    var undoBtn=document.getElementById('undo-btn-'+kidName);
    if (undoBtn) undoBtn.classList.add('visible');

    fireConfetti(kidName);
    showToast('+'+spaceBucks+' Space Bucks for '+kidName+'! 🚀');
    writeChoreComplete(choreName, kidName, spaceBucks, freq, timestamp);
  }

  /* ── Confirm reward ── */
  function promptRedeem(kidName, rewardName, cost, icon) {
    var kid=appData.kids.find(function(k){return String(k['Name']||'').trim().toLowerCase()===kidName.toLowerCase();});
    var bucks=parseInt(kid&&kid['Total Bucks'])||0;
    showModal(icon, 'Redeem Reward?',
      '"'+rewardName+'" costs '+cost+' ⭐. '+kidName+' has '+bucks+'. Confirm?',
      [
        { label: 'Redeem!', cls: 'btn-confirm', action: function(){
            closeModal(); confirmRedeem(kidName,rewardName,cost,icon);
        }},
        { label: 'Cancel', cls: 'btn-cancel', action: closeModal }
      ]
    );
  }

  /* ── Redeem reward ── */
  function confirmRedeem(kidName, rewardName, cost, icon) {
    var kid=appData.kids.find(function(k){return String(k['Name']||'').trim().toLowerCase()===kidName.toLowerCase();});
    var oldBucks=parseInt(kid&&kid['Total Bucks'])||0;
    if (kid){
      kid['Total Bucks']=Math.max(0,oldBucks-cost);
      var el=document.getElementById('bucks-'+kidName);
      if (el){el.textContent=kid['Total Bucks']; animateBucks(el);}
      var body=document.getElementById('body-'+kidName);
      if (body&&pageTab==='rewards') renderRewards(body,kidName,kid['Total Bucks']);
    }

    var timestamp=friendlyTimestamp();
    undoStacks[kidName]={type:'reward', kidName, rewardName, cost, icon, timestamp, oldBucks};
    var undoBtn=document.getElementById('undo-btn-'+kidName);
    if (undoBtn) undoBtn.classList.add('visible');

    fireConfettiReward(kidName);
    showToast(rewardName+' redeemed by '+kidName+'! 🎉');
    writeRedeemReward(kidName, rewardName, cost, timestamp);
  }

  /* ── Undo prompt ── */
  function promptUndo(kidName) {
    var action=undoStacks[kidName];
    if (!action) return;
    var desc = action.type==='chore'
      ? 'Undo "'+action.choreName+'" for '+kidName+'? This will remove +'+action.spaceBucks+' Space Bucks.'
      : 'Undo redeem "'+action.rewardName+'" for '+kidName+'? This will restore '+action.cost+' Space Bucks.';
    showModal('↩', 'Undo Last Action?', desc, [
      { label: 'Yes, Undo', cls: 'btn-undo-confirm', action: function(){
          closeModal(); executeUndo(kidName);
      }},
      { label: 'Cancel', cls: 'btn-cancel', action: closeModal }
    ]);
  }

  async function executeUndo(kidName) {
    var action=undoStacks[kidName];
    if (!action) return;

    /* Restore bucks locally */
    var kid=appData.kids.find(function(k){return String(k['Name']||'').trim().toLowerCase()===kidName.toLowerCase();});
    if (kid) {
      kid['Total Bucks']=action.oldBucks;
      var el=document.getElementById('bucks-'+kidName);
      if (el) el.textContent=action.oldBucks;
    }

    /* If it was a chore, restore it to the local list */
    if (action.type==='chore') {
      appData.chores.push({
        'Chore Name': action.choreName, 'Assigned To': kidName,
        'Space Bucks': String(action.spaceBucks), 'Frequency': action.freq||'', 'Status':''
      });
      if (pageTab==='missions') {
        var myChores=appData.chores.filter(function(c){
          return String(c['Assigned To']||'').trim().toLowerCase()===kidName.toLowerCase();
        });
        var body=document.getElementById('body-'+kidName);
        if (body) renderMissions(body,kidName,myChores);
      }
    } else {
      if (pageTab==='rewards') {
        var body=document.getElementById('body-'+kidName);
        if (body) renderRewards(body,kidName,action.oldBucks);
      }
    }

    /* Clear undo state */
    delete undoStacks[kidName];
    var undoBtn=document.getElementById('undo-btn-'+kidName);
    if (undoBtn) undoBtn.classList.remove('visible');

    showToast('↩ Undone for '+kidName);
    await writeUndo(action);
  }

  /* ── Sheets API writes ── */
  async function writeChoreComplete(choreName, kidName, spaceBucks, freq, timestamp) {
    setSyncStatus('syncing','↑ saving…');
    try {
      /* 1. Mark chore done in Chores sheet */
      var choresData = await sheetsGET('Chores');
      var rows=choresData.values||[]; var hdr=rows[0]||[];
      var nameIdx=hdr.indexOf('Chore Name'), assignIdx=hdr.indexOf('Assigned To');
      var statusIdx=hdr.indexOf('Status'), lastIdx=hdr.indexOf('Last Completed');
      var targetRow=-1;
      for (var i=1;i<rows.length;i++){
        if (String(rows[i][nameIdx]||'').trim().toLowerCase()===choreName.toLowerCase()
         && String(rows[i][assignIdx]||'').trim().toLowerCase()===kidName.toLowerCase()){
          targetRow=i+1; break;
        }
      }
      if (targetRow>0){
        if (statusIdx>=0) await sheetsPUT('Chores!'+colLetter(statusIdx)+targetRow,[['TRUE']]);
        if (lastIdx>=0)   await sheetsPUT('Chores!'+colLetter(lastIdx)+targetRow,[[timestamp]],'USER_ENTERED');
      }

      /* 2. Update kid bucks */
      await updateKidBucks(kidName, spaceBucks);

      /* 3. Append to log */
      await sheetsAppend('Log', [[timestamp, kidName, 'Chore Completed', choreName, spaceBucks]]);

      setSyncStatus('','');
    } catch(e){ handleSyncError(e); }
  }

  async function writeRedeemReward(kidName, rewardName, cost, timestamp) {
    setSyncStatus('syncing','↑ saving…');
    try {
      await updateKidBucks(kidName, -cost);
      await sheetsAppend('Log', [[timestamp, kidName, 'Reward Redeemed', rewardName, -cost]]);
      setSyncStatus('','');
    } catch(e){ handleSyncError(e); }
  }

  async function writeUndo(action) {
    setSyncStatus('syncing','↑ saving…');
    try {
      if (action.type==='chore'){
        /* Un-mark chore in sheet */
        var choresData = await sheetsGET('Chores');
        var rows=choresData.values||[]; var hdr=rows[0]||[];
        var nameIdx=hdr.indexOf('Chore Name'), assignIdx=hdr.indexOf('Assigned To');
        var statusIdx=hdr.indexOf('Status'), lastIdx=hdr.indexOf('Last Completed');
        for (var i=1;i<rows.length;i++){
          if (String(rows[i][nameIdx]||'').trim().toLowerCase()===action.choreName.toLowerCase()
           && String(rows[i][assignIdx]||'').trim().toLowerCase()===action.kidName.toLowerCase()){
            if (statusIdx>=0) await sheetsPUT('Chores!'+colLetter(statusIdx)+(i+1),[['']]);
            if (lastIdx>=0)   await sheetsPUT('Chores!'+colLetter(lastIdx)+(i+1),[['']]);
            break;
          }
        }
        await updateKidBucks(action.kidName, -action.spaceBucks);
        await sheetsAppend('Log', [[friendlyTimestamp(), action.kidName, 'UNDO Chore', action.choreName, -action.spaceBucks]]);
      } else {
        await updateKidBucks(action.kidName, action.cost);
        await sheetsAppend('Log', [[friendlyTimestamp(), action.kidName, 'UNDO Reward', action.rewardName, action.cost]]);
      }
      setSyncStatus('','');
    } catch(e){ handleSyncError(e); }
  }

  /* ── Sheets API helpers — all calls routed through Cloudflare Worker proxy ──
   * The Worker expects:  ?path=/SHEET_ID/values/SheetName!A1:append&qi=valueInputOption=...
   * Paths must NOT be double-encoded — pass the raw path string, Worker handles forwarding.
   * ── */
  function proxyURL(rawPath, qi) {
    var url = PROXY_BASE + '?path=' + encodeURIComponent(rawPath);
    if (qi) url += '&qi=' + encodeURIComponent(qi);
    return url;
  }

  async function sheetsGET(range) {
    var path = '/' + SHEET_ID + '/values/' + range;
    var resp = await fetch(proxyURL(path));
    if (!resp.ok) throw new Error('GET failed: ' + resp.status + ' ' + await resp.text());
    return resp.json();
  }

  async function sheetsPUT(range, values, inputOption) {
    var path = '/' + SHEET_ID + '/values/' + range;
    var resp = await fetch(proxyURL(path, 'valueInputOption=' + (inputOption||'RAW')), {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ values: values })
    });
    if (!resp.ok) throw new Error('PUT failed: ' + resp.status + ' ' + await resp.text());
    return resp;
  }

  async function sheetsAppend(sheetName, values) {
    /* :append must be in the path BEFORE encoding, not appended after */
    var path = '/' + SHEET_ID + '/values/' + sheetName + '!A1:append';
    var resp = await fetch(
      proxyURL(path, 'valueInputOption=USER_ENTERED&insertDataOption=INSERT_ROWS'),
      { method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ values: values }) }
    );
    if (!resp.ok) throw new Error('Append failed: ' + resp.status + ' ' + await resp.text());
    return resp;
  }

  async function updateKidBucks(kidName, delta) {
    var data = await sheetsGET('Kids');
    var rows = data.values||[]; var hdr = rows[0]||[];
    var kNameIdx = hdr.indexOf('Name'), kBuckIdx = hdr.indexOf('Total Bucks');
    for (var j = 1; j < rows.length; j++) {
      if (String(rows[j][kNameIdx]||'').trim().toLowerCase() === kidName.toLowerCase()) {
        var current = parseInt(rows[j][kBuckIdx])||0;
        await sheetsPUT('Kids!'+colLetter(kBuckIdx)+(j+1), [[Math.max(0, current+delta)]]);
        break;
      }
    }
  }

  /* Returns a human-readable timestamp for the Log sheet, e.g. "May 27, 2026 2:32 PM" */
  function friendlyTimestamp() {
    return new Date().toLocaleString('en-US', {
      month: 'short', day: 'numeric', year: 'numeric',
      hour: 'numeric', minute: '2-digit', hour12: true
    });
  }

  function colLetter(idx){
    var s=''; idx++;
    while(idx>0){idx--;s=String.fromCharCode(65+(idx%26))+s;idx=Math.floor(idx/26);}
    return s;
  }
  function handleSyncError(e){
    console.error('Sync error:',e);
    setSyncStatus('error','⚠ sync failed');
    setTimeout(function(){setSyncStatus('','');},4000);
  }

  /* ── Modal helper ── */
  function showModal(icon, title, body, buttons) {
    document.getElementById('modal-icon').textContent=icon;
    document.getElementById('modal-title').textContent=title;
    document.getElementById('modal-body').textContent=body;
    var actions=document.getElementById('modal-actions');
    actions.innerHTML='';
    buttons.forEach(function(b){
      var btn=document.createElement('button');
      btn.className=b.cls; btn.textContent=b.label;
      btn.addEventListener('click',b.action);
      actions.appendChild(btn);
    });
    document.getElementById('modal-overlay').classList.add('open');
  }
  function closeModal(){
    document.getElementById('modal-overlay').classList.remove('open');
  }

  /* ── Animations ── */
  function animateBucks(el){
    el.style.transform='scale(1.25)'; el.style.color='#FFD700';
    el.style.transition='transform 0.15s, color 0.3s';
    setTimeout(function(){el.style.transform='scale(1)';el.style.color='#66FCF1';},400);
  }
  function getCardOriginX(kidName){
    var card=document.getElementById('card-'+kidName);
    if (!card) return 0.5;
    var rect=card.getBoundingClientRect();
    return (rect.left+rect.width/2)/window.innerWidth;
  }
  function fireConfetti(kidName){
    var ox=getCardOriginX(kidName), end=Date.now()+2500;
    var colors=['#66FCF1','#45A29E','#FFD700','#fff'];
    (function frame(){
      confetti({particleCount:6,angle:60, spread:45,origin:{x:ox-0.04,y:0.3},colors:colors});
      confetti({particleCount:6,angle:120,spread:45,origin:{x:ox+0.04,y:0.3},colors:colors});
      if(Date.now()<end) requestAnimationFrame(frame);
    }());
  }
  function fireConfettiReward(kidName){
    confetti({particleCount:120,spread:70,origin:{x:getCardOriginX(kidName),y:0.45},
      colors:['#FFD700','#FFA500','#66FCF1','#fff','#4CAF50']});
  }
  function showToast(msg){
    var el=document.getElementById('toast');
    el.textContent=msg; el.classList.add('show');
    setTimeout(function(){el.classList.remove('show');},3000);
  }
  function setSyncStatus(type,msg){
    var el=document.getElementById('sync-status');
    el.textContent=msg; el.className=type;
  }

  /* ── Auto-refresh chores every 5 min ── */
  setInterval(function(){
    fetch(CSV_CHORES+'&t='+Date.now()).then(function(r){return r.text();}).then(function(text){
      var all=parseCSV(text);
      appData.chores=all.filter(function(c){return String(c['Status']||'').trim().toUpperCase()!=='TRUE';});
      renderApp();
    }).catch(function(){});
  }, 5*60*1000);

  loadData();
</script>