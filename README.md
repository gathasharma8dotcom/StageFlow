<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>StageFlow — Choreography Made Simple</title>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0a0a0f;
  --surface: #13131a;
  --panel: #1a1a24;
  --border: #2a2a3a;
  --accent: #e8ff47;
  --accent2: #ff4785;
  --accent3: #47c8ff;
  --text: #f0f0f8;
  --muted: #6b6b8a;
  --panel-w: 220px;
}
* { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
html, body { height: 100%; overflow: hidden; }
body { background:var(--bg); color:var(--text); font-family:'DM Sans',sans-serif; display:flex; flex-direction:column; touch-action:none; }

header {
  display:flex; align-items:center; justify-content:space-between;
  padding:0 14px; height:52px;
  background:var(--surface); border-bottom:1px solid var(--border);
  flex-shrink:0; z-index:200; gap:8px;
}
.logo { font-family:'Bebas Neue',sans-serif; font-size:20px; letter-spacing:3px; color:var(--accent); line-height:1; flex-shrink:0; }
.logo small { color:var(--muted); font-size:9px; letter-spacing:2px; font-family:'DM Mono',monospace; display:block; margin-top:-2px; }
.header-actions { display:flex; gap:6px; align-items:center; }

.btn {
  padding:6px 12px; border-radius:6px; border:1px solid var(--border);
  background:transparent; color:var(--text); font-family:'DM Sans',sans-serif;
  font-size:12px; font-weight:500; cursor:pointer; transition:all 0.15s;
  display:flex; align-items:center; gap:5px; white-space:nowrap; user-select:none;
}
.btn:hover,.btn:active { background:var(--panel); border-color:var(--muted); }
.btn.primary { background:var(--accent); color:#000; border-color:var(--accent); font-weight:700; }
.btn.primary:hover,.btn.primary:active { background:#d4eb30; }
.btn.active-mode { background:var(--panel); border-color:var(--accent3); color:var(--accent3); }
.btn.sm { padding:5px 9px; font-size:11px; }

.app-body { display:flex; flex:1; overflow:hidden; position:relative; }

.panel {
  width:var(--panel-w); background:var(--surface);
  display:flex; flex-direction:column; flex-shrink:0;
  overflow:hidden; transition:transform 0.28s cubic-bezier(.4,0,.2,1); z-index:100;
}
.left-panel { border-right:1px solid var(--border); }
.right-panel { border-left:1px solid var(--border); }

.panel-section { padding:12px 14px; border-bottom:1px solid var(--border); }
.panel-label { font-family:'DM Mono',monospace; font-size:9px; letter-spacing:2px; color:var(--muted); text-transform:uppercase; margin-bottom:10px; }

.input-row { display:flex; align-items:center; gap:6px; margin-bottom:7px; }
.input-row label { font-size:11px; color:var(--muted); width:44px; flex-shrink:0; }
.input-row input[type=number],.input-row select {
  flex:1; min-width:0; background:var(--bg); border:1px solid var(--border);
  border-radius:5px; color:var(--text); font-family:'DM Mono',monospace;
  font-size:11px; padding:5px 7px; outline:none;
}
.input-row input:focus,.input-row select:focus { border-color:var(--accent); }
.input-row input[type=checkbox] { width:15px; height:15px; accent-color:var(--accent); cursor:pointer; flex-shrink:0; }

.color-row { display:flex; flex-wrap:wrap; gap:5px; margin-top:7px; }
.color-swatch { width:20px; height:20px; border-radius:50%; cursor:pointer; border:2px solid transparent; transition:border-color 0.12s,transform 0.12s; }
.color-swatch:hover,.color-swatch:active { transform:scale(1.25); }
.color-swatch.active { border-color:white; }

.add-dancer-row { display:flex; gap:6px; align-items:center; }
.add-dancer-row input[type=text] {
  flex:1; min-width:0; background:var(--bg); border:1px solid var(--border);
  border-radius:5px; color:var(--text); font-family:'DM Sans',sans-serif;
  font-size:12px; padding:6px 8px; outline:none;
}
.add-dancer-row input:focus { border-color:var(--accent); }
.color-dot { width:14px; height:14px; border-radius:50%; border:1.5px solid var(--border); flex-shrink:0; }

.dancers-list { flex:1; overflow-y:auto; padding:6px; }
.dancers-list::-webkit-scrollbar { width:3px; }
.dancers-list::-webkit-scrollbar-thumb { background:var(--border); }

.dancer-item {
  display:flex; align-items:center; gap:7px;
  padding:6px 7px; border-radius:6px; cursor:pointer;
  transition:background 0.1s; border:1px solid transparent; margin-bottom:2px;
}
.dancer-item:hover,.dancer-item.selected { background:var(--panel); border-color:var(--border); }
.dancer-dot-sm { width:12px; height:12px; border-radius:50%; flex-shrink:0; }
.dancer-item input.diname {
  background:transparent; border:none; color:var(--text); font-size:11px;
  font-family:'DM Sans',sans-serif; outline:none; flex:1; min-width:0;
}
.del-btn { opacity:0; background:none; border:none; color:var(--accent2); cursor:pointer; font-size:15px; padding:0 2px; line-height:1; transition:opacity 0.15s; flex-shrink:0; }
.dancer-item:hover .del-btn { opacity:1; }

.canvas-area {
  flex:1; display:flex; flex-direction:column;
  align-items:center; justify-content:center;
  background:var(--bg); overflow:hidden; position:relative; padding:8px;
}

.stage-scroll { overflow:auto; max-width:100%; max-height:100%; display:flex; align-items:center; justify-content:center; }
.stage-scroll::-webkit-scrollbar { width:4px; height:4px; }
.stage-scroll::-webkit-scrollbar-thumb { background:var(--border); }

.stage-wrapper { position:relative; box-shadow:0 0 60px rgba(232,255,71,0.06),0 0 0 1px var(--border); border-radius:3px; flex-shrink:0; }
#stage-canvas { display:block; touch-action:none; }
.stage-dim-label { text-align:center; font-family:'DM Mono',monospace; font-size:10px; color:var(--muted); margin-top:6px; }

.mode-badge {
  position:absolute; top:10px; left:10px;
  font-family:'DM Mono',monospace; font-size:9px; letter-spacing:1px;
  padding:3px 9px; border-radius:20px; background:var(--panel);
  border:1px solid var(--border); color:var(--muted); pointer-events:none; z-index:50;
}
.mode-badge.place { color:var(--accent); border-color:var(--accent); }
.mode-badge.select { color:var(--accent3); border-color:var(--accent3); }

.hint-bar {
  position:absolute; bottom:8px; left:50%; transform:translateX(-50%);
  display:flex; gap:12px; font-family:'DM Mono',monospace; font-size:9px; color:var(--muted);
  background:rgba(10,10,15,0.85); padding:5px 12px; border-radius:20px;
  border:1px solid var(--border); white-space:nowrap; pointer-events:none;
}
.hint-bar kbd { background:var(--border); padding:1px 4px; border-radius:3px; font-size:8px; color:var(--text); }

.selected-bar {
  position:absolute; bottom:34px; left:50%; transform:translateX(-50%);
  background:var(--panel); border:1px solid var(--border); border-radius:8px;
  padding:6px 12px; display:flex; align-items:center; gap:8px; font-size:11px;
  z-index:50; opacity:0; pointer-events:none; transition:opacity 0.2s; white-space:nowrap;
}
.selected-bar.visible { opacity:1; pointer-events:auto; }
.selected-bar label { color:var(--muted); font-size:9px; font-family:'DM Mono',monospace; }
.rot-display { font-family:'DM Mono',monospace; font-size:11px; color:var(--accent); min-width:36px; }

.formations-list { flex:1; overflow-y:auto; padding:6px; }
.formations-list::-webkit-scrollbar { width:3px; }
.formations-list::-webkit-scrollbar-thumb { background:var(--border); }

.animate-row { display:flex; gap:6px; align-items:center; padding:8px 12px; border-bottom:1px solid var(--border); }
.speed-select { flex:1; background:var(--bg); border:1px solid var(--border); border-radius:5px; color:var(--text); font-family:'DM Mono',monospace; font-size:11px; padding:5px 7px; outline:none; }

.formation-card {
  background:var(--bg); border:1px solid var(--border); border-radius:7px;
  padding:8px; margin-bottom:7px; cursor:pointer; transition:all 0.15s; position:relative;
}
.formation-card:hover { border-color:var(--muted); }
.formation-card.active { border-color:var(--accent); box-shadow:0 0 0 1px var(--accent); }
.formation-preview { width:100%; height:60px; background:var(--panel); border-radius:4px; overflow:hidden; margin-bottom:7px; display:block; }
.formation-name-row { display:flex; align-items:center; justify-content:space-between; margin-bottom:2px; }
.formation-name-row input { background:transparent; border:none; color:var(--text); font-size:11px; font-weight:500; font-family:'DM Sans',sans-serif; outline:none; flex:1; }
.formation-meta { font-size:10px; color:var(--muted); font-family:'DM Mono',monospace; }
.formation-del { background:none; border:none; color:var(--muted); cursor:pointer; font-size:13px; opacity:0; transition:opacity 0.15s; line-height:1; }
.formation-card:hover .formation-del { opacity:1; }
.formation-card:hover .formation-del:hover { color:var(--accent2); }

.toast {
  position:fixed; bottom:20px; left:50%;
  transform:translateX(-50%) translateY(80px);
  background:var(--accent); color:#000; padding:9px 18px;
  border-radius:8px; font-weight:700; font-size:12px;
  transition:transform 0.3s cubic-bezier(.175,.885,.32,1.275);
  z-index:9999; pointer-events:none;
}
.toast.show { transform:translateX(-50%) translateY(0); }

.panel-toggle {
  display:none; position:absolute; top:8px; z-index:150;
  background:var(--surface); border:1px solid var(--border);
  border-radius:6px; padding:6px 8px; cursor:pointer;
  color:var(--text); font-size:16px; line-height:1;
}
.panel-toggle.left { left:8px; }
.panel-toggle.right { right:8px; }

@media (max-width:700px) {
  .panel { position:absolute; top:0; bottom:0; box-shadow:4px 0 24px rgba(0,0,0,0.6); }
  .left-panel { left:0; transform:translateX(-100%); }
  .right-panel { right:0; transform:translateX(100%); }
  .panel.open { transform:translateX(0) !important; }
  .panel-toggle { display:flex; }
  .hide-mobile { display:none !important; }
  .hint-bar { display:none; }
  .selected-bar { flex-wrap:wrap; max-width:90vw; }
}
@media (max-width:480px) {
  :root { --panel-w:88vw; }
  .logo small { display:none; }
  header { padding:0 10px; height:48px; }
}
</style>
</head>
<body>

<header>
  <div class="logo">StageFlow <small>CHOREOGRAPHY MADE SIMPLE</small></div>
  <div class="header-actions">
    <button class="btn hide-mobile" id="btn-select-mode">
      <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M5 3l14 9-7 1-4 7z"/></svg> SELECT
    </button>
    <button class="btn hide-mobile" id="btn-place-mode">
      <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><circle cx="12" cy="12" r="4"/><line x1="12" y1="2" x2="12" y2="7"/><line x1="12" y1="17" x2="12" y2="22"/><line x1="2" y1="12" x2="7" y2="12"/><line x1="17" y1="12" x2="22" y2="12"/></svg> PLACE
    </button>
    <button class="btn primary" id="btn-save-formation">
      <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M19 21H5a2 2 0 01-2-2V5a2 2 0 012-2h11l5 5v11a2 2 0 01-2 2z"/><polyline points="17 21 17 13 7 13 7 21"/></svg> SAVE
    </button>
  </div>
</header>

<div class="app-body">
  <button class="panel-toggle left" id="toggle-left">☰</button>
  <button class="panel-toggle right" id="toggle-right">⋮</button>

  <!-- LEFT PANEL -->
  <div class="panel left-panel" id="left-panel">
    <div class="panel-section">
      <div class="panel-label">Stage</div>
      <div class="input-row"><label>Width</label><input type="number" id="stage-w" value="40" min="5" max="200"> ft</div>
      <div class="input-row"><label>Depth</label><input type="number" id="stage-d" value="30" min="5" max="200"> ft</div>
      <div class="input-row"><label>Shape</label>
        <select id="stage-shape">
          <option value="rect">Rectangle</option>
          <option value="thrust">Thrust</option>
          <option value="circle">Circle</option>
          <option value="diamond">Diamond</option>
        </select>
      </div>
      <div class="input-row">
        <label>Wings</label>
        <input type="checkbox" id="show-wings">
        <span style="font-size:11px;color:var(--muted)">Show wings</span>
      </div>
      <div class="input-row" id="wing-depth-row" style="display:none">
        <label>W.Depth</label>
        <input type="number" id="wing-depth" value="8" min="2" max="50"> ft
      </div>
      <button class="btn sm" style="width:100%;justify-content:center;margin-top:6px" id="btn-apply-stage">APPLY STAGE</button>
    </div>
    <div class="panel-section" id="mobile-mode-section" style="display:none">
      <div class="panel-label">Mode</div>
      <div style="display:flex;gap:6px">
        <button class="btn sm" id="btn-select-mode-m" style="flex:1;justify-content:center">SELECT</button>
        <button class="btn sm" id="btn-place-mode-m" style="flex:1;justify-content:center">PLACE</button>
      </div>
    </div>
    <div class="panel-section">
      <div class="panel-label">Add Dancer</div>
      <div class="add-dancer-row">
        <div class="color-dot" id="selected-color-dot"></div>
        <input type="text" id="dancer-name-input" placeholder="Name..." maxlength="20">
        <button class="btn sm primary" id="btn-add-dancer">+</button>
      </div>
      <div class="color-row" id="color-palette"></div>
    </div>
    <div class="panel-label" style="padding:10px 14px 4px;border-top:1px solid var(--border)"><span id="dancer-count">0</span> DANCERS</div>
    <div class="dancers-list" id="dancers-list"></div>
  </div>

  <!-- STAGE -->
  <div class="canvas-area" id="canvas-area">
    <div class="mode-badge select" id="mode-badge">SELECT MODE</div>
    <div class="stage-scroll" id="stage-scroll">
      <div>
        <div class="stage-wrapper" id="stage-wrapper">
          <canvas id="stage-canvas"></canvas>
        </div>
        <div class="stage-dim-label" id="stage-dim-label">40ft × 30ft</div>
      </div>
    </div>
    <div class="selected-bar" id="selected-bar">
      <label>FACING</label>
      <span class="rot-display" id="rot-display">0°</span>
      <button class="btn sm" id="rot-ccw">↺45°</button>
      <button class="btn sm" id="rot-cw">↻45°</button>
      <button class="btn sm" style="border-color:var(--accent2);color:var(--accent2)" id="btn-del-selected">✕</button>
    </div>
    <div class="hint-bar">
      <span><kbd>S</kbd>Select</span>
      <span><kbd>P</kbd>Place</span>
      <span><kbd>Del</kbd>Remove</span>
      <span><kbd>←/→</kbd>Rotate</span>
      <span>Drag to move</span>
      <span>Right-click=rotate</span>
    </div>
  </div>

  <!-- RIGHT PANEL -->
  <div class="panel right-panel" id="right-panel">
    <div class="panel-section">
      <div class="panel-label">Formations</div>
      <button class="btn sm" style="width:100%;justify-content:center" id="btn-new-formation">+ SAVE FORMATION</button>
    </div>
    <div class="animate-row">
      <select class="speed-select" id="anim-speed">
        <option value="1500">Cinematic 1.5s</option>
        <option value="800">Slow 0.8s</option>
        <option value="500" selected>Normal 0.5s</option>
        <option value="250">Fast 0.25s</option>
      </select>
      <button class="btn sm" id="btn-animate">▶ GO</button>
    </div>
    <div class="formations-list" id="formations-list">
      <div style="padding:16px 8px;text-align:center;color:var(--muted);font-size:11px;line-height:1.6">Save formations to store dancer positions &amp; directions</div>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
// ═══════════════════════════════════════
// STATE
// ═══════════════════════════════════════
const S = {
  stageW:40, stageD:30, stageShape:'rect',
  wings:false, wingDepth:8,
  mode:'select',
  dancers:[], // {id,name,color,x,y,angle}
  formations:[], // {id,name,snapshot}
  selId:null,
  activeFormation:null,
  nextId:1,
  color:'#e8ff47',
  animating:false,
};
const COLORS=['#e8ff47','#ff4785','#47c8ff','#ff9f47','#b847ff','#47ff9f','#ff4747','#ffffff','#ffd447','#47ffee'];
const PPF=13;

// ═══════════════════════════════════════
// STAGE DRAW
// ═══════════════════════════════════════
const canvas=document.getElementById('stage-canvas');
const ctx=canvas.getContext('2d');
const wrapper=document.getElementById('stage-wrapper');

function ssz(){return{w:S.stageW*PPF,h:S.stageD*PPF};}

function drawStage(){
  const {w,h}=ssz();
  canvas.width=w; canvas.height=h;
  wrapper.style.width=w+'px'; wrapper.style.height=h+'px';
  ctx.clearRect(0,0,w,h);
  ctx.fillStyle='#0e0e18'; ctx.fillRect(0,0,w,h);

  ctx.save();
  sPath(w,h);
  ctx.fillStyle='#14141f'; ctx.fill();

  // Grid
  ctx.strokeStyle='rgba(255,255,255,0.032)'; ctx.lineWidth=1;
  for(let x=0;x<=w;x+=PPF){ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,h);ctx.stroke();}
  for(let y=0;y<=h;y+=PPF){ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(w,y);ctx.stroke();}
  // Center lines
  ctx.strokeStyle='rgba(255,255,255,0.07)'; ctx.setLineDash([4,5]);
  ctx.beginPath();ctx.moveTo(w/2,0);ctx.lineTo(w/2,h);ctx.stroke();
  ctx.beginPath();ctx.moveTo(0,h/2);ctx.lineTo(w,h/2);ctx.stroke();
  ctx.setLineDash([]);
  ctx.restore();

  // Stage border glow
  ctx.save(); sPath(w,h);
  ctx.strokeStyle='rgba(232,255,71,0.2)'; ctx.lineWidth=2; ctx.stroke();
  ctx.restore();

  // Wings
  if(S.wings){
    const wd=S.wingDepth*PPF;
    wrapper.style.overflowX='visible';
    ctx.save();
    ctx.fillStyle='rgba(71,200,255,0.05)';
    ctx.strokeStyle='rgba(71,200,255,0.3)';
    ctx.lineWidth=1.5; ctx.setLineDash([5,4]);
    ctx.fillRect(-wd,h*0.08,wd,h*0.84); ctx.strokeRect(-wd,h*0.08,wd,h*0.84);
    ctx.fillRect(w,h*0.08,wd,h*0.84); ctx.strokeRect(w,h*0.08,wd,h*0.84);
    ctx.setLineDash([]);
    ctx.fillStyle='rgba(71,200,255,0.4)'; ctx.font='9px DM Mono,monospace'; ctx.textAlign='center';
    ctx.fillText('WING L',-wd/2,h/2);
    ctx.fillText('WING R',w+wd/2,h/2);
    ctx.restore();
  } else { wrapper.style.overflowX='hidden'; }

  // Direction labels
  ctx.fillStyle='rgba(107,107,138,0.5)'; ctx.font='9px DM Mono,monospace'; ctx.textAlign='center';
  ctx.fillText('DOWNSTAGE',w/2,h-5);
  ctx.fillText('UPSTAGE',w/2,13);
  ctx.save(); ctx.translate(10,h/2); ctx.rotate(-Math.PI/2); ctx.fillText('STAGE L',0,0); ctx.restore();
  ctx.save(); ctx.translate(w-10,h/2); ctx.rotate(Math.PI/2); ctx.fillText('STAGE R',0,0); ctx.restore();

  document.getElementById('stage-dim-label').textContent=`${S.stageW}ft × ${S.stageD}ft${S.wings?' + wings':''}`;
}

function sPath(w,h){
  ctx.beginPath();
  if(S.stageShape==='rect'){ ctx.rect(0,0,w,h); }
  else if(S.stageShape==='thrust'){ ctx.moveTo(0,0);ctx.lineTo(w,0);ctx.lineTo(w,h*0.65);ctx.lineTo(w*0.65,h);ctx.lineTo(w*0.35,h);ctx.lineTo(0,h*0.65);ctx.closePath(); }
  else if(S.stageShape==='circle'){ ctx.ellipse(w/2,h/2,w/2-2,h/2-2,0,0,Math.PI*2); }
  else if(S.stageShape==='diamond'){ ctx.moveTo(w/2,2);ctx.lineTo(w-2,h/2);ctx.lineTo(w/2,h-2);ctx.lineTo(2,h/2);ctx.closePath(); }
}

// ═══════════════════════════════════════
// DANCER RENDERING (SVG arrows as bg)
// ═══════════════════════════════════════
const AR=14; // arrow radius

function arrowSVG(color, angle, selected){
  const sz=AR*2+(selected?12:4);
  const cx=sz/2,cy=sz/2,r=AR;
  const a=angle*Math.PI/180;
  const tx=cx+r*Math.sin(a), ty=cy-r*Math.cos(a);
  const b1x=cx+(r*0.58)*Math.sin(a+2.45), b1y=cy-(r*0.58)*Math.cos(a+2.45);
  const b2x=cx+(r*0.58)*Math.sin(a-2.45), b2y=cy-(r*0.58)*Math.cos(a-2.45);
  const ring=selected?`<circle cx="${cx}" cy="${cy}" r="${r+4}" fill="none" stroke="white" stroke-width="1.5" stroke-dasharray="4 3" opacity="0.8"/>`:'';
  const glow=selected?`filter="url(#g)"`:'';
  return `data:image/svg+xml,${encodeURIComponent(
    `<svg xmlns="http://www.w3.org/2000/svg" width="${sz}" height="${sz}">
    <defs><filter id="g"><feGaussianBlur stdDeviation="3" result="b"/><feMerge><feMergeNode in="b"/
