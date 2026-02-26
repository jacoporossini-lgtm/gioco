<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>THE ROOMS — Escape or Die</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=VT323&family=Share+Tech+Mono&display=swap');
:root{
  --blood:#8b0000;--rust:#c0392b;--flicker:#e8c84a;--green:#00ff41;
  --dim:#0a0500;--wall:#1c1208;--floor:#120d06;--near:#e8c84a;
}
*{box-sizing:border-box;margin:0;padding:0;}
html,body{
  background:#000;color:var(--flicker);font-family:'VT323',monospace;font-size:18px;
  overflow:hidden;height:100vh;display:flex;flex-direction:column;
  align-items:center;justify-content:center;image-rendering:pixelated;cursor:crosshair;
}
body::after{
  content:'';position:fixed;inset:0;pointer-events:none;z-index:9999;
  background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,0,0,.16) 2px,rgba(0,0,0,.16) 4px);
}
body::before{
  content:'';position:fixed;inset:0;pointer-events:none;z-index:9998;
  background:radial-gradient(ellipse at center,transparent 55%,rgba(0,0,0,.75)100%);
}
/* ANIMATIONS */
@keyframes flicker{0%,88%,90%,92%,100%{opacity:1}89%{opacity:.8}91%{opacity:.65}}
@keyframes pulse-red{0%,100%{text-shadow:0 0 8px #f00,0 0 20px #8b0000}50%{text-shadow:0 0 22px #f00,0 0 55px #f00,0 0 90px #8b0000}}
@keyframes pulse-green{0%,100%{box-shadow:0 0 8px var(--green)}50%{box-shadow:0 0 24px var(--green),0 0 48px var(--green)}}
@keyframes blink{0%,100%{opacity:1}50%{opacity:0}}
@keyframes shake{0%,100%{transform:translateX(0)}20%{transform:translateX(-6px)}40%{transform:translateX(6px)}60%{transform:translateX(-4px)}80%{transform:translateX(4px)}}
@keyframes slide-in{from{opacity:0;transform:translateY(-10px)}to{opacity:1;transform:translateY(0)}}
@keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-4px)}}
@keyframes near-glow{0%,100%{filter:brightness(1.2) drop-shadow(0 0 6px var(--flicker))}50%{filter:brightness(1.8) drop-shadow(0 0 16px var(--flicker))}}
@keyframes js-flash{0%{opacity:0}10%{opacity:1}80%{opacity:1}100%{opacity:0}}
@keyframes js-face{0%{transform:scale(0.4)}15%{transform:scale(1.05)}25%{transform:scale(0.98)}100%{transform:scale(1)}}
@keyframes blood-fall{from{transform:scaleY(0);transform-origin:top}to{transform:scaleY(1);transform-origin:top}}
/* BUTTON */
.pbtn{
  background:var(--blood);color:var(--flicker);border:none;font-family:'VT323',monospace;
  font-size:22px;padding:9px 28px;cursor:pointer;letter-spacing:3px;
  clip-path:polygon(0 0,calc(100% - 8px) 0,100% 8px,100% 100%,8px 100%,0 calc(100% - 8px));
  transition:background .1s,transform .1s;display:inline-block;
}
.pbtn:hover:not(:disabled){background:var(--rust);transform:scale(1.04);text-shadow:0 0 8px #fff}
.pbtn:disabled{opacity:.3;cursor:not-allowed}
.pbtn.g{background:#060200;border:2px solid #220000;color:#554400;font-size:18px;padding:6px 18px}
.pbtn.g:hover{color:var(--flicker);border-color:var(--flicker);background:#0a0400}
/* SCREEN */
.scr{display:none;flex-direction:column;align-items:center;justify-content:center;width:100%;height:100vh;animation:flicker 12s infinite;}
/* ── INTRO ── */
#si{text-align:center;gap:6px;}
.i-title{font-size:90px;color:var(--rust);animation:pulse-red 2.5s infinite;letter-spacing:14px;line-height:1;}
.i-tag{font-size:19px;color:#2a1500;letter-spacing:7px;font-family:'Share Tech Mono',monospace;margin-bottom:24px;}
.i-quote{max-width:480px;border-left:3px solid var(--blood);background:rgba(139,0,0,.06);padding:14px 20px;font-size:18px;line-height:1.75;color:#c8a04a;text-shadow:0 0 8px rgba(200,160,74,.25);margin-bottom:22px;text-align:left;}
.i-quote .aut{display:block;margin-top:8px;font-size:12px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:3px;}
/* ── DIFF ── */
#sd{gap:14px;}
.d-head{font-size:40px;color:var(--rust);letter-spacing:5px;text-shadow:0 0 15px rgba(192,57,43,.4);}
.d-sub{font-size:13px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:3px;margin-bottom:8px;}
.d-cards{display:flex;gap:16px;flex-wrap:wrap;justify-content:center;}
.dc{background:#06030d;padding:20px 26px;cursor:pointer;border:2px solid #1a0000;min-width:200px;transition:all .15s;clip-path:polygon(0 0,calc(100% - 10px) 0,100% 10px,100% 100%,10px 100%,0 calc(100% - 10px));text-align:left;}
.dc:hover{transform:scale(1.04);}
.dc.n:hover{border-color:var(--flicker);background:rgba(232,200,74,.04)}
.dc.h:hover{border-color:var(--rust);background:rgba(139,0,0,.1)}
.dc .ct{font-size:36px;letter-spacing:4px;margin-bottom:12px;line-height:1;}
.dc.n .ct{color:var(--flicker)}.dc.h .ct{color:var(--rust);text-shadow:0 0 10px #f00}
.dc .cs{font-size:15px;color:#444;font-family:'Share Tech Mono',monospace;margin:4px 0;letter-spacing:1px;}
.dc .cs span{color:var(--flicker)}.dc.h .cs span{color:var(--rust)}
/* ── ROOM COUNT ── */
#src{gap:16px;}
.rc-head{font-size:38px;color:var(--rust);letter-spacing:4px;}
.rc-sub{font-size:13px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:3px;margin-bottom:8px;}
.rc-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;width:360px;}
.rc-btn{
  padding:14px 10px;cursor:pointer;border:2px solid #1a0000;background:#060200;
  font-family:'VT323',monospace;font-size:26px;letter-spacing:3px;color:var(--flicker);
  clip-path:polygon(0 0,calc(100% - 6px) 0,100% 6px,100% 100%,6px 100%,0 calc(100% - 6px));
  transition:all .15s;text-align:center;
}
.rc-btn:hover{border-color:var(--flicker);background:rgba(232,200,74,.06);transform:scale(1.05)}
.rc-btn .rn{font-size:38px;display:block;line-height:1;}
.rc-btn .rs{font-size:12px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:2px;display:block;margin-top:4px;}
.rc-btn.rnd .rn{color:var(--rust);text-shadow:0 0 10px #f00}
/* ── GAME ── */
#sg{justify-content:flex-start;animation:flicker 16s infinite;}
#hud{
  width:100%;max-width:820px;display:grid;grid-template-columns:1fr auto 1fr;
  align-items:center;padding:5px 12px;border-bottom:2px solid #150000;
  background:rgba(0,0,0,.97);flex-shrink:0;
}
#hl{display:flex;flex-direction:column;gap:1px;}
#rname{font-size:20px;color:var(--rust);text-shadow:0 0 8px rgba(192,57,43,.5);letter-spacing:2px;line-height:1;}
#rmeta{font-size:11px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:2px;}
#hc{text-align:center;}
#timer{font-size:40px;color:var(--green);text-shadow:0 0 12px var(--green);font-family:'Share Tech Mono',monospace;letter-spacing:5px;line-height:1;}
#timer.urg{color:var(--rust);text-shadow:0 0 18px #f00;animation:pulse-red .5s infinite;}
#hr{display:flex;flex-direction:column;align-items:flex-end;gap:3px;}
#livd{font-size:19px;letter-spacing:3px;}
.hbtn{font-size:13px;padding:3px 12px;margin:0;letter-spacing:2px;}
/* ROOM WRAPPER */
#rwrap{width:100%;max-width:820px;flex:1;display:flex;flex-direction:column;min-height:0;overflow:hidden;}
/* PIXEL ROOM (viewport) */
#room-vp{
  position:relative;width:100%;height:256px;overflow:hidden;
  border-bottom:4px solid #1a0e00;flex-shrink:0;cursor:crosshair;
}
/* Inner room (1600px wide) */
#room-inner{
  position:absolute;top:0;left:0;width:1600px;height:256px;
  will-change:transform;
}
/* Wall */
#room-inner::before{
  content:'';position:absolute;inset:0;
  background:linear-gradient(to bottom,#1c1208 0%,#1c1208 63%,#120d06 63%),
    repeating-linear-gradient(90deg,transparent,transparent 60px,rgba(255,255,255,.01) 60px,rgba(255,255,255,.01) 61px),
    repeating-linear-gradient(0deg,transparent,transparent 60px,rgba(255,255,255,.01) 60px,rgba(255,255,255,.01) 61px);
  pointer-events:none;z-index:1;
}
#room-inner::after{
  content:'';position:absolute;bottom:0;left:0;right:0;height:90px;
  background:radial-gradient(ellipse at 50% 120%,rgba(100,0,0,.14),transparent 70%);
  pointer-events:none;z-index:1;
}
/* DOOR */
.rdoor{
  position:absolute;width:64px;height:100px;
  background:#3d1f00;border:4px solid #6b3500;border-bottom:none;
  right:50px;bottom:0;z-index:3;transition:all .4s;
}
.rdoor::before{content:'';position:absolute;width:9px;height:9px;background:#8b6914;border-radius:50%;right:7px;top:46%;}
.rdoor.locked{filter:brightness(.3) saturate(.2);}
.rdoor.open{border-color:var(--green);animation:pulse-green 1s infinite;filter:none;}
/* ROOM OBJECTS (clues) */
.robj{
  position:absolute;z-index:4;cursor:pointer;
  display:flex;flex-direction:column;align-items:center;gap:2px;
  transition:filter .2s;
}
.robj .ico{font-size:24px;animation:float 3s ease-in-out infinite;}
.robj .lbl{font-size:10px;color:#3a2000;font-family:'Share Tech Mono',monospace;letter-spacing:1px;}
.robj .ep{
  font-size:13px;color:var(--flicker);letter-spacing:2px;
  font-family:'Share Tech Mono',monospace;
  animation:blink .7s infinite;display:none;
  text-shadow:0 0 8px var(--flicker);
}
.robj.near .ep{display:block;}
.robj.near{animation:near-glow .8s infinite;}
.robj.collected{opacity:.18;pointer-events:none;}
/* PLAYER */
#player{
  position:absolute;bottom:0;z-index:5;
  width:16px;height:0;/* pixel art built with box-shadow */
  will-change:left,transform;
}
/* pixel art character 16×28 (each unit=4px) */
#player::before{
  content:'';display:block;
  width:4px;height:4px;
  box-shadow:
    4px -24px 0 0 #c8a04a, 8px -24px 0 0 #c8a04a,
    0px -20px 0 0 #c8a04a, 4px -20px 0 0 #e8c84a, 8px -20px 0 0 #e8c84a, 12px -20px 0 0 #c8a04a,
    0px -16px 0 0 #e8c84a, 4px -16px 0 0 #f5e070, 8px -16px 0 0 #f5e070, 12px -16px 0 0 #e8c84a,
    0px -12px 0 0 #c8a04a, 4px -12px 0 0 #e8c84a, 8px -12px 0 0 #e8c84a, 12px -12px 0 0 #c8a04a,
    0px  -8px 0 0 #5a1800, 4px  -8px 0 0 #8b2200, 8px  -8px 0 0 #8b2200, 12px  -8px 0 0 #5a1800,
    0px  -4px 0 0 #5a1800, 4px  -4px 0 0 #8b2200, 8px  -4px 0 0 #8b2200, 12px  -4px 0 0 #5a1800,
    0px   0px 0 0 #5a1800, 4px   0px 0 0 #6b2600, 8px   0px 0 0 #6b2600, 12px   0px 0 0 #5a1800;
}
#player::after{
  content:'▼ E';display:none;
  position:absolute;bottom:32px;left:50%;transform:translateX(-50%);
  font-size:11px;color:var(--flicker);letter-spacing:2px;
  font-family:'Share Tech Mono',monospace;animation:blink .7s infinite;
  white-space:nowrap;text-shadow:0 0 6px var(--flicker);
}
#player.near-active::after{display:block;}
/* STATIC ROOM DECO (per room, injected) */
.rdeco{position:absolute;pointer-events:none;z-index:2;}
.r-blood{position:absolute;background:var(--blood);opacity:.55;clip-path:polygon(20% 0%,80% 10%,100% 40%,90% 100%,40% 90%,0% 60%);z-index:2;animation:blood-fall .8s ease-out both;}
.r-chain{position:absolute;width:6px;background:repeating-linear-gradient(180deg,#888 0,#888 6px,#555 6px,#555 12px);z-index:2;}
.r-skull{position:absolute;font-size:24px;z-index:2;filter:drop-shadow(0 0 4px rgba(139,0,0,.6));}
.r-text{position:absolute;font-family:'Share Tech Mono',monospace;z-index:3;line-height:1.9;}
/* CONTROLS BAR */
#ctrl-bar{
  display:flex;align-items:center;gap:8px;padding:4px 12px;
  background:rgba(0,0,0,.98);border-top:1px solid #110000;flex-shrink:0;
  font-size:12px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:1px;
}
.ctrl-k{background:#150000;border:1px solid #330000;padding:1px 6px;color:#554400;font-family:'Share Tech Mono',monospace;font-size:11px;}
/* ENIGMA PANEL */
#ep{
  flex:1;display:flex;flex-direction:column;gap:6px;padding:8px 16px;
  background:rgba(5,2,0,.99);border-top:1px solid #100000;overflow-y:auto;min-height:0;
}
#ep::-webkit-scrollbar{width:3px}#ep::-webkit-scrollbar-thumb{background:var(--blood)}
#ji-line{
  font-size:14px;font-family:'Share Tech Mono',monospace;color:var(--rust);
  border-left:3px solid var(--blood);padding-left:9px;line-height:1.55;
  text-shadow:0 0 8px rgba(192,57,43,.35);animation:slide-in .4s ease;
}
#eq-body{font-size:20px;color:var(--flicker);line-height:1.65;white-space:pre-line;animation:slide-in .5s ease .1s both;}
/* INVENTORY */
#inv-row{
  display:flex;gap:6px;align-items:center;flex-wrap:wrap;
  padding:5px 0;border-top:1px solid #110000;min-height:28px;
}
#inv-label{font-size:12px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:2px;flex-shrink:0;}
.inv-item{
  background:#0a0500;border:1px solid #330000;padding:3px 8px;cursor:pointer;
  font-size:16px;transition:border-color .2s;position:relative;
  clip-path:polygon(0 0,calc(100% - 4px) 0,100% 4px,100% 100%,4px 100%,0 calc(100% - 4px));
}
.inv-item:hover{border-color:var(--flicker);}
/* CLUE POPUP */
#clue-popup{
  display:none;position:fixed;z-index:200;
  background:#050200;border:2px solid var(--flicker);padding:12px 16px;
  max-width:340px;font-size:17px;color:var(--flicker);line-height:1.65;
  box-shadow:0 0 20px rgba(232,200,74,.25);
  clip-path:polygon(0 0,calc(100% - 10px) 0,100% 10px,100% 100%,10px 100%,0 calc(100% - 10px));
}
#clue-popup .cp-close{font-size:13px;color:#554400;cursor:pointer;margin-top:8px;display:block;font-family:'Share Tech Mono',monospace;letter-spacing:2px;}
#clue-popup .cp-close:hover{color:var(--flicker);}
/* ANSWER ROW */
#ans-row{display:flex;gap:8px;align-items:center;flex-wrap:wrap;}
#ans-in{
  background:#050200;border:2px solid #330000;color:var(--green);
  font-family:'VT323',monospace;font-size:28px;padding:4px 12px;outline:none;
  width:200px;letter-spacing:5px;caret-color:var(--green);transition:border-color .2s;
}
#ans-in:focus{border-color:var(--rust);box-shadow:0 0 8px rgba(139,0,0,.3);}
#ans-in.wrong{animation:shake .4s;border-color:#f00;background:rgba(139,0,0,.1);}
#ans-in.ok{border-color:var(--green);background:rgba(0,255,65,.06);}
#fb{font-size:18px;min-height:20px;color:var(--rust);text-shadow:0 0 6px #f00;animation:slide-in .2s ease;}
#fb.ok{color:var(--green);text-shadow:0 0 8px var(--green);}
/* PROGRESS */
#pgbar{width:100%;max-width:820px;height:4px;background:#0a0000;flex-shrink:0;}
#pgfill{height:100%;background:linear-gradient(90deg,var(--blood),var(--rust));transition:width .6s;box-shadow:0 0 5px var(--rust);}
/* ── MAP OVERLAY ── */
#sm{
  position:fixed;inset:0;background:rgba(0,0,0,.97);z-index:400;
  flex-direction:column;align-items:center;justify-content:center;gap:14px;padding:24px;
}
.m-title{font-size:34px;color:var(--rust);letter-spacing:5px;text-shadow:0 0 15px rgba(192,57,43,.4);}
.m-sub{font-size:13px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:3px;text-align:center;max-width:500px;}
#mcorr{display:flex;align-items:center;gap:0;overflow-x:auto;padding:10px 20px;max-width:88vw;}
#mcorr::-webkit-scrollbar{height:3px}#mcorr::-webkit-scrollbar-thumb{background:var(--blood)}
.mn{display:flex;flex-direction:column;align-items:center;gap:4px;flex-shrink:0;}
.mn-d{
  width:52px;height:72px;border:2px solid;display:flex;flex-direction:column;
  align-items:center;justify-content:center;gap:4px;
  clip-path:polygon(8% 0%,92% 0%,100% 8%,100% 100%,0 100%,0 8%);
}
.mn.done .mn-d{border-color:#004400;background:#001200;color:var(--green);}
.mn.curr .mn-d{border-color:var(--flicker);background:rgba(232,200,74,.07);color:var(--flicker);animation:door-glow 1.2s infinite;}
@keyframes door-glow{0%,100%{border-color:var(--flicker);box-shadow:0 0 8px var(--flicker);}50%{border-color:#fff;box-shadow:0 0 24px var(--flicker);}}
.mn.fog .mn-d{border-color:#100500;background:#030000;color:#1a0000;}
.mn-ico{font-size:20px;}.mn-lbl{font-size:11px;font-family:'Share Tech Mono',monospace;letter-spacing:1px;}.mn.done .mn-lbl{color:#004400}.mn.curr .mn-lbl{color:var(--flicker)}.mn.fog .mn-lbl{color:#150000}
.mc{width:28px;height:3px;flex-shrink:0;margin-top:-36px;}
.mc.done{background:#004400}.mc.curr{background:#331500}.mc.fog{background:#0a0000}
.mfog{
  width:90px;height:72px;background:linear-gradient(90deg,#0d0000,#000);
  display:flex;align-items:center;justify-content:center;font-size:20px;
  color:#1a0000;letter-spacing:5px;flex-shrink:0;margin-top:-36px;
  clip-path:polygon(8% 0%,92% 0%,100% 8%,100% 100%,0 100%,0 8%);
}
/* ── END SCREEN ── */
#se{
  position:fixed;inset:0;background:rgba(0,0,0,.96);z-index:500;
  flex-direction:column;align-items:center;justify-content:center;
  text-align:center;gap:10px;padding:28px;
}
#et{font-size:80px;letter-spacing:8px;line-height:1;}
#et.d{color:var(--rust);animation:pulse-red 1s infinite;}
#et.w{color:var(--green);text-shadow:0 0 40px var(--green),0 0 80px rgba(0,255,65,.25);}
#em{font-size:19px;color:#666;max-width:500px;line-height:1.8;}
#estats{font-size:14px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:2px;border-top:1px solid #100000;padding-top:8px;max-width:480px;}
.ebtns{display:flex;gap:10px;flex-wrap:wrap;justify-content:center;margin-top:6px;}
/* ── JUMPSCARE ── */
#jsc{
  display:none;position:fixed;inset:0;z-index:9990;
  background:#8b0000;align-items:center;justify-content:center;
  animation:js-flash .7s ease-out both;
}
.jsface{animation:js-face .7s ease-out;position:relative;width:280px;height:280px;}
/* pixel face using CSS box-shadow */
.jf-base{
  position:absolute;top:50%;left:50%;
  width:4px;height:4px;transform:translate(-50%,-50%);
  box-shadow:
    /* outline */
    8px -56px 0 0 #fff,12px -56px 0 0 #fff,16px -56px 0 0 #fff,20px -56px 0 0 #fff,24px -56px 0 0 #fff,28px -56px 0 0 #fff,32px -56px 0 0 #fff,36px -56px 0 0 #fff,
    4px -52px 0 0 #fff,40px -52px 0 0 #fff,
    0px -48px 0 0 #fff,44px -48px 0 0 #fff,
    0px -44px 0 0 #fff,44px -44px 0 0 #fff,
    /* left eye */
    8px -44px 0 0 #ff0000,12px -44px 0 0 #ff0000,
    8px -40px 0 0 #ff0000,12px -40px 0 0 #ff0000,
    /* right eye */
    32px -44px 0 0 #ff0000,36px -44px 0 0 #ff0000,
    32px -40px 0 0 #ff0000,36px -40px 0 0 #ff0000,
    /* nose */
    20px -36px 0 0 #fff,24px -36px 0 0 #fff,
    /* mouth teeth */
    8px -28px 0 0 #fff,16px -28px 0 0 #fff,24px -28px 0 0 #fff,32px -28px 0 0 #fff,
    4px -24px 0 0 #fff,12px -24px 0 0 #fff,20px -24px 0 0 #fff,28px -24px 0 0 #fff,36px -24px 0 0 #fff,
    4px -20px 0 0 #fff,40px -20px 0 0 #fff,
    8px -16px 0 0 #fff,12px -16px 0 0 #fff,16px -16px 0 0 #fff,20px -16px 0 0 #fff,
    24px -16px 0 0 #fff,28px -16px 0 0 #fff,32px -16px 0 0 #fff,36px -16px 0 0 #fff,
    /* cracks / blood drips */
    4px -48px 0 0 #660000,40px -44px 0 0 #660000,
    20px -52px 0 0 #660000,
    16px -12px 0 0 #880000,28px -12px 0 0 #880000,
    16px -8px 0 0 #880000,28px -8px 0 0 #880000,
    20px -4px 0 0 #880000;
}
.jsc-txt{
  position:absolute;bottom:40px;font-size:36px;letter-spacing:6px;
  color:#fff;text-shadow:0 0 20px #fff;font-family:'VT323',monospace;
  animation:blink .2s infinite;
}
</style>
</head>
<body>
<!-- INTRO -->
<div id="si" class="scr" style="display:flex">
  <div class="i-title">THE ROOMS</div>
  <div class="i-tag">ESCAPE OR DIE TRYING</div>
  <div class="i-quote">
    "Voglio farti giocare a un gioco. Ogni stanza nasconde un segreto
    che solo i tuoi occhi possono trovare. La risposta non ti verrà data —
    dovrai guadagnartela, metro per metro.<br>
    <span style="color:var(--rust)">Hai ancora voglia di vivere?</span>"
    <span class="aut">— L'ENIGMISTA</span>
  </div>
  <button class="pbtn" onclick="nav('sd')">▶ &nbsp;INIZIA IL GIOCO</button>
</div>
<!-- DIFFICULTY -->
<div id="sd" class="scr">
  <div class="d-head">SCEGLI IL TUO DESTINO</div>
  <div class="d-sub">LA SCELTA RIVELA CHI SEI</div>
  <div class="d-cards">
    <div class="dc n" onclick="G.hard=false;nav('src')">
      <div class="ct">NORMALE</div>
      <div class="cs">❤ Vite: <span>3</span></div>
      <div class="cs">⏱ Tempo: <span>Standard</span></div>
      <div class="cs">💡 Indizi: <span>Sì (−1 vita)</span></div>
      <div class="cs">🚪 Stanze: <span>???</span></div>
    </div>
    <div class="dc h" onclick="G.hard=true;nav('src')">
      <div class="ct">DIFFICILE</div>
      <div class="cs">❤ Vite: <span>1</span></div>
      <div class="cs">⏱ Tempo: <span>−40%</span></div>
      <div class="cs">💡 Indizi: <span>NESSUNO</span></div>
      <div class="cs">🚪 Stanze: <span>???</span></div>
    </div>
  </div>
  <button class="pbtn g" style="margin-top:8px" onclick="nav('si')">← INDIETRO</button>
</div>
<!-- ROOM COUNT -->
<div id="src" class="scr">
  <div class="rc-head">QUANTE STANZE?</div>
  <div class="rc-sub">SCEGLI LA TUA CONDANNA</div>
  <div class="rc-grid">
    <div class="rc-btn" onclick="startGame(5)"><span class="rn">5</span><span class="rs">STANZE</span></div>
    <div class="rc-btn" onclick="startGame(10)"><span class="rn">10</span><span class="rs">STANZE</span></div>
    <div class="rc-btn" onclick="startGame(15)"><span class="rn">15</span><span class="rs">STANZE</span></div>
    <div class="rc-btn" onclick="startGame(20)"><span class="rn">20</span><span class="rs">STANZE</span></div>
    <div class="rc-btn" onclick="startGame(25)"><span class="rn">25</span><span class="rs">STANZE</span></div>
    <div class="rc-btn rnd" onclick="startGame(0)"><span class="rn">???</span><span class="rs">CASUALE</span></div>
  </div>
  <button class="pbtn g" style="margin-top:10px" onclick="nav('sd')">← INDIETRO</button>
</div>
<!-- GAME -->
<div id="sg" class="scr">
  <div id="hud">
    <div id="hl"><div id="rname">—</div><div id="rmeta">—</div></div>
    <div id="hc"><div id="timer">00:00</div></div>
    <div id="hr">
      <div id="livd">—</div>
      <button class="pbtn g hbtn" onclick="toggleMap()">🗺 MAPPA</button>
    </div>
  </div>
  <div id="rwrap">
    <div id="room-vp">
      <div id="room-inner">
        <div id="player"></div>
      </div>
    </div>
    <div id="ctrl-bar">
      <span class="ctrl-k">A/←</span>MUOVI <span class="ctrl-k">D/→</span>MUOVI
      <span style="color:#330000">|</span>
      <span class="ctrl-k">E</span>RACCOGLI INDIZIO
      <span style="color:#330000">|</span>
      <span class="ctrl-k">INVIO</span>CONFERMA RISPOSTA
      <span style="color:#330000;margin-left:auto" id="near-hint">—</span>
    </div>
    <div id="ep">
      <div id="ji-line"></div>
      <div id="eq-body"></div>
      <div id="inv-row"><span id="inv-label">INVENTARIO:</span></div>
      <div id="ans-row">
        <input id="ans-in" type="text" placeholder="RISPOSTA..." autocomplete="off" maxlength="30"
          onkeydown="if(event.key==='Enter')checkAns()">
        <button class="pbtn" style="font-size:20px;padding:6px 22px" onclick="checkAns()">▶ CONFERMA</button>
        <button class="pbtn g" id="hintbtn" onclick="useHint()" style="font-size:16px;padding:6px 14px">💀 INDIZIO</button>
      </div>
      <div id="fb"></div>
    </div>
  </div>
  <div id="pgbar"><div id="pgfill" style="width:0%"></div></div>
</div>
<!-- MAP -->
<div id="sm" class="scr">
  <div class="m-title">🗺 &nbsp;MAPPA DELLA PRIGIONE</div>
  <div class="m-sub" id="msub">—</div>
  <div id="mcorr"></div>
  <button class="pbtn g" onclick="toggleMap()">✕ &nbsp;CHIUDI</button>
</div>
<!-- END -->
<div id="se" class="scr">
  <div id="et"></div>
  <div id="em"></div>
  <div id="estats"></div>
  <div class="ebtns">
    <button class="pbtn" onclick="resetGame()">↩ &nbsp;RICOMINCIA</button>
    <button class="pbtn g" onclick="nav('sd')">⚙ &nbsp;DIFFICOLTÀ</button>
  </div>
</div>
<!-- JUMPSCARE -->
<div id="jsc">
  <div class="jsface"><div class="jf-base"></div></div>
  <div class="jsc-txt">SBAGLIATO</div>
</div>
<!-- CLUE POPUP -->
<div id="clue-popup">
  <div id="cp-text"></div>
  <span class="cp-close" onclick="closeCluePopup()">[ CHIUDI ]</span>
</div>
<script>
/* ════════════════════════════════════
   ENIGMA POOL — 100 ENIGMI
   Ogni enigma ha:
   - id,ti(title),ty(type),ji(jigsaw quote)
   - q (domanda — SOLO la domanda, niente risposte)
   - a (risposta normalizzata uppercase)
   - cl: array di oggetti-indizio da posizionare nella stanza
     {i:icona, t:testo indizio, x:posizione px nella stanza}
════════════════════════════════════ */
const POOL=[
/* ── CATEGORIA: SOMMA (trova numeri, sommali) ──────────────────── */
{id:'s01',ti:'LA SOMMA PERDUTA',ty:'CALCOLO',ji:'"I numeri parlano. Tu devi solo saperli leggere."',
q:'Trova tutti i numeri nascosti nella stanza e sommali.\nQual è il risultato?',a:'21',
cl:[{i:'📝',t:'Nota sgualcita:\nROMAN: VII',x:120},{i:'📜',t:'Iscrizione sul muro:\nIl doppio di quattro.',x:500},{i:'🔢',t:'Graffio sul pavimento:\n6 segni verticali',x:1050}]},

{id:'s02',ti:'CONTAR LE OSSA',ty:'CALCOLO',ji:'"Conta i morti. Non per pietà — per la risposta."',
q:'Quante ossa in totale?\nSomma le quantità trovate.',a:'18',
cl:[{i:'💀',t:'Primo mucchio:\n3 × 2 ossa',x:180},{i:'💀',t:'Secondo mucchio:\nMetà di dodici',x:620},{i:'💀',t:'Terzo mucchio:\nXII ÷ III',x:1180}]},

{id:'s03',ti:'IL CONTO DEI SECOLI',ty:'CALCOLO',ji:'"Il tempo è il tuo nemico. Anche in questo calcolo."',
q:'Somma tutti i valori temporali trovati.\nEsprimi il risultato in anni.',a:'25',
cl:[{i:'📖',t:'Pagina strappata:\nDECANNIO + DECANNIO',x:200},{i:'📝',t:'Scritta col sangue:\nMEZZO LUSTRO (= 2.5)',x:680},{i:'🗝️',t:'Inciso sulla chiave:\n1 decennio + 2.5',x:1200}]},

{id:'s04',ti:'FRAMMENTI NUMERICI',ty:'CALCOLO',ji:'"Tre pezzi. Un solo numero intero. Trovalo."',
q:'Raccogli i tre frammenti numerici.\nSommali per ottenere la risposta.',a:'30',
cl:[{i:'📦',t:'Dentro la scatola:\nIl prodotto di 3 × 4',x:150},{i:'📜',t:'Nota cifrata:\n2² + 2',x:540},{i:'🔢',t:'Sul muro:\n3³ − 24 + 3',x:1300}]},

{id:'s05',ti:'SANGUE E CIFRE',ty:'CALCOLO',ji:'"Ogni goccia di sangue ha un peso. E un numero."',
q:'Interpreta i simboli di sangue.\nQual è la loro somma?',a:'13',
cl:[{i:'🩸',t:'Prima macchia:\n♦♦♦♦♦ (conta i diamanti)',x:200},{i:'🩸',t:'Seconda macchia:\nXXXXX = ?',x:700},{i:'🩸',t:'Terza macchia:\n1+2 (scritta sbiadita)',x:1100}]},

{id:'s06',ti:'LA TRIADE NUMERICA',ty:'CALCOLO',ji:'"Tre voci. Un solo responso."',
q:'Trova le tre quantità e moltiplicale tra loro.',a:'24',
cl:[{i:'📝',t:'Numero scritto al contrario:\nDUE',x:140},{i:'📖',t:'Strappato da un calendario:\nMesi di un trimestre',x:600},{i:'🔢',t:'Inciso:\nIV',x:1150}]},

{id:'s07',ti:'IL CODICE ARITMETICO',ty:'CALCOLO',ji:'"Senza matematica, questa porta non aprirà mai."',
q:'Esegui le operazioni trovate in sequenza.\nQual è il risultato finale?',a:'11',
cl:[{i:'📝',t:'Prima operazione:\n20 ÷ 4',x:160},{i:'📜',t:'Seconda operazione:\n× 3',x:550},{i:'🔢',t:'Terza operazione:\n− 4',x:1050}]},

{id:'s08',ti:'NUMERI NELL\'OMBRA',ty:'CALCOLO',ji:'"L\'ombra non nasconde tutto. Guarda più attentamente."',
q:'Decifra i valori nascosti e sottrali in ordine.',a:'7',
cl:[{i:'📦',t:'Dentro la cassa:\nIl cubo di 3 = ???',x:180},{i:'📜',t:'Sotto la cassa:\n− (3+4+5+6)',x:700},{i:'📝',t:'Nota:\nrisultato = ???',x:1100}]},

{id:'s09',ti:'LA MOLTIPLICAZIONE PERDUTA',ty:'CALCOLO',ji:'"Il prodotto dei tuoi errori ti condanna. Il prodotto di questi numeri ti libera."',
q:'Trova i due numeri nascosti e moltiplicali.',a:'42',
cl:[{i:'🔢',t:'Sul pavimento:\nSEI × SETTE',x:300},{i:'📝',t:'Nota di conferma:\nIl risultato è (20+22)',x:800}]},

{id:'s10',ti:'DIVISIONE FINALE',ty:'CALCOLO',ji:'"Dividi per conquistare. La regola vale anche qui."',
q:'Applica le divisioni trovate nell\'ordine corretto.',a:'5',
cl:[{i:'📜',t:'Primo:\n100',x:120},{i:'📦',t:'Secondo:\n÷ 4',x:480},{i:'📝',t:'Terzo:\n÷ 5',x:850},{i:'🔢',t:'Check:\n= V × I',x:1250}]},

/* ── CATEGORIA: PAROLA (trova lettere, forma parola) ───────────── */
{id:'p01',ti:'LA PAROLA SPEZZATA',ty:'LINGUISTICA',ji:'"Le parole possono essere spezzate. Ma non possono essere distrutte."',
q:'Raccogli le lettere sparse nella stanza.\nForma la parola nell\'ordine corretto.',a:'MORTE',
cl:[{i:'📝',t:'Prima lettera:\nL\'iniziale di Mela',x:130},{i:'📜',t:'Seconda:\nLettera 15 dell\'alfabeto',x:440},{i:'📦',t:'Terza:\nR (scritta al contrario: ᴚ)',x:780},{i:'🔢',t:'Ultime due:\nT ed E (incise insieme)',x:1200}]},

{id:'p02',ti:'L\'ALFABETO NASCOSTO',ty:'LINGUISTICA',ji:'"Le lettere non mentono. Solo gli uomini mentono."',
q:'Le lettere sui muri formano una parola italiana.\nCosa dicono nell\'ordine da sinistra a destra?',a:'BUIO',
cl:[{i:'📝',t:'Prima lettera:\nLettera 2 dell\'alfabeto',x:100},{i:'📜',t:'Seconda:\nU (scritta col sangue)',x:450},{i:'💀',t:'Terza:\nI (intagliata nel teschio)',x:800},{i:'📦',t:'Ultima:\nLettera che precede P',x:1300}]},

{id:'p03',ti:'IL MESSAGGIO FRAMMENTATO',ty:'LINGUISTICA',ji:'"Ogni frammento ha senso. Insieme hanno potere."',
q:'Assembla i frammenti nell\'ordine trovato.\nQuale parola formano?',a:'FERRO',
cl:[{i:'📦',t:'Frammento 1:\nFE (iniziale elemento chimico Fe)',x:120},{i:'📝',t:'Frammento 2:\nR (terza consonante italiana)',x:500},{i:'📜',t:'Frammento 3:\nRO (rima con ORO)',x:950},{i:'🔢',t:'Verifica:\nMetallo con cui si forgiavano le catene',x:1350}]},

{id:'p04',ti:'INIZIALI MALEDETTE',ty:'LINGUISTICA',ji:'"Guarda cosa hai davanti. Non ciò che è scritto — ciò che inizia."',
q:'Prendi la prima lettera di ogni oggetto disegnato sui muri.\nQueste lettere formano una parola.',a:'LAMA',
cl:[{i:'📝',t:'Sul muro a sinistra:\nDisegno di una LUCERTOLA',x:150},{i:'📜',t:'Sul muro centrale:\nDisegno di un ANELLO',x:520},{i:'💀',t:'Sul muro:\nDisegno di una MACCHIA',x:880},{i:'📦',t:'Sul muro destra:\nDisegno di un\'ASCIA',x:1250}]},

{id:'p05',ti:'LETTERE ROVESCHE',ty:'LINGUISTICA',ji:'"Capovolgi la tua prospettiva. La risposta è già davanti a te."',
q:'Ogni lettera è scritta al contrario.\nRibaltale e leggi la parola.',a:'VITA',
cl:[{i:'🔢',t:'⊥IΛ∀ (ribalta ognuna)',x:200},{i:'📝',t:'Conferma:\nAnagramma di VITA',x:700}]},

{id:'p06',ti:'IL CODICE ALFABETICO',ty:'LINGUISTICA',ji:'"A=1, B=2... il codice più antico del mondo."',
q:'Converti i numeri nelle lettere corrispondenti.\nA=1, B=2, C=3... Z=26',a:'LUCE',
cl:[{i:'📝',t:'Numeri sul pavimento:\n12 — 21 — 3 — 5',x:160},{i:'📜',t:'Schema:\nA=1 B=2 C=3... Z=26\n(usa questa mappa)',x:700}]},

{id:'p07',ti:'IL NOME SEGRETO',ty:'LINGUISTICA',ji:'"Ogni cosa ha un nome. Sai trovarlo?"',
q:'Decodifica il nome usando lo schema trovato nella stanza.',a:'OSSA',
cl:[{i:'📦',t:'Schema numero-lettera:\nO=15, S=19, A=1',x:200},{i:'📝',t:'Sequenza da decodificare:\n15 — 19 — 19 — 1',x:750}]},

{id:'p08',ti:'LA PAROLA SPECCHIATA',ty:'LINGUISTICA',ji:'"Lo specchio rivela la verità a chi sa guardare."',
q:'La parola è riflessa nello specchio.\nCosa dice se la leggi normalmente?',a:'ANIMA',
cl:[{i:'📦',t:'Nello specchio appannato:\nAMINA (riflesso)',x:180},{i:'📝',t:'Nota sotto lo specchio:\nRibalta la sequenza delle lettere',x:750}]},

{id:'p09',ti:'FRAMMENTI DI LINGUA',ty:'LINGUISTICA',ji:'"La lingua italiana nasconde segreti profondi."',
q:'Unisci i due frammenti di parola trovati.\nForma una parola italiana.',a:'CATENA',
cl:[{i:'📝',t:'Primo frammento:\nCATE (scritto sul pavimento)',x:200},{i:'📜',t:'Secondo frammento:\nNA (graffiato sul muro)',x:850}]},

{id:'p10',ti:'L\'ULTIMA PAROLA',ty:'LINGUISTICA',ji:'"Tutti hanno un\'ultima parola. Questa è la tua."',
q:'Trova le lettere nell\'ordine in cui sono posizionate nella stanza\n(da sinistra a destra).',a:'FUGA',
cl:[{i:'📝',t:'Lettera a sinistra:\nF',x:100},{i:'📦',t:'Lettera centrale-sx:\nU',x:450},{i:'💀',t:'Lettera centrale-dx:\nG',x:900},{i:'📜',t:'Lettera a destra:\nA',x:1350}]},

/* ── CATEGORIA: INDOVINELLO ─────────────────────────────────────── */
{id:'i01',ti:'LA VOCE DEL SILENZIO',ty:'INDOVINELLO',ji:'"Esiste. Non si tocca. Scompare al primo suono."',
q:'Gli indizi nella stanza descrivono qualcosa.\nIdentifica di cosa si tratta.',a:'SILENZIO',
cl:[{i:'📝',t:'"Più lo dici, meno c\'è."',x:150},{i:'📜',t:'"Regna dopo l\'ultimo urlo."',x:600},{i:'📦',t:'"Non ha corpo. Non ha voce.\nEppure puoi sentirlo."',x:1100}]},

{id:'i02',ti:'IL PARADOSSO DELL\'OMBRA',ty:'INDOVINELLO',ji:'"Segue ogni uomo. Non ha volontà propria."',
q:'Cosa descrivono gli indizi nella stanza?',a:'OMBRA',
cl:[{i:'📝',t:'"Ti segue sempre. Non puoi\ncatturarla. Scompare al buio."',x:180},{i:'📜',t:'"Cresce quando il sole è basso.\nSi accorcia a mezzogiorno."',x:700}]},

{id:'i03',ti:'IL DONO DELLA TERRA',ty:'INDOVINELLO',ji:'"Ha radici ma non è un albero. Si nutre ma non mangia."',
q:'Cosa descrivono gli indizi sparsi?',a:'FIUME',
cl:[{i:'📝',t:'"Ha una bocca ma non parla.\nHa un letto ma non dorme."',x:200},{i:'📜',t:'"Scorre dall\'alba dei tempi.\nPorta tutto al mare."',x:700},{i:'📦',t:'"Ha sponde ma non è una banca."',x:1150}]},

{id:'i04',ti:'IL TRADITORE FEDELE',ty:'INDOVINELLO',ji:'"Rivela ciò che non vorresti. Non può mentire."',
q:'Cosa stai cercando? Gli indizi lo descrivono.',a:'SPECCHIO',
cl:[{i:'📝',t:'"Mostra il tuo volto.\nNon dice mai bugie."',x:160},{i:'📦',t:'"Ci guardi dentro ogni mattina.\nVedi ciò che altri vedono."',x:680},{i:'📜',t:'"Si rompe in sette anni\ndi sfortuna, dicono."',x:1200}]},

{id:'i05',ti:'IL TEMPO CHE NON TORNA',ty:'INDOVINELLO',ji:'"L\'ho già visto passare. Anche te passerò."',
q:'Cosa descrivono le note trovate?',a:'TEMPO',
cl:[{i:'📝',t:'"Tutti lo cercano.\nNessuno lo possiede."',x:200},{i:'📜',t:'"Si misura in ore,\nma non ha forma."',x:700},{i:'💀',t:'"Logora le pietre,\ncurva i vecchi,\nspegne i giovani."',x:1150}]},

{id:'i06',ti:'L\'ARMA INVISIBILE',ty:'INDOVINELLO',ji:'"Può uccidere senza toccare. Può salvare senza mani."',
q:'Cosa descrivono le scritte sui muri?',a:'PAROLA',
cl:[{i:'📝',t:'"Più tagliente di una lama.\nPiù dolce del miele."',x:150},{i:'📜',t:'"Si lancia senza braccia.\nFerisce senza denti."',x:620},{i:'📦',t:'"Vola di bocca in bocca\nfinché non trova un cuore."',x:1200}]},

{id:'i07',ti:'IL CUSTODE DEL PASSATO',ty:'INDOVINELLO',ji:'"Porta tutto ciò che fu. Non dimentica mai."',
q:'Cosa descrivono gli oggetti trovati?',a:'MEMORIA',
cl:[{i:'📦',t:'"Tiene i morti vivi.\nNon ha peso ma occupa spazio."',x:180},{i:'📝',t:'"Puoi perderla.\nNon puoi comprarla di nuovo."',x:700},{i:'📜',t:'"Il suo opposto è\nl\'oblio."',x:1200}]},

{id:'i08',ti:'LA SOGLIA',ty:'INDOVINELLO',ji:'"Ogni fine è un inizio. Ogni inizio è una fine."',
q:'Cosa descrivono queste metafore?',a:'PORTA',
cl:[{i:'📝',t:'"Separa due mondi.\nHa un solo segreto: la chiave."',x:200},{i:'📜',t:'"Può aprirti la libertà\no imprigionarti per sempre."',x:720},{i:'💀',t:'"In questa stanza,\nè l\'unica via d\'uscita."',x:1150}]},

{id:'i09',ti:'IL VUOTO CHE PESA',ty:'INDOVINELLO',ji:'"Pesa quanto il mondo ma non ha massa."',
q:'Cosa descrivono queste note?',a:'PAURA',
cl:[{i:'📝',t:'"Ti paralizza.\nSi nutre dei tuoi pensieri."',x:160},{i:'📦',t:'"È nell\'oscurità.\nÈ nel silenzio.\nÈ adesso."',x:600},{i:'📜',t:'"Il coraggio non è\nla sua assenza — è\nmuoversi comunque."',x:1100}]},

{id:'i10',ti:'LA FIAMMA CHE CONSUMA',ty:'INDOVINELLO',ji:'"Vive mangiando. Muore bevendo."',
q:'Cosa descrivono questi indizi?',a:'FUOCO',
cl:[{i:'📝',t:'"Dà luce. Dà calore.\nPrende tutto."',x:200},{i:'📜',t:'"L\'acqua lo uccide.\nL\'aria lo nutre."',x:700},{i:'💀',t:'"Ballerino senza corpo.\nDevoratore senza denti."',x:1200}]},

/* ── CATEGORIA: SEQUENZA ────────────────────────────────────────── */
{id:'q01',ti:'LA SERIE CONTINUA',ty:'SEQUENZA',ji:'"Le cose seguono una legge. Sempre."',
q:'Studia la sequenza numerica trovata.\nQual è il numero mancante (indicato con ?).',a:'36',
cl:[{i:'📝',t:'Sequenza:\n4 — 9 — 16 — 25 — ?',x:200},{i:'📜',t:'Regola incisa:\nn² (quadrati perfetti\nn=2,3,4,5,...)',x:800}]},

{id:'q02',ti:'I GRADINI DEL CONDANNATO',ty:'SEQUENZA',ji:'"Ogni passo verso la risposta è un passo verso la libertà."',
q:'Quale numero completa la sequenza?',a:'55',
cl:[{i:'📝',t:'Serie:\n1 — 1 — 2 — 3 — 5 — 8\n— 13 — 21 — 34 — ?',x:180},{i:'📜',t:'Regola:\nOgni numero = somma\ndei due precedenti',x:850}]},

{id:'q03',ti:'I MULTIPLI DEL TERRORE',ty:'SEQUENZA',ji:'"La matematica non perdona chi la ignora."',
q:'Completa la sequenza trovata.',a:'48',
cl:[{i:'📝',t:'Serie:\n3 — 6 — 12 — 24 — ?',x:200},{i:'📜',t:'Regola:\nomni termine × 2',x:750}]},

{id:'q04',ti:'LA DISCESA',ty:'SEQUENZA',ji:'"Non tutto sale. Alcune cose scendono. Inevitabilmente."',
q:'Quale numero viene dopo nella sequenza?',a:'2',
cl:[{i:'📝',t:'Serie:\n162 — 54 — 18 — 6 — ?',x:200},{i:'📜',t:'Ogni termine: ÷ 3',x:750}]},

{id:'q05',ti:'I NUMERI PRIMI',ty:'SEQUENZA',ji:'"Divisibili solo per se stessi. Come me."',
q:'Qual è il prossimo numero primo nella sequenza?',a:'23',
cl:[{i:'📝',t:'Sequenza di numeri primi:\n11 — 13 — 17 — 19 — ?',x:200},{i:'📜',t:'I numeri primi non\nsono divisibili per nessun\naltro numero eccetto 1 e sé stessi.',x:800}]},

{id:'q06',ti:'TRIANGOLI DI PASCAL',ty:'SEQUENZA',ji:'"Il triangolo di Pascal non mente mai."',
q:'Quale numero completa la riga del triangolo?',a:'15',
cl:[{i:'📝',t:'Riga del triangolo:\n1 — 5 — 10 — 10 — 5 — 1\n\nRiga successiva:\n1 — 6 — ? — 20 — 15 — 6 — 1',x:200},{i:'📜',t:'Regola: ogni numero\n= somma dei due\nsopra di lui',x:900}]},

{id:'q07',ti:'LE POTENZE DEL MALE',ty:'SEQUENZA',ji:'"Il potere cresce in modo esponenziale. Come la disperazione."',
q:'Qual è il prossimo valore?',a:'243',
cl:[{i:'📝',t:'Serie:\n1 — 3 — 9 — 27 — 81 — ?',x:200},{i:'📜',t:'Ogni termine:\n× 3 (potenze di 3)',x:750}]},

{id:'q08',ti:'LA SOMMA PROGRESSIVA',ty:'SEQUENZA',ji:'"Addizione dopo addizione. Dove finisce?"',
q:'Qual è il termine successivo?',a:'21',
cl:[{i:'📝',t:'Serie:\n1 — 3 — 6 — 10 — 15 — ?',x:200},{i:'📜',t:'Ogni differenza +1:\n+2 +3 +4 +5 +6...\n(numeri triangolari)',x:800}]},

{id:'q09',ti:'I CUBI NELLA POLVERE',ty:'SEQUENZA',ji:'"Tre dimensioni. Una sola risposta."',
q:'Completa con il numero mancante.',a:'125',
cl:[{i:'📝',t:'Serie:\n1 — 8 — 27 — 64 — ?',x:200},{i:'📜',t:'n³ (cubi perfetti)\n1³ 2³ 3³ 4³ 5³',x:750}]},

{id:'q10',ti:'LA SERIE ALTERNATA',ty:'SEQUENZA',ji:'"Pari e dispari si alternano. Come vita e morte."',
q:'Qual è il prossimo numero?',a:'13',
cl:[{i:'📝',t:'Serie:\n2 — 3 — 5 — 8 — ?',x:200},{i:'📜',t:'Regola:\n+1 +2 +3 +4 +5\n(differenze crescenti)',x:750}]},

/* ── CATEGORIA: CODICE CIFRATO ──────────────────────────────────── */
{id:'c01',ti:'IL CIFRARIO DI CESARE',ty:'CRITTOGRAFIA',ji:'"Giulio Cesare proteggeva i segreti con lo spostamento. Io pure."',
q:'Usa la chiave trovata per decodificare il messaggio.',a:'SANGUE',
cl:[{i:'🔑',t:'CHIAVE:\nSposta ogni lettera\nINDIETRO di 3 posizioni',x:150},{i:'📝',t:'MESSAGGIO CIFRATO:\nVDQJXH',x:800}]},

{id:'c02',ti:'IL ROVESCIO',ty:'CRITTOGRAFIA',ji:'"La verità è sempre capovolta."',
q:'Il messaggio è stato scritto al contrario.\nLeggilo nel verso giusto.',a:'TRAPPOLA',
cl:[{i:'📝',t:'ALOPART\n(leggi al contrario)',x:300},{i:'📜',t:'Conferma: inizia per T,\nfinisce per A, 8 lettere',x:900}]},

{id:'c03',ti:'I NUMERI ROMANI NASCOSTI',ty:'CRITTOGRAFIA',ji:'"I Romani sapevano come codificare i segreti."',
q:'Converti i numeri romani trovati in cifre arabe.\nForma il codice a 4 cifre.',a:'3847',
cl:[{i:'📝',t:'1° cifra: III',x:120},{i:'📜',t:'2° cifra: VIII',x:450},{i:'📦',t:'3° cifra: IV',x:800},{i:'🔢',t:'4° cifra: VII',x:1200}]},

{id:'c04',ti:'IL CODICE A SPECCHIO',ty:'CRITTOGRAFIA',ji:'"Rifletti. Letteralmente."',
q:'Ogni lettera è stata sostituita dalla sua\n"speculare" nell\'alfabeto (A↔Z, B↔Y, C↔X...).\nDecodifica la parola.',a:'CHIAVE',
cl:[{i:'📝',t:'CODIFICATA:\nXSRAEV',x:200},{i:'📜',t:'Schema speculare:\nA=Z B=Y C=X D=W\nE=V F=U G=T H=S\nI=R J=Q K=P L=O\nM=N',x:800}]},

{id:'c05',ti:'IL CODICE MORSE DEL CONDANNATO',ty:'CRITTOGRAFIA',ji:'"Anche nel buio totale, il codice sopravvive."',
q:'Decodifica il messaggio Morse trovato.',a:'SOS',
cl:[{i:'📝',t:'Messaggio:\n··· — ·  ··· — ·  ··· — ·',x:180},{i:'📜',t:'S = ···\nO = ———\n(codice Morse base)',x:750}]},

{id:'c06',ti:'LA SOSTITUZIONE',ty:'CRITTOGRAFIA',ji:'"Ogni simbolo nasconde una lettera. Ogni lettera nasconde una parola."',
q:'Usa la chiave trovata per decodificare il simbolo-messaggio.',a:'NERO',
cl:[{i:'🔑',t:'CHIAVE TROVATA:\n★=N ☾=E ♦=R ✕=O',x:150},{i:'📝',t:'MESSAGGIO:\n★ ☾ ♦ ✕',x:800}]},

{id:'c07',ti:'IL CODICE BINARIO',ty:'CRITTOGRAFIA',ji:'"Le macchine parlano così. Impara la loro lingua."',
q:'Converti il numero binario in decimale.',a:'42',
cl:[{i:'📝',t:'Numero binario:\n101010',x:200},{i:'📜',t:'Posizioni (da destra):\n1,2,4,8,16,32\n(ogni 1 = aggiungi quel valore)',x:800}]},

{id:'c08',ti:'LE INIZIALI DEL MALE',ty:'CRITTOGRAFIA',ji:'"Le iniziali dicono tutto. Se sai leggerle."',
q:'Prendi la prima lettera di ogni frase trovata.\nFormano una parola.',a:'MORTE',
cl:[{i:'📝',t:'Nota 1: "Mi mancano le forze."',x:120},{i:'📜',t:'Nota 2: "Oscurità totale qui."',x:450},{i:'📦',t:'Nota 3: "Rido solo per non piangere."',x:780},{i:'🔢',t:'Nota 4: "Tutto ha un prezzo."\nNota 5: "Esco solo se trovo la chiave."',x:1200}]},

{id:'c09',ti:'IL NUMERO TELEFERICO',ty:'CRITTOGRAFIA',ji:'"Le lettere si nascondono nei numeri. Come su un vecchio telefono."',
q:'Usa la tastiera del telefono per decodificare i numeri.\nOgni numero corrisponde a una lettera.',a:'CIBO',
cl:[{i:'📝',t:'Sequenza: 2 — 4 — 2 — 6',x:200},{i:'📜',t:'Tastiera telefono:\n2=ABC 3=DEF 4=GHI\n5=JKL 6=MNO 7=PQRS\n8=TUV 9=WXYZ\n(usa la prima lettera del tasto)',x:800}]},

{id:'c10',ti:'IL CODICE MILITARE',ty:'CRITTOGRAFIA',ji:'"L\'alfabeto militare non mente."',
q:'Decodifica usando l\'alfabeto fonetico NATO trovato.',a:'LIMA',
cl:[{i:'📝',t:'Messaggio:\nLUCIA-INDIA-MARIO-ANNA',x:200},{i:'📜',t:'NATO: ogni parola\nrappresenta la sua\nlettera iniziale',x:800}]},

/* ── CATEGORIA: LOGICA ──────────────────────────────────────────── */
{id:'l01',ti:'LE TRE SORELLE',ty:'LOGICA',ji:'"Tre vite. Una sola verità. Trovala."',
q:'Leggi tutti i fatti trovati e deduci la risposta.\nChi è la sorella di mezzo (altezza)?',a:'BEA',
cl:[{i:'📝',t:'Fatto 1:\nAda è più alta di Bea.',x:150},{i:'📜',t:'Fatto 2:\nCara è più bassa di Bea.',x:600},{i:'📦',t:'Fatto 3:\nLa sorella di mezzo è\nquella di altezza intermedia.',x:1100}]},

{id:'l02',ti:'IL TESTIMONE INAFFIDABILE',ty:'LOGICA',ji:'"Non tutti i testimoni dicono la verità. Uno sì."',
q:'Solo uno dei tre ha ragione. Chi mente non mente su sé stesso.\nChi ha ucciso il gatto?',a:'MARCO',
cl:[{i:'📝',t:'Luca dice: "Non sono stato io."',x:120},{i:'📜',t:'Marco dice: "È stato Luca."',x:520},{i:'📦',t:'Sofia dice: "Marco mente."\n\n(Solo una affermazione è vera)',x:1000}]},

{id:'l03',ti:'IL CONTENITORE',ty:'LOGICA',ji:'"Non sempre ciò che vedi è ciò che è."',
q:'Quale contenitore contiene la chiave?\nUsa la logica degli indizi.',a:'ROSSO',
cl:[{i:'📝',t:'Il contenitore ROSSO\nnon è accanto al BLU.',x:150},{i:'📜',t:'La chiave non è nel\ncontenitore più grande.',x:600},{i:'📦',t:'Il BLU è il più grande.\nIl ROSSO è il più piccolo.',x:1100}]},

{id:'l04',ti:'L\'ORDINE DEI PRIGIONIERI',ty:'LOGICA',ji:'"Siete stati portati nell\'ordine sbagliato. Scopri quello corretto."',
q:'In quale posizione si trova il prigioniero di nome CARLO?',a:'3',
cl:[{i:'📝',t:'Anna è prima di Carlo.',x:150},{i:'📜',t:'Bruno è tra Anna e Carlo.',x:600},{i:'📦',t:'Daniele è dopo Carlo.',x:1100}]},

{id:'l05',ti:'LA PROVA BOOLEANA',ty:'LOGICA',ji:'"Vero o falso. Non esistono vie di mezzo."',
q:'Se tutte le affermazioni sono collegate con E (AND),\nquante sono VERE contemporaneamente?\nRispondi con il numero.',a:'2',
cl:[{i:'📝',t:'A: Il cielo è blu. (VERO/FALSO?)',x:150},{i:'📜',t:'B: 2+2=5. (VERO/FALSO?)',x:600},{i:'📦',t:'C: Roma è in Italia. (VERO/FALSO?)',x:1100}]},

{id:'l06',ti:'IL PARADOSSO DEL RITRATTO',ty:'LOGICA',ji:'"Guarda il ritratto. Ascolta chi parla."',
q:'Chi è raffigurato nel ritratto?\n(Una parola)',a:'FIGLIO',
cl:[{i:'📝',t:'"Guardo il ritratto e dico:\n\'Fratelli e sorelle non ho,\nma il padre di quest\'uomo\nè il figlio di mio padre.\'"',x:300},{i:'📜',t:'Nota: "figlio di mio padre"\nsenza fratelli = io stesso.\nDunque il padre del ritratto\nsono io. Il ritratto raffigura...?',x:900}]},

{id:'l07',ti:'IL PESO DELLA VERITA\'',ty:'LOGICA',ji:'"Una bilancia non mente. La logica neanche."',
q:'Quale forma è più pesante?',a:'CERCHIO',
cl:[{i:'📝',t:'QUADRATO > TRIANGOLO\n(la bilancia pende a sinistra)',x:150},{i:'📜',t:'CERCHIO > QUADRATO\n(la bilancia pende a sinistra)',x:600},{i:'📦',t:'TRIANGOLO vs CERCHIO:\nchi pesa di più?',x:1100}]},

{id:'l08',ti:'L\'AULA NERA',ty:'LOGICA',ji:'"In questa classe, tutti mentono — tranne uno."',
q:'Chi dice la verità?\n(Una delle tre persone)',a:'ELENA',
cl:[{i:'📝',t:'ANDREA dice: "Sono innocente."',x:150},{i:'📜',t:'BETO dice: "Andrea è colpevole."',x:600},{i:'📦',t:'ELENA dice: "Beto mente."\n\nSolo Elena dice il vero.',x:1100}]},

{id:'l09',ti:'IL LABIRINTO LOGICO',ty:'LOGICA',ji:'"Ogni porta è una scelta. Ogni scelta è irreversibile."',
q:'Se A → B e B → C, ma NOT C,\ncosa possiamo dedurre su A?',a:'FALSO',
cl:[{i:'📝',t:'Regola 1: A implica B',x:150},{i:'📜',t:'Regola 2: B implica C',x:600},{i:'📦',t:'Fatto noto: C è FALSO.\nCosa significa per A?',x:1100}]},

{id:'l10',ti:'IL NUMERO MISTERIOSO',ty:'LOGICA',ji:'"Ho un carattere. Ho un valore. Ho un segreto."',
q:'Trova il numero che soddisfa TUTTE le condizioni trovate.',a:'7',
cl:[{i:'📝',t:'Condizione 1:\nÈ un numero primo.',x:150},{i:'📜',t:'Condizione 2:\nÈ tra 5 e 10.',x:600},{i:'📦',t:'Condizione 3:\nNon è divisibile per 2\nnon è 5 né 9.',x:1100}]},

/* ── CATEGORIA: GEOMETRIA ───────────────────────────────────────── */
{id:'g01',ti:'I LATI DEL DESTINO',ty:'GEOMETRIA',ji:'"Ogni forma ha le sue leggi. Conosci le tue?"',
q:'Somma il numero di lati delle figure trovate.',a:'18',
cl:[{i:'📝',t:'Sul muro: un ESAGONO\ne un QUADRATO disegnati.',x:200},{i:'📜',t:'Sul pavimento:\nDue TRIANGOLI incisi.',x:800}]},

{id:'g02',ti:'L\'AREA NASCOSTA',ty:'GEOMETRIA',ji:'"Calcola. Non c\'è spazio per l\'imprecisione."',
q:'Qual è l\'area totale delle figure trovate?',a:'29',
cl:[{i:'📝',t:'Figura 1:\nQuadrato di lato 4',x:200},{i:'📜',t:'Figura 2:\nRettangolo 5×2 − 1',x:800}]},

{id:'g03',ti:'GLI ANGOLI INTERNI',ty:'GEOMETRIA',ji:'"La somma degli angoli interni rivela tutto."',
q:'Qual è la somma degli angoli interni trovati?',a:'540',
cl:[{i:'📝',t:'Forma incisa:\nPENTAGONO REGOLARE',x:200},{i:'📜',t:'Formula:\n(n−2)×180°\ndove n = numero di lati',x:800}]},

{id:'g04',ti:'IL PERIMETRO PERDUTO',ty:'GEOMETRIA',ji:'"Misura ogni lato. Poi misura ancora."',
q:'Qual è il perimetro della figura trovata?',a:'24',
cl:[{i:'📝',t:'Figura: rettangolo\nLato lungo = 8\nLato corto = 4',x:200},{i:'📜',t:'Perimetro = 2×(a+b)',x:800}]},

{id:'g05',ti:'IL VOLUME DEL CUBO',ty:'GEOMETRIA',ji:'"Tre dimensioni. Un solo numero."',
q:'Qual è il volume del cubo trovato?',a:'125',
cl:[{i:'📝',t:'Cubo inciso sul muro:\nLato = 5 cm',x:200},{i:'📜',t:'Volume cubo = lato³',x:800}]},

{id:'g06',ti:'ANGOLI SUPPLEMENTARI',ty:'GEOMETRIA',ji:'"Due angoli. Una somma fissa. Trovali entrambi."',
q:'Se un angolo è 65°, qual è il suo supplementare?',a:'115',
cl:[{i:'📝',t:'Sul muro:\nAngolo 1 = 65°',x:200},{i:'📜',t:'Angoli supplementari:\nla somma è SEMPRE 180°',x:800}]},

{id:'g07',ti:'LA DIAGONALE',ty:'GEOMETRIA',ji:'"Taglia il quadrato. Quante diagonali ha?"',
q:'Quante diagonali ha un ottagono regolare?',a:'20',
cl:[{i:'📝',t:'Forma: OTTAGONO\n(8 lati)',x:200},{i:'📜',t:'Formula diagonali:\nn×(n−3)÷2\ndove n = numero vertici',x:800}]},

{id:'g08',ti:'PITAGORA NEL BUIO',ty:'GEOMETRIA',ji:'"Il teorema più antico del mondo. Ancora non lo conosci?"',
q:'Quanto misura l\'ipotenusa del triangolo trovato?',a:'10',
cl:[{i:'📝',t:'Triangolo rettangolo:\ncateto a = 6\ncateto b = 8',x:200},{i:'📜',t:'Teorema di Pitagora:\nc² = a² + b²',x:800}]},

{id:'g09',ti:'I CERCHI CONCENTRICI',ty:'GEOMETRIA',ji:'"Quanti cerchi? Conta bene."',
q:'Qual è la somma delle aree dei cerchi trovati (π≈3)?',a:'27',
cl:[{i:'📝',t:'Cerchio 1: raggio = 1\nCerchio 2: raggio = 2\nCerchio 3: raggio = 3',x:200},{i:'📜',t:'Area cerchio = π × r²\nUsa π = 3\n(arrotonda)',x:800}]},

{id:'g10',ti:'IL TANGRAM',ty:'GEOMETRIA',ji:'"Sette pezzi. Una sola figura. Quanti triangoli?"',
q:'In un tangram standard, quanti pezzi sono triangoli?',a:'5',
cl:[{i:'📝',t:'Un tangram ha 7 pezzi:\n- 5 triangoli\n- 1 quadrato\n- 1 parallelogramma',x:300},{i:'📜',t:'Quanti di questi pezzi\nsono triangoli?',x:900}]},

/* ── CATEGORIA: SCIENZA ─────────────────────────────────────────── */
{id:'sc01',ti:'LA TAVOLA DI MENDELEEV',ty:'CHIMICA',ji:'"Gli elementi non mentono. Solo gli uomini mentono."',
q:'Qual è il simbolo chimico trovato?\nNominalo in italiano.',a:'ORO',
cl:[{i:'📝',t:'Simbolo sul muro:\nAU',x:200},{i:'📜',t:'Numero atomico: 79\nMetallo prezioso,\nglobale, immutabile.',x:800}]},

{id:'sc02',ti:'I PIANETI PERDUTI',ty:'ASTRONOMIA',ji:'"L\'universo non aspetta. Neanche io."',
q:'Quanti pianeti ha il Sistema Solare secondo la definizione attuale?',a:'8',
cl:[{i:'📝',t:'Lista trovata:\nMercurio Venere Terra\nMarte Giove Saturno\nUrano Nettuno',x:200},{i:'📜',t:'Nota: Plutone è stato\nriclassificato come\npianeta nano nel 2006.',x:800}]},

{id:'sc03',ti:'L\'ELETTRICITA\' DEL CONDANNATO',ty:'FISICA',ji:'"La corrente non ferma. Come il mio piano."',
q:'Qual è la corrente (in A) secondo la Legge di Ohm?',a:'3',
cl:[{i:'📝',t:'Sul circuito trovato:\nV = 12 Volt\nR = 4 Ohm',x:200},{i:'📜',t:'Legge di Ohm:\nI = V ÷ R',x:800}]},

{id:'sc04',ti:'IL DNA',ty:'BIOLOGIA',ji:'"La vita è scritta in un codice. Come la tua fine."',
q:'Quante basi azotate ha il DNA?',a:'4',
cl:[{i:'📝',t:'Basi trovate:\nAdenina — Timina\nGuanina — Citosina',x:200},{i:'📜',t:'Conta le basi\nazotate del DNA.',x:800}]},

{id:'sc05',ti:'LA VELOCITA\' DELLA LUCE',ty:'FISICA',ji:'"Anche la luce ha un limite. Come la tua pazienza."',
q:'La velocità della luce nel vuoto è circa X × 10⁸ m/s.\nQual è X (numero intero approssimato)?',a:'3',
cl:[{i:'📝',t:'c ≈ 299.792.458 m/s\n(nel vuoto)',x:200},{i:'📜',t:'Approssima a\nX × 10⁸ m/s.\nX = ?',x:800}]},

{id:'sc06',ti:'NEWTON E LA MELA',ty:'FISICA',ji:'"La gravità è inevitabile. Come la tua situazione."',
q:'Qual è l\'accelerazione di gravità sulla Terra\n(valore approssimato intero, m/s²)?',a:'10',
cl:[{i:'📝',t:'g = 9.81 m/s²\n(superficie terrestre)',x:200},{i:'📜',t:'Arrotonda all\'intero\npiù vicino.',x:800}]},

{id:'sc07',ti:'LA CELLULA',ty:'BIOLOGIA',ji:'"L\'unità fondamentale della vita. Anche questa stanza è una cella."',
q:'Quante cellule (approssimate) ha un corpo umano adulto?\nRispondi in TRILIONI.',a:'37',
cl:[{i:'📝',t:'Studio scientifico 2013:\ncirca 37 trilioni di\ncellule nel corpo umano.',x:200},{i:'📜',t:'Quanti trilioni\ndi cellule?',x:800}]},

{id:'sc08',ti:'LA FORZA',ty:'FISICA',ji:'"F = ma. La legge è uguale per tutti. Anche per te."',
q:'Qual è la forza risultante (in Newton)?',a:'30',
cl:[{i:'📝',t:'Dati trovati:\nmassa = 3 kg\naccelera = 10 m/s²',x:200},{i:'📜',t:'Seconda legge di Newton:\nF = m × a',x:800}]},

{id:'sc09',ti:'IL PH DELLA DISPERAZIONE',ty:'CHIMICA',ji:'"Né acido né base. Esattamente nel mezzo."',
q:'Qual è il valore del pH per una soluzione neutra?',a:'7',
cl:[{i:'📝',t:'Scala pH trovata:\n0 = massima acidità\n7 = neutro\n14 = massima basicità',x:200},{i:'📜',t:'L\'acqua pura ha pH = ?',x:800}]},

{id:'sc10',ti:'I PROTONI',ty:'FISICA',ji:'"Tutto è fatto di particelle. Anche la tua gabbia."',
q:'Quanti protoni ha l\'atomo di carbonio?',a:'6',
cl:[{i:'📝',t:'Carbonio: C\nNumero atomico: 6',x:200},{i:'📜',t:'Il numero atomico\ncorrisponde al numero\ndi protoni.',x:800}]},

/* ── CATEGORIA: STORIA E CULTURA IT ────────────────────────────── */
{id:'h01',ti:'LA DATA MALEDETTA',ty:'STORIA',ji:'"Le date non dimenticano. Neanche io."',
q:'In quale anno Colombo arrivò in America?\n(Rispondi con le ultime 2 cifre)',a:'92',
cl:[{i:'📝',t:'Evento trovato:\nCristoforo Colombo\narriva nelle Americhe',x:200},{i:'📜',t:'Anno: 14_ _\nL\'anno è nel 1400\n(quindicesimo secolo)',x:800}]},

{id:'h02',ti:'L\'IMPERATORE',ty:'STORIA',ji:'"Il potere assoluto. Anche qui."',
q:'In quale anno Napoleone fu esiliato definitivamente\na Sant\'Elena?',a:'1815',
cl:[{i:'📝',t:'Evento: Battaglia di\nWaterloo — anno 1815',x:200},{i:'📜',t:'Stesso anno: esilio\ndefinitivo di Napoleone\na Sant\'Elena.',x:800}]},

{id:'h03',ti:'LA DIVINA COMMEDIA',ty:'LETTERATURA',ji:'"Nel mezzo del cammin di nostra vita..."',
q:'In quanti Canti è divisa la Divina Commedia in totale?',a:'100',
cl:[{i:'📝',t:'Struttura trovata:\n3 cantiche:\nInferno — Purgatorio — Paradiso',x:200},{i:'📜',t:'Ogni cantica ha 33 canti\n+ 1 canto introduttivo\nnell\'Inferno.',x:800}]},

{id:'h04',ti:'LA TORRE DI PISA',ty:'CULTURA',ji:'"Storta ma in piedi. Come me."',
q:'Quanti piani (livelli) ha la Torre di Pisa?',a:'8',
cl:[{i:'📝',t:'Torre di Pisa:\ncampanile del Duomo\ndi Pisa (Toscana)',x:200},{i:'📜',t:'Struttura: 8 piani\n(incluso il piano base\ne la cella campanaria)',x:800}]},

{id:'h05',ti:'IL COLOSSEO',ty:'STORIA',ji:'"L\'arena dei morti. Questo posto la supera."',
q:'In quale anno fu completato il Colosseo di Roma?\n(Anno d.C.)',a:'80',
cl:[{i:'📝',t:'Anfiteatro Flavio:\ncostruito tra 72 e 80 d.C.',x:200},{i:'📜',t:'Anno di completamento\ne inaugurazione:\n80 d.C.',x:800}]},

{id:'h06',ti:'LA LINGUA DEI POETI',ty:'LETTERATURA',ji:'"La lingua italiana nasce da un poeta. Sai chi?"',
q:'Chi è considerato il padre della lingua italiana?',a:'DANTE',
cl:[{i:'📝',t:'Scrittore del XIV secolo.\nAutore della Divina Commedia.',x:200},{i:'📜',t:'Il suo volgare fiorentino\ndivenne la base\ndella lingua italiana.',x:800}]},

{id:'h07',ti:'LA LUNA',ty:'STORIA',ji:'"L\'uomo ha raggiunto la Luna. Tu puoi raggiungere la porta."',
q:'In quale anno Neil Armstrong mise piede sulla Luna?',a:'1969',
cl:[{i:'📝',t:'Missione trovata:\nApollo 11\nLuna — 1969',x:200},{i:'📜',t:'Primo allunaggio\ndella storia.',x:800}]},

{id:'h08',ti:'ROMA CAPITALE',ty:'STORIA',ji:'"Roma non fu costruita in un giorno. Questa trappola sì."',
q:'In quale anno Roma divenne capitale del Regno d\'Italia?',a:'1871',
cl:[{i:'📝',t:'Roma Capitale:\nstabilita dopo il\n20 settembre 1870.',x:200},{i:'📜',t:'Anno ufficiale:\n1871 (trasferimento\ndel governo)',x:800}]},

{id:'h09',ti:'LA COSTITUZIONE',ty:'CULTURA',ji:'"Persino il tuo Paese ha delle regole. Io le mie."',
q:'In quanti articoli è divisa la Costituzione italiana?',a:'139',
cl:[{i:'📝',t:'Costituzione della\nRepubblica Italiana\n(in vigore dal 1948)',x:200},{i:'📜',t:'Numero di articoli:\n139 articoli totali.',x:800}]},

{id:'h10',ti:'IL GENIO DI VINCI',ty:'ARTE',ji:'"Leonardo disegnava macchine impossibili. Io costruisco trappole impossibili."',
q:'In quale anno nacque Leonardo da Vinci?',a:'1452',
cl:[{i:'📝',t:'Leonardo da Vinci:\nnato il 15 aprile\nnear Vinci, Toscana.',x:200},{i:'📜',t:'Anno di nascita:\n1452',x:800}]},

/* ── CATEGORIA: MISTO / CREATIVI ────────────────────────────────── */
{id:'m01',ti:'LA SETTIMANA PERDUTA',ty:'CALENDARIO',ji:'"I giorni si accumulano. Come i tuoi errori."',
q:'Quante ore ci sono in una settimana?',a:'168',
cl:[{i:'📝',t:'Dato 1:\nUna settimana = 7 giorni',x:200},{i:'📜',t:'Dato 2:\nUn giorno = 24 ore\n7 × 24 = ?',x:800}]},

{id:'m02',ti:'LA STANZA DEGLI SPECCHI',ty:'PERCEZIONE',ji:'"Quante immagini vedi? Più di quante pensi."',
q:'Con 3 specchi paralleli a coppie,\nquante riflessioni puoi vedere in totale?',a:'INFINITE',
cl:[{i:'📝',t:'Sperimenta mentalmente:\n2 specchi paralleli =\nriflessioni infinite.',x:200},{i:'📜',t:'Con specchi perfettamente\nparalleli il numero di\nriflessioni è...',x:800}]},

{id:'m03',ti:'IL PREZZO DELLA LIBERTA\'',ty:'MATEMATICA',ji:'"Tutto ha un prezzo. Anche la tua vita."',
q:'Trova il prezzo totale usando le informazioni della stanza.',a:'47',
cl:[{i:'📝',t:'Lista trovata:\n3 oggetti da €12 l\'uno',x:150},{i:'📜',t:'Sconto trovato:\n−€4 su ogni oggetto',x:600},{i:'📦',t:'Tassa aggiuntiva:\n+€5 totale',x:1100}]},

{id:'m04',ti:'L\'OROLOGIO ROTTO',ty:'LOGICA',ji:'"L\'orologio rotto ha ragione due volte al giorno. Il tuo cervello deve funzionare."',
q:'L\'orologio avanza di 5 minuti ogni ora.\nSe segna le 10:00, che ora segna dopo 4 ore reali?',a:'14:20',
cl:[{i:'📝',t:'Guadagno orario:\n+5 minuti/ora reale',x:200},{i:'📜',t:'Dopo 4 ore reali:\n4 ore + 4×5min guadagnati\n= 4 ore e 20 minuti\n+ 10:00 = ?',x:800}]},

{id:'m05',ti:'IL CODICE DEL COLORE',ty:'PERCEZIONE',ji:'"I colori nascondono numeri. I numeri nascondono la verità."',
q:'Somma i valori RGB trovati per il colore specificato.',a:'255',
cl:[{i:'📝',t:'Colore: GIALLO PURO\nRGB: R=255, G=255, B=0',x:200},{i:'📜',t:'Somma R+G+B = ?\n(solo R e G)',x:800}]},

{id:'m06',ti:'LA VELOCITA\' DEL TRENO',ty:'MATEMATICA',ji:'"Il tempo passa. La distanza si accumula. Calcola."',
q:'A che velocità (km/h) va il treno?',a:'90',
cl:[{i:'📝',t:'Distanza percorsa:\n270 km',x:200},{i:'📜',t:'Tempo impiegato:\n3 ore\n\nv = d ÷ t',x:800}]},

{id:'m07',ti:'IL CENSIMENTO DEI MORTI',ty:'MATEMATICA',ji:'"Conta i vivi. Sottrai i morti. Ottieni la verità."',
q:'Quante persone sono sopravvissute?',a:'14',
cl:[{i:'📝',t:'Totale iniziale:\n30 prigionieri',x:200},{i:'📜',t:'Morti: il 40% del totale\nFeriti sopravvissuti: 2\n\nVivi = 30 − (30×0.4) + 2... no:\n30−12 = 18. 18−(18÷9×2)=14',x:800}]},

{id:'m08',ti:'IL CALENDARIO MALEDETTO',ty:'CALENDARIO',ji:'"Quanti giorni hai vissuto? Quanti ne restano?"',
q:'Quanti giorni ci sono in un anno bisestile?',a:'366',
cl:[{i:'📝',t:'Anno normale: 365 giorni.\nAnno bisestile:\naggiunge febbraio 29.',x:200},{i:'📜',t:'365 + 1 = ?',x:800}]},

{id:'m09',ti:'L\'EQUAZIONE DELLA PRIGIONE',ty:'ALGEBRA',ji:'"X marks the spot. Trova X."',
q:'Risolvi l\'equazione trovata sui muri.',a:'9',
cl:[{i:'📝',t:'Equazione incisa:\n3x − 6 = 21',x:200},{i:'📜',t:'Isolate x:\n3x = 21 + 6\n3x = 27\nx = ?',x:800}]},

{id:'m10',ti:'LA TAVOLA PITAGORICA',ty:'MATEMATICA',ji:'"Il più antico degli strumenti. Lo conosci ancora?"',
q:'Trovata la riga e la colonna, qual è il loro prodotto?',a:'63',
cl:[{i:'📝',t:'Riga trovata: 7\nColonna trovata: 9',x:200},{i:'📜',t:'Tavola pitagorica:\n7 × 9 = ?',x:800}]},
/* ── CATEGORIA EXTRA: BONUS CREATIVI ───────────────────────────── */
{id:'b01',ti:'IL LABIRINTO DI NUMERI',ty:'MATEMATICA',ji:'"Ogni porta ha un numero. Trova il numero della tua porta."',
q:'Se sommi tutti i numeri da 1 a 10, qual è il risultato?',a:'55',
cl:[{i:'📝',t:'Serie: 1+2+3+4+5\n+6+7+8+9+10 = ?',x:200},{i:'📜',t:'Formula di Gauss:\nn×(n+1)÷2\ncon n=10',x:800}]},

{id:'b02',ti:'IL NUMERO FELICE',ty:'MATEMATICA',ji:'"Alcuni numeri portano fortuna. Questo è uno di quelli."',
q:'Qual è il fattoriale di 5 (5!)?',a:'120',
cl:[{i:'📝',t:'5! = 5×4×3×2×1',x:200},{i:'📜',t:'Calcola il prodotto\n5 × 4 × 3 × 2 × 1 = ?',x:800}]},

{id:'b03',ti:'LA MEDIA DEI CONDANNATI',ty:'MATEMATICA',ji:'"La media è una bugia gentile. Ma in questo caso è necessaria."',
q:'Qual è la media aritmetica dei valori trovati?',a:'8',
cl:[{i:'📝',t:'Valori trovati:\n4 — 6 — 8 — 10 — 12',x:200},{i:'📜',t:'Media = somma ÷ n\nsomma=40, n=5\n40 ÷ 5 = ?',x:800}]},

{id:'b04',ti:'I GRADI DEL TRIANGOLO',ty:'GEOMETRIA',ji:'"La somma degli angoli è immutabile. Come il mio piano."',
q:'Se un triangolo ha due angoli di 60° e 80°,\nquanto misura il terzo?',a:'40',
cl:[{i:'📝',t:'Angolo 1: 60°\nAngolo 2: 80°',x:200},{i:'📜',t:'Somma angoli triangolo\n= 180°\n180 − 60 − 80 = ?',x:800}]},

{id:'b05',ti:'IL CODICE POSTALE DEL TERRORE',ty:'MATEMATICA',ji:'"Anche un codice postale può essere un enigma."',
q:'Calcola il valore del codice trovato.',a:'12',
cl:[{i:'📝',t:'Codice: 3 × (2 + 2)',x:200},{i:'📜',t:'Rispetta l\'ordine:\nprima le parentesi,\npoi la moltiplicazione.',x:800}]},

{id:'b06',ti:'L\'ANGOLO GIRO',ty:'GEOMETRIA',ji:'"Gira intorno. Troverai la risposta."',
q:'Quanti gradi ha un angolo giro (un giro completo)?',a:'360',
cl:[{i:'📝',t:'Un angolo piatto = 180°\nUn angolo retto = 90°',x:200},{i:'📜',t:'Un giro completo = ?\n(doppio dell\'angolo piatto)',x:800}]},

{id:'b07',ti:'LA PERCENTUALE DEL MALE',ty:'MATEMATICA',ji:'"Anche il male ha la sua percentuale."',
q:'Qual è il 30% di 90?',a:'27',
cl:[{i:'📝',t:'Valore base trovato: 90',x:200},{i:'📜',t:'Percentuale da trovare: 30%\n90 × 0.30 = ?',x:800}]},

{id:'b08',ti:'IL QUADRATO MAGICO',ty:'MATEMATICA',ji:'"Ogni riga, ogni colonna, uguale. Magia? No, matematica."',
q:'In un quadrato magico 3×3 con numeri 1-9,\nqual è la somma magica di ogni riga?',a:'15',
cl:[{i:'📝',t:'Numeri usati: 1,2,3,4,5,6,7,8,9',x:200},{i:'📜',t:'Somma totale: 45\n3 righe uguale.\n45 ÷ 3 = ?',x:800}]},

{id:'b09',ti:'I SECONDI NELL\'ETERNITA\'',ty:'MATEMATICA',ji:'"Stai contando i secondi. Spendili bene."',
q:'Quanti secondi ci sono in un\'ora?',a:'3600',
cl:[{i:'📝',t:'1 ora = 60 minuti\n1 minuto = 60 secondi',x:200},{i:'📜',t:'60 × 60 = ?',x:800}]},

{id:'b10',ti:'LA SPIRALE FINALE',ty:'MATEMATICA',ji:'"Tutto torna. Tutto si ripete. Come questa stanza."',
q:'Qual è il 7° numero della serie di Fibonacci\n(partendo da 1,1,2,3,...)?',a:'13',
cl:[{i:'📝',t:'Serie:\n1 — 1 — 2 — 3 — 5 — 8 — ?',x:200},{i:'📜',t:'Ogni numero = somma\ndei 2 precedenti.\nSettimo elemento = ?',x:800}]}
]; // fine POOL

/* ════════════════════════════════════
   ROOM THEMES (10 visual themes)
════════════════════════════════════ */
const THEMES=[
  {bg:'#1c1208',floor:'#120d06',accent:'#8b0000',name:'dungeon'},
  {bg:'#0a1008',floor:'#060c04',accent:'#003300',name:'morgue'},
  {bg:'#180820',floor:'#0e0414',accent:'#5a0080',name:'lab'},
  {bg:'#1a1000',floor:'#100800',accent:'#664400',name:'sewer'},
  {bg:'#080818',floor:'#040410',accent:'#000088',name:'cold'},
  {bg:'#1a0808',floor:'#120404',accent:'#660000',name:'fire'},
  {bg:'#181818',floor:'#101010',accent:'#444444',name:'metal'},
  {bg:'#0a1410',floor:'#060c09',accent:'#1a4428',name:'jungle'},
  {bg:'#14100a',floor:'#0c0806',accent:'#4a3000',name:'wood'},
  {bg:'#0a0a14',floor:'#060610',accent:'#2a2a60',name:'ice'}
];

/* ════════════════════════════════════
   GAME STATE
════════════════════════════════════ */
const G={
  hard:false,roomCount:5,enigmas:[],idx:0,
  lives:3,hints:3,timer:null,timeLeft:0,startMs:0,
  mapOpen:false,inventory:[],nearObj:null,
  px:100,pdir:1,camX:0,running:false,
  keys:{},raf:null
};
const ROOM_W=1600,VIEW_W=820,PLAYER_SPEED=3;

/* ════════════════════════════════════
   NAVIGATION
════════════════════════════════════ */
function nav(to){
  document.querySelectorAll('.scr').forEach(s=>s.style.display='none');
  document.getElementById('s'+to).style.display='flex';
}
function toggleMap(){G.mapOpen=!G.mapOpen;if(G.mapOpen){buildMap();nav('sm');}else nav('sg');}

/* ════════════════════════════════════
   SHUFFLE
════════════════════════════════════ */
function shuffle(a){
  const b=[...a];
  for(let i=b.length-1;i>0;i--){const j=0|Math.random()*(i+1);[b[i],b[j]]=[b[j],b[i]];}
  return b;
}

/* ════════════════════════════════════
   START GAME
════════════════════════════════════ */
function startGame(n){
  let count=n;
  if(count===0) count=5+Math.floor(Math.random()*46); // 5–50
  G.hard=!!G.hard;
  G.lives=G.hard?1:3;
  G.hints=G.hard?0:3;
  G.idx=0;G.startMs=Date.now();G.mapOpen=false;
  G.enigmas=shuffle(POOL).slice(0,Math.min(count,POOL.length));
  nav('sg');
  loadRoom(0);
}

/* ════════════════════════════════════
   LOAD ROOM
════════════════════════════════════ */
function loadRoom(idx){
  G.idx=idx;
  G.inventory=[];G.nearObj=null;
  const e=G.enigmas[idx];
  const theme=THEMES[idx%THEMES.length];

  // HUD
  $('rname').textContent=e.ti;
  $('rmeta').textContent=`STANZA ${idx+1} · ${e.ty}${G.hard?' · ⚠ DIFFICILE':''}`;
  $('timer').className='';
  $('fb').textContent='';$('fb').className='';
  const ai=$('ans-in');ai.value='';ai.className='';
  $('near-hint').textContent='—';

  // Hint button
  const hb=$('hintbtn');
  if(G.hard){hb.textContent='⛔ NESSUN INDIZIO';hb.disabled=true;hb.style.opacity='.3';}
  else{hb.textContent=`💀 INDIZIO (${G.hints}r, −1 vita)`;hb.disabled=G.hints<=0;hb.style.opacity=G.hints>0?'1':'.35';}

  // Build room
  buildRoom(e,theme);
  renderInventory();

  // Enigma text (solo la domanda!)
  $('ji-line').textContent=e.ji;
  $('eq-body').textContent=e.q;

  // Progress
  $('pgfill').style.width=`${(idx/G.enigmas.length)*100}%`;

  // Lives
  renderLives();

  // Timer
  clearInterval(G.timer);
  G.timeLeft=G.hard?Math.floor(getTime(idx)*.58):getTime(idx);
  renderTimer();
  G.timer=setInterval(tick,1000);

  // Player reset
  G.px=100;G.pdir=1;G.camX=0;
  renderPlayerDOM();
  renderCamera();

  // Start movement loop
  G.running=true;
  if(G.raf) cancelAnimationFrame(G.raf);
  G.raf=requestAnimationFrame(gameLoop);

  setTimeout(()=>ai.focus(),120);
}

function getTime(idx){
  // Time increases slightly with room depth
  return Math.max(120,220+idx*5);
}

/* ════════════════════════════════════
   BUILD ROOM VISUAL
════════════════════════════════════ */
function buildRoom(e,theme){
  const inner=$('room-inner');
  inner.style.background=`linear-gradient(to bottom,${theme.bg} 0%,${theme.bg} 63%,${theme.floor} 63%)`;

  // Clear old objects (keep player)
  const player=$('player');
  inner.innerHTML='';
  inner.appendChild(player);

  // Static decor: random blood, chains, skulls
  const decors=[
    `<div class="r-blood" style="left:${rand(5,30)}%;top:${rand(40,60)}%;width:${rand(28,50)}px;height:${rand(18,36)}px;transform:rotate(${rand(-30,30)}deg)"></div>`,
    `<div class="r-blood" style="left:${rand(50,80)}%;top:${rand(42,62)}%;width:${rand(24,44)}px;height:${rand(16,30)}px"></div>`,
    `<div class="r-chain" style="height:${rand(44,80)}px;top:${rand(12,32)}px;left:${rand(150,350)}px"></div>`,
    `<div class="r-chain" style="height:${rand(40,70)}px;top:${rand(14,30)}px;left:${rand(900,1200)}px"></div>`,
    `<div class="r-skull" style="bottom:${rand(68,90)}px;left:${rand(400,700)}px">💀</div>`,
    `<div class="r-text" style="top:${rand(25,45)}px;left:${rand(30,120)}px;font-size:11px;color:${theme.accent}44;letter-spacing:3px;opacity:.4">` +
     shuffle(['USCITA','TRAPPOLA','AIUTO','NESSUNA VIA','TROPPO TARDI','RIMANI','CORRI','MUORI']).slice(0,3).join('  ') +
    `</div>`
  ];
  inner.insertAdjacentHTML('beforeend',decors.join(''));

  // Door
  inner.insertAdjacentHTML('beforeend',`<div class="rdoor locked" id="rdoor"><div></div></div>`);

  // Clue objects
  e.cl.forEach((cl,i)=>{
    const div=document.createElement('div');
    div.className='robj';
    div.style.cssText=`left:${cl.x}px;bottom:${rand(72,110)}px;`;
    div.dataset.idx=i;
    div.dataset.text=cl.t;
    div.dataset.collected='false';
    div.innerHTML=`<div class="ep">▼ E</div><div class="ico">${cl.i}</div><div class="lbl">OGGETTO</div>`;
    inner.appendChild(div);
  });
}

function rand(a,b){return a+Math.floor(Math.random()*(b-a+1));}
function $(id){return document.getElementById(id);}

/* ════════════════════════════════════
   GAME LOOP (movement)
════════════════════════════════════ */
function gameLoop(){
  if(!G.running) return;
  const k=G.keys;
  if(k['ArrowLeft']||k['KeyA']){G.px=Math.max(8,G.px-PLAYER_SPEED);G.pdir=-1;}
  if(k['ArrowRight']||k['KeyD']){G.px=Math.min(ROOM_W-28,G.px+PLAYER_SPEED);G.pdir=1;}
  renderCamera();
  renderPlayerDOM();
  checkProximity();
  G.raf=requestAnimationFrame(gameLoop);
}

function renderCamera(){
  G.camX=Math.max(0,Math.min(ROOM_W-VIEW_W,G.px-VIEW_W/2));
  $('room-inner').style.transform=`translateX(${-G.camX}px)`;
}

function renderPlayerDOM(){
  const p=$('player');
  p.style.left=G.px+'px';
  p.style.transform=`scaleX(${G.pdir})`;
}

/* ════════════════════════════════════
   PROXIMITY CHECK
════════════════════════════════════ */
function checkProximity(){
  const objs=document.querySelectorAll('.robj');
  let found=null;
  objs.forEach(obj=>{
    if(obj.dataset.collected==='true') return;
    const ox=parseInt(obj.style.left);
    const dist=Math.abs(G.px-ox);
    if(dist<70){obj.classList.add('near');found=obj;}
    else obj.classList.remove('near');
  });
  G.nearObj=found;
  const p=$('player');
  if(found){p.classList.add('near-active');}
  else{p.classList.remove('near-active');}

  // Update hint bar
  if(found){
    $('near-hint').style.color='var(--flicker)';
    $('near-hint').textContent='PREMI E PER RACCOGLIERE';
  } else {
    $('near-hint').style.color='#330000';
    $('near-hint').textContent='ESPLORA LA STANZA';
  }
}

/* ════════════════════════════════════
   COLLECT CLUE
════════════════════════════════════ */
function collectClue(){
  if(!G.nearObj||G.nearObj.dataset.collected==='true') return;
  G.nearObj.dataset.collected='true';
  G.nearObj.classList.add('collected');
  G.nearObj.classList.remove('near');
  const icon=G.nearObj.querySelector('.ico').textContent;
  const text=G.nearObj.dataset.text;
  G.inventory.push({icon,text});
  renderInventory();
  showCluePopup(icon,text);
}

function renderInventory(){
  const row=$('inv-row');
  row.innerHTML='<span id="inv-label">INDIZI: </span>';
  if(G.inventory.length===0){
    row.insertAdjacentHTML('beforeend','<span style="font-size:14px;color:#1a0800;font-family:Share Tech Mono,monospace">Nessuno ancora — esplora la stanza.</span>');
    return;
  }
  G.inventory.forEach((item,i)=>{
    row.insertAdjacentHTML('beforeend',
      `<div class="inv-item" onclick="showCluePopup('${item.icon}','${item.text.replace(/'/g,"\\'").replace(/\n/g,'\\n')}')" title="${item.text}">${item.icon} #${i+1}</div>`
    );
  });
}

function showCluePopup(icon,text){
  const popup=$('clue-popup');
  $('cp-text').innerHTML=`<span style="font-size:24px">${icon}</span> &nbsp;<span style="font-size:13px;color:#2a1500;font-family:'Share Tech Mono',monospace;letter-spacing:2px">INDIZIO RACCOLTO</span><br><br>${text.replace(/\n/g,'<br>')}`;
  popup.style.display='block';
  popup.style.left='50%';popup.style.top='40%';
  popup.style.transform='translate(-50%,-50%)';
  popup.style.zIndex='300';
}
function closeCluePopup(){$('clue-popup').style.display='none';}

/* ════════════════════════════════════
   TIMER
════════════════════════════════════ */
function tick(){
  G.timeLeft--;renderTimer();
  if(G.timeLeft<=25)$('timer').className='urg';
  if(G.timeLeft<=0){clearInterval(G.timer);endGame(false,'⏰ TEMPO SCADUTO');}
}
function renderTimer(){
  const m=String(Math.floor(G.timeLeft/60)).padStart(2,'0');
  const s=String(G.timeLeft%60).padStart(2,'0');
  $('timer').textContent=`${m}:${s}`;
}

/* ════════════════════════════════════
   CHECK ANSWER
════════════════════════════════════ */
function checkAns(){
  const ai=$('ans-in');
  const val=ai.value.trim().toUpperCase().replace(/\s+/g,'');
  const correct=G.enigmas[G.idx].a.toUpperCase().replace(/\s+/g,'');
  if(val===correct){
    ai.className='ok';
    const d=$('rdoor');if(d){d.classList.remove('locked');d.classList.add('open');}
    $('fb').className='ok';$('fb').textContent='✓ CORRETTO. La porta si apre.';
    G.running=false;clearInterval(G.timer);
    setTimeout(()=>{
      if(G.idx+1>=G.enigmas.length) endGame(true);
      else loadRoom(G.idx+1);
    },1600);
  } else {
    ai.className='wrong';
    triggerJumpscare();
    G.lives--;renderLives();
    if(G.lives<=0){
      clearInterval(G.timer);G.running=false;
      setTimeout(()=>endGame(false,'❌ VITE ESAURITE'),900);
    } else {
      $('fb').textContent=`✗ SBAGLIATO. ${G.lives} ${G.lives===1?'vita':'vite'} rimaste.`;
      setTimeout(()=>{ai.className='';},700);
    }
  }
}

/* ════════════════════════════════════
   JUMPSCARE
════════════════════════════════════ */
function triggerJumpscare(){
  const j=$('jsc');j.style.display='flex';
  playScreech();
  setTimeout(()=>{j.style.display='none';},750);
}

function playScreech(){
  try{
    const ctx=new(window.AudioContext||window.webkitAudioContext)();
    const osc=ctx.createOscillator();
    const gain=ctx.createGain();
    const dist=ctx.createWaveShaper();
    // distortion curve
    const curve=new Float32Array(256);
    for(let i=0;i<256;i++) curve[i]=i<128?(i*3-300)/128:1;
    dist.curve=curve;
    osc.connect(dist);dist.connect(gain);gain.connect(ctx.destination);
    osc.type='sawtooth';
    osc.frequency.setValueAtTime(900,ctx.currentTime);
    osc.frequency.exponentialRampToValueAtTime(180,ctx.currentTime+0.55);
    gain.gain.setValueAtTime(0.6,ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.001,ctx.currentTime+0.6);
    osc.start();osc.stop(ctx.currentTime+0.65);
  }catch(e){}
}

/* ════════════════════════════════════
   HINT
════════════════════════════════════ */
function useHint(){
  if(G.hard||G.hints<=0) return;
  G.lives=Math.max(0,G.lives-1);G.hints--;
  renderLives();
  const hb=$('hintbtn');
  hb.textContent=`💀 INDIZIO (${G.hints}r, −1 vita)`;
  if(G.hints<=0){hb.disabled=true;hb.style.opacity='.35';}
  // Highlight uncollected clues
  document.querySelectorAll('.robj:not(.collected)').forEach(o=>{
    o.style.animation='near-glow .4s infinite';
    setTimeout(()=>{o.style.animation='';},3000);
  });
  showCluePopup('💡',`Cerca oggetti non ancora raccolti.\nLa risposta richiede: ${G.enigmas[G.idx].cl.length} indizi.\nHai raccolto: ${G.inventory.length}`);
  if(G.lives<=0){clearInterval(G.timer);G.running=false;setTimeout(()=>endGame(false,'❌ VITE ESAURITE'),400);}
}

/* ════════════════════════════════════
   LIVES
════════════════════════════════════ */
function renderLives(){
  const total=G.hard?1:3;let s='';
  for(let i=0;i<total;i++) s+=i<G.lives?'❤ ':'🖤 ';
  $('livd').textContent=s.trim();
}

/* ════════════════════════════════════
   MAP
════════════════════════════════════ */
function buildMap(){
  const corr=$('mcorr');corr.innerHTML='';
  const subs=[
    'HAI APPENA INIZIATO.','UNA STANZA ALLE SPALLE.',
    'DUE STANZE. STAI RESISTENDO?','TRE STANZE. CONTINUA A GUARDARE.',
    'QUATTRO STANZE. L\'ENIGMISTA TI OSSERVA.','CINQUE STANZE SUPERATE.',
    'SEI STANZE. LA MENTE REGGE ANCORA.','SETTE STANZE. UN SOLDATO CADREBBE.',
    'OTTO STANZE. STRAORDINARIO.','NOVE STANZE. QUASI UN MITO.'
  ];
  $('msub').textContent=subs[Math.min(G.idx,subs.length-1)]+' LE PROSSIME STANZE SONO AVVOLTE NEL BUIO.';
  const icons=['🔢','🔤','🧩','💡','🔁','🔐','🧠','📐','🏛','⚗','🎯','📊','🗺','⚡','🌀'];
  for(let i=0;i<=G.idx;i++){
    const e=G.enigmas[i];const done=i<G.idx;const curr=i===G.idx;
    if(i>0){const c=document.createElement('div');c.className='mc '+(done?'done':curr?'curr':'fog');corr.appendChild(c);}
    const node=document.createElement('div');node.className='mn '+(done?'done':curr?'curr':'fog');
    node.innerHTML=`<div class="mn-d"><div class="mn-ico">${done?'✓':curr?'★':'?'}</div></div>
<div class="mn-lbl">${done||curr?'STZ '+(i+1):'???'}</div>`;
    corr.appendChild(node);
  }
  // fog
  const fc=document.createElement('div');fc.className='mc fog';corr.appendChild(fc);
  const fog=document.createElement('div');fog.className='mfog';fog.innerHTML='? ? ?';corr.appendChild(fog);
}

/* ════════════════════════════════════
   END GAME
════════════════════════════════════ */
function endGame(win,reason=''){
  G.running=false;clearInterval(G.timer);closeCluePopup();
  const sec=Math.round((Date.now()-G.startMs)/1000);
  const m=Math.floor(sec/60),s=sec%60;
  $('et').textContent=win?'🔓 LIBERO':'GAME OVER';
  $('et').className=win?'w':'d';
  $('em').innerHTML=win
    ?`"Hai superato tutte le stanze. La tua mente ha vinto dove molti hanno fallito.<br>Ricorda: il vero gioco è quello che ti giochi dentro, ogni giorno."<br><br><span style="font-size:14px;color:#224422">— L'Enigmista</span>`
    :`${reason}<br><br>"Non eri abbastanza. Forse un'altra vita ti darà la saggezza<br>che questa non ti ha dato."<br><br><span style="font-size:14px;color:#330000">— L'Enigmista</span>`;
  $('estats').textContent=`STANZE: ${G.idx+(win?1:0)}/${G.enigmas.length}  ·  TEMPO: ${m}m${s}s  ·  VITE: ${G.lives}  ·  MODO: ${G.hard?'DIFFICILE':'NORMALE'}`;
  nav('se');
}

function resetGame(){G.running=false;clearInterval(G.timer);nav('si');}

/* ════════════════════════════════════
   KEYBOARD INPUT
════════════════════════════════════ */
document.addEventListener('keydown',e=>{
  G.keys[e.code]=true;
  if(e.code==='KeyE') collectClue();
  if(e.code==='Escape') closeCluePopup();
});
document.addEventListener('keyup',e=>{G.keys[e.code]=false;});
</script>
</body>
</html>
