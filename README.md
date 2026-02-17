<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Block Crush</title>
<link href="https://fonts.googleapis.com/css2?family=Exo+2:wght@400;700;900&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;user-select:none;-webkit-tap-highlight-color:transparent;}
:root{
  --bg1:#0d0d1a;--bg2:#13132b;
  --panel:rgba(255,255,255,0.05);--border:rgba(255,255,255,0.12);
  --accent:#7c5cfc;--accent2:#00d4ff;--accent3:#ff6b9d;
  --glow:0 0 40px rgba(124,92,252,0.35);
  --cell-empty:rgba(255,255,255,0.04);--cell-border:rgba(255,255,255,0.07);
  --text:#e8e8ff;--text2:rgba(232,232,255,0.45);
}
html,body{
  width:100%;min-height:100vh;
  background:var(--bg1);
  background-image:
    radial-gradient(ellipse at 15% 15%,rgba(124,92,252,0.18) 0%,transparent 55%),
    radial-gradient(ellipse at 85% 85%,rgba(0,212,255,0.12) 0%,transparent 55%),
    radial-gradient(ellipse at 50% 50%,rgba(255,107,157,0.06) 0%,transparent 70%);
  font-family:'Exo 2',sans-serif;
  display:flex;justify-content:center;align-items:center;
  padding:10px;overflow-x:hidden;
}

/* Particles */
.particles{position:fixed;top:0;left:0;width:100%;height:100%;pointer-events:none;z-index:0;overflow:hidden;}
.particle{position:absolute;border-radius:50%;opacity:0;animation:floatUp linear infinite;}
@keyframes floatUp{0%{opacity:0;transform:translateY(100vh) scale(0);}10%{opacity:0.7;}90%{opacity:0.2;}100%{opacity:0;transform:translateY(-30px) scale(2);}}

