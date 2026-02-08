# guess-your-father<!DOCTYPE html><html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>我知道你爸爸是谁</title>
  <style>
    :root{
      --bg:#020617;--text:#e5e7eb;--muted:#94a3b8;--accent:#6366f1;--danger:#f43f5e
    }
    *{box-sizing:border-box}
    body{margin:0;min-height:100vh;display:flex;justify-content:center;align-items:center;background:radial-gradient(1200px 600px at 50% -10%, #0f172a, #020617 60%);color:var(--text);font-family:system-ui,-apple-system,Segoe UI,Roboto,Helvetica,Arial,"PingFang SC","Microsoft YaHei",sans-serif}
    .box{background:linear-gradient(180deg,rgba(255,255,255,.04),rgba(255,255,255,.02));padding:36px 28px 30px;border-radius:18px;width:100%;max-width:420px;box-shadow:0 30px 60px rgba(0,0,0,.6), inset 0 1px 0 rgba(255,255,255,.06);text-align:center;position:relative;overflow:hidden}
    .badge{position:absolute;inset:-40px auto auto -40px;width:140px;height:140px;background:linear-gradient(135deg,#6366f1,#22c55e);opacity:.15;filter:blur(30px)}
    h1{margin:0 0 12px;font-size:1.9rem}
    .sub{color:var(--muted);font-size:.95rem;margin-bottom:16px}
    input{width:100%;padding:14px;border-radius:12px;border:1px solid rgba(255,255,255,.08);background:rgba(2,6,23,.7);color:var(--text);font-size:1rem;margin-bottom:12px}
    button{width:100%;padding:14px;border:none;border-radius:12px;font-size:1rem;cursor:pointer;background:linear-gradient(135deg,#6366f1,#4f46e5);color:#fff;box-shadow:0 10px 25px rgba(99,102,241,.35)}
    button:active{transform:translateY(1px)}
    .hintbar{display:none;margin-top:10px;color:#fecaca;font-size:.85rem}
    .loading{display:none;margin:16px auto 4px;gap:6px;justify-content:center}
    .dot{width:8px;height:8px;border-radius:50%;background:#94a3b8;animation:pulse 1.2s infinite ease-in-out}
    .dot:nth-child(2){animation-delay:.15s}.dot:nth-child(3){animation-delay:.3s}
    @keyframes pulse{0%,80%,100%{opacity:.3}40%{opacity:1}}
    .result{margin-top:16px;font-size:1.25rem;min-height:1.6em;letter-spacing:1px}
    .name{display:inline-block;margin-left:6px;color:var(--danger);font-weight:800;transform:scale(.7);opacity:0}
    .name.show{animation:pop .45s ease-out forwards}
    @keyframes pop{from{transform:scale(.6);opacity:0}to{transform:scale(1);opacity:1}}
    .qr{margin-top:18px;opacity:0;transform:translateY(10px) scale(.98);pointer-events:none}
    .qr.show{animation:reveal .6s ease-out forwards;pointer-events:auto}
    @keyframes reveal{to{opacity:1;transform:translateY(0) scale(1)}}
    .qr img{width:220px;max-width:100%;border-radius:14px;box-shadow:0 12px 30px rgba(0,0,0,.5)}
    .blink{margin-top:10px;color:#fecaca;animation:blink 1.2s infinite}
    @keyframes blink{0%,100%{opacity:.6}50%{opacity:1}}
    footer{margin-top:10px;font-size:.75rem;color:#64748b}
  </style>
</head>
<body>
  <div class="box">
    <div class="badge"></div>
    <h1>我知道你爸爸是谁</h1>
    <div class="sub">输入名字 · 点击搜寻 · 真相揭晓</div><input id="nameInput" placeholder="请输入你的名字（可不填）" />
<button id="btn">搜寻</button>
<div class="hintbar" id="hintbar">如未听到声音，请先点击一次页面以解锁音频（iPhone 需要）</div>

<div class="loading" id="loading"><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
<div class="result" id="result"></div>

<!-- Audio pack -->
<audio id="bgm" src="bgm.mp3" loop preload="auto"></audio>
<audio id="tick" src="tick.mp3" preload="auto"></audio>
<audio id="beep" src="beep.mp3" preload="auto"></audio>
<audio id="reveal" src="reveal.mp3" preload="auto"></audio>
<audio id="boom" src="boom.mp3" preload="auto"></audio>

<div class="qr" id="qr">
  <img src="qr.png" alt="支付二维码" />
  <p class="blink">快点孝敬父亲吧</p>
</div>

<footer>Malaysia National QR · Demo</footer>

  </div>  <script>
    const btn = document.getElementById('btn');
    const loading = document.getElementById('loading');
    const result = document.getElementById('result');
    const qr = document.getElementById('qr');
    const hintbar = document.getElementById('hintbar');

    const bgm = document.getElementById('bgm');
    const tick = document.getElementById('tick');
    const beep = document.getElementById('beep');
    const reveal = document.getElementById('reveal');
    const boom = document.getElementById('boom');

    // unlock audio on first interaction (iOS)
    document.addEventListener('click', unlockOnce, { once:true });
    function unlockOnce(){
      [bgm,tick,beep,reveal,boom].forEach(a=>{ try{ a.volume=0; a.play(); a.pause(); a.currentTime=0; a.volume=1;}catch(e){} });
    }

    btn.addEventListener('click', run);
    document.addEventListener('keydown', e=>{ if(e.key==='Enter') run(); });

    function run(){
      // reset
      result.innerHTML=''; qr.classList.remove('show'); loading.style.display='flex'; hintbar.style.display='none';

      // background music fade-in
      try{ bgm.volume=0; bgm.play(); fadeTo(bgm, .25, 800);}catch(e){ hintbar.style.display='block'; }

      // fake search
      setTimeout(()=>{
        loading.style.display='none';
        vibrate(30);
        play(beep);
        typeLine('你亲爱的父亲就是', ()=>{
          const span=document.createElement('span'); span.className='name'; span.textContent='杨崴翔'; result.appendChild(span);
          play(reveal); vibrate([40,40,40]);
          requestAnimationFrame(()=>span.classList.add('show'));
          setTimeout(()=>{ play(boom); qr.classList.add('show'); }, 500);
        });
      }, 1200);
    }

    function typeLine(text, done){
      let i=0;
      const t=setInterval(()=>{
        result.textContent += text[i++]; play(tick, .25);
        if(i>=text.length){ clearInterval(t); done&&done(); }
      }, 140);
    }

    function play(a, vol=1){ try{ a.currentTime=0; a.volume=vol; a.play(); }catch(e){ hintbar.style.display='block'; } }
    function fadeTo(a, target, ms){ const start=a.volume, steps=20, step=(target-start)/steps; let i=0; const it=setInterval(()=>{ a.volume = Math.max(0, Math.min(1, a.volume+step)); if(++i>=steps) clearInterval(it); }, ms/steps); }
    function vibrate(p){ if(navigator.vibrate) navigator.vibrate(p); }
  </script></body>
</html>
