<!DOCTYPE html>
<html lang="nl">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
<meta name="mobile-web-app-capable" content="yes"/>
<meta name="apple-mobile-web-app-capable" content="yes"/>
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent"/>
<title>🍲 Fufu Run!</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent;}
body{background:#0a0318;min-height:100dvh;display:flex;flex-direction:column;align-items:center;
  font-family:sans-serif;overflow:hidden;user-select:none;-webkit-user-select:none;touch-action:none;}
#hud{display:none;gap:6px;padding:6px 8px;align-items:center;justify-content:space-between;width:100%;}
#hud.show{display:flex;}
.hbadge{background:rgba(255,255,255,0.08);border:1px solid rgba(255,255,255,0.12);border-radius:7px;
  padding:3px 8px;color:#fde68a;font-weight:700;font-size:0.75rem;}
.hbadge span{color:#fff;}
#wrap{position:relative;border-radius:10px;overflow:hidden;line-height:0;
  border:2px solid rgba(255,179,71,0.18);box-shadow:0 0 30px rgba(255,140,50,0.15);}
canvas{display:block;}
#controls{display:none;justify-content:space-around;align-items:center;
  width:100%;padding:12px 20px 6px;gap:16px;}
#controls.show{display:flex;}
.gbtn{width:108px;height:108px;border-radius:50%;border:3px solid rgba(255,255,255,0.2);
  display:flex;align-items:center;justify-content:center;flex-direction:column;gap:2px;
  cursor:pointer;box-shadow:0 4px 18px rgba(0,0,0,0.4);touch-action:none;font-size:2rem;}
