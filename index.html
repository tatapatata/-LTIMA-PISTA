<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Para Zaye — Mini Candy Crush</title>
  <style>
    :root{--bg:#0f1724;--panel:#0b1220;--accent:#f59e0b}
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,'Helvetica Neue',Arial}
    body{background:linear-gradient(180deg,#071023 0%, #0b1220 100%);color:#e6eef8;display:flex;align-items:center;justify-content:center}
    .wrap{width:880px;padding:18px;border-radius:14px;background:linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));box-shadow:0 10px 30px rgba(2,6,23,0.6)}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:10px}
    h1{font-size:18px;margin:0}
    .info{font-size:13px;opacity:.9}
    .board-area{display:flex;gap:16px}
    canvas{background:linear-gradient(180deg,#082032,#051226);border-radius:8px;box-shadow:0 6px 18px rgba(2,6,23,0.5);cursor:pointer}
    .side{width:260px}
    .panel{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));padding:12px;border-radius:10px}
    .score{font-size:24px;text-align:center;margin-bottom:8px}
    .bar-wrap{height:18px;background:rgba(255,255,255,0.06);border-radius:12px;overflow:hidden}
    .bar{height:100%;width:0%;background:linear-gradient(90deg,#f97316,#ef4444);transition:width 300ms}
    .target{font-size:12px;text-align:center;margin-top:6px;opacity:.9}
    .controls{display:flex;gap:8px;margin-top:10px}
    button{flex:1;padding:8px;border-radius:8px;border:0;background:#0b1220;color:#e6eef8;cursor:pointer}
    .overlay{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(2,6,23,0.6);backdrop-filter:blur(3px);}
    .modal{background:#071428;padding:22px;border-radius:12px;text-align:center;width:420px}
    .modal a{display:inline-block;margin-top:12px;padding:10px 14px;background:linear-gradient(90deg,#1db954,#0aa14b);color:white;border-radius:8px;text-decoration:none}
    footer{margin-top:8px;font-size:12px;text-align:center;opacity:.7}
    @media(max-width:920px){.wrap{width:94vw}.side{display:none}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Para Zaye ❤️ — Un juego pequeño</h1>
      <div class="info">Intercambia fichas adyacentes — completa la barra para una sorpresa 🎵</div>
    </header>

    <div class="board-area">
      <canvas id="game" width="512" height="512"></canvas>

      <div class="side">
        <div class="panel">
          <div class="score" id="score">Puntos: 0</div>
          <div class="bar-wrap">
            <div class="bar" id="bar"></div>
          </div>
          <div class="target" id="target">Meta: 10000 puntos</div>
          <div class="controls">
            <button id="restart">Reiniciar</button>
            <button id="hint">Sugerencia</button>
          </div>
          <div style="margin-top:12px;font-size:13px;opacity:.9">
            <strong>Instrucciones:</strong>
            <p style="margin:6px 0">Toca (o haz click) una ficha y luego otra adyacente para intercambiar. Forma 3 o más en línea para eliminarlas — la barra de la derecha se llenará con tus puntos. Cuando la barra se llene, aparecerá una sorpresa.</p>
          </div>
        </div>
      </div>
    </div>

    <footer>Hecho con cariño para Zaye</footer>
  </div>

  <div id="winModal" class="overlay" style="display:none">
    <div class="modal">
      <h2>¡Lo lograste! 🎉</h2>
      <p>Has llenado la barra. Aquí está tu playlist especial:</p>
      <a id="spotifyLink" href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr" target="_blank" rel="noopener">Abrir playlist en Spotify</a>
      <div style="margin-top:10px"><button id="closeWin">Cerrar</button></div>
    </div>
  </div>

  <script>
    // --- Configuración ---
    const ROWS = 8, COLS = 8; // tablero
    const TILE = 64; // pixeles
    const COLORS = ['#ff6b6b','#ffd166','#06d6a0','#4d96ff','#c77dff','#ff9f1c'];
    const BASE_SCORE_PER_TILE = 35; // para que 3 fichas ~ 105 puntos
    const TARGET_SCORE = 10000; // meta

    // --- Canvas ---
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');

    canvas.width = COLS * TILE;
    canvas.height = ROWS * TILE;

    // --- Estado del juego ---
    let grid = [];
    let score = 0;
    let first = null;
    let animating = false;

    function randInt(n){return Math.floor(Math.random()*n)}

    function initGrid(){
      grid = [];
      for(let r=0;r<ROWS;r++){
        const row = [];
        for(let c=0;c<COLS;c++){
          row.push({color:randInt(COLORS.length), yOffset:0, pop:0});
        }
        grid.push(row);
      }
      eliminateAllMatchesAtStart();
    }

    function eliminateAllMatchesAtStart(){
      let tries = 0;
      while(true){
        const matches = findMatches();
        if(matches.length===0) break;
        for(const m of matches){
          for(const cell of m){
            grid[cell.r][cell.c].color = randInt(COLORS.length);
          }
        }
        if(++tries>10) break;
      }
    }

    function draw(){
      ctx.clearRect(0,0,canvas.width,canvas.height);
      for(let r=0;r<ROWS;r++){
        for(let c=0;c<COLS;c++){
          const x = c*TILE, y = r*TILE;
          const cell = grid[r][c];
          if(!cell) continue;
          const cx = x + TILE/2;
          const cy = y + TILE/2 + cell.yOffset;
          const pop = 1 + cell.pop*0.35;
          ctx.save();
          ctx.translate(cx,cy);
          ctx.scale(pop,pop);
          // Dibujar como flor en vez de círculo
          ctx.fillStyle = COLORS[cell.color];
          drawFlower(ctx, 6, TILE*0.18);
          ctx.restore();
        }
      }
      if(first){
        ctx.strokeStyle = 'rgba(255,255,255,0.9)';
        ctx.lineWidth = 3;
        ctx.strokeRect(first.c*TILE+4, first.r*TILE+4, TILE-8, TILE-8);
      }
    }

    function drawFlower(ctx, petals, radius){
      ctx.beginPath();
      for(let i=0;i<petals;i++){
        const angle = (i * 2 * Math.PI) / petals;
        const x = Math.cos(angle) * radius;
        const y = Math.sin(angle) * radius;
        ctx.moveTo(0,0);
        ctx.arc(x,y,radius/2,0,Math.PI*2);
      }
      ctx.fill();
    }

    function findMatches(){
      const matches = [];
      for(let r=0;r<ROWS;r++){
        let runColor = -1, runStart=0, runLen=0;
        for(let c=0;c<=COLS;c++){
          const color = c<COLS?grid[r][c].color:-1;
          if(color===runColor){runLen++;} else {
            if(runLen>=3){
              const cells = [];
              for(let k=runStart;k<runStart+runLen;k++) cells.push({r,c:k});
              matches.push(cells);
            }
            runColor=color; runStart=c; runLen=1;
          }
        }
      }
      for(let c=0;c<COLS;c++){
        let runColor=-1, runStart=0, runLen=0;
        for(let r=0;r<=ROWS;r++){
          const color = r<ROWS?grid[r][c].color:-1;
          if(color===runColor){runLen++;} else {
            if(runLen>=3){
              const cells = [];
              for(let k=runStart;k<runStart+runLen;k++) cells.push({r:k,c});
              matches.push(cells);
            }
            runColor=color; runStart=r; runLen=1;
          }
        }
      }
      return matches;
    }

    function removeMatches(matches){
      const mark = Array.from({length:ROWS}, ()=>Array(COLS,false));
      for(const m of matches) for(const cell of m) mark[cell.r][cell.c]=true;
      let removed = 0;
      for(let c=0;c<COLS;c++){
        let write=ROWS-1;
        for(let r=ROWS-1;r>=0;r--){
          if(!mark[r][c]){
            grid[write][c].color = grid[r][c].color;
            write--;
          }
        }
        for(let r=write;r>=0;r--){
          grid[r][c].color = randInt(COLORS.length);
        }
      }
      for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++) if(mark[r][c]) removed++;
      return removed;
    }

    function areAdjacent(a,b){
      return (Math.abs(a.r-b.r)+Math.abs(a.c-b.c))===1;
    }

    function swapCells(a,b){
      const tmp = grid[a.r][a.c].color;
      grid[a.r][a.c].color = grid[b.r][b.c].color;
      grid[b.r][b.c].color = tmp;
    }

    function step(dt){
      for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++){
        const cell = grid[r][c];
        if(cell.pop>0) cell.pop = Math.max(0, cell.pop - dt*0.01);
        if(cell.yOffset!==0){
          cell.yOffset *= Math.pow(0.9, dt*0.06);
          if(Math.abs(cell.yOffset)<0.5) cell.yOffset = 0;
        }
      }
      draw();
      requestAnimationFrame(()=>step(16));
    }

    canvas.addEventListener('click', e=>{
      if(animating) return;
      const rect = canvas.getBoundingClientRect();
      const x = e.clientX - rect.left, y = e.clientY - rect.top;
      const c = Math.floor(x / TILE), r = Math.floor(y / TILE);
      if(r<0||r>=ROWS||c<0||c>=COLS) return;
      if(!first){ first = {r,c}; return; }
      const second = {r,c};
      if(first.r===second.r && first.c===second.c){ first=null; return; }
      if(!areAdjacent(first,second)){ first=second; return; }
      animating = true;
      swapCells(first,second);
      const matches = findMatches();
      if(matches.length>0){
        processMatches(matches).then(()=>{ first=null; animating=false; });
      } else {
        setTimeout(()=>{
          swapCells(first,second);
          first=null; animating=false;
        },200);
      }
    });

    async function processMatches(initialMatches){
      let matches = initialMatches;
      while(matches.length>0){
        for(const m of matches){
          for(const cell of m){
            grid[cell.r][cell.c].pop = 1.2;
          }
        }
        await sleep(220);
        const removed = removeMatches(matches);
        // multiplicador según el tamaño del match
        for(const m of matches){
          const mult = m.length >= 4 ? m.length/3 : 1;
          score += m.length * BASE_SCORE_PER_TILE * mult;
        }
        updateUI();
        for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++) grid[r][c].yOffset = (Math.random()-0.5)*6;
        await sleep(120);
        matches = findMatches();
      }
      if(score >= TARGET_SCORE) showWin();
    }

    function sleep(ms){return new Promise(res=>setTimeout(res,ms));}

    function updateUI(){
      document.getElementById('score').innerText = `Puntos: ${score}`;
      const pct = Math.min(100, Math.floor((score / TARGET_SCORE) * 100));
      document.getElementById('bar').style.width = pct + '%';
    }

    function showWin(){
      document.getElementById('winModal').style.display = 'flex';
    }
    document.getElementById('closeWin').addEventListener('click', ()=>{
      document.getElementById('winModal').style.display = 'none';
    });

    document.getElementById('restart').addEventListener('click', ()=>{
      score = 0; updateUI(); initGrid();
    });

    initGrid(); updateUI(); requestAnimationFrame(()=>step(16));

    document.getElementById('target').innerText = `Meta: ${TARGET_SCORE} puntos`;

  </script>
</body>
</html>
