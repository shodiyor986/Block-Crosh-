<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0,user-scalable=no,viewport-fit=cover">
<title>Block Crush</title>
<link href="https://fonts.googleapis.com/css2?family=Exo+2:wght@400;700;900&display=swap" rel="stylesheet">
<style>
/* ── RESET & LOCK ─────────────────────────────── */
*{margin:0;padding:0;box-sizing:border-box;user-select:none;-webkit-tap-highlight-color:transparent;touch-action:none;}

/* Scroll va harakat butunlay bloklangan */
html,body{
  position:fixed;top:0;left:0;
  width:100%;height:100%;
  overflow:hidden;
  -webkit-overflow-scrolling:none;
  overscroll-behavior:none;
}

:root{
  --bg:#0d0d1a;
  --border:rgba(255,255,255,0.11);
  --glow:0 0 40px rgba(124,92,252,0.3);
  --ce:rgba(255,255,255,0.04);
  --cb:rgba(255,255,255,0.07);
  --tx:#e8e8ff;
  --tx2:rgba(232,232,255,0.42);
  /* Dinamik o'lchamlar CSS variables orqali JS dan to'ldiriladi */
  --cell:30px;
  --bpad:10px;
  --bsize:320px;
}

body{
  background:var(--bg);
  background-image:
    radial-gradient(ellipse at 15% 15%,rgba(124,92,252,0.16) 0%,transparent 52%),
    radial-gradient(ellipse at 85% 85%,rgba(0,212,255,0.11) 0%,transparent 52%);
  font-family:'Exo 2',sans-serif;
  display:flex;flex-direction:column;
  align-items:center;justify-content:center;
}

/* ── PARTICLES ─────────────────────────────────── */
#ptc{position:fixed;inset:0;pointer-events:none;z-index:0;overflow:hidden;}
.pt{position:absolute;border-radius:50%;opacity:0;animation:fup linear infinite;}
@keyframes fup{
  0%  {opacity:0;transform:translateY(100vh) scale(0);}
  10% {opacity:0.6;}
  90% {opacity:0.15;}
  100%{opacity:0;transform:translateY(-40px) scale(1.8);}
}

/* ── SCREENS ────────────────────────────────────── */
.screen{
  position:relative;z-index:10;
  display:flex;flex-direction:column;align-items:center;
  width:100%;max-width:520px;
  padding:0 12px;
}