/* MENU */
.screen{position:relative;z-index:10;width:min(460px,96%);}
.menu{
  background:linear-gradient(145deg,rgba(124,92,252,0.13),rgba(0,212,255,0.07));
  backdrop-filter:blur(24px);-webkit-backdrop-filter:blur(24px);
  border:1px solid var(--border);padding:52px 28px 44px;
  border-radius:36px;text-align:center;
  box-shadow:var(--glow),inset 0 1px 0 rgba(255,255,255,0.1),inset 0 -1px 0 rgba(0,0,0,0.2);
}
.logo-wrap{margin-bottom:36px;}
.logo-main{
  font-size:clamp(52px,15vw,72px);font-weight:900;letter-spacing:-3px;
  background:linear-gradient(135deg,#a78bfa 0%,#7c5cfc 40%,#00d4ff 100%);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
  line-height:0.95;display:block;
}
.logo-sub{
  font-size:12px;color:var(--text2);letter-spacing:6px;text-transform:uppercase;
  margin-top:8px;display:block;
}
.mbtn{
  display:flex;align-items:center;justify-content:center;gap:14px;
  width:100%;padding:17px 20px;margin:10px 0;
  border:none;border-radius:18px;cursor:pointer;
  font-family:'Exo 2',sans-serif;font-size:17px;font-weight:800;
  letter-spacing:2px;text-transform:uppercase;transition:all 0.2s;
}
.mbtn.play{
  background:linear-gradient(135deg,#7c5cfc,#4f35c8);color:#fff;
  box-shadow:0 8px 32px rgba(124,92,252,0.55),inset 0 1px 0 rgba(255,255,255,0.25);
}
.mbtn.play:hover{transform:translateY(-3px);box-shadow:0 14px 40px rgba(124,92,252,0.7),inset 0 1px 0 rgba(255,255,255,0.25);}
.mbtn.play:active{transform:translateY(1px);}
.mbtn.info{
  background:rgba(255,255,255,0.07);color:var(--text);
  border:1px solid var(--border);
}
.mbtn.info:hover{background:rgba(255,255,255,0.13);}
.bico{
  width:38px;height:38px;border-radius:12px;
  background:rgba(255,255,255,0.15);
  display:flex;align-items:center;justify-content:center;flex-shrink:0;
}
.bico svg{width:20px;height:20px;}

/* GAME CONTAINER */
.game-container{display:none;position:relative;z-index:10;width:min(500px,98vw);}

/* TOPBAR */
.topbar{display:flex;align-items:stretch;gap:10px;margin-bottom:14px;}
.score-card{
  flex:1;
  background:rgba(255,255,255,0.06);
  border:1px solid var(--border);border-radius:18px;
  padding:12px 18px;backdrop-filter:blur(12px);
  display:flex;flex-direction:column;justify-content:center;
}
.slabel{font-size:10px;color:var(--text2);letter-spacing:4px;text-transform:uppercase;margin-bottom:2px;}
.sval{
  font-size:34px;font-weight:900;line-height:1;
  background:linear-gradient(135deg,#fff 0%,#a78bfa 100%);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
}
.ibtn{
  width:50px;height:50px;border-radius:15px;border:none;cursor:pointer;
  background:rgba(255,255,255,0.07);border:1px solid var(--border);
  display:flex;align-items:center;justify-content:center;
  transition:all 0.2s;flex-shrink:0;
}
.ibtn:hover{background:rgba(255,255,255,0.15);transform:scale(1.07);}
.ibtn:active{transform:scale(0.93);}
.ibtn svg{width:22px;height:22px;stroke:var(--text);}

/* BOARD */
.board-wrap{
  background:rgba(255,255,255,0.03);
  border:1px solid var(--border);
  border-radius:26px;padding:11px;
  box-shadow:var(--glow),inset 0 1px 0 rgba(255,255,255,0.05);
  margin-bottom:14px;
}
.board{
  width:100%;aspect-ratio:1;display:grid;
  grid-template-columns:repeat(10,1fr);
  grid-template-rows:repeat(10,1fr);
  gap:3px;border-radius:18px;overflow:hidden;
}
.cell{
  background:var(--cell-empty);border-radius:5px;
  border:1px solid var(--cell-border);aspect-ratio:1;
  transition:background 0.12s;
}
.cell.filled{border:none;border-radius:6px;position:relative;}
.cell.filled::after{
  content:'';position:absolute;inset:0;border-radius:6px;
  background:linear-gradient(135deg,rgba(255,255,255,0.28) 0%,transparent 55%);
  pointer-events:none;
}

/* COLORS */
.cell.filled.red    {background:linear-gradient(135deg,#ff6b6b,#b91c1c);box-shadow:0 0 9px rgba(255,107,107,0.55);}
.cell.filled.blue   {background:linear-gradient(135deg,#60a5fa,#1d4ed8);box-shadow:0 0 9px rgba(96,165,250,0.55);}
.cell.filled.green  {background:linear-gradient(135deg,#4ade80,#15803d);box-shadow:0 0 9px rgba(74,222,128,0.55);}
.cell.filled.purple {background:linear-gradient(135deg,#c084fc,#7e22ce);box-shadow:0 0 9px rgba(192,132,252,0.55);}
.cell.filled.orange {background:linear-gradient(135deg,#fb923c,#c2410c);box-shadow:0 0 9px rgba(251,146,60,0.55);}
.cell.filled.yellow {background:linear-gradient(135deg,#fde047,#a16207);box-shadow:0 0 9px rgba(253,224,71,0.55);}
.cell.filled.pink   {background:linear-gradient(135deg,#f472b6,#be185d);box-shadow:0 0 9px rgba(244,114,182,0.55);}
.cell.filled.cyan   {background:linear-gradient(135deg,#22d3ee,#0e7490);box-shadow:0 0 9px rgba(34,211,238,0.55);}
.cell.filled.lime   {background:linear-gradient(135deg,#a3e635,#3f6212);box-shadow:0 0 9px rgba(163,230,53,0.55);}
.cell.filled.rose   {background:linear-gradient(135deg,#fb7185,#9f1239);box-shadow:0 0 9px rgba(251,113,133,0.55);}

/* PREVIEW */
.cell.preview{border:2px solid rgba(255,255,255,0.45)!important;animation:prevPulse 0.7s ease-in-out infinite;}
.cell.preview.red    {background:rgba(255,107,107,0.22)!important;border-color:#ff6b6b!important;}
.cell.preview.blue   {background:rgba(96,165,250,0.22)!important; border-color:#60a5fa!important;}
.cell.preview.green  {background:rgba(74,222,128,0.22)!important; border-color:#4ade80!important;}
.cell.preview.purple {background:rgba(192,132,252,0.22)!important;border-color:#c084fc!important;}
.cell.preview.orange {background:rgba(251,146,60,0.22)!important; border-color:#fb923c!important;}
.cell.preview.yellow {background:rgba(253,224,71,0.22)!important; border-color:#fde047!important;}
.cell.preview.pink   {background:rgba(244,114,182,0.22)!important;border-color:#f472b6!important;}
.cell.preview.cyan   {background:rgba(34,211,238,0.22)!important; border-color:#22d3ee!important;}
.cell.preview.lime   {background:rgba(163,230,53,0.22)!important; border-color:#a3e635!important;}
.cell.preview.rose   {background:rgba(251,113,133,0.22)!important;border-color:#fb7185!important;}

@keyframes prevPulse{0%,100%{opacity:0.55;}50%{opacity:1;}}
@keyframes pop{0%{transform:scale(1);}45%{transform:scale(1.18);}100%{transform:scale(1);}}
.cell.filled{animation:pop 0.18s ease;}
@keyframes flashClear{0%{background:rgba(255,255,255,0.95)!important;}100%{background:rgba(255,255,255,0)!important;}}
.cell.clearing{animation:flashClear 0.32s ease forwards!important;}

/* PIECES TRAY */
.pieces-tray{
  background:rgba(255,255,255,0.04);border:1px solid var(--border);
  border-radius:24px;padding:18px 14px;
  display:flex;justify-content:center;gap:18px;flex-wrap:wrap;
  min-height:120px;align-items:center;
  box-shadow:inset 0 1px 0 rgba(255,255,255,0.05);
}
.piece{
  display:grid;gap:3px;
  background:rgba(255,255,255,0.08);
  padding:10px;border-radius:14px;
  border:1px solid var(--border);cursor:grab;
  transition:all 0.18s;position:relative;
}
.piece:hover{background:rgba(255,255,255,0.14);transform:translateY(-4px) scale(1.05);box-shadow:0 10px 30px rgba(0,0,0,0.35);}
.piece:active,.piece.dragging{opacity:0.25;cursor:grabbing;transform:scale(0.95);}
.block{width:min(31px,6.8vw);height:min(31px,6.8vw);border-radius:5px;position:relative;}
.block::after{content:'';position:absolute;inset:0;border-radius:5px;background:linear-gradient(135deg,rgba(255,255,255,0.3) 0%,transparent 55%);}
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

.rotate-hint{
  position:absolute;top:-28px;left:50%;transform:translateX(-50%);
  background:rgba(124,92,252,0.92);backdrop-filter:blur(8px);
  color:#fff;font-size:10px;font-weight:800;padding:4px 10px;
  border-radius:20px;border:1px solid rgba(255,255,255,0.2);
  opacity:0;transition:opacity 0.2s;pointer-events:none;
  white-space:nowrap;letter-spacing:2px;text-transform:uppercase;
}
.piece:hover .rotate-hint{opacity:1;}

/* SCORE POPUP */
.score-popup{
  position:fixed;pointer-events:none;z-index:999;
  font-family:'Exo 2',sans-serif;font-size:26px;font-weight:900;
  color:#fff;text-shadow:0 0 20px rgba(124,92,252,0.9);
  animation:sFloat 1s ease forwards;
}
@keyframes sFloat{0%{opacity:1;transform:translateY(0) scale(1);}100%{opacity:0;transform:translateY(-90px) scale(1.5);}}

/* MODALS */
.modal{
  position:fixed;inset:0;
  background:rgba(5,5,20,0.88);
  backdrop-filter:blur(18px);-webkit-backdrop-filter:blur(18px);
  display:none;justify-content:center;align-items:center;
  z-index:2000;padding:20px;
}
.mbox{
  background:linear-gradient(145deg,rgba(124,92,252,0.14),rgba(0,212,255,0.07));
  border:1px solid var(--border);backdrop-filter:blur(20px);
  border-radius:30px;padding:40px 26px;text-align:center;
  width:min(370px,95%);
  box-shadow:0 40px 80px rgba(0,0,0,0.65),inset 0 1px 0 rgba(255,255,255,0.1);
}
.mtitle{
  font-size:38px;font-weight:900;margin-bottom:6px;
  background:linear-gradient(135deg,#a78bfa,#00d4ff);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
}
.mscore-label{color:var(--text2);font-size:11px;letter-spacing:4px;text-transform:uppercase;margin:22px 0 4px;}
.mscore-num{
  font-size:62px;font-weight:900;line-height:1;margin-bottom:28px;
  background:linear-gradient(135deg,#fff,#a78bfa);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
}
.modalbtn{
  display:flex;align-items:center;justify-content:center;gap:10px;
  width:100%;padding:16px;margin:8px 0;
  border:none;border-radius:15px;cursor:pointer;
  font-family:'Exo 2',sans-serif;font-size:16px;font-weight:800;
  text-transform:uppercase;letter-spacing:2px;transition:all 0.2s;
}
.modalbtn.prim{background:linear-gradient(135deg,#7c5cfc,#4f35c8);color:#fff;box-shadow:0 8px 32px rgba(124,92,252,0.5);}
.modalbtn.prim:hover{transform:translateY(-2px);box-shadow:0 12px 40px rgba(124,92,252,0.7);}
.modalbtn.dang{background:rgba(255,107,107,0.12);color:#ff6b6b;border:1px solid rgba(255,107,107,0.28);}
.modalbtn.dang:hover{background:rgba(255,107,107,0.22);}
.rules-list{text-align:left;margin:18px 0;background:rgba(255,255,255,0.04);border:1px solid var(--border);border-radius:16px;padding:14px 18px;}
.rules-list li{color:var(--text);font-size:14px;padding:8px 0;border-bottom:1px solid rgba(255,255,255,0.06);list-style:none;display:flex;align-items:center;gap:12px;}
.rules-list li:last-child{border-bottom:none;}
.rico{font-size:19px;flex-shrink:0;}
</style>
</head>
<body>
<div class="particles" id="particles"></div>

<!-- MENU -->
<div class="screen" id="menuScreen">
  <div class="menu">
    <div class="logo-wrap">
      <span class="logo-main">BLOCK<br>CRUSH</span>
      <span class="logo-sub">Puzzle Game</span>
    </div>
    <button class="mbtn play" id="startBtn">
      <span class="bico"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polygon points="5,3 19,12 5,21" fill="currentColor"/></svg></span>
      O'YNASH
    </button>
    <button class="mbtn info" id="howBtn">
      <span class="bico"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4M12 8h.01" stroke-linecap="round"/></svg></span>
      QOIDALAR
    </button>
  </div>
</div>

<!-- GAME -->
<div class="game-container" id="gameContainer">
  <div class="topbar">
    <div class="score-card">
      <div class="slabel">Ball</div>
      <div class="sval" id="scoreVal">0</div>
    </div>
    <button class="ibtn" id="muteBtn" title="Ovoz">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
        <polygon points="11,5 6,9 2,9 2,15 6,15 11,19"/>
        <path id="wave1" d="M15.54 8.46a5 5 0 0 1 0 7.07"/>
        <path id="wave2" d="M19.07 4.93a10 10 0 0 1 0 14.14"/>
      </svg>
    </button>
    <button class="ibtn" id="menuBtn" title="Menyu">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
        <line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="18" x2="21" y2="18"/>
      </svg>
    </button>
  </div>
  <div class="board-wrap"><div class="board" id="board"></div></div>
  <div class="pieces-tray" id="pieces"></div>
</div>

<!-- GAME OVER -->
<div class="modal" id="gameOverModal">
  <div class="mbox">
    <div class="mtitle">GAME<br>OVER</div>
    <div class="mscore-label">Yakuniy ball</div>
    <div class="mscore-num" id="finalScore">0</div>
    <button class="modalbtn prim" id="playAgainBtn">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="23,4 23,10 17,10"/><path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"/></svg>
      QAYTA O'YNASH
    </button>
    <button class="modalbtn dang" id="gameOverMenuBtn">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/></svg>
      BOSH MENYU
    </button>
  </div>
</div>

<!-- HOW TO PLAY -->
<div class="modal" id="howModal">
  <div class="mbox">
    <div class="mtitle" style="font-size:26px">QOIDALAR</div>
    <ul class="rules-list">
      <li><span class="rico">🎨</span> 10 xil rangdagi figuralar</li>
      <li><span class="rico">🖱️</span> Sichqoncha / barmoq bilan suring</li>
      <li><span class="rico">🔄</span> R tugmasi yoki 1s bosib aylantiring</li>
      <li><span class="rico">⬛</span> To'liq qator/ustun = +100 ball</li>
      <li><span class="rico">💎</span> Har bir blok = +10 ball</li>
      <li><span class="rico">🔊</span> Ovoz tugmasi bilan o'chirib/yoqing</li>
    </ul>
    <button class="modalbtn prim" id="closeHowBtn">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20,6 9,17 4,12"/></svg>
      TUSHUNDIM
    </button>
  </div>
</div>

<script>
// PARTICLES
(()=>{
  const con=document.getElementById('particles');
  const cols=['#7c5cfc','#00d4ff','#ff6b9d','#fbbf24','#4ade80','#f472b6'];
  for(let i=0;i<30;i++){
    const p=document.createElement('div');
    p.className='particle';
    const sz=Math.random()*3+1;
    p.style.cssText=`left:${Math.random()*100}%;width:${sz}px;height:${sz}px;background:${cols[Math.floor(Math.random()*cols.length)]};animation-duration:${Math.random()*18+10}s;animation-delay:${Math.random()*12}s;`;
    con.appendChild(p);
  }
})();

// AUDIO
let isMuted=false;
let actx=null;
function getACtx(){if(!actx){try{actx=new(window.AudioContext||window.webkitAudioContext)();}catch(e){}}return actx;}

function playSound(type){
  if(isMuted)return;
  try{
    const ctx=getACtx();if(!ctx)return;
    if(ctx.state==='suspended')ctx.resume();
    const now=ctx.currentTime;

    const mk=(freq,type2,dur,gainVal,freqEnd)=>{
      const o=ctx.createOscillator(),g=ctx.createGain();
      o.connect(g);g.connect(ctx.destination);
      o.type=type2||'sine';
      o.frequency.setValueAtTime(freq,now);
      if(freqEnd)o.frequency.exponentialRampToValueAtTime(freqEnd,now+dur*0.8);
      g.gain.setValueAtTime(gainVal||0.15,now);
      g.gain.exponentialRampToValueAtTime(0.001,now+dur);
      o.start(now);o.stop(now+dur);
    };

    if(type==='place'){mk(420,'sine',0.15,0.16,680);}
    else if(type==='clear'){
      [[0,523],[0.06,659],[0.12,784],[0.2,1047]].forEach(([d,f])=>{
        const o=ctx.createOscillator(),g=ctx.createGain();
        o.connect(g);g.connect(ctx.destination);
        o.type='sine';o.frequency.value=f;
        g.gain.setValueAtTime(0.2,now+d);
        g.gain.exponentialRampToValueAtTime(0.001,now+d+0.28);
        o.start(now+d);o.stop(now+d+0.3);
      });
    }
    else if(type==='gameover'){mk(280,'sawtooth',0.6,0.2,75);}
    else if(type==='rotate'){mk(900,'triangle',0.09,0.09,1200);}
    else if(type==='menu'){mk(580,'sine',0.14,0.11,860);}
  }catch(e){}
}

// MUTE
const muteBtn=document.getElementById('muteBtn');
const wave1=document.getElementById('wave1');
const wave2=document.getElementById('wave2');
function updateMuteUI(){
  if(isMuted){
    wave1.style.display='none';wave2.style.display='none';
    muteBtn.style.opacity='0.45';
  } else {
    wave1.style.display='';wave2.style.display='';
    muteBtn.style.opacity='1';
  }
}
muteBtn.addEventListener('click',()=>{
  isMuted=!isMuted;updateMuteUI();
  if(!isMuted)playSound('menu');
});

// ELEMENTS
const menuScreen=document.getElementById('menuScreen');
const gameContainer=document.getElementById('gameContainer');
const boardEl=document.getElementById('board');
const piecesEl=document.getElementById('pieces');
const scoreValEl=document.getElementById('scoreVal');
const gameOverModal=document.getElementById('gameOverModal');
const finalScoreEl=document.getElementById('finalScore');
const howModal=document.getElementById('howModal');

// STATE
let grid=[],gridData=[],score=0,currentPieces=[];
let draggedPiece=null,draggedElement=null,draggedIndex=-1;
let previewRow=-1,previewCol=-1,isDragging=false;
let longPressTimer=null,previewCells=[];

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

// BOARD
function createBoard(){
  boardEl.innerHTML='';grid=[];gridData=[];
  for(let i=0;i<100;i++){
    const c=document.createElement('div');
    c.className='cell';c.dataset.index=i;
    c.dataset.row=Math.floor(i/10);c.dataset.col=i%10;
    boardEl.appendChild(c);grid.push(c);
    gridData.push({filled:false,color:null});
  }
}

function renderCell(i){
  const c=grid[i],d=gridData[i];
  c.className='cell';
  c.dataset.index=i;c.dataset.row=Math.floor(i/10);c.dataset.col=i%10;
  if(d.filled&&d.color)c.classList.add('filled',d.color);
}

function clearPreview(){previewCells.forEach(i=>renderCell(i));previewCells=[];}

function showPreview(piece,sr,sc){
  clearPreview();if(!piece)return false;
  for(let r=0;r<piece.pattern.length;r++)
    for(let c=0;c<piece.pattern[0].length;c++)
      if(piece.pattern[r][c]===1){
        const nr=sr+r,nc=sc+c;
        if(nr<0||nr>=10||nc<0||nc>=10)return false;
        if(gridData[nr*10+nc].filled)return false;
      }
  for(let r=0;r<piece.pattern.length;r++)
    for(let c=0;c<piece.pattern[0].length;c++)
      if(piece.pattern[r][c]===1){
        const idx=(sr+r)*10+(sc+c);
        grid[idx].className='cell preview '+piece.color;
        previewCells.push(idx);
      }
  return true;
}

function rotatePiece(piece){
  const rows=piece.pattern.length,cols=piece.pattern[0].length,np=[];
  for(let c=0;c<cols;c++){const row=[];for(let r=rows-1;r>=0;r--)row.push(piece.pattern[r][c]);np.push(row);}
  return{pattern:np,color:piece.color};
}

function getRandomPiece(){
  const s=SHAPES[Math.floor(Math.random()*SHAPES.length)];
  return{pattern:s.p.map(r=>[...r]),color:COLORS[Math.floor(Math.random()*COLORS.length)]};
}

function canPlace(piece,row,col){
  for(let r=0;r<piece.pattern.length;r++)
    for(let c=0;c<piece.pattern[0].length;c++)
      if(piece.pattern[r][c]===1){
        const nr=row+r,nc=col+c;
        if(nr>=10||nc>=10)return false;
        if(gridData[nr*10+nc].filled)return false;
      }
  return true;
}

function placePiece(piece,row,col){
  if(!canPlace(piece,row,col))return false;
  let blocks=0;
  for(let r=0;r<piece.pattern.length;r++)
    for(let c=0;c<piece.pattern[0].length;c++)
      if(piece.pattern[r][c]===1){
        const idx=(row+r)*10+(col+c);
        gridData[idx]={filled:true,color:piece.color};
        renderCell(idx);blocks++;
      }
  score+=blocks*10;updateScore();playSound('place');
  return true;
}

function clearLines(){
  let cleared=0;const toClear=new Set();
  for(let r=0;r<10;r++)
    if([...Array(10)].every((_,c)=>gridData[r*10+c].filled))
      {for(let c=0;c<10;c++)toClear.add(r*10+c);cleared++;}
  for(let c=0;c<10;c++)
    if([...Array(10)].every((_,r)=>gridData[r*10+c].filled))
      {for(let r=0;r<10;r++)toClear.add(r*10+c);cleared++;}
  if(toClear.size>0){
    toClear.forEach(i=>grid[i].className='cell clearing');
    setTimeout(()=>{toClear.forEach(i=>{gridData[i]={filled:false,color:null};renderCell(i);});},320);
    score+=cleared*100;updateScore();playSound('clear');
    showPopup('+'+cleared*100);
  }
}

function showPopup(txt){
  const el=document.createElement('div');el.className='score-popup';el.textContent=txt;
  const r=boardEl.getBoundingClientRect();
  el.style.cssText=`left:${r.left+r.width/2-40}px;top:${r.top+r.height/2-20}px;`;
  document.body.appendChild(el);setTimeout(()=>el.remove(),1100);
}

// DRAG
function startDrag(piece,el,idx,cx,cy){
  draggedPiece=JSON.parse(JSON.stringify(piece));
  draggedElement=el;draggedIndex=idx;isDragging=true;
  el.classList.add('dragging');previewRow=-1;previewCol=-1;clearPreview();
}

function onDrag(cx,cy){
  if(!draggedPiece||!isDragging)return;
  const rect=boardEl.getBoundingClientRect();
  if(cx>=rect.left&&cx<=rect.right&&cy>=rect.top&&cy<=rect.bottom){
    const cs=rect.width/10;
    const col=Math.floor((cx-rect.left)/cs),row=Math.floor((cy-rect.top)/cs);
    const sr=Math.max(0,Math.min(9-draggedPiece.pattern.length+1,row-Math.floor(draggedPiece.pattern.length/2)));
    const sc=Math.max(0,Math.min(9-draggedPiece.pattern[0].length+1,col-Math.floor(draggedPiece.pattern[0].length/2)));
    if(previewRow!==sr||previewCol!==sc){previewRow=sr;previewCol=sc;showPreview(draggedPiece,sr,sc);}
  }else{clearPreview();previewRow=-1;previewCol=-1;}
}

function endDrag(){
  if(!draggedPiece||!isDragging)return;
  if(previewRow>=0&&previewCol>=0&&canPlace(draggedPiece,previewRow,previewCol)){
    clearPreview();
    if(placePiece(draggedPiece,previewRow,previewCol)){
      clearLines();
      const arr=Array.from(piecesEl.children);
      if(arr[draggedIndex]){piecesEl.removeChild(arr[draggedIndex]);currentPieces.splice(draggedIndex,1);}
      while(currentPieces.length<2)currentPieces.push(getRandomPiece());
      refreshPieces();
      setTimeout(()=>{if(checkGameOver())endGame();},380);
    }
  }
  if(draggedElement)draggedElement.classList.remove('dragging');
  draggedPiece=null;draggedElement=null;isDragging=false;
  clearPreview();previewRow=-1;previewCol=-1;
}

function rotateCurrent(idx){
  if(idx>=0&&idx<currentPieces.length){
    currentPieces[idx]=rotatePiece(currentPieces[idx]);
    refreshPieces();playSound('rotate');
  }
}

document.addEventListener('mousemove',e=>{e.preventDefault();onDrag(e.clientX,e.clientY);});
document.addEventListener('mouseup',e=>{e.preventDefault();endDrag();});
document.addEventListener('touchmove',e=>{
  e.preventDefault();
  if(longPressTimer){clearTimeout(longPressTimer);longPressTimer=null;}
  onDrag(e.touches[0].clientX,e.touches[0].clientY);
},{passive:false});
document.addEventListener('touchend',e=>{
  e.preventDefault();
  if(longPressTimer){clearTimeout(longPressTimer);longPressTimer=null;}
  endDrag();
});
document.addEventListener('touchcancel',()=>{
  if(longPressTimer){clearTimeout(longPressTimer);longPressTimer=null;}
  endDrag();
});
document.addEventListener('keydown',e=>{
  if((e.key==='r'||e.key==='R')&&currentPieces.length>0&&!isDragging)rotateCurrent(0);
});

function createPieceEl(piece,idx){
  const div=document.createElement('div');
  div.className='piece';div.dataset.index=idx;
  const hint=document.createElement('div');
  hint.className='rotate-hint';hint.textContent='↻ ROTATE';
  div.appendChild(hint);
  const cols=piece.pattern[0].length;
  div.style.gridTemplateColumns=`repeat(${cols},1fr)`;
  piece.pattern.forEach(row=>row.forEach(v=>{
    const b=document.createElement('div');
    b.className='block '+(v===1?piece.color:'empty');
    div.appendChild(b);
  }));
  div.addEventListener('mousedown',e=>{e.preventDefault();startDrag(piece,div,idx,e.clientX,e.clientY);});
  div.addEventListener('touchstart',e=>{
    e.preventDefault();
    longPressTimer=setTimeout(()=>{rotateCurrent(idx);longPressTimer=null;},800);
    startDrag(piece,div,idx,e.touches[0].clientX,e.touches[0].clientY);
  });
  return div;
}

function refreshPieces(){
  piecesEl.innerHTML='';
  currentPieces.forEach((p,i)=>piecesEl.appendChild(createPieceEl(p,i)));
}

function generatePieces(){
  currentPieces=[];
  for(let i=0;i<2;i++)currentPieces.push(getRandomPiece());
  refreshPieces();
}

function updateScore(){scoreValEl.textContent=score;}

function checkGameOver(){
  return currentPieces.every(piece=>{
    for(let r=0;r<=10-piece.pattern.length;r++)
      for(let c=0;c<=10-piece.pattern[0].length;c++)
        if(canPlace(piece,r,c))return false;
    return true;
  });
}

function endGame(){
  playSound('gameover');
  finalScoreEl.textContent=score;
  gameOverModal.style.display='flex';
}

function restartGame(){
  score=0;updateScore();
  for(let i=0;i<100;i++){gridData[i]={filled:false,color:null};renderCell(i);}
  previewCells=[];generatePieces();
  gameOverModal.style.display='none';
}

// BUTTON EVENTS
document.getElementById('startBtn').addEventListener('click',()=>{
  playSound('menu');
  menuScreen.style.display='none';
  gameContainer.style.display='block';
  restartGame();
});
document.getElementById('howBtn').addEventListener('click',()=>{playSound('menu');howModal.style.display='flex';});
document.getElementById('closeHowBtn').addEventListener('click',()=>{playSound('menu');howModal.style.display='none';});
document.getElementById('playAgainBtn').addEventListener('click',()=>{playSound('menu');gameOverModal.style.display='none';restartGame();});
document.getElementById('gameOverMenuBtn').addEventListener('click',()=>{
  playSound('menu');gameOverModal.style.display='none';
  gameContainer.style.display='none';menuScreen.style.display='flex';
});
document.getElementById('menuBtn').addEventListener('click',()=>{
  playSound('menu');gameContainer.style.display='none';menuScreen.style.display='flex';
});

// INIT
createBoard();generatePieces();
</script>
</body>
</html>
