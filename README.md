<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>Block Crush</title>
<link href="https://fonts.googleapis.com/css2?family=Exo+2:wght@400;700;900&display=swap" rel="stylesheet">
<style>
/* RESET - touch-action faqat kerakli joylarda */
* { margin:0; padding:0; box-sizing:border-box; user-select:none; -webkit-tap-highlight-color:transparent; }

/* HTML/BODY - scroll yo'q, lekin ko'rinadigan */
html {
  height:100%;
  overflow:hidden;
}
body {
  height:100%;
  width:100%;
  overflow:hidden;
  overscroll-behavior:none;
  background:#0d0d1a;
  background-image:
    radial-gradient(ellipse at 15% 15%, rgba(124,92,252,0.16) 0%, transparent 52%),
    radial-gradient(ellipse at 85% 85%, rgba(0,212,255,0.11) 0%, transparent 52%);
  font-family:'Exo 2', sans-serif;
  color:#e8e8ff;
  display:flex;
  align-items:center;
  justify-content:center;
}

/* PARTICLES */
#ptc { position:fixed; inset:0; pointer-events:none; z-index:0; overflow:hidden; }
.pt  { position:absolute; border-radius:50%; opacity:0; animation:fup linear infinite; }
@keyframes fup {
  0%   { opacity:0; transform:translateY(100vh) scale(0); }
  10%  { opacity:0.55; }
  90%  { opacity:0.12; }
  100% { opacity:0; transform:translateY(-40px) scale(1.6); }
}

/* ── APP WRAPPER ── */
#app {
  position:relative;
  z-index:10;
  width:100%;
  max-width:480px;
  height:100vh;
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:center;
  padding:8px 10px;
  gap:8px;
}

/* ── SCREEN ── */
.scr { width:100%; display:none; flex-direction:column; align-items:center; }
.scr.active { display:flex; }