/* ── MENU ───────────────────────────────────────── */
#menuScreen .inner{
  width:100%;
  background:linear-gradient(145deg,rgba(124,92,252,0.12),rgba(0,212,255,0.06));
  backdrop-filter:blur(22px);-webkit-backdrop-filter:blur(22px);
  border:1px solid var(--border);
  border-radius:clamp(20px,5vw,36px);
  padding:clamp(28px,6vw,52px) clamp(18px,5vw,32px) clamp(24px,5vw,44px);
  text-align:center;
  box-shadow:var(--glow),inset 0 1px 0 rgba(255,255,255,0.09);
}
.logo-main{
  font-size:clamp(40px,12vw,68px);font-weight:900;letter-spacing:-2px;
  background:linear-gradient(135deg,#a78bfa 0%,#7c5cfc 40%,#00d4ff 100%);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
  line-height:0.92;display:block;
}
.logo-sub{font-size:clamp(9px,2.5vw,12px);color:var(--tx2);letter-spacing:5px;text-transform:uppercase;margin:8px 0 clamp(22px,5vw,36px);display:block;}
.mbtn{
  display:flex;align-items:center;justify-content:center;gap:12px;
  width:100%;padding:clamp(13px,3.5vw,18px) 20px;margin:8px 0;
  border:none;border-radius:clamp(12px,3vw,18px);cursor:pointer;
  font-family:'Exo 2',sans-serif;font-size:clamp(14px,4vw,17px);font-weight:800;
  letter-spacing:2px;text-transform:uppercase;transition:all 0.18s;
}
.mbtn.play{background:linear-gradient(135deg,#7c5cfc,#4f35c8);color:#fff;box-shadow:0 8px 30px rgba(124,92,252,0.5),inset 0 1px 0 rgba(255,255,255,0.22);}
.mbtn.play:active{transform:scale(0.97);}
.mbtn.info{background:rgba(255,255,255,0.07);color:var(--tx);border:1px solid var(--border);}
.mbtn.info:active{background:rgba(255,255,255,0.13);}
.bico{width:clamp(30px,8vw,38px);height:clamp(30px,8vw,38px);border-radius:10px;background:rgba(255,255,255,0.15);display:flex;align-items:center;justify-content:center;flex-shrink:0;}
.bico svg{width:18px;height:18px;}

/* ── GAME ───────────────────────────────────────── */
#gameScreen{display:none;}

/* TOPBAR */
.topbar{
  display:flex;align-items:stretch;gap:8px;
  width:100%;margin-bottom:clamp(6px,2vw,12px);
}
.score-card{
  flex:1;background:rgba(255,255,255,0.06);
  border:1px solid var(--border);border-radius:clamp(12px,3vw,18px);
  padding:clamp(8px,2vw,13px) clamp(12px,3vw,18px);
  display:flex;flex-direction:column;justify-content:center;
}
.slabel{font-size:clamp(8px,2vw,10px);color:var(--tx2);letter-spacing:3px;text-transform:uppercase;margin-bottom:1px;}
.sval{
  font-size:clamp(22px,6vw,34px);font-weight:900;line-height:1;
  background:linear-gradient(135deg,#fff,#a78bfa);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
}
.ibtn{
  width:clamp(42px,11vw,50px);height:clamp(42px,11vw,50px);
  border-radius:clamp(10px,3vw,15px);border:none;cursor:pointer;
  background:rgba(255,255,255,0.07);border:1px solid var(--border);
  display:flex;align-items:center;justify-content:center;transition:all 0.18s;flex-shrink:0;
}
.ibtn:active{transform:scale(0.92);background:rgba(255,255,255,0.14);}
.ibtn svg{width:20px;height:20px;stroke:var(--tx);}

/* BOARD WRAPPER */
.board-wrap{
  width:100%;
  background:rgba(255,255,255,0.03);
  border:1px solid var(--border);
  border-radius:clamp(14px,4vw,26px);
  padding:clamp(7px,2vw,11px);
  box-shadow:var(--glow),inset 0 1px 0 rgba(255,255,255,0.05);
  margin-bottom:clamp(6px,2vw,12px);
}
#board{
  width:100%;aspect-ratio:1/1;
  display:grid;
  grid-template-columns:repeat(10,1fr);
  grid-template-rows:repeat(10,1fr);
  gap:clamp(2px,0.5vw,3px);
  border-radius:clamp(10px,3vw,18px);
  overflow:hidden;
}
.cell{
  background:var(--ce);border-radius:clamp(3px,1vw,6px);
  border:1px solid var(--cb);aspect-ratio:1;
}
.cell.filled{border:none;border-radius:clamp(3px,1vw,6px);position:relative;}
.cell.filled::after{content:'';position:absolute;inset:0;border-radius:inherit;background:linear-gradient(135deg,rgba(255,255,255,0.26) 0%,transparent 55%);pointer-events:none;}

/* COLORS */
.cell.filled.red    {background:linear-gradient(135deg,#ff6b6b,#b91c1c);box-shadow:0 0 8px rgba(255,107,107,0.5);}
.cell.filled.blue   {background:linear-gradient(135deg,#60a5fa,#1d4ed8);box-shadow:0 0 8px rgba(96,165,250,0.5);}
.cell.filled.green  {background:linear-gradient(135deg,#4ade80,#15803d);box-shadow:0 0 8px rgba(74,222,128,0.5);}
.cell.filled.purple {background:linear-gradient(135deg,#c084fc,#7e22ce);box-shadow:0 0 8px rgba(192,132,252,0.5);}
.cell.filled.orange {background:linear-gradient(135deg,#fb923c,#c2410c);box-shadow:0 0 8px rgba(251,146,60,0.5);}
.cell.filled.yellow {background:linear-gradient(135deg,#fde047,#a16207);box-shadow:0 0 8px rgba(253,224,71,0.5);}
.cell.filled.pink   {background:linear-gradient(135deg,#f472b6,#be185d);box-shadow:0 0 8px rgba(244,114,182,0.5);}
.cell.filled.cyan   {background:linear-gradient(135deg,#22d3ee,#0e7490);box-shadow:0 0 8px rgba(34,211,238,0.5);}
.cell.filled.lime   {background:linear-gradient(135deg,#a3e635,#3f6212);box-shadow:0 0 8px rgba(163,230,53,0.5);}
.cell.filled.rose   {background:linear-gradient(135deg,#fb7185,#9f1239);box-shadow:0 0 8px rgba(251,113,133,0.5);}

/* PREVIEW */
.cell.preview{border:2px solid rgba(255,255,255,0.4)!important;animation:pp 0.75s ease-in-out infinite;}
.cell.preview.red    {background:rgba(255,107,107,0.2)!important;border-color:#ff6b6b!important;}
.cell.preview.blue   {background:rgba(96,165,250,0.2)!important; border-color:#60a5fa!important;}
.cell.preview.green  {background:rgba(74,222,128,0.2)!important; border-color:#4ade80!important;}
.cell.preview.purple {background:rgba(192,132,252,0.2)!important;border-color:#c084fc!important;}
.cell.preview.orange {background:rgba(251,146,60,0.2)!important; border-color:#fb923c!important;}
.cell.preview.yellow {background:rgba(253,224,71,0.2)!important; border-color:#fde047!important;}
.cell.preview.pink   {background:rgba(244,114,182,0.2)!important;border-color:#f472b6!important;}
.cell.preview.cyan   {background:rgba(34,211,238,0.2)!important; border-color:#22d3ee!important;}
.cell.preview.lime   {background:rgba(163,230,53,0.2)!important; border-color:#a3e635!important;}
.cell.preview.rose   {background:rgba(251,113,133,0.2)!important;border-color:#fb7185!important;}
@keyframes pp{0%,100%{opacity:0.5;}50%{opacity:1;}}
@keyframes pop{0%{transform:scale(1);}45%{transform:scale(1.15);}100%{transform:scale(1);}}
.cell.filled{animation:pop 0.17s ease;}
@keyframes fc{0%{background:rgba(255,255,255,0.92)!important;}100%{background:transparent!important;}}
.cell.clearing{animation:fc 0.3s ease forwards!important;border:none!important;}

/* PIECES TRAY */
.pieces-tray{
  width:100%;
  background:rgba(255,255,255,0.04);border:1px solid var(--border);
  border-radius:clamp(14px,4vw,24px);
  padding:clamp(12px,3vw,18px) clamp(10px,2.5vw,16px);
  display:flex;justify-content:center;gap:clamp(12px,3vw,20px);
  flex-wrap:nowrap;align-items:center;
  box-shadow:inset 0 1px 0 rgba(255,255,255,0.04);
  overflow:hidden;
}
.piece{
  display:grid;gap:clamp(2px,0.5vw,3px);
  background:rgba(255,255,255,0.08);
  padding:clamp(7px,2vw,11px);
  border-radius:clamp(10px,2.5vw,14px);
  border:1px solid var(--border);cursor:grab;
  transition:transform 0.16s,box-shadow 0.16s,background 0.16s;
  position:relative;flex-shrink:0;
}
.piece:active,.piece.dragging{opacity:0.22;cursor:grabbing;transform:scale(0.94);}
.block{
  width:var(--bk);height:var(--bk);
  border-radius:clamp(3px,1vw,5px);position:relative;
}
.block::after{content:'';position:absolute;inset:0;border-radius:inherit;background:linear-gradient(135deg,rgba(255,255,255,0.28) 0%,transparent 55%);}
.block.red    {background:linear-gradient(135deg,#ff6b6b,#b91c1c);}
.block.blue   {background:linear-gradient(135deg,#60a5fa,#1d4ed8);}
.block.green  {background:linear-gradient(135deg,#4ade80,#15803d);}
.block.purple {background:linear-gradient(135deg,#c084fc,#7e22ce);}
.block.orange {background:linear-gradient(135deg,#fb923c,#c2410c);}
.block.yellow {background:linear-gradient(135deg,#fde047,#a16207);}
.block.pink   {background:linear-gradient(135deg,#f472b6,#be185d);}
.block.cyan   {background:linear-gradient(135deg,#22d3ee,#0e7490);}
.block.lime   {background:linear-gradient(135deg,#a3e635,#3f6212);}
.block.rose   {background:linear-gradient(135deg,#fb7185,#9f1239);}
.block.empty  {visibility:hidden;}

.rh{
  position:absolute;top:-26px;left:50%;transform:translateX(-50%);
  background:rgba(124,92,252,0.9);color:#fff;
  font-size:10px;font-weight:800;padding:3px 9px;
  border-radius:20px;border:1px solid rgba(255,255,255,0.18);
  opacity:0;pointer-events:none;white-space:nowrap;
  letter-spacing:2px;text-transform:uppercase;
}

/* SCORE POPUP */
.spop{
  position:fixed;pointer-events:none;z-index:999;
  font-family:'Exo 2',sans-serif;font-size:clamp(20px,5vw,26px);font-weight:900;
  color:#fff;text-shadow:0 0 18px rgba(124,92,252,0.9);
  animation:sf 0.95s ease forwards;
}
@keyframes sf{0%{opacity:1;transform:translateY(0) scale(1);}100%{opacity:0;transform:translateY(-80px) scale(1.4);}}

/* ── MODALS ─────────────────────────────────────── */
.modal{
  position:fixed;inset:0;
  background:rgba(5,5,18,0.87);
  backdrop-filter:blur(16px);-webkit-backdrop-filter:blur(16px);
  display:none;justify-content:center;align-items:center;
  z-index:2000;padding:16px;
}
.mbox{
  background:linear-gradient(145deg,rgba(124,92,252,0.13),rgba(0,212,255,0.06));
  border:1px solid var(--border);
  border-radius:clamp(18px,5vw,30px);
  padding:clamp(24px,6vw,40px) clamp(18px,5vw,28px);
  text-align:center;width:min(360px,95%);
  box-shadow:0 36px 72px rgba(0,0,0,0.6),inset 0 1px 0 rgba(255,255,255,0.09);
}
.mt{
  font-size:clamp(28px,8vw,38px);font-weight:900;margin-bottom:6px;
  background:linear-gradient(135deg,#a78bfa,#00d4ff);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
}
.msl{color:var(--tx2);font-size:clamp(9px,2.5vw,11px);letter-spacing:4px;text-transform:uppercase;margin:18px 0 4px;}
.msn{
  font-size:clamp(44px,12vw,62px);font-weight:900;line-height:1;margin-bottom:22px;
  background:linear-gradient(135deg,#fff,#a78bfa);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
}
.mdbtn{
  display:flex;align-items:center;justify-content:center;gap:9px;
  width:100%;padding:clamp(13px,3.5vw,16px);margin:7px 0;
  border:none;border-radius:clamp(11px,3vw,15px);cursor:pointer;
  font-family:'Exo 2',sans-serif;font-size:clamp(13px,3.5vw,16px);font-weight:800;
  text-transform:uppercase;letter-spacing:2px;transition:all 0.18s;
}
.mdbtn.prim{background:linear-gradient(135deg,#7c5cfc,#4f35c8);color:#fff;box-shadow:0 8px 28px rgba(124,92,252,0.45);}
.mdbtn.prim:active{transform:scale(0.97);}
.mdbtn.dang{background:rgba(255,107,107,0.1);color:#ff6b6b;border:1px solid rgba(255,107,107,0.26);}
.mdbtn.dang:active{background:rgba(255,107,107,0.2);}

.rl{text-align:left;margin:16px 0;background:rgba(255,255,255,0.04);border:1px solid var(--border);border-radius:14px;padding:12px 16px;}
.rl li{color:var(--tx);font-size:clamp(12px,3.5vw,14px);padding:7px 0;border-bottom:1px solid rgba(255,255,255,0.06);list-style:none;display:flex;align-items:center;gap:11px;}
.rl li:last-child{border-bottom:none;}
.ri{font-size:18px;flex-shrink:0;}
</style>
</head>
<body>

<!-- Particles -->
<div id="ptc"></div>

<!-- MENU -->
<div class="screen" id="menuScreen">
  <div class="inner">
    <span class="logo-main">BLOCK<br>CRUSH</span>
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
<div class="screen" id="gameScreen">
  <div class="topbar">
    <div class="score-card">
      <div class="slabel">Ball</div>
      <div class="sval" id="scoreVal">0</div>
    </div>
    <button class="ibtn" id="muteBtn">
      <svg id="muteIcon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
        <polygon points="11,5 6,9 2,9 2,15 6,15 11,19"/>
        <path id="w1" d="M15.54 8.46a5 5 0 0 1 0 7.07"/>
        <path id="w2" d="M19.07 4.93a10 10 0 0 1 0 14.14"/>
      </svg>
    </button>
    <button class="ibtn" id="menuBtn">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
        <line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="18" x2="21" y2="18"/>
      </svg>
    </button>
  </div>
  <div class="board-wrap"><div id="board"></div></div>
  <div class="pieces-tray" id="pieces"></div>
</div>

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
      <li><span class="ri">🎨</span> 10 xil rangdagi figuralar</li>
      <li><span class="ri">🖱️</span> Sichqoncha / barmoq bilan suring</li>
      <li><span class="ri">🔄</span> R tugmasi yoki 1s bosib aylantiring</li>
      <li><span class="ri">⬛</span> To'liq qator/ustun = +100 ball</li>
      <li><span class="ri">💎</span> Har bir blok = +10 ball</li>
      <li><span class="ri">🔊</span> Ovoz tugmasi bilan o'chiring/yoqing</li>
    </ul>
    <button class="mdbtn prim" id="closeHowBtn">
      <svg width="17" height="17" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20,6 9,17 4,12"/></svg>
      TUSHUNDIM
    </button>
  </div>
</div>

<script>
/* ── TELEGRAM WEBAPP INIT ──────────────────────── */
(function(){
  try{
    const tg=window.Telegram&&window.Telegram.WebApp;
    if(tg){tg.ready();tg.expand();tg.disableVerticalSwipes&&tg.disableVerticalSwipes();}
  }catch(e){}
  // Github Pages / raw host da inyeksiya qilingan elementlarni yashirish
  // Barcha body children ni tekshirib, bizning div lar emas bo'lsa yashiramiz
  function clean(){
    const keep=new Set(['ptc','menuScreen','gameScreen','goModal','howModal']);
    document.body.childNodes.forEach(n=>{
      if(n.nodeType===3){n.remove();}// text node
      else if(n.nodeType===1&&!keep.has(n.id)&&!['SCRIPT','STYLE','LINK','META'].includes(n.tagName)){
        n.style.cssText='display:none!important;visibility:hidden!important;height:0!important;overflow:hidden!important;';
      }
    });
  }
  if(document.readyState==='loading'){document.addEventListener('DOMContentLoaded',clean);}
  else{clean();}
})();

/* ── PARTICLES ──────────────────────────────────── */
(function(){
  const c=document.getElementById('ptc');
  const col=['#7c5cfc','#00d4ff','#ff6b9d','#fbbf24','#4ade80','#f472b6'];
  for(let i=0;i<28;i++){
    const p=document.createElement('div');p.className='pt';
    const sz=Math.random()*2.5+0.8;
    p.style.cssText=`left:${Math.random()*100}%;width:${sz}px;height:${sz}px;background:${col[~~(Math.random()*col.length)]};animation-duration:${Math.random()*16+9}s;animation-delay:${Math.random()*11}s;`;
    c.appendChild(p);
  }
})();

/* ── AUDIO ──────────────────────────────────────── */
let muted=false,actx=null;
function ac(){if(!actx){try{actx=new(window.AudioContext||window.webkitAudioContext)();}catch(e){}}return actx;}
function snd(type){
  if(muted)return;
  try{
    const ctx=ac();if(!ctx)return;
    if(ctx.state==='suspended')ctx.resume();
    const t=ctx.currentTime;
    const mk=(f,tp,dur,g,f2)=>{
      const o=ctx.createOscillator(),gn=ctx.createGain();
      o.connect(gn);gn.connect(ctx.destination);
      o.type=tp||'sine';o.frequency.setValueAtTime(f,t);
      if(f2)o.frequency.exponentialRampToValueAtTime(f2,t+dur*0.8);
      gn.gain.setValueAtTime(g||0.14,t);
      gn.gain.exponentialRampToValueAtTime(0.001,t+dur);
      o.start(t);o.stop(t+dur);
    };
    if(type==='place') mk(420,'sine',0.14,0.15,660);
    else if(type==='clear'){[[0,523],[0.06,659],[0.13,784],[0.21,1047]].forEach(([d,f])=>{mk(f,'sine',0.26,0.18);});}
    else if(type==='over') mk(260,'sawtooth',0.55,0.18,72);
    else if(type==='rot')  mk(880,'triangle',0.08,0.08,1150);
    else if(type==='ui')   mk(560,'sine',0.13,0.1,840);
  }catch(e){}
}

/* MUTE TOGGLE */
const muteBtn=document.getElementById('muteBtn');
const w1=document.getElementById('w1'),w2=document.getElementById('w2');
muteBtn.addEventListener('click',()=>{
  muted=!muted;
  w1.style.display=muted?'none':'';
  w2.style.display=muted?'none':'';
  muteBtn.style.opacity=muted?'0.4':'1';
  if(!muted)snd('ui');
});

/* ── RESPONSIVE SIZING ──────────────────────────── */
function calcSizes(){
  const vw=window.innerWidth,vh=window.innerHeight;
  // Kichik qurilmalarda ham sig'ishi uchun
  // Board = viewport kichik tomonining ~62%
  const topbarH=Math.min(56,vh*0.08);
  const trayH=Math.min(110,vh*0.14);
  const gap=Math.min(10,vh*0.012);
  const available=vh-topbarH-trayH-gap*4-24; // padding
  const maxBoard=Math.min(vw-24,available,480);
  document.getElementById('board').parentElement.style.width=maxBoard+'px';
  document.getElementById('board').parentElement.style.margin='0 auto '+gap+'px';
  // Block size for pieces tray
  const bk=Math.min(28,vw*0.065,vh*0.038);
  document.documentElement.style.setProperty('--bk',bk+'px');
}
window.addEventListener('resize',calcSizes);
calcSizes();

/* ── GAME DATA ──────────────────────────────────── */
const COLORS=['red','blue','green','purple','orange','yellow','pink','cyan','lime','rose'];
const SHAPES=[
  {p:[[1,1,1,1]]},{p:[[1],[1],[1],[1]]},
  {p:[[1,1,1,1,1]]},{p:[[1],[1],[1],[1],[1]]},
  {p:[[1,0],[1,0],[1,1]]},{p:[[1,1,1],[1,0,0]]},
  {p:[[1,1],[0,1],[0,1]]},{p:[[0,0,1],[1,1,1]]},
  {p:[[0,1],[0,1],[1,1]]},{p:[[1,0,0],[1,1,1]]},
  {p:[[1,1],[1,0],[1,0]]},{p:[[1,1,1],[0,0,1]]},
  {p:[[0,1,0],[1,1,1]]},{p:[[1,0],[1,1],[1,0]]},
  {p:[[1,1,1],[0,1,0]]},{p:[[0,1],[1,1],[0,1]]},
  {p:[[0,1,1],[1,1,0]]},{p:[[1,0],[1,1],[0,1]]},
  {p:[[1,1,0],[0,1,1]]},{p:[[0,1],[1,1],[1,0]]},
  {p:[[1,1],[1,1]]},
  {p:[[1,1],[1,0]]},{p:[[1,1],[0,1]]},
  {p:[[0,1],[1,1]]},{p:[[1,0],[1,1]]},
  {p:[[0,1,0],[1,1,1],[0,1,0]]},
  {p:[[1,0,1],[1,1,1]]},{p:[[1,1,1],[1,0,1]]},
  {p:[[1,1],[1,0],[1,1]]},{p:[[1,1],[0,1],[1,1]]},
  {p:[[1,1,1]]},{p:[[1],[1],[1]]},
  {p:[[1,0,0],[1,0,0],[1,1,1]]},{p:[[1,1,1],[1,0,0],[1,0,0]]},
  {p:[[1,1,1],[0,0,1],[0,0,1]]},{p:[[0,0,1],[0,0,1],[1,1,1]]},
  {p:[[1,0,0],[1,1,0],[1,1,1]]},{p:[[0,0,1],[0,1,1],[1,1,1]]},
  {p:[[1,1,1],[0,1,1],[0,0,1]]},{p:[[1,1,1],[1,1,0],[1,0,0]]},
  {p:[[1,1],[1,1],[1,1]]},{p:[[1,1,1],[1,1,1]]},
  {p:[[1]]},{p:[[1,1]]},
  {p:[[0,0,1],[0,1,1],[1,1,0]]},{p:[[1,0,0],[1,1,0],[0,1,1]]},
  {p:[[1,1,0],[0,1,0],[0,1,1]]},{p:[[0,1,1],[0,1,0],[1,1,0]]},
  {p:[[1,1,1,0],[0,0,1,1]]},{p:[[0,1,1,1],[1,1,0,0]]},
];

let grid=[],gd=[],score=0,pieces=[];
let dPiece=null,dEl=null,dIdx=-1;
let pRow=-1,pCol=-1,dragging=false;
let lpt=null,pCells=[];

const boardEl=document.getElementById('board');
const piecesEl=document.getElementById('pieces');
const scoreEl=document.getElementById('scoreVal');
const goModal=document.getElementById('goModal');
const finalEl=document.getElementById('finalScore');

/* ── BOARD ──────────────────────────────────────── */
function createBoard(){
  boardEl.innerHTML='';grid=[];gd=[];
  for(let i=0;i<100;i++){
    const c=document.createElement('div');c.className='cell';
    c.dataset.i=i;boardEl.appendChild(c);
    grid.push(c);gd.push({f:false,c:null});
  }
}

function rc(i){
  const c=grid[i],d=gd[i];
  c.className='cell';
  if(d.f&&d.c)c.classList.add('filled',d.c);
}

function clrPv(){pCells.forEach(i=>rc(i));pCells=[];}

function showPv(piece,sr,sc){
  clrPv();if(!piece)return false;
  for(let r=0;r<piece.p.length;r++)
    for(let c=0;c<piece.p[0].length;c++)
      if(piece.p[r][c]){
        const nr=sr+r,nc=sc+c;
        if(nr<0||nr>=10||nc<0||nc>=10)return false;
        if(gd[nr*10+nc].f)return false;
      }
  for(let r=0;r<piece.p.length;r++)
    for(let c=0;c<piece.p[0].length;c++)
      if(piece.p[r][c]){
        const i=(sr+r)*10+(sc+c);
        grid[i].className='cell preview '+piece.c;
        pCells.push(i);
      }
  return true;
}

/* ── PIECE LOGIC ────────────────────────────────── */
function rot(piece){
  const rw=piece.p.length,cl=piece.p[0].length,np=[];
  for(let c=0;c<cl;c++){const row=[];for(let r=rw-1;r>=0;r--)row.push(piece.p[r][c]);np.push(row);}
  return{p:np,c:piece.c};
}
function rnd(){
  const s=SHAPES[~~(Math.random()*SHAPES.length)];
  return{p:s.p.map(r=>[...r]),c:COLORS[~~(Math.random()*COLORS.length)]};
}
function canP(piece,row,col){
  for(let r=0;r<piece.p.length;r++)
    for(let c=0;c<piece.p[0].length;c++)
      if(piece.p[r][c]){
        const nr=row+r,nc=col+c;
        if(nr>=10||nc>=10)return false;
        if(gd[nr*10+nc].f)return false;
      }
  return true;
}
function placeP(piece,row,col){
  if(!canP(piece,row,col))return false;
  let bl=0;
  for(let r=0;r<piece.p.length;r++)
    for(let c=0;c<piece.p[0].length;c++)
      if(piece.p[r][c]){
        const i=(row+r)*10+(col+c);
        gd[i]={f:true,c:piece.c};rc(i);bl++;
      }
  score+=bl*10;updScore();snd('place');
  return true;
}
function clearLines(){
  let cl=0;const tc=new Set();
  for(let r=0;r<10;r++)
    if([...Array(10)].every((_,c)=>gd[r*10+c].f))
      {for(let c=0;c<10;c++)tc.add(r*10+c);cl++;}
  for(let c=0;c<10;c++)
    if([...Array(10)].every((_,r)=>gd[r*10+c].f))
      {for(let r=0;r<10;r++)tc.add(r*10+c);cl++;}
  if(tc.size){
    tc.forEach(i=>grid[i].className='cell clearing');
    setTimeout(()=>{tc.forEach(i=>{gd[i]={f:false,c:null};rc(i);});},300);
    score+=cl*100;updScore();snd('clear');
    popup('+'+cl*100);
  }
}
function popup(t){
  const el=document.createElement('div');el.className='spop';el.textContent=t;
  const r=boardEl.getBoundingClientRect();
  el.style.cssText=`left:${r.left+r.width/2-35}px;top:${r.top+r.height/2-15}px;`;
  document.body.appendChild(el);setTimeout(()=>el.remove(),1000);
}
function gameOver(){
  return pieces.every(piece=>{
    for(let r=0;r<=10-piece.p.length;r++)
      for(let c=0;c<=10-piece.p[0].length;c++)
        if(canP(piece,r,c))return false;
    return true;
  });
}

/* ── DRAG ───────────────────────────────────────── */
function startD(piece,el,idx,cx,cy){
  dPiece=JSON.parse(JSON.stringify(piece));
  dEl=el;dIdx=idx;dragging=true;
  el.classList.add('dragging');pRow=-1;pCol=-1;clrPv();
}
function moveD(cx,cy){
  if(!dPiece||!dragging)return;
  const rect=boardEl.getBoundingClientRect();
  if(cx>=rect.left&&cx<=rect.right&&cy>=rect.top&&cy<=rect.bottom){
    const cs=rect.width/10;
    const c=~~((cx-rect.left)/cs),r=~~((cy-rect.top)/cs);
    const sr=Math.max(0,Math.min(9-dPiece.p.length+1,r-~~(dPiece.p.length/2)));
    const sc=Math.max(0,Math.min(9-dPiece.p[0].length+1,c-~~(dPiece.p[0].length/2)));
    if(pRow!==sr||pCol!==sc){pRow=sr;pCol=sc;showPv(dPiece,sr,sc);}
  }else{clrPv();pRow=-1;pCol=-1;}
}
function endD(){
  if(!dPiece||!dragging)return;
  if(pRow>=0&&pCol>=0&&canP(dPiece,pRow,pCol)){
    clrPv();
    if(placeP(dPiece,pRow,pCol)){
      clearLines();
      const arr=Array.from(piecesEl.children);
      if(arr[dIdx]){piecesEl.removeChild(arr[dIdx]);pieces.splice(dIdx,1);}
      while(pieces.length<2)pieces.push(rnd());
      refPieces();
      setTimeout(()=>{if(gameOver())doGameOver();},360);
    }
  }
  if(dEl)dEl.classList.remove('dragging');
  dPiece=null;dEl=null;dragging=false;clrPv();pRow=-1;pCol=-1;
}
function rotIdx(i){
  if(i>=0&&i<pieces.length){pieces[i]=rot(pieces[i]);refPieces();snd('rot');}
}

/* Global drag listeners */
document.addEventListener('mousemove',e=>{moveD(e.clientX,e.clientY);},{passive:true});
document.addEventListener('mouseup',()=>{endD();});
document.addEventListener('touchmove',e=>{
  e.preventDefault();
  if(lpt){clearTimeout(lpt);lpt=null;}
  moveD(e.touches[0].clientX,e.touches[0].clientY);
},{passive:false});
document.addEventListener('touchend',e=>{
  e.preventDefault();
  if(lpt){clearTimeout(lpt);lpt=null;}
  endD();
});
document.addEventListener('touchcancel',()=>{if(lpt){clearTimeout(lpt);lpt=null;}endD();});
document.addEventListener('keydown',e=>{
  if((e.key==='r'||e.key==='R')&&pieces.length&&!dragging)rotIdx(0);
});

/* ── PIECES TRAY ────────────────────────────────── */
function mkPiece(piece,idx){
  const div=document.createElement('div');div.className='piece';
  const rh=document.createElement('div');rh.className='rh';rh.textContent='↻ ROTATE';
  div.appendChild(rh);
  div.style.gridTemplateColumns=`repeat(${piece.p[0].length},1fr)`;
  piece.p.forEach(row=>row.forEach(v=>{
    const b=document.createElement('div');
    b.className='block '+(v?piece.c:'empty');
    div.appendChild(b);
  }));
  div.addEventListener('mousedown',e=>{e.preventDefault();startD(piece,div,idx,e.clientX,e.clientY);});
  div.addEventListener('touchstart',e=>{
    e.preventDefault();
    lpt=setTimeout(()=>{rotIdx(idx);lpt=null;},800);
    startD(piece,div,idx,e.touches[0].clientX,e.touches[0].clientY);
  });
  return div;
}
function refPieces(){piecesEl.innerHTML='';pieces.forEach((p,i)=>piecesEl.appendChild(mkPiece(p,i)));}
function genPieces(){pieces=[];for(let i=0;i<2;i++)pieces.push(rnd());refPieces();}

/* ── SCORE / GAME STATE ─────────────────────────── */
function updScore(){scoreEl.textContent=score;}
function doGameOver(){snd('over');finalEl.textContent=score;goModal.style.display='flex';}
function restart(){
  score=0;updScore();
  for(let i=0;i<100;i++){gd[i]={f:false,c:null};rc(i);}
  pCells=[];genPieces();goModal.style.display='none';
}

/* ── SCREEN NAV ─────────────────────────────────── */
function showMenu(){
  document.getElementById('gameScreen').style.display='none';
  document.getElementById('menuScreen').style.display='flex';
}
function showGame(){
  document.getElementById('menuScreen').style.display='none';
  document.getElementById('gameScreen').style.display='flex';
}

document.getElementById('startBtn').addEventListener('click',()=>{snd('ui');showGame();restart();});
document.getElementById('howBtn').addEventListener('click',()=>{snd('ui');document.getElementById('howModal').style.display='flex';});
document.getElementById('closeHowBtn').addEventListener('click',()=>{snd('ui');document.getElementById('howModal').style.display='none';});
document.getElementById('replayBtn').addEventListener('click',()=>{snd('ui');goModal.style.display='none';restart();});
document.getElementById('goMenuBtn').addEventListener('click',()=>{snd('ui');goModal.style.display='none';showMenu();});
document.getElementById('menuBtn').addEventListener('click',()=>{snd('ui');showMenu();});

/* ── INIT ───────────────────────────────────────── */
createBoard();
genPieces();
</script>
</body>
</html>
