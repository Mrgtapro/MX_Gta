# MX_Gta
GTA Mexico (echo con chat GPT) 
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
  <title>Mini GTA México — Realista</title>
  <style>
    body { margin:0; background:#0f0f0f; color:#fff; font-family: Arial, sans-serif; touch-action:none; }
    #ui { text-align:center; padding:6px; font-size:14px; background:#111; }
    canvas { display:block; margin:auto; background:#1c1c1c; }
    #controls { position:fixed; bottom:10px; left:10px; right:10px; display:flex; justify-content:space-between; gap:10px; }
    .pad { background:#1a1a1a; border-radius:14px; padding:10px; display:grid; grid-template-columns:repeat(3,42px); grid-template-rows:repeat(3,42px); gap:6px; }
    .btn { background:#333; border-radius:10px; display:flex; align-items:center; justify-content:center; user-select:none; }
    .btn:active { background:#555; }
    .action { flex:1; background:#333; border-radius:14px; display:flex; align-items:center; justify-content:center; font-size:18px; }
  </style>
</head>
<body>
  <div id="ui"><b>Mini GTA México 🇲🇽</b> — estilo más realista (móvil)</div>
  <canvas id="game" width="360" height="560"></canvas>

  <div id="controls">
    <div class="pad">
      <div></div><div class="btn" data-dir="up">⬆️</div><div></div>
      <div class="btn" data-dir="left">⬅️</div><div></div><div class="btn" data-dir="right">➡️</div>
      <div></div><div class="btn" data-dir="down">⬇️</div><div></div>
    </div>
    <div class="action" id="act">MISIÓN</div>
  </div>

  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');

    const player = { x: 180, y: 420, speed: 2.6, anim:0 };
    const police = { x: 200, y: 120, speed: 1.9, anim:0 };
    const mission = { x: 180, y: 260, r: 14 };

    let dir = { up:false, down:false, left:false, right:false };
    let wanted = false;

    document.querySelectorAll('.btn').forEach(b=>{
      const d=b.dataset.dir;
      b.addEventListener('touchstart',e=>{e.preventDefault(); dir[d]=true;});
      b.addEventListener('touchend',e=>{e.preventDefault(); dir[d]=false;});
    });

    document.getElementById('act').addEventListener('touchstart', e=>{
      e.preventDefault();
      if (dist(player, mission) < 28) {
        wanted = true;
        mission.x = 40 + Math.random()*(canvas.width-80);
        mission.y = 120 + Math.random()*(canvas.height-200);
      }
    });

    function update() {
      let moving = false;
      if (dir.up) { player.y -= player.speed; moving=true; }
      if (dir.down) { player.y += player.speed; moving=true; }
      if (dir.left) { player.x -= player.speed; moving=true; }
      if (dir.right) { player.x += player.speed; moving=true; }

      player.x = Math.max(20, Math.min(canvas.width-20, player.x));
      player.y = Math.max(40, Math.min(canvas.height-20, player.y));

      if (moving) player.anim += 0.2;

      if (wanted) {
        follow(police, player, police.speed);
        police.anim += 0.15;
      }

      if (dist(player, police) < 18) {
        alert('🚓 Te atrapó la policía');
        reset();
      }
    }

    function follow(a,b,s){ if(a.x<b.x) a.x+=s; if(a.x>b.x) a.x-=s; if(a.y<b.y) a.y+=s; if(a.y>b.y) a.y-=s; }
    function dist(a,b){ return Math.hypot(a.x-b.x, a.y-b.y); }
    function reset(){ player.x=180; player.y=420; police.x=200; police.y=120; wanted=false; }

    function drawShadow(x,y){ ctx.fillStyle='rgba(0,0,0,0.4)'; ctx.beginPath(); ctx.ellipse(x, y+22, 10, 4, 0, 0, Math.PI*2); ctx.fill(); }

    function drawPerson(x,y,color,anim){
      drawShadow(x,y);
      const leg = Math.sin(anim)*4;
      // cabeza
      ctx.fillStyle='#f2c9ac'; ctx.beginPath(); ctx.arc(x, y-14, 7, 0, Math.PI*2); ctx.fill();
      // cuerpo
      ctx.fillStyle=color; ctx.fillRect(x-7, y-6, 14, 20);
      // piernas animadas
      ctx.fillRect(x-6, y+14, 4, 10+leg);
      ctx.fillRect(x+2, y+14, 4, 10-leg);
    }

    function draw() {
      ctx.clearRect(0,0,canvas.width,canvas.height);

      // fondo ciudad
      ctx.fillStyle='#2a2a2a'; ctx.fillRect(0,0,360,560);

      // calles
      ctx.fillStyle='#3f3f3f'; ctx.fillRect(0,300,360,90); ctx.fillRect(140,0,80,560);

      // banquetas
      ctx.fillStyle='#555'; ctx.fillRect(0,290,360,10); ctx.fillRect(0,390,360,10);
      ctx.fillRect(130,0,10,560); ctx.fillRect(210,0,10,560);

      // misión
      ctx.fillStyle='lime'; ctx.beginPath(); ctx.arc(mission.x, mission.y, mission.r, 0, Math.PI*2); ctx.fill();

      // policía
      drawPerson(police.x, police.y, '#1e6bd6', police.anim);

      // jugador
      drawPerson(player.x, player.y, wanted?'#c0392b':'#f39c12', player.anim);
    }

    function loop(){ update(); draw(); requestAnimationFrame(loop); }
    loop();
  </script>
</body>
</html>