/* ── MENU ── */
#menuScr .card {
  width:100%;
  background:linear-gradient(145deg, rgba(124,92,252,0.13), rgba(0,212,255,0.07));
  backdrop-filter:blur(20px);
  border:1px solid rgba(255,255,255,0.11);
  border-radius:clamp(18px,5vw,32px);
  padding:clamp(24px,6vh,48px) clamp(16px,5vw,32px) clamp(20px,5vh,40px);
  text-align:center;
  box-shadow:0 0 40px rgba(124,92,252,0.28), inset 0 1px 0 rgba(255,255,255,0.09);
}
.logo {
  font-size:clamp(38px,11vw,64px);
  font-weight:900;
  letter-spacing:-2px;
  line-height:0.9;
  display:block;
  background:linear-gradient(135deg,#a78bfa 0%,#7c5cfc 40%,#00d4ff 100%);
  -webkit-background-clip:text;
  -webkit-text-fill-color:transparent;
  background-clip:text;
}
.logo-sub {
  font-size:clamp(9px,2.5vw,11px);
  color:rgba(232,232,255,0.4);
  letter-spacing:5px;
  text-transform:uppercase;
  display:block;
  margin:6px 0 clamp(20px,5vh,36px);
}
.mbtn {
  display:flex; align-items:center; justify-content:center; gap:12px;
  width:100%; padding:clamp(12px,3.5vh,17px) 16px; margin:7px 0;
  border:none; border-radius:clamp(12px,3vw,16px); cursor:pointer;
  font-family:'Exo 2',sans-serif; font-size:clamp(14px,4vw,17px); font-weight:800;
  letter-spacing:2px; text-transform:uppercase; transition:transform 0.15s;
}
.mbtn:active { transform:scale(0.96); }
.mbtn.play {
  background:linear-gradient(135deg,#7c5cfc,#4f35c8); color:#fff;
  box-shadow:0 8px 28px rgba(124,92,252,0.5), inset 0 1px 0 rgba(255,255,255,0.2);
}
.mbtn.info {
  background:rgba(255,255,255,0.07); color:#e8e8ff;
  border:1px solid rgba(255,255,255,0.11);
}
.bico {
  width:clamp(28px,8vw,36px); height:clamp(28px,8vw,36px);
  border-radius:9px; background:rgba(255,255,255,0.15);
  display:flex; align-items:center; justify-content:center; flex-shrink:0;
}
.bico svg { width:17px; height:17px; }

/* ── GAME SCREEN ── */
#gameScr {
  width:100%;
  gap:clamp(5px,1.5vh,10px);
}

/* TOPBAR */
.topbar { display:flex; align-items:stretch; gap:8px; width:100%; }
.score-card {
  flex:1;
  background:rgba(255,255,255,0.06);
  border:1px solid rgba(255,255,255,0.11);
  border-radius:clamp(10px,3vw,16px);
  padding:clamp(7px,1.8vh,12px) clamp(10px,3vw,16px);
  display:flex; flex-direction:column; justify-content:center;
}
.slabel { font-size:clamp(8px,2vw,10px); color:rgba(232,232,255,0.4); letter-spacing:3px; text-transform:uppercase; }
.sval {
  font-size:clamp(20px,6vw,32px); font-weight:900; line-height:1.1;
  background:linear-gradient(135deg,#fff,#a78bfa);
  -webkit-background-clip:text; -webkit-text-fill-color:transparent; background-clip:text;
}
.ibtn {
  width:clamp(40px,11vw,50px); height:clamp(40px,11vw,50px);
  border-radius:clamp(10px,3vw,14px); border:1px solid rgba(255,255,255,0.11); cursor:pointer;
  background:rgba(255,255,255,0.07);
  display:flex; align-items:center; justify-content:center;
  transition:transform 0.15s; flex-shrink:0;
}
.ibtn:active { transform:scale(0.91); background:rgba(255,255,255,0.14); }
.ibtn svg { width:20px; height:20px; stroke:#e8e8ff; }

/* BOARD */
.board-wrap {
  width:100%;
  background:rgba(255,255,255,0.03);
  border:1px solid rgba(255,255,255,0.1);
  border-radius:clamp(12px,4vw,22px);
  padding:clamp(6px,1.5vw,10px);
  box-shadow:0 0 38px rgba(124,92,252,0.28), inset 0 1px 0 rgba(255,255,255,0.05);
}
#board {
  width:100%; aspect-ratio:1/1;
  display:grid;
  grid-template-columns:repeat(10,1fr);
  grid-template-rows:repeat(10,1fr);
  gap:clamp(2px,0.6vw,4px);
  border-radius:clamp(8px,2.5vw,14px);
  overflow:hidden;
}
.cell {
  background:rgba(255,255,255,0.04);
  border-radius:clamp(2px,0.8vw,5px);
  border:1px solid rgba(255,255,255,0.07);
  aspect-ratio:1;
}
.cell.filled { border:none; border-radius:clamp(2px,0.8vw,5px); position:relative; }
.cell.filled::after {
  content:''; position:absolute; inset:0; border-radius:inherit;
  background:linear-gradient(135deg, rgba(255,255,255,0.25) 0%, transparent 55%);
  pointer-events:none;
}

/* RENKLAR */
.cell.filled.red    { background:linear-gradient(135deg,#ff6b6b,#b91c1c); box-shadow:0 0 7px rgba(255,107,107,0.5); }
.cell.filled.blue   { background:linear-gradient(135deg,#60a5fa,#1d4ed8); box-shadow:0 0 7px rgba(96,165,250,0.5); }
.cell.filled.green  { background:linear-gradient(135deg,#4ade80,#15803d); box-shadow:0 0 7px rgba(74,222,128,0.5); }
.cell.filled.purple { background:linear-gradient(135deg,#c084fc,#7e22ce); box-shadow:0 0 7px rgba(192,132,252,0.5); }
.cell.filled.orange { background:linear-gradient(135deg,#fb923c,#c2410c); box-shadow:0 0 7px rgba(251,146,60,0.5); }
.cell.filled.yellow { background:linear-gradient(135deg,#fde047,#a16207); box-shadow:0 0 7px rgba(253,224,71,0.5); }
.cell.filled.pink   { background:linear-gradient(135deg,#f472b6,#be185d); box-shadow:0 0 7px rgba(244,114,182,0.5); }
.cell.filled.cyan   { background:linear-gradient(135deg,#22d3ee,#0e7490); box-shadow:0 0 7px rgba(34,211,238,0.5); }
.cell.filled.lime   { background:linear-gradient(135deg,#a3e635,#3f6212); box-shadow:0 0 7px rgba(163,230,53,0.5); }
.cell.filled.rose   { background:linear-gradient(135deg,#fb7185,#9f1239); box-shadow:0 0 7px rgba(251,113,133,0.5); }

/* PREVIEW */
.cell.preview { border:2px solid rgba(255,255,255,0.4) !important; animation:pp 0.75s ease-in-out infinite; }
.cell.preview.red    { background:rgba(255,107,107,0.2)  !important; border-color:#ff6b6b !important; }
.cell.preview.blue   { background:rgba(96,165,250,0.2)   !important; border-color:#60a5fa !important; }
.cell.preview.green  { background:rgba(74,222,128,0.2)   !important; border-color:#4ade80 !important; }
.cell.preview.purple { background:rgba(192,132,252,0.2)  !important; border-color:#c084fc !important; }
.cell.preview.orange { background:rgba(251,146,60,0.2)   !important; border-color:#fb923c !important; }
.cell.preview.yellow { background:rgba(253,224,71,0.2)   !important; border-color:#fde047 !important; }
.cell.preview.pink   { background:rgba(244,114,182,0.2)  !important; border-color:#f472b6 !important; }
.cell.preview.cyan   { background:rgba(34,211,238,0.2)   !important; border-color:#22d3ee !important; }
.cell.preview.lime   { background:rgba(163,230,53,0.2)   !important; border-color:#a3e635 !important; }
.cell.preview.rose   { background:rgba(251,113,133,0.2)  !important; border-color:#fb7185 !important; }

@keyframes pp  { 0%,100%{opacity:0.5;} 50%{opacity:1;} }
@keyframes pop { 0%{transform:scale(1);} 45%{transform:scale(1.14);} 100%{transform:scale(1);} }
.cell.filled { animation:pop 0.16s ease; }
@keyframes fc  { 0%{background:rgba(255,255,255,0.9) !important;} 100%{background:transparent !important;} }
.cell.clearing { animation:fc 0.3s ease forwards !important; border:none !important; }

/* PIECES TRAY */
.tray {
  width:100%;
  background:rgba(255,255,255,0.04);
  border:1px solid rgba(255,255,255,0.1);
  border-radius:clamp(12px,4vw,22px);
  padding:clamp(10px,2.5vh,16px) clamp(8px,2vw,14px);
  display:flex; justify-content:center; align-items:center;
  gap:clamp(10px,3vw,20px);
  min-height:clamp(80px,15vh,120px);
  box-shadow:inset 0 1px 0 rgba(255,255,255,0.04);
  overflow:hidden;
}
.piece {
  display:grid;
  gap:clamp(2px,0.6vw,3px);
  background:rgba(255,255,255,0.08);
  padding:clamp(6px,1.5vw,10px);
  border-radius:clamp(8px,2.5vw,13px);
  border:1px solid rgba(255,255,255,0.11);
  cursor:grab; position:relative; flex-shrink:0;
  transition:transform 0.15s;
}
.piece.dragging { opacity:0.2; cursor:grabbing; transform:scale(0.93); }
.block {
  border-radius:clamp(2px,0.8vw,4px);
  position:relative;
}
.block::after {
  content:''; position:absolute; inset:0; border-radius:inherit;
  background:linear-gradient(135deg,rgba(255,255,255,0.28) 0%,transparent 55%);
}
.block.red    { background:linear-gradient(135deg,#ff6b6b,#b91c1c); }
.block.blue   { background:linear-gradient(135deg,#60a5fa,#1d4ed8); }
.block.green  { background:linear-gradient(135deg,#4ade80,#15803d); }
.block.purple { background:linear-gradient(135deg,#c084fc,#7e22ce); }
.block.orange { background:linear-gradient(135deg,#fb923c,#c2410c); }
.block.yellow { background:linear-gradient(135deg,#fde047,#a16207); }
.block.pink   { background:linear-gradient(135deg,#f472b6,#be185d); }
.block.cyan   { background:linear-gradient(135deg,#22d3ee,#0e7490); }
.block.lime   { background:linear-gradient(135deg,#a3e635,#3f6212); }
.block.rose   { background:linear-gradient(135deg,#fb7185,#9f1239); }
.block.empty  { visibility:hidden; }

/* SCORE POPUP */
.spop {
  position:fixed; pointer-events:none; z-index:999;
  font-family:'Exo 2',sans-serif; font-size:clamp(18px,5vw,26px); font-weight:900;
  color:#fff; text-shadow:0 0 16px rgba(124,92,252,0.9);
  animation:sf 0.95s ease forwards;
}
@keyframes sf { 0%{opacity:1;transform:translateY(0) scale(1);} 100%{opacity:0;transform:translateY(-80px) scale(1.4);} }

/* MODALS */
.modal {
  position:fixed; inset:0;
  background:rgba(5,5,18,0.86);
  backdrop-filter:blur(14px); -webkit-backdrop-filter:blur(14px);
  display:none; justify-content:center; align-items:center;
  z-index:2000; padding:16px;
  overscroll-behavior:none;
}
.mbox {
  background:linear-gradient(145deg,rgba(124,92,252,0.13),rgba(0,212,255,0.06));
  border:1px solid rgba(255,255,255,0.11);
  border-radius:clamp(16px,5vw,28px);
  padding:clamp(22px,5vh,38px) clamp(16px,5vw,26px);
  text-align:center; width:min(360px,95%);
  box-shadow:0 32px 64px rgba(0,0,0,0.6), inset 0 1px 0 rgba(255,255,255,0.09);
}
.mt {
  font-size:clamp(26px,8vw,36px); font-weight:900; margin-bottom:6px;
  background:linear-gradient(135deg,#a78bfa,#00d4ff);
  -webkit-background-clip:text; -webkit-text-fill-color:transparent; background-clip:text;
}
.msl { color:rgba(232,232,255,0.4); font-size:clamp(9px,2.5vw,11px); letter-spacing:4px; text-transform:uppercase; margin:18px 0 4px; }
.msn {
  font-size:clamp(42px,12vw,58px); font-weight:900; line-height:1; margin-bottom:20px;
  background:linear-gradient(135deg,#fff,#a78bfa);
  -webkit-background-clip:text; -webkit-text-fill-color:transparent; background-clip:text;
}
.mdbtn {
  display:flex; align-items:center; justify-content:center; gap:9px;
  width:100%; padding:clamp(12px,3vh,15px); margin:7px 0;
  border:none; border-radius:clamp(10px,3vw,14px); cursor:pointer;
  font-family:'Exo 2',sans-serif; font-size:clamp(13px,3.5vw,15px); font-weight:800;
  text-transform:uppercase; letter-spacing:2px; transition:transform 0.14s;
}
.mdbtn:active { transform:scale(0.96); }
.mdbtn.prim { background:linear-gradient(135deg,#7c5cfc,#4f35c8); color:#fff; box-shadow:0 8px 26px rgba(124,92,252,0.45); }
.mdbtn.dang { background:rgba(255,107,107,0.1); color:#ff6b6b; border:1px solid rgba(255,107,107,0.26); }
.rl { text-align:left; margin:14px 0; background:rgba(255,255,255,0.04); border:1px solid rgba(255,255,255,0.1); border-radius:12px; padding:10px 14px; }
.rl li { color:#e8e8ff; font-size:clamp(12px,3.5vw,14px); padding:7px 0; border-bottom:1px solid rgba(255,255,255,0.06); list-style:none; display:flex; align-items:center; gap:10px; }
.rl li:last-child { border-bottom:none; }
</style>
</head>
<body>

<div id="ptc"></div>

<div id="app">

  <!-- MENU -->
  <div class="scr active" id="menuScr">
    <div class="card">
      <span class="logo">BLOCK<br>CRUSH</span>
      <span class="logo-sub">Puzzle Game</span>
      <button class="mbtn play" id="startBtn">
        <span class="bico"><svg viewBox="0 0 24 24" fill="currentColor"><polygon points="5,3 19,12 5,21"/></svg></span>
        O'YNASH
      </button>
      <button class="mbtn info" id="howBtn">
        <span class="bico"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4M12 8h.01"/></svg></span>
        QOIDALAR
      </button>
    </div>
  </div>

  <!-- GAME -->
  <div class="scr" id="gameScr">
    <div class="topbar">
      <div class="score-card">
        <div class="slabel">Ball</div>
        <div class="sval" id="scoreVal">0</div>
      </div>
      <button class="ibtn" id="muteBtn">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
          <polygon points="11,5 6,9 2,9 2,15 6,15 11,19"/>
          <path id="w1" d="M15.54 8.46a5 5 0 0 1 0 7.07"/>
          <path id="w2" d="M19.07 4.93a10 10 0 0 1 0 14.14"/>
        </svg>
      </button>
      <button class="ibtn" id="menuBtn">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
          <line x1="3" y1="6" x2="21" y2="6"/>
          <line x1="3" y1="12" x2="21" y2="12"/>
          <line x1="3" y1="18" x2="21" y2="18"/>
        </svg>
      </button>
    </div>
    <div class="board-wrap"><div id="board"></div></div>
    <div class="tray" id="pieces"></div>
  </div>

</div><!-- #app -->

<!-- GAME OVER -->
<div class="modal" id="goModal">
  <div class="mbox">
    <div class="mt">GAME<br>OVER</div>
    <div class="msl">Yakuniy ball</div>
    <div class="msn" id="finalScore">0</div>
    <button class="mdbtn prim" id="replayBtn">
      <svg width="17" height="17" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="23,4 23,10 17,10"/><path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"/></svg>
      QAYTA O'YNASH
    </button>
    <button class="mdbtn dang" id="goMenuBtn">
      <svg width="17" height="17" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/></svg>
      BOSH MENYU
    </button>
  </div>
</div>

<!-- HOW TO PLAY -->
<div class="modal" id="howModal">
  <div class="mbox">
    <div class="mt" style="font-size:clamp(20px,6vw,26px)">QOIDALAR</div>
    <ul class="rl">
      <li><span>🎨</span> 10 xil rangdagi figuralar</li>
      <li><span>🖱️</span> Sichqoncha / barmoq bilan suring</li>
      <li><span>🔄</span> R tugmasi yoki 1s bosib aylantiring</li>
      <li><span>⬛</span> To'liq qator/ustun = +100 ball</li>
      <li><span>💎</span> Har bir blok = +10 ball</li>
      <li><span>🔊</span> Ovoz tugmasi bilan o'chirib/yoqing</li>
    </ul>
    <button class="mdbtn prim" id="closeHowBtn">
      <svg width="17" height="17" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20,6 9,17 4,12"/></svg>
      TUSHUNDIM
    </button>
  </div>
</div>

<script>
/* ── TELEGRAM / CLEANUP ── */
(function(){
  try {
    var tg = window.Telegram && window.Telegram.WebApp;
    if(tg){ tg.ready(); tg.expand(); }
  } catch(e){}

  // GitHub Pages yoki boshqa host inyeksiya qilgan elementlarni yashirish
  var keep = {ptc:1, app:1, goModal:1, howModal:1};
  function clean(){
    Array.prototype.forEach.call(document.body.childNodes, function(n){
      if(n.nodeType === 3 /* TEXT_NODE */){ n.remove(); return; }
      if(n.nodeType === 1 && !keep[n.id] && ['SCRIPT','STYLE','LINK'].indexOf(n.tagName) < 0){
        n.style.cssText = 'display:none!important;';
      }
    });
  }
  if(document.readyState === 'loading'){ document.addEventListener('DOMContentLoaded', clean); }
  else { clean(); }
})();

/* ── PARTICLES ── */
(function(){
  var con = document.getElementById('ptc');
  var col = ['#7c5cfc','#00d4ff','#ff6b9d','#fbbf24','#4ade80','#f472b6'];
  for(var i=0; i<26; i++){
    var p = document.createElement('div'); p.className = 'pt';
    var sz = Math.random()*2.2+0.7;
    p.style.cssText = 'left:'+Math.random()*100+'%;width:'+sz+'px;height:'+sz+'px;background:'+col[Math.floor(Math.random()*col.length)]+';animation-duration:'+(Math.random()*15+9)+'s;animation-delay:'+(Math.random()*10)+'s;';
    con.appendChild(p);
  }
})();

/* ── RESPONSIVE BLOCK SIZE ── */
function setBlockSize(){
  var vw = window.innerWidth;
  var vh = window.innerHeight;
  // Tray uchun block o'lchami: viewport ga qarab
  var bk = Math.min(Math.floor(vw * 0.065), Math.floor(vh * 0.04), 30);
  bk = Math.max(bk, 18);
  document.documentElement.style.setProperty('--bk', bk + 'px');
}
window.addEventListener('resize', setBlockSize);
setBlockSize();

/* ── AUDIO ── */
var muted = false, actx = null;
function getCtx(){
  if(!actx){ try{ actx = new (window.AudioContext||window.webkitAudioContext)(); }catch(e){} }
  return actx;
}
function snd(type){
  if(muted) return;
  try{
    var ctx = getCtx(); if(!ctx) return;
    if(ctx.state === 'suspended') ctx.resume();
    var t = ctx.currentTime;
    function mk(f, tp, dur, g, f2){
      var o = ctx.createOscillator(), gn = ctx.createGain();
      o.connect(gn); gn.connect(ctx.destination);
      o.type = tp || 'sine';
      o.frequency.setValueAtTime(f, t);
      if(f2) o.frequency.exponentialRampToValueAtTime(f2, t + dur * 0.8);
      gn.gain.setValueAtTime(g || 0.14, t);
      gn.gain.exponentialRampToValueAtTime(0.001, t + dur);
      o.start(t); o.stop(t + dur);
    }
    if(type==='place') mk(420,'sine',0.14,0.14,650);
    else if(type==='clear'){
      [[0,520],[0.07,660],[0.14,784],[0.22,1046]].forEach(function(d){ mk(d[1],'sine',0.25,0.17); });
    }
    else if(type==='over') mk(260,'sawtooth',0.5,0.17,72);
    else if(type==='rot')  mk(880,'triangle',0.08,0.08,1150);
    else if(type==='ui')   mk(560,'sine',0.12,0.1,840);
  }catch(e){}
}

/* MUTE */
var muteBtn = document.getElementById('muteBtn');
var w1 = document.getElementById('w1'), w2 = document.getElementById('w2');
muteBtn.addEventListener('click', function(){
  muted = !muted;
  w1.style.display = muted ? 'none' : '';
  w2.style.display = muted ? 'none' : '';
  muteBtn.style.opacity = muted ? '0.4' : '1';
  if(!muted) snd('ui');
});

/* ── GAME DATA ── */
var COLORS = ['red','blue','green','purple','orange','yellow','pink','cyan','lime','rose'];
var SHAPES = [
  [[1,1,1,1]],[[1],[1],[1],[1]],
  [[1,1,1,1,1]],[[1],[1],[1],[1],[1]],
  [[1,0],[1,0],[1,1]],[[1,1,1],[1,0,0]],
  [[1,1],[0,1],[0,1]],[[0,0,1],[1,1,1]],
  [[0,1],[0,1],[1,1]],[[1,0,0],[1,1,1]],
  [[1,1],[1,0],[1,0]],[[1,1,1],[0,0,1]],
  [[0,1,0],[1,1,1]],[[1,0],[1,1],[1,0]],
  [[1,1,1],[0,1,0]],[[0,1],[1,1],[0,1]],
  [[0,1,1],[1,1,0]],[[1,0],[1,1],[0,1]],
  [[1,1,0],[0,1,1]],[[0,1],[1,1],[1,0]],
  [[1,1],[1,1]],
  [[1,1],[1,0]],[[1,1],[0,1]],
  [[0,1],[1,1]],[[1,0],[1,1]],
  [[0,1,0],[1,1,1],[0,1,0]],
  [[1,0,1],[1,1,1]],[[1,1,1],[1,0,1]],
  [[1,1],[1,0],[1,1]],[[1,1],[0,1],[1,1]],
  [[1,1,1]],[[1],[1],[1]],
  [[1,0,0],[1,0,0],[1,1,1]],[[1,1,1],[1,0,0],[1,0,0]],
  [[1,1,1],[0,0,1],[0,0,1]],[[0,0,1],[0,0,1],[1,1,1]],
  [[1,0,0],[1,1,0],[1,1,1]],[[0,0,1],[0,1,1],[1,1,1]],
  [[1,1,1],[0,1,1],[0,0,1]],[[1,1,1],[1,1,0],[1,0,0]],
  [[1,1],[1,1],[1,1]],[[1,1,1],[1,1,1]],
  [[1]],[[1,1]],
  [[0,0,1],[0,1,1],[1,1,0]],[[1,0,0],[1,1,0],[0,1,1]],
  [[1,1,0],[0,1,0],[0,1,1]],[[0,1,1],[0,1,0],[1,1,0]],
  [[1,1,1,0],[0,0,1,1]],[[0,1,1,1],[1,1,0,0]],
];

var grid=[], gd=[], score=0, pieces=[];
var dPiece=null, dEl=null, dIdx=-1;
var pRow=-1, pCol=-1, dragging=false;
var lpt=null, pCells=[];

var boardEl  = document.getElementById('board');
var piecesEl = document.getElementById('pieces');
var scoreEl  = document.getElementById('scoreVal');
var goModal  = document.getElementById('goModal');
var finalEl  = document.getElementById('finalScore');

/* ── BOARD ── */
function createBoard(){
  boardEl.innerHTML=''; grid=[]; gd=[];
  for(var i=0; i<100; i++){
    var c = document.createElement('div'); c.className='cell';
    boardEl.appendChild(c); grid.push(c);
    gd.push({f:false, c:null});
  }
}

function rc(i){
  var c=grid[i], d=gd[i];
  c.className = 'cell';
  if(d.f && d.c) c.classList.add('filled', d.c);
}

function clrPv(){ pCells.forEach(function(i){ rc(i); }); pCells=[]; }

function showPv(piece, sr, sc){
  clrPv(); if(!piece) return false;
  var pat=piece.p;
  for(var r=0; r<pat.length; r++)
    for(var c2=0; c2<pat[0].length; c2++)
      if(pat[r][c2]){
        var nr=sr+r, nc=sc+c2;
        if(nr<0||nr>=10||nc<0||nc>=10) return false;
        if(gd[nr*10+nc].f) return false;
      }
  for(var r=0; r<pat.length; r++)
    for(var c2=0; c2<pat[0].length; c2++)
      if(pat[r][c2]){
        var idx=(sr+r)*10+(sc+c2);
        grid[idx].className='cell preview '+piece.c;
        pCells.push(idx);
      }
  return true;
}

/* ── PIECE LOGIC ── */
function rot(piece){
  var p=piece.p, rw=p.length, cl=p[0].length, np=[];
  for(var c2=0; c2<cl; c2++){
    var row=[];
    for(var r=rw-1; r>=0; r--) row.push(p[r][c2]);
    np.push(row);
  }
  return {p:np, c:piece.c};
}
function rndPiece(){
  var s=SHAPES[Math.floor(Math.random()*SHAPES.length)];
  return {p:s.map(function(r){return r.slice();}), c:COLORS[Math.floor(Math.random()*COLORS.length)]};
}
function canP(piece, row, col){
  var pat=piece.p;
  for(var r=0; r<pat.length; r++)
    for(var c2=0; c2<pat[0].length; c2++)
      if(pat[r][c2]){
        var nr=row+r, nc=col+c2;
        if(nr>=10||nc>=10) return false;
        if(gd[nr*10+nc].f) return false;
      }
  return true;
}
function placeP(piece, row, col){
  if(!canP(piece,row,col)) return false;
  var bl=0, pat=piece.p;
  for(var r=0; r<pat.length; r++)
    for(var c2=0; c2<pat[0].length; c2++)
      if(pat[r][c2]){
        var i=(row+r)*10+(col+c2);
        gd[i]={f:true, c:piece.c}; rc(i); bl++;
      }
  score += bl*10; updScore(); snd('place');
  return true;
}
function clearLines(){
  var cl=0, tc=[];
  var inTc=function(i){ return tc.indexOf(i)>=0; };
  for(var r=0; r<10; r++){
    var full=true;
    for(var c2=0; c2<10; c2++) if(!gd[r*10+c2].f){ full=false; break; }
    if(full){ for(var c2=0;c2<10;c2++) if(!inTc(r*10+c2)) tc.push(r*10+c2); cl++; }
  }
  for(var c2=0; c2<10; c2++){
    var full=true;
    for(var r=0; r<10; r++) if(!gd[r*10+c2].f){ full=false; break; }
    if(full){ for(var r=0;r<10;r++) if(!inTc(r*10+c2)) tc.push(r*10+c2); cl++; }
  }
  if(tc.length){
    tc.forEach(function(i){ grid[i].className='cell clearing'; });
    setTimeout(function(){
      tc.forEach(function(i){ gd[i]={f:false,c:null}; rc(i); });
    }, 300);
    score += cl*100; updScore(); snd('clear'); popup('+'+cl*100);
  }
}
function popup(txt){
  var el=document.createElement('div'); el.className='spop'; el.textContent=txt;
  var r=boardEl.getBoundingClientRect();
  el.style.left=(r.left+r.width/2-35)+'px';
  el.style.top=(r.top+r.height/2-15)+'px';
  document.body.appendChild(el);
  setTimeout(function(){ el.remove(); }, 1000);
}
function isGameOver(){
  return pieces.every(function(piece){
    for(var r=0; r<=10-piece.p.length; r++)
      for(var c2=0; c2<=10-piece.p[0].length; c2++)
        if(canP(piece,r,c2)) return false;
    return true;
  });
}

/* ── DRAG ── */
function startD(piece, el, idx, cx, cy){
  dPiece=JSON.parse(JSON.stringify(piece));
  dEl=el; dIdx=idx; dragging=true;
  el.classList.add('dragging'); pRow=-1; pCol=-1; clrPv();
}
function moveD(cx, cy){
  if(!dPiece||!dragging) return;
  var rect=boardEl.getBoundingClientRect();
  if(cx>=rect.left && cx<=rect.right && cy>=rect.top && cy<=rect.bottom){
    var cs=rect.width/10;
    var cc=Math.floor((cx-rect.left)/cs), rr=Math.floor((cy-rect.top)/cs);
    var sr=Math.max(0,Math.min(9-dPiece.p.length+1,   rr-Math.floor(dPiece.p.length/2)));
    var sc=Math.max(0,Math.min(9-dPiece.p[0].length+1,cc-Math.floor(dPiece.p[0].length/2)));
    if(pRow!==sr||pCol!==sc){ pRow=sr; pCol=sc; showPv(dPiece,sr,sc); }
  } else { clrPv(); pRow=-1; pCol=-1; }
}
function endD(){
  if(!dPiece||!dragging) return;
  if(pRow>=0 && pCol>=0 && canP(dPiece,pRow,pCol)){
    clrPv();
    if(placeP(dPiece,pRow,pCol)){
      clearLines();
      var arr=Array.from(piecesEl.children);
      if(arr[dIdx]){ piecesEl.removeChild(arr[dIdx]); pieces.splice(dIdx,1); }
      while(pieces.length<2) pieces.push(rndPiece());
      refPieces();
      setTimeout(function(){ if(isGameOver()) doGameOver(); }, 360);
    }
  }
  if(dEl) dEl.classList.remove('dragging');
  dPiece=null; dEl=null; dragging=false; clrPv(); pRow=-1; pCol=-1;
}
function rotIdx(i){
  if(i>=0&&i<pieces.length){ pieces[i]=rot(pieces[i]); refPieces(); snd('rot'); }
}

/* Mouse */
document.addEventListener('mousemove', function(e){ moveD(e.clientX, e.clientY); });
document.addEventListener('mouseup',   function(){ endD(); });

/* Touch — faqat board va pieces da touch-action none */
boardEl.style.touchAction   = 'none';
piecesEl.style.touchAction  = 'none';

document.addEventListener('touchmove', function(e){
  if(dragging){ e.preventDefault(); }
  if(lpt){ clearTimeout(lpt); lpt=null; }
  if(e.touches.length) moveD(e.touches[0].clientX, e.touches[0].clientY);
}, {passive:false});

document.addEventListener('touchend', function(e){
  e.preventDefault();
  if(lpt){ clearTimeout(lpt); lpt=null; }
  endD();
}, {passive:false});

document.addEventListener('touchcancel', function(){
  if(lpt){ clearTimeout(lpt); lpt=null; }
  endD();
});

/* Keyboard */
document.addEventListener('keydown', function(e){
  if((e.key==='r'||e.key==='R') && pieces.length && !dragging) rotIdx(0);
});

/* ── PIECES TRAY ── */
function mkPiece(piece, idx){
  var div=document.createElement('div'); div.className='piece';
  var cols=piece.p[0].length;
  div.style.gridTemplateColumns='repeat('+cols+',1fr)';
  // Block o'lchami
  var bk = parseInt(getComputedStyle(document.documentElement).getPropertyValue('--bk')) || 24;
  piece.p.forEach(function(row){
    row.forEach(function(v){
      var b=document.createElement('div');
      b.className='block '+(v ? piece.c : 'empty');
      b.style.width=bk+'px'; b.style.height=bk+'px';
      div.appendChild(b);
    });
  });

  div.addEventListener('mousedown', function(e){
    e.preventDefault(); startD(piece,div,idx,e.clientX,e.clientY);
  });
  div.addEventListener('touchstart', function(e){
    e.preventDefault();
    lpt = setTimeout(function(){ rotIdx(idx); lpt=null; }, 800);
    startD(piece,div,idx,e.touches[0].clientX,e.touches[0].clientY);
  }, {passive:false});
  return div;
}

function refPieces(){
  piecesEl.innerHTML='';
  pieces.forEach(function(p,i){ piecesEl.appendChild(mkPiece(p,i)); });
}
function genPieces(){
  pieces=[]; for(var i=0;i<2;i++) pieces.push(rndPiece()); refPieces();
}

/* ── SCORE / STATE ── */
function updScore(){ scoreEl.textContent=score; }
function doGameOver(){ snd('over'); finalEl.textContent=score; goModal.style.display='flex'; }
function restart(){
  score=0; updScore();
  for(var i=0;i<100;i++){ gd[i]={f:false,c:null}; rc(i); }
  pCells=[]; genPieces(); goModal.style.display='none';
}

/* ── SCREENS ── */
function showMenu(){
  document.getElementById('gameScr').classList.remove('active');
  document.getElementById('menuScr').classList.add('active');
}
function showGame(){
  document.getElementById('menuScr').classList.remove('active');
  document.getElementById('gameScr').classList.add('active');
}

/* ── BUTTONS ── */
document.getElementById('startBtn').addEventListener('click',  function(){ snd('ui'); showGame(); restart(); });
document.getElementById('howBtn').addEventListener('click',    function(){ snd('ui'); document.getElementById('howModal').style.display='flex'; });
document.getElementById('closeHowBtn').addEventListener('click',function(){ snd('ui'); document.getElementById('howModal').style.display='none'; });
document.getElementById('replayBtn').addEventListener('click', function(){ snd('ui'); goModal.style.display='none'; restart(); });
document.getElementById('goMenuBtn').addEventListener('click', function(){ snd('ui'); goModal.style.display='none'; showMenu(); });
document.getElementById('menuBtn').addEventListener('click',   function(){ snd('ui'); showMenu(); });

/* ── INIT ── */
createBoard();
genPieces();
</script>
</body>
</html>