#hint{color:rgba(255,255,255,0.18);font-size:0.6rem;text-align:center;padding-bottom:4px;}
/* Overlay panels */
.ov{position:absolute;inset:0;background:rgba(8,2,18,0.93);backdrop-filter:blur(6px);
  display:flex;flex-direction:column;align-items:center;justify-content:center;
  gap:10px;padding:18px;color:#fff;}
.ov.top{justify-content:flex-start;padding-top:18px;}
.ov.hidden{display:none;}
.ov h1{font-size:1.8rem;font-weight:900;color:#ffb347;}
.ov h2{font-size:1.2rem;font-weight:900;}
.sub{color:rgba(255,255,255,0.4);font-size:0.78rem;}
.bigbtn{border:none;border-radius:50px;padding:11px 26px;font-size:1rem;font-weight:900;
  cursor:pointer;font-family:sans-serif;color:#fff;touch-action:none;}
.bigbtn.play{background:linear-gradient(135deg,#ff8c00,#ff4fa3);padding:14px 44px;font-size:1.15rem;
  box-shadow:0 4px 20px rgba(255,100,50,0.4);}
.bigbtn.back{background:rgba(255,255,255,0.1);border:1px solid rgba(255,255,255,0.2);}
.bigbtn.shop-btn{background:rgba(234,179,8,0.5);border:2px solid rgba(253,224,71,0.4);
  border-radius:12px;padding:10px 16px;font-size:0.88rem;}
.bigbtn.skin-btn{background:rgba(124,58,237,0.55);border:2px solid rgba(167,139,250,0.3);
  border-radius:12px;padding:10px 16px;font-size:0.88rem;}
.bigbtn.map-btn{background:rgba(6,182,212,0.38);border:2px solid rgba(6,182,212,0.3);
  border-radius:12px;padding:10px 16px;font-size:0.88rem;}
.bigbtn.menu-btn{background:rgba(255,255,255,0.08);border:1px solid rgba(255,255,255,0.15);
  border-radius:8px;padding:3px 10px;font-size:0.72rem;}
.row{display:flex;gap:8px;flex-wrap:wrap;justify-content:center;}
.infobox{background:rgba(255,255,255,0.06);border-radius:12px;padding:8px 14px;
  font-size:0.75rem;color:rgba(255,255,255,0.45);text-align:center;}
.coinbadge{color:#fde68a;font-size:0.82rem;font-weight:700;}
/* Grid cards */
.skin-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;width:100%;max-width:320px;}
.map-list{display:flex;flex-direction:column;gap:8px;width:100%;max-width:320px;
  overflow-y:auto;max-height:260px;}
.shop-grid{display:grid;grid-template-columns:repeat(2,1fr);gap:8px;width:100%;max-width:340px;
  overflow-y:auto;max-height:260px;}
.card{background:rgba(255,255,255,0.07);border:2px solid rgba(255,255,255,0.12);
  border-radius:12px;padding:8px 6px;cursor:pointer;text-align:center;transition:all 0.12s;}
.card.sel{background:rgba(255,140,0,0.28);border-color:#ffb347;box-shadow:0 0 10px rgba(255,179,71,0.3);}
.card.locked{opacity:0.45;}
.card .em{font-size:1.9rem;}
.card .nm{font-size:0.68rem;font-weight:700;color:#fff;margin-top:3px;}
.card .pr{font-size:0.6rem;color:#fbbf24;}
.card .ok{font-size:0.6rem;color:#4ade80;}
.mapcard{display:flex;align-items:center;gap:12px;padding:9px 12px;}
.mapcard .me{font-size:1.6rem;flex-shrink:0;}
.mapcard .mn{font-weight:800;font-size:0.88rem;color:#fff;}
.mapcard .ms{color:rgba(255,255,255,0.35);font-size:0.66rem;}
.shopcard{background:rgba(255,255,255,0.06);border:2px solid rgba(255,255,255,0.1);
  border-radius:12px;padding:10px 8px;text-align:center;}
.shopcard.owned{background:rgba(34,197,94,0.2);border-color:#22c55e;}
.shopcard.canbuy{border-color:#fbbf24;}
.shopcard .sem{font-size:2rem;}
.shopcard .snm{font-size:0.72rem;font-weight:800;color:#fff;margin:3px 0;}
.shopcard .spr{font-size:0.68rem;color:#fbbf24;font-weight:700;margin-bottom:4px;}
.shopcard .sown{font-size:0.65rem;color:#4ade80;font-weight:700;}
.buybtn{border:none;border-radius:8px;padding:5px 10px;font-size:0.68rem;font-weight:800;
  cursor:pointer;font-family:sans-serif;color:#fff;}
.buybtn.can{background:linear-gradient(135deg,#f59e0b,#d97706);}
.buybtn.cant{background:rgba(100,100,100,0.3);cursor:not-allowed;}
</style>
</head>
<body>

<div id="hud">
  <button class="bigbtn menu-btn" id="menuBtn">☰ Menu</button>
  <div style="display:flex;gap:5px;">
    <div class="hbadge">🏆 <span id="hScore">0</span></div>
    <div class="hbadge">❤️ <span id="hLives">3</span></div>
    <div class="hbadge">🍲 <span id="hFufu">0</span></div>
    <div class="hbadge">🪙 <span id="hCoins">0</span></div>
  </div>
</div>

<div id="wrap">
  <canvas id="c"></canvas>

  <!-- MENU -->
  <div class="ov" id="ovMenu">
    <div style="font-size:3rem">🍲</div>
    <h1>Fufu Run!</h1>
    <div class="sub">Spring · Buk · Pak fufu!</div>
    <div class="infobox" id="selInfo">Skin: 👨‍🍳 Chef · Map: 🌙 Nacht</div>
    <div class="coinbadge" id="menuCoins">🪙 0 munten</div>
    <div class="row">
      <button class="bigbtn shop-btn" onclick="show('shop')">🪙 Winkel</button>
      <button class="bigbtn skin-btn" onclick="show('skins')">🎭 Skins</button>
      <button class="bigbtn map-btn"  onclick="show('maps')">🗺️ Maps</button>
    </div>
    <button class="bigbtn play" onclick="startGame()">▶ Spelen!</button>
  </div>

  <!-- SKINS -->
  <div class="ov top hidden" id="ovSkins">
    <h2 style="color:#ffb347">Kies je skin</h2>
    <div class="skin-grid" id="skinGrid"></div>
    <button class="bigbtn back" onclick="show('menu')">← Terug</button>
  </div>

  <!-- MAPS -->
  <div class="ov top hidden" id="ovMaps">
    <h2 style="color:#67e8f9">Kies je map</h2>
    <div class="map-list" id="mapList"></div>
    <button class="bigbtn back" onclick="show('menu')">← Terug</button>
  </div>

  <!-- SHOP -->
  <div class="ov top hidden" id="ovShop">
    <h2 style="color:#fde68a">🪙 Winkel</h2>
    <div class="sub">Skins kopen met munten</div>
    <div class="coinbadge" id="shopCoins">Jouw munten: 🪙 0</div>
    <div class="shop-grid" id="shopGrid"></div>
    <button class="bigbtn back" onclick="show('menu')">← Terug</button>
  </div>

  <!-- GAME OVER -->
  <div class="ov hidden" id="ovOver">
    <div style="font-size:2.6rem">💀</div>
    <div style="color:#ffb347;font-size:1.4rem;font-weight:900">Game Over!</div>
    <div style="color:#fde68a;font-size:0.82rem;opacity:0.85" id="overText"></div>
    <div class="row">
      <button class="bigbtn play" style="padding:10px 22px;font-size:0.95rem" onclick="startGame()">Opnieuw</button>
      <button class="bigbtn back" onclick="show('menu')">Menu</button>
    </div>
  </div>
</div>

<div id="controls">
  <div class="gbtn" id="btnJump" style="background:rgba(255,140,0,0.65)">
    <span>🍲</span><span style="font-size:0.58rem;color:rgba(255,255,255,0.8)">SPRING</span>
  </div>
  <div class="gbtn" id="btnDuck" style="background:rgba(124,58,237,0.65)">
    <span>⬇️</span><span style="font-size:0.58rem;color:rgba(255,255,255,0.8)">BUK</span>
  </div>
</div>
<div id="hint">🔴 stekel = spring · 🟣 balk = buk · 🍲 = punten</div>

<script>
// ── Constants ────────────────────────────────────────────────────────────────
const GW=420,GH=240,GROUND_Y=GH-42,PW=16,PH_STAND=22,PH_DUCK=11;
const GRAVITY=0.52,JUMP_VEL=-10,MAX_JUMPS=2,BASE_SPEED=2.6;

const SKINS=[
  {id:"chef", name:"Chef", emoji:"👨‍🍳",body:"#fde68a",outline:"#b45309",accent:"#ff8c00",price:0},
  {id:"robot",name:"Robot",emoji:"🤖", body:"#94a3b8",outline:"#334155",accent:"#22d3ee",price:150},
  {id:"ghost",name:"Spook",emoji:"👻",body:"#e0e7ff",outline:"#818cf8",accent:"#818cf8",price:200},
  {id:"fire", name:"Vlam", emoji:"🔥", body:"#f97316",outline:"#7c2d12",accent:"#fbbf24",price:250},
  {id:"alien",name:"Alien",emoji:"👽",body:"#4ade80",outline:"#14532d",accent:"#86efac",price:350},
  {id:"dino", name:"Dino", emoji:"🦕", body:"#34d399",outline:"#065f46",accent:"#6ee7b7",price:500},
];
const MAPS=[
  {id:"night", name:"Nacht",   emoji:"🌙",sky1:"#1a0a2e",sky2:"#3a1560",ground:"#5c3d1e",groundTop:"#8b5e2a",stars:true,
   spike:"#a855f7",spikeGlow:"rgba(168,85,247,0.3)",bar:"#7c3aed",ball:"#c084fc",stack:["#4c1d95","#6d28d9","#7c3aed"]},
  {id:"jungle",name:"Jungle",  emoji:"🌿",sky1:"#052e16",sky2:"#166534",ground:"#713f12",groundTop:"#a16207",stars:false,
   spike:"#65a30d",spikeGlow:"rgba(101,163,13,0.3)",bar:"#166534",ball:"#78350f",stack:["#78350f","#92400e","#a16207"]},
  {id:"desert",name:"Woestijn",emoji:"🏜️",sky1:"#7c2d12",sky2:"#ea580c",ground:"#d97706",groundTop:"#fbbf24",stars:false,
   spike:"#fef3c7",spikeGlow:"rgba(254,243,199,0.3)",bar:"#d97706",ball:"#a16207",stack:["#d97706","#b45309","#92400e"]},
  {id:"space", name:"Ruimte",  emoji:"🚀",sky1:"#000000",sky2:"#0f172a",ground:"#1e293b",groundTop:"#334155",stars:true,
   spike:"#f43f5e",spikeGlow:"rgba(244,63,94,0.4)",bar:"#06b6d4",ball:"#64748b",stack:["#1e3a5f","#1d4ed8","#3b82f6"]},
  {id:"candy", name:"Snoep",   emoji:"🍬",sky1:"#831843",sky2:"#be185d",ground:"#9d174d",groundTop:"#f9a8d4",stars:false,
   spike:"#f43f5e",spikeGlow:"rgba(244,63,94,0.3)",bar:"#7c3aed",ball:"#ec4899",stack:["#db2777","#be185d","#9d174d"]},
];

// ── State ─────────────────────────────────────────────────────────────────────
let coins=0,owned=["chef"],selSkin=0,selMap=0,screen="menu";
let curSkin=SKINS[0],curMap=MAPS[0];
// Persist to localStorage
function save(){try{localStorage.setItem("fr",JSON.stringify({coins,owned,selSkin,selMap}));}catch(e){}}
function load(){try{const d=JSON.parse(localStorage.getItem("fr")||"{}");
  if(d.coins!=null)coins=d.coins;if(d.owned)owned=d.owned;
  if(d.selSkin!=null)selSkin=d.selSkin;if(d.selMap!=null)selMap=d.selMap;}catch(e){}}
load();

// ── Canvas setup ─────────────────────────────────────────────────────────────
const canvas=document.getElementById("c");
const ctx=canvas.getContext("2d");
canvas.width=GW; canvas.height=GH;
function resize(){
  const s=Math.min(window.innerWidth/GW,(window.innerHeight*0.72)/GH);
  canvas.style.width=GW*s+"px"; canvas.style.height=GH*s+"px";
}
resize(); window.addEventListener("resize",resize);

// ── Touch input ───────────────────────────────────────────────────────────────
const touch={jump:false,duck:false,_edge:false};
function setupBtn(id,down,up){
  const el=document.getElementById(id);
  el.addEventListener("touchstart",e=>{e.preventDefault();down();},{passive:false});
  el.addEventListener("touchend",  e=>{e.preventDefault();up();},  {passive:false});
  el.addEventListener("touchcancel",e=>{e.preventDefault();up();}, {passive:false});
  el.addEventListener("mousedown",  e=>{e.preventDefault();down();});
  el.addEventListener("mouseup",    e=>{e.preventDefault();up();});
}
setupBtn("btnJump",()=>{touch.jump=true;touch._edge=true;},()=>{touch.jump=false;});
setupBtn("btnDuck",()=>{touch.duck=true;},()=>{touch.duck=false;});
document.getElementById("menuBtn").onclick=()=>show("menu");

// ── Game world ────────────────────────────────────────────────────────────────
let _obsToggle=0;
function makePlatform(x){const w=48+Math.floor(Math.random()*38),rise=28+Math.floor(Math.random()*34);return{x,y:GROUND_Y-rise,w};}
function makeObs(x,dist){
  const hard=dist>350,vhard=dist>800;
  _obsToggle++;
  if(_obsToggle%2===0){
    if(hard&&Math.random()<0.45)return{type:"bar",x,y:GROUND_Y-PH_STAND-2,w:13,barH:11};
    return{type:"bar",x,y:GROUND_Y-PH_STAND-5,w:13,barH:16};
  }else{
    const r=Math.random();
    if(r<0.28)return{type:"spike",x,y:GROUND_Y-13,w:13,h:13,n:1};
    if(r<0.52)return{type:"spike",x,y:GROUND_Y-13,w:26,h:13,n:2};
    if(r<0.64&&hard)return{type:"spike",x,y:GROUND_Y-22,w:13,h:22,n:1};
    if(r<0.76&&hard)return{type:"ball",x,y:GROUND_Y-11,r:11};
    if(r<0.88&&vhard)return{type:"spike",x,y:GROUND_Y-13,w:39,h:13,n:3};
    const levels=hard?(vhard?2+Math.floor(Math.random()*2):2):1,cw=16,ch=14;
    return{type:"stack",x,w:cw,h:ch*levels,levels,cw,ch};
  }
}
function makeFufu(x,py){return{x,y:py!=null?py-22:GROUND_Y-36,done:false};}
function makeClouds(){return Array.from({length:9},(_,i)=>({x:i*90+20,y:15+Math.random()*65,r:14+Math.random()*22,s:0.06+Math.random()*0.1}));}

const G={running:false,speed:BASE_SPEED,dist:0,obs:[],fufus:[],clouds:makeClouds(),parts:[],platforms:[],
  nextPlatform:180,nextObs:160,nextFufu:90,score:0,lives:3,fufu:0,p:null};

function newP(){G.p={x:55,y:GROUND_Y-PH_STAND,vy:0,ground:false,jumps:0,duck:false,ph:PH_STAND,rf:0,dead:false,inv:0};}
newP();

function syncHUD(){
  document.getElementById("hScore").textContent=Math.floor(G.score);
  document.getElementById("hLives").textContent=G.lives;
  document.getElementById("hFufu").textContent=G.fufu;
  document.getElementById("hCoins").textContent=coins;
}
function burst(x,y,col,n){for(let i=0;i<n;i++){const a=(Math.PI*2/n)*i+Math.random()*0.5,s=1+Math.random()*2;G.parts.push({x,y,vx:Math.cos(a)*s,vy:Math.sin(a)*s-1,life:1,decay:0.05+Math.random()*0.04,r:1.5+Math.random()*2,col});}}

function die(){
  const p=G.p;if(p.inv>0||p.dead)return;
  G.lives--;burst(p.x+PW/2,p.y+p.ph/2,"#f87171",12);syncHUD();
  if(G.lives<=0){
    p.dead=true;G.running=false;
    setTimeout(()=>{
      const earned=Math.floor(G.score/10)+G.fufu*5;
      coins+=earned;save();
      document.getElementById("overText").textContent=`Score: ${Math.floor(G.score)} · Fufu: ${G.fufu} · +${earned}🪙`;
      show("over");
    },600);
  }else{p.inv=85;p.vy=0;p.y=GROUND_Y-PH_STAND;p.ph=PH_STAND;p.duck=false;p.jumps=0;}
}

// ── Update ────────────────────────────────────────────────────────────────────
let fr=0;
function update(){
  if(!G.running)return;
  const p=G.p;if(p.dead)return;
  fr++;
  G.speed=BASE_SPEED+G.dist*0.0007;G.score+=G.speed*0.05;G.dist+=G.speed;
  if(touch.duck&&p.ground){if(!p.duck){p.duck=true;p.y+=PH_STAND-PH_DUCK;p.ph=PH_DUCK;}}
  else if(p.duck){p.duck=false;p.y-=PH_STAND-PH_DUCK;p.ph=PH_STAND;}
  if(touch._edge&&p.jumps<MAX_JUMPS&&!p.duck){p.vy=JUMP_VEL*(p.jumps===1?0.82:1);p.jumps++;burst(p.x+PW/2,p.y+p.ph,curSkin.accent,5);}
  touch._edge=false;
  p.vy+=GRAVITY;p.y+=p.vy;
  const gt=GROUND_Y-p.ph;p.ground=false;
  if(p.y>=gt){p.y=gt;p.vy=0;p.ground=true;p.jumps=0;}
  if(p.inv>0)p.inv--;if(p.ground)p.rf+=0.25;
  // platforms
  G.nextPlatform--;
  if(G.nextPlatform<=0){const pl=makePlatform(GW+20);G.platforms.push(pl);G.nextPlatform=Math.max(180,300-G.dist*0.04);if(Math.random()<0.6)G.fufus.push(makeFufu(pl.x+pl.w/2,pl.y));}
  G.platforms=G.platforms.filter(pl=>{pl.x-=G.speed;return pl.x+pl.w+4>0;});
  if(!p.duck)G.platforms.forEach(pl=>{if(p.x+PW-2>pl.x&&p.x+2<pl.x+pl.w&&p.vy>=0&&p.y+p.ph>pl.y&&p.y+p.ph<pl.y+18){p.y=pl.y-p.ph;p.vy=0;p.ground=true;p.jumps=0;}});
  // spawn
  G.nextObs--;if(G.nextObs<=0){G.obs.push(makeObs(GW+18,G.dist));G.nextObs=Math.max(130,220-G.dist*0.04);}
  G.nextFufu--;if(G.nextFufu<=0){G.fufus.push(makeFufu(GW+18,null));G.nextFufu=Math.max(58,125-G.dist*0.03);}
  G.obs=G.obs.filter(o=>{o.x-=G.speed;return o.x+(o.w||30)+4>0;});
  G.fufus=G.fufus.filter(f=>{f.x-=G.speed;return f.x+12>0;});
  // collision
  function hS(o){return p.x+PW-2>o.x+2&&p.x+2<o.x+o.w-2&&p.y+p.ph>o.y+3&&p.y<o.y+o.h;}
  function hB(o){return p.x+PW-2>o.x+2&&p.x+2<o.x+o.w-2&&p.y<o.y+o.barH;}
  function hBl(o){const dx=(p.x+PW/2)-o.x,dy=(p.y+p.ph/2)-o.y;return dx*dx+dy*dy<(o.r+6)*(o.r+6);}
  if(p.inv===0)for(const o of G.obs){let hit=false;if(o.type==="spike")hit=hS(o);else if(o.type==="bar")hit=hB(o);else if(o.type==="ball")hit=hBl(o);else if(o.type==="stack")hit=(p.x+PW-2>o.x+1&&p.x+2<o.x+o.w-1&&p.y+p.ph>GROUND_Y-o.h&&p.y<GROUND_Y);if(hit){die();break;}}
  G.fufus.forEach(f=>{if(!f.done&&p.x+PW>f.x-9&&p.x<f.x+9&&p.y+p.ph>f.y-9&&p.y<f.y+9){f.done=true;G.fufu++;G.score+=150;burst(f.x,f.y,"#d4a853",8);syncHUD();}});
  if(fr%18===0)syncHUD();
  G.clouds.forEach(c=>{c.x-=c.s;if(c.x+c.r<0)c.x=GW+c.r;});
  G.parts=G.parts.filter(p=>p.life>0);G.parts.forEach(p=>{p.x+=p.vx;p.y+=p.vy;p.vy+=0.1;p.life-=p.decay;});
}

// ── Draw ──────────────────────────────────────────────────────────────────────
function draw(){
  const map=curMap;
  const sk=ctx.createLinearGradient(0,0,0,GH);sk.addColorStop(0,map.sky1);sk.addColorStop(1,map.sky2);
  ctx.fillStyle=sk;ctx.fillRect(0,0,GW,GH);
  if(map.stars){ctx.fillStyle="rgba(255,255,255,0.45)";for(let i=0;i<30;i++){const sx=((i*139+17-G.dist*0.1)%GW+GW)%GW,sy=(i*67+5)%(GH*0.46);ctx.beginPath();ctx.arc(sx,sy,0.4+(i%3)*0.3,0,Math.PI*2);ctx.fill();}}
  G.clouds.forEach(c=>{ctx.fillStyle="rgba(255,255,255,0.08)";ctx.beginPath();ctx.arc(c.x,c.y,c.r,0,Math.PI*2);ctx.arc(c.x+c.r*0.55,c.y-c.r*0.28,c.r*0.65,0,Math.PI*2);ctx.arc(c.x-c.r*0.5,c.y-c.r*0.2,c.r*0.54,0,Math.PI*2);ctx.fill();});
  ctx.fillStyle=map.ground;ctx.fillRect(0,GROUND_Y,GW,GH-GROUND_Y);
  ctx.fillStyle=map.groundTop;ctx.fillRect(0,GROUND_Y,GW,4);
  G.platforms.forEach(pl=>{ctx.fillStyle="rgba(0,0,0,0.18)";ctx.fillRect(pl.x+3,pl.y+5,pl.w,10);ctx.fillStyle=map.ground;ctx.fillRect(pl.x,pl.y,pl.w,12);ctx.fillStyle=map.groundTop;ctx.fillRect(pl.x,pl.y,pl.w,4);ctx.fillStyle=map.ground;ctx.fillRect(pl.x+pl.w/2-3,pl.y+12,6,GROUND_Y-(pl.y+12));});
  // fufu
  G.fufus.forEach(f=>{if(f.done)return;const b=Math.sin(fr*0.06+f.x*0.05)*2,fy=f.y+b;
    ctx.fillStyle="#c17f3a";ctx.beginPath();ctx.ellipse(f.x,fy+3,7,4,0,0,Math.PI);ctx.fill();
    ctx.fillStyle="#5c3d1e";ctx.fillRect(f.x-7,fy+1,14,3);
    ctx.fillStyle="#f5f0e0";ctx.strokeStyle="#d4a853";ctx.lineWidth=1;ctx.beginPath();ctx.arc(f.x,fy-2,6,0,Math.PI*2);ctx.fill();ctx.stroke();
    ctx.fillStyle="#fffde8";ctx.beginPath();ctx.arc(f.x-2,fy-4,1.6,0,Math.PI*2);ctx.fill();});
  // obstacles
  function dSpike(ox,o){
    const tw=o.w/o.n;ctx.fillStyle=map.spikeGlow||"rgba(239,68,68,0.2)";ctx.fillRect(ox-2,o.y+o.h-3,o.w+4,5);
    for(let i=0;i<o.n;i++){const bx=ox+i*tw;
      if(map.id==="night"){const cg=ctx.createLinearGradient(bx+tw/2,o.y,bx+tw/2,o.y+o.h);cg.addColorStop(0,"#e879f9");cg.addColorStop(1,"#6d28d9");ctx.fillStyle=cg;ctx.beginPath();ctx.moveTo(bx,o.y+o.h);ctx.lineTo(bx+tw*0.3,o.y+o.h*0.4);ctx.lineTo(bx+tw/2,o.y);ctx.lineTo(bx+tw*0.7,o.y+o.h*0.4);ctx.lineTo(bx+tw,o.y+o.h);ctx.closePath();ctx.fill();ctx.strokeStyle="#f0abfc";ctx.lineWidth=0.6;ctx.stroke();}
      else if(map.id==="jungle"){ctx.fillStyle="#4d7c0f";ctx.beginPath();ctx.moveTo(bx+tw/2,o.y);ctx.lineTo(bx+tw,o.y+o.h);ctx.lineTo(bx,o.y+o.h);ctx.closePath();ctx.fill();ctx.strokeStyle="#65a30d";ctx.lineWidth=0.8;for(let seg=1;seg<3;seg++){const sy=o.y+o.h-(o.h/3)*seg;ctx.beginPath();ctx.moveTo(bx+tw/2-tw*0.2*seg/3,sy);ctx.lineTo(bx+tw/2+tw*0.2*seg/3,sy);ctx.stroke();}}
      else if(map.id==="desert"){ctx.fillStyle="#fef9c3";ctx.beginPath();ctx.moveTo(bx+tw/2,o.y);ctx.lineTo(bx+tw,o.y+o.h);ctx.lineTo(bx+tw*0.6,o.y+o.h*0.7);ctx.lineTo(bx+tw/2,o.y+o.h*0.85);ctx.lineTo(bx+tw*0.4,o.y+o.h*0.7);ctx.lineTo(bx,o.y+o.h);ctx.closePath();ctx.fill();ctx.strokeStyle="#d4d4aa";ctx.lineWidth=0.5;ctx.stroke();}
      else if(map.id==="space"){const lg=ctx.createLinearGradient(bx+tw/2,o.y,bx+tw/2,o.y+o.h);lg.addColorStop(0,"#f43f5e");lg.addColorStop(1,"rgba(244,63,94,0.3)");ctx.fillStyle=lg;ctx.fillRect(bx+tw/2-2,o.y,4,o.h);ctx.fillStyle="rgba(244,63,94,0.15)";ctx.fillRect(bx+tw/2-6,o.y,12,o.h);ctx.fillStyle="#fff";ctx.beginPath();ctx.arc(bx+tw/2,o.y,2,0,Math.PI*2);ctx.fill();}
      else if(map.id==="candy"){const st=ctx.createLinearGradient(bx,o.y,bx+tw,o.y);st.addColorStop(0,"#f43f5e");st.addColorStop(0.5,"#fff");st.addColorStop(1,"#f43f5e");ctx.fillStyle=st;ctx.beginPath();ctx.moveTo(bx+tw/2,o.y);ctx.lineTo(bx+tw,o.y+o.h);ctx.lineTo(bx,o.y+o.h);ctx.closePath();ctx.fill();ctx.strokeStyle="#fb7185";ctx.lineWidth=0.6;ctx.stroke();}
      else{ctx.fillStyle=map.spike;ctx.beginPath();ctx.moveTo(bx,o.y+o.h);ctx.lineTo(bx+tw/2,o.y);ctx.lineTo(bx+tw,o.y+o.h);ctx.closePath();ctx.fill();}
    }
  }
  function dBar(ox,o){
    if(map.id==="night"){ctx.fillStyle="#4c1d95";ctx.fillRect(ox,0,o.w,o.y+o.barH);ctx.fillStyle="rgba(216,180,254,0.15)";for(let y=0;y<o.y+o.barH;y+=7)ctx.fillRect(ox+1,y,o.w-2,4);ctx.strokeStyle="#c084fc";ctx.lineWidth=1;for(let y=4;y<o.y+o.barH;y+=8){ctx.beginPath();ctx.rect(ox+2,y,o.w-4,4);ctx.stroke();}}
    else if(map.id==="jungle"){ctx.fillStyle="#14532d";ctx.fillRect(ox,0,o.w,o.y+o.barH);ctx.fillStyle="#16a34a";for(let y=6;y<o.y+o.barH;y+=12){ctx.beginPath();ctx.ellipse(ox-3,y,5,3,0.5,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.ellipse(ox+o.w+2,y+5,5,3,-0.5,0,Math.PI*2);ctx.fill();}}
    else if(map.id==="desert"){ctx.fillStyle="#c2410c";ctx.fillRect(ox,0,o.w,o.y+o.barH);ctx.fillStyle="rgba(251,191,36,0.2)";for(let y=0;y<o.y+o.barH;y+=5){const wv=Math.sin(y*0.4+fr*0.1)*2;ctx.fillRect(ox+wv,y,o.w,3);}}
    else if(map.id==="space"){ctx.fillStyle="rgba(6,182,212,0.15)";ctx.fillRect(ox-3,0,o.w+6,o.y+o.barH);ctx.fillStyle="#06b6d4";ctx.fillRect(ox,0,o.w,o.y+o.barH);ctx.fillStyle="rgba(255,255,255,0.2)";const pulse=Math.floor(fr/8)%Math.max(1,Math.floor(o.barH/6));ctx.fillRect(ox,pulse*6,o.w,4);}
    else if(map.id==="candy"){for(let y=0;y<o.y+o.barH;y+=5){ctx.fillStyle=Math.floor(y/5)%2===0?"#1c1917":"#9f1239";ctx.fillRect(ox,y,o.w,5);}}
    else{ctx.fillStyle=map.bar;ctx.fillRect(ox,0,o.w,o.y+o.barH);}
    const eg=ctx.createLinearGradient(ox,o.y+o.barH-3,ox,o.y+o.barH+6);eg.addColorStop(0,map.bar+"cc");eg.addColorStop(1,"transparent");ctx.fillStyle=eg;ctx.fillRect(ox-2,o.y+o.barH-3,o.w+4,9);
  }
  function dBall(o){
    const ang=G.dist*0.08;
    if(map.id==="night"){ctx.fillStyle="rgba(192,132,252,0.2)";ctx.beginPath();ctx.arc(o.x,o.y,o.r+5,0,Math.PI*2);ctx.fill();ctx.fillStyle="#e9d5ff";ctx.strokeStyle="#a855f7";ctx.lineWidth=1;ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.fill();ctx.stroke();ctx.fillStyle="#6b21a8";ctx.beginPath();ctx.arc(o.x-3,o.y-2,2.5,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(o.x+3,o.y-2,2.5,0,Math.PI*2);ctx.fill();}
    else if(map.id==="jungle"){ctx.fillStyle="#57534e";ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.fill();ctx.fillStyle="#78716c";ctx.beginPath();ctx.arc(o.x-2,o.y-2,o.r*0.55,0,Math.PI*2);ctx.fill();ctx.strokeStyle="#292524";ctx.lineWidth=0.8;ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.stroke();}
    else if(map.id==="desert"){ctx.strokeStyle="#a16207";ctx.lineWidth=1.5;ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.stroke();for(let a=0;a<Math.PI;a+=Math.PI/3){ctx.beginPath();ctx.moveTo(o.x+Math.cos(a+ang)*o.r*0.9,o.y+Math.sin(a+ang)*o.r*0.9);ctx.lineTo(o.x+Math.cos(a+ang+Math.PI)*o.r*0.9,o.y+Math.sin(a+ang+Math.PI)*o.r*0.9);ctx.stroke();}}
    else if(map.id==="space"){ctx.fillStyle="#475569";ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.fill();ctx.fillStyle="#64748b";ctx.beginPath();ctx.arc(o.x-3,o.y-3,o.r*0.45,0,Math.PI*2);ctx.fill();ctx.strokeStyle="#1e293b";ctx.lineWidth=0.8;ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.stroke();}
    else if(map.id==="candy"){ctx.fillStyle="#ec4899";ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.fill();const gg=ctx.createRadialGradient(o.x-3,o.y-3,1,o.x,o.y,o.r);gg.addColorStop(0,"rgba(255,255,255,0.5)");gg.addColorStop(1,"transparent");ctx.fillStyle=gg;ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.fill();}
    else{ctx.fillStyle=map.ball||map.spike;ctx.beginPath();ctx.arc(o.x,o.y,o.r,0,Math.PI*2);ctx.fill();}
  }
  function dStack(o){
    const cols=map.stack||["#78716c","#a8a29e","#57534e"];
    for(let i=0;i<o.levels;i++){const cy=GROUND_Y-o.ch*(i+1);ctx.fillStyle="rgba(0,0,0,0.22)";ctx.fillRect(o.x+2,cy+3,o.cw,o.ch);ctx.fillStyle=cols[i%cols.length];ctx.fillRect(o.x,cy,o.cw,o.ch);ctx.strokeStyle="rgba(0,0,0,0.3)";ctx.lineWidth=0.8;ctx.strokeRect(o.x,cy,o.cw,o.ch);
      if(map.id==="night"){ctx.fillStyle="rgba(255,255,255,0.1)";ctx.fillRect(o.x+1,cy+1,o.cw-2,o.ch/2-1);ctx.strokeStyle="#c084fc";ctx.lineWidth=0.6;ctx.strokeRect(o.x+o.cw/2-2,cy+o.ch/2-1,4,4);}
      else if(map.id==="jungle"){ctx.strokeStyle="rgba(0,0,0,0.2)";ctx.lineWidth=0.6;ctx.beginPath();ctx.ellipse(o.x+o.cw/2,cy+o.ch/2,o.cw/2-2,o.ch/2-2,0,0,Math.PI*2);ctx.stroke();}
      else if(map.id==="desert"){ctx.strokeStyle="rgba(0,0,0,0.2)";ctx.lineWidth=0.6;ctx.beginPath();ctx.moveTo(o.x+3,cy+4);ctx.lineTo(o.x+o.cw-3,cy+o.ch-4);ctx.stroke();}
      else if(map.id==="space"){ctx.strokeStyle="rgba(96,165,250,0.4)";ctx.lineWidth=0.7;for(let px=2;px<o.cw-2;px+=3){ctx.beginPath();ctx.moveTo(o.x+px,cy+2);ctx.lineTo(o.x+px,cy+o.ch-2);ctx.stroke();}}
      else if(map.id==="candy"){ctx.fillStyle="#fbcfe8";ctx.fillRect(o.x,cy,o.cw,3);if(i===o.levels-1){ctx.fillStyle="#fcd34d";ctx.fillRect(o.x+o.cw/2-1,cy-5,2,5);ctx.fillStyle="#f97316";ctx.beginPath();ctx.arc(o.x+o.cw/2,cy-6,2,0,Math.PI*2);ctx.fill();}}
      else{ctx.strokeStyle="rgba(0,0,0,0.18)";ctx.lineWidth=0.6;ctx.beginPath();ctx.moveTo(o.x+2,cy+2);ctx.lineTo(o.x+o.cw-2,cy+o.ch-2);ctx.moveTo(o.x+o.cw-2,cy+2);ctx.lineTo(o.x+2,cy+o.ch-2);ctx.stroke();}
      ctx.fillStyle="rgba(255,255,255,0.12)";ctx.fillRect(o.x+1,cy+1,o.cw-2,2);
    }
  }
  G.obs.forEach(o=>{if(o.type==="spike")dSpike(o.x,o);else if(o.type==="bar")dBar(o.x,o);else if(o.type==="ball")dBall(o);else if(o.type==="stack")dStack(o);});
  // particles
  G.parts.forEach(pp=>{ctx.save();ctx.globalAlpha=Math.max(0,pp.life);ctx.fillStyle=pp.col;ctx.beginPath();ctx.arc(pp.x,pp.y,Math.max(0.1,pp.r*pp.life),0,Math.PI*2);ctx.fill();ctx.restore();});
  // player
  const p=G.p;if(p&&!(p.inv>0&&Math.floor(fr/4)%2===0)){
    const bob=p.ground&&!p.duck?Math.sin(p.rf*2)*0.9:0;
    const scY=p.duck?0.56:(p.vy<-3?0.83:(p.vy>3?1.1:1));
    ctx.save();ctx.translate(p.x+PW/2,p.y+p.ph/2+bob);ctx.scale(1,scY);
    drawSkin(ctx,curSkin,p.ph,p.duck);ctx.restore();
  }
  // speed bar
  if(G.running){const sp=Math.min(1,(G.speed-BASE_SPEED)/4);ctx.fillStyle="rgba(0,0,0,0.28)";ctx.fillRect(5,5,48,5);const sg=ctx.createLinearGradient(5,0,53,0);sg.addColorStop(0,"#34d399");sg.addColorStop(1,"#f59e0b");ctx.fillStyle=sg;ctx.fillRect(5,5,48*sp,5);ctx.fillStyle="rgba(255,255,255,0.3)";ctx.font="3.5px sans-serif";ctx.fillText("SNELHEID",5,14);}
}

function drawSkin(ctx,skin,ph,ducking){
  ctx.fillStyle="rgba(0,0,0,0.18)";ctx.beginPath();ctx.ellipse(0,ph/2+1,8,2.5,0,0,Math.PI*2);ctx.fill();
  ctx.fillStyle=skin.body;ctx.strokeStyle=skin.outline;ctx.lineWidth=1.5;ctx.beginPath();ctx.ellipse(0,0,PW/2,ph/2,0,0,Math.PI*2);ctx.fill();ctx.stroke();
  if(skin.id==="chef"){if(!ducking){ctx.fillStyle="#fff";ctx.beginPath();ctx.arc(4,-3,3,0,Math.PI*2);ctx.fill();ctx.fillStyle="#111";ctx.beginPath();ctx.arc(5,-3,1.5,0,Math.PI*2);ctx.fill();ctx.strokeStyle=skin.outline;ctx.lineWidth=1;ctx.beginPath();ctx.arc(3,1,2.5,0.2,Math.PI-0.2);ctx.stroke();ctx.fillStyle="#fff";ctx.strokeStyle="#d4a853";ctx.lineWidth=1;ctx.beginPath();ctx.ellipse(0,-ph/2+1,7,2,0,0,Math.PI*2);ctx.fill();ctx.stroke();ctx.beginPath();ctx.rect(-4.5,-ph/2-7,9,7);ctx.fill();ctx.stroke();}else{ctx.fillStyle="#fff";ctx.beginPath();ctx.arc(3,-1,2.5,0,Math.PI*2);ctx.fill();ctx.fillStyle="#111";ctx.beginPath();ctx.arc(4,-0.8,1.2,0,Math.PI*2);ctx.fill();ctx.fillStyle="#7dd3fc";ctx.beginPath();ctx.arc(7,-3,1.2,0,Math.PI*2);ctx.fill();}}
  else if(skin.id==="robot"){ctx.strokeStyle=skin.accent;ctx.lineWidth=0.8;ctx.strokeRect(-4,-ph/2+2,8,ph-4);ctx.fillStyle=skin.accent;ctx.fillRect(-3,-ph/2+4,6,2);if(!ducking){ctx.strokeStyle=skin.outline;ctx.lineWidth=1;ctx.beginPath();ctx.moveTo(0,-ph/2);ctx.lineTo(0,-ph/2-6);ctx.stroke();ctx.fillStyle=skin.accent;ctx.beginPath();ctx.arc(0,-ph/2-7,2,0,Math.PI*2);ctx.fill();}}
  else if(skin.id==="ghost"){ctx.fillStyle="rgba(255,255,255,0.25)";ctx.beginPath();ctx.ellipse(0,0,PW/2-1,ph/2-1,0,0,Math.PI*2);ctx.fill();ctx.fillStyle="#312e81";ctx.beginPath();ctx.arc(-3,-3,2.5,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(3,-3,2.5,0,Math.PI*2);ctx.fill();ctx.fillStyle="#fff";ctx.beginPath();ctx.arc(-2,-4,0.9,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(4,-4,0.9,0,Math.PI*2);ctx.fill();}
  else if(skin.id==="fire"){ctx.fillStyle="#fbbf24";ctx.beginPath();ctx.ellipse(0,-2,PW/2-3,ph/2-4,0,0,Math.PI*2);ctx.fill();ctx.fillStyle="#fff";ctx.beginPath();ctx.ellipse(0,-4,3,4,0,0,Math.PI*2);ctx.fill();ctx.fillStyle="#fef08a";ctx.beginPath();ctx.arc(-2,-4,1.8,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(3,-4,1.8,0,Math.PI*2);ctx.fill();}
  else if(skin.id==="alien"){ctx.fillStyle="#000";ctx.beginPath();ctx.ellipse(-3,-4,3.5,2.5,-0.3,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.ellipse(3,-4,3.5,2.5,0.3,0,Math.PI*2);ctx.fill();ctx.fillStyle=skin.accent;ctx.beginPath();ctx.arc(-3,-4,1.4,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(3,-4,1.4,0,Math.PI*2);ctx.fill();if(!ducking){ctx.strokeStyle=skin.outline;ctx.lineWidth=1;ctx.beginPath();ctx.moveTo(-3,-ph/2);ctx.lineTo(-5,-ph/2-5);ctx.stroke();ctx.beginPath();ctx.moveTo(3,-ph/2);ctx.lineTo(5,-ph/2-5);ctx.stroke();ctx.fillStyle=skin.accent;ctx.beginPath();ctx.arc(-5,-ph/2-6,1.8,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(5,-ph/2-6,1.8,0,Math.PI*2);ctx.fill();}}
  else if(skin.id==="dino"){if(!ducking){ctx.fillStyle=skin.accent;for(let i=0;i<3;i++){ctx.beginPath();ctx.moveTo(-4+i*4,-ph/2+2);ctx.lineTo(-2+i*4,-ph/2-4-i*2);ctx.lineTo(i*4,-ph/2+2);ctx.closePath();ctx.fill();}}ctx.fillStyle="#fff";ctx.beginPath();ctx.arc(4,-4,2.8,0,Math.PI*2);ctx.fill();ctx.fillStyle=skin.outline;ctx.beginPath();ctx.arc(5,-4,1.4,0,Math.PI*2);ctx.fill();}
}

// ── Game loop ─────────────────────────────────────────────────────────────────
function loop(){update();draw();requestAnimationFrame(loop);}
loop();

// ── Screen management ─────────────────────────────────────────────────────────
function show(s){
  screen=s;
  document.getElementById("ovMenu").classList.toggle("hidden",s!=="menu");
  document.getElementById("ovSkins").classList.toggle("hidden",s!=="skins");
  document.getElementById("ovMaps").classList.toggle("hidden",s!=="maps");
  document.getElementById("ovShop").classList.toggle("hidden",s!=="shop");
  document.getElementById("ovOver").classList.toggle("hidden",s!=="over");
  document.getElementById("hud").classList.toggle("show",s==="playing");
  document.getElementById("controls").classList.toggle("show",s==="playing"&&!G.p?.dead);
  document.getElementById("hint").style.display=s==="playing"?"block":"none";
  if(s==="menu")updateMenuUI();
  if(s==="shop")updateShopUI();
  if(s==="skins")updateSkinsUI();
  if(s==="maps")updateMapsUI();
}

function updateMenuUI(){
  document.getElementById("menuCoins").textContent=`🪙 ${coins} munten`;
  document.getElementById("selInfo").textContent=`Skin: ${SKINS[selSkin].emoji} ${SKINS[selSkin].name} · Map: ${MAPS[selMap].emoji} ${MAPS[selMap].name}`;
}
function updateShopUI(){
  document.getElementById("shopCoins").textContent=`Jouw munten: 🪙 ${coins}`;
  const g=document.getElementById("shopGrid");g.innerHTML="";
  SKINS.forEach((sk,i)=>{
    const isOwned=owned.includes(sk.id),canBuy=!isOwned&&coins>=sk.price;
    const d=document.createElement("div");d.className="shopcard"+(isOwned?" owned":canBuy?" canbuy":"");
    d.innerHTML=`<div class="sem">${sk.emoji}</div><div class="snm">${sk.name}</div>`+
      (isOwned?`<div class="sown">✓ Eigendom</div>`:`<div class="spr">🪙 ${sk.price}</div><button class="buybtn ${canBuy?"can":"cant"}">${canBuy?"Kopen!":"Te weinig 🪙"}</button>`);
    if(!isOwned&&canBuy)d.querySelector("button").onclick=()=>{coins-=sk.price;owned.push(sk.id);save();updateShopUI();};
    g.appendChild(d);
  });
}
function updateSkinsUI(){
  const g=document.getElementById("skinGrid");g.innerHTML="";
  SKINS.forEach((sk,i)=>{
    const isOwned=owned.includes(sk.id);
    const d=document.createElement("div");
    d.className="card"+(selSkin===i&&isOwned?" sel":"")+(isOwned?"":" locked");
    d.innerHTML=`<div class="em">${sk.emoji}</div><div class="nm">${sk.name}</div>`+
      (!isOwned?`<div class="pr">🪙${sk.price}</div>`:(selSkin===i?`<div class="ok">✓</div>`:""));
    d.onclick=()=>{if(isOwned){selSkin=i;save();updateSkinsUI();}else show("shop");};
    g.appendChild(d);
  });
}
function updateMapsUI(){
  const g=document.getElementById("mapList");g.innerHTML="";
  MAPS.forEach((mp,i)=>{
    const d=document.createElement("div");d.className="card mapcard"+(selMap===i?" sel":"");
    d.innerHTML=`<div class="me">${mp.emoji}</div><div><div class="mn">${mp.name}</div><div class="ms">${mp.stars?"⭐ Sterren ":""}${mp.id==="space"?"🌌 Nevel":mp.id==="candy"?"🌈 Snoep":mp.id==="desert"?"☀️ Zon":mp.id==="jungle"?"🌿 Jungle":""}</div></div>`;
    d.onclick=()=>{selMap=i;save();updateMapsUI();};
    g.appendChild(d);
  });
}

function startGame(){
  curSkin=SKINS[selSkin];curMap=MAPS[selMap];
  G.running=true;G.score=0;G.lives=3;G.fufu=0;G.speed=BASE_SPEED;G.dist=0;
  G.obs=[];G.fufus=[];G.parts=[];G.clouds=makeClouds();
  G.platforms=[];G.nextPlatform=180;G.nextObs=160;G.nextFufu=90;
  touch.jump=false;touch.duck=false;touch._edge=false;_obsToggle=0;
  newP();fr=0;syncHUD();show("playing");
}

// init
show("menu");
</script>
</body>
</html>
