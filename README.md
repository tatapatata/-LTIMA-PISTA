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
          <div class="target" id="target">Meta: 2000 puntos</div>
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

    <footer>Hecho con cariño para Zaye — Puedes editar la meta desde el código.</footer>
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
    const SCORE_PER_TILE = 10;
    const TARGET_SCORE = 2000; // meta para llenar la barra

    // --- Canvas ---
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');

    canvas.width = COLS * TILE;
    canvas.height = ROWS * TILE;

    // --- Estado del juego ---
    let grid = []; // cada celda: {colorIndex, yOffset, popAnim}
    let score = 0;

    // For click handling
    let first = null; // {r,c}
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
      // avoid starting matches
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

    // --- Draw ---
    function draw(){
      ctx.clearRect(0,0,canvas.width,canvas.height);
      // background grid subtle
      for(let r=0;r<ROWS;r++){
        for(let c=0;c<COLS;c++){
          const x = c*TILE, y = r*TILE;
          // tile background
          ctx.fillStyle = 'rgba(255,255,255,0.02)';
          ctx.fillRect(x+2,y+2,TILE-4,TILE-4);

          const cell = grid[r][c];
          if(!cell) continue;
          const cx = x + TILE/2;
          const cy = y + TILE/2 + cell.yOffset;
          // pop animation scale
          const pop = 1 + cell.pop*0.35;
          ctx.save();
          ctx.translate(cx,cy);
          ctx.scale(pop,pop);
          // draw gem as circle with subtle inner gradient
          const g = ctx.createRadialGradient(-6,-6,4,0,0, TILE*0.6);
          g.addColorStop(0,'rgba(255,255,255,0.4)');
          g.addColorStop(1,'rgba(0,0,0,0.05)');
          ctx.beginPath();
          ctx.arc(0,0,TILE*0.33,0,Math.PI*2);
          ctx.fillStyle = COLORS[cell.color];
          ctx.fill();
          ctx.fillStyle = g;
          ctx.fill();
          ctx.restore();
        }
      }
      // draw selection
      if(first){
        ctx.strokeStyle = 'rgba(255,255,255,0.9)';
        ctx.lineWidth = 3;
        ctx.strokeRect(first.c*TILE+4, first.r*TILE+4, TILE-8, TILE-8);
      }
    }

    // --- Game logic: match detection ---
    function findMatches(){
      const matches = [];
      // rows
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
      // cols
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

    // remove matches: returns total tiles removed
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

    // --- Swap and helper ---
    function areAdjacent(a,b){
      return (Math.abs(a.r-b.r)+Math.abs(a.c-b.c))===1;
    }

    function swapCells(a,b){
      const tmp = grid[a.r][a.c].color;
      grid[a.r][a.c].color = grid[b.r][b.c].color;
      grid[b.r][b.c].color = tmp;
    }

    // --- Game loop & animation ---
    function step(dt){
      // simple pop decay
      for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++){
        const cell = grid[r][c];
        if(cell.pop>0) cell.pop = Math.max(0, cell.pop - dt*0.01);
        if(cell.yOffset!==0){
          // spring back to 0 for a tiny bounce effect
          cell.yOffset *= Math.pow(0.9, dt*0.06);
          if(Math.abs(cell.yOffset)<0.5) cell.yOffset = 0;
        }
      }
      draw();
      requestAnimationFrame(()=>step(16));
    }

    // --- Click handling ---
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
      // try swap
      animating = true;
      swapCells(first,second);
      const matches = findMatches();
      if(matches.length>0){
        // good swap
        processMatches(matches).then(()=>{ first=null; animating=false; });
      } else {
        // revert swap with tiny delay
        setTimeout(()=>{
          swapCells(first,second);
          first=null; animating=false;
        },200);
      }
    });

    // mobile: also support touch
    canvas.addEventListener('touchstart', e=>{ e.preventDefault(); const t=e.touches[0]; const rect=canvas.getBoundingClientRect(); const x=t.clientX-rect.left; const y=t.clientY-rect.top; const c=Math.floor(x/TILE), r=Math.floor(y/TILE); if(r<0||r>=ROWS||c<0||c>=COLS) return; if(!first) first={r,c}; else{ const second={r,c}; if(first.r===second.r && first.c===second.c){ first=null; return;} if(!areAdjacent(first,second)){ first=second; return;} if(animating) return; animating=true; swapCells(first,second); const matches=findMatches(); if(matches.length>0){ processMatches(matches).then(()=>{ first=null; animating=false; }); } else{ setTimeout(()=>{ swapCells(first,second); first=null; animating=false; },200); } } }, {passive:false});

    // Process matches chain reaction
    async function processMatches(initialMatches){
      let totalRemoved = 0;
      let matches = initialMatches;
      while(matches.length>0){
        // mark pop animation
        for(const m of matches){
          for(const cell of m){
            grid[cell.r][cell.c].pop = 1.2; // pop visual
          }
        }
        // small delay to show pop
        await sleep(220);
        const removed = removeMatches(matches);
        totalRemoved += removed;
        // award score
        const gained = removed * SCORE_PER_TILE;
        score += gained;
        updateUI();
        // tiny bounce: cells above move down visually
        for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++) grid[r][c].yOffset = (Math.random()-0.5)*6;
        await sleep(120);
        // continue chain
        matches = findMatches();
      }
      // after chain check for no possible moves: (simple refill shuffle)
      if(!hasPossibleMove()){
        shuffleGridUntilMove();
      }
      // check win
      if(score >= TARGET_SCORE) showWin();
      return totalRemoved;
    }

    function sleep(ms){return new Promise(res=>setTimeout(res,ms));}

    function updateUI(){
      document.getElementById('score').innerText = `Puntos: ${score}`;
      const pct = Math.min(100, Math.floor((score / TARGET_SCORE) * 100));
      document.getElementById('bar').style.width = pct + '%';
    }

    // Very naive check for possible moves: try swaps and see if they create a match
    function hasPossibleMove(){
      for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++){
        const dirs = [[1,0],[-1,0],[0,1],[0,-1]];
        for(const d of dirs){
          const r2=r+d[0], c2=c+d[1];
          if(r2<0||r2>=ROWS||c2<0||c2>=COLS) continue;
          swapCells({r,c},{r:r2,c:c2});
          const m = findMatches();
          swapCells({r,c},{r:r2,c:c2});
          if(m.length>0) return true;
        }
      }
      return false;
    }

    function shuffleGridUntilMove(){
      // random shuffle until a possible move exists
      let tries=0;
      do{
        // Fisher-Yates over colors
        const flat = [];
        for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++) flat.push(grid[r][c].color);
        for(let i=flat.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [flat[i],flat[j]]=[flat[j],flat[i]]; }
        let k=0; for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++) grid[r][c].color = flat[k++];
        tries++;
        if(tries>50) break;
      } while(!hasPossibleMove());
    }

    // Hint: find a swap that produces a match
    function findHint(){
      for(let r=0;r<ROWS;r++) for(let c=0;c<COLS;c++){
        const dirs = [[1,0],[-1,0],[0,1],[0,-1]];
        for(const d of dirs){
          const r2=r+d[0], c2=c+d[1];
          if(r2<0||r2>=ROWS||c2<0||c2>=COLS) continue;
          swapCells({r,c},{r:r2,c:c2});
          const m = findMatches();
          swapCells({r,c},{r:r2,c:c2});
          if(m.length>0) return [{r,c},{r:r2,c:c2}];
        }
      }
      return null;
    }

    document.getElementById('hint').addEventListener('click', ()=>{
      const h = findHint();
      if(!h) return alert('Sin sugerencias — se mezclará el tablero');
      // briefly show selection
      first = h[0];
      setTimeout(()=>{ first=null; }, 1200);
    });

    document.getElementById('restart').addEventListener('click', ()=>{
      score = 0; updateUI(); initGrid();
    });

    // Win modal handling
    function showWin(){
      document.getElementById('winModal').style.display = 'flex';
    }
    document.getElementById('closeWin').addEventListener('click', ()=>{
      document.getElementById('winModal').style.display = 'none';
    });

    // Initialize
    initGrid(); updateUI(); requestAnimationFrame(()=>step(16));

    // small utility: allow keyboard to adjust target (developer convenience)
    window.addEventListener('keydown', e=>{
      if(e.key==='+'||e.key==='='){ TARGET_SCORE += 200; document.getElementById('target').innerText = `Meta: ${TARGET_SCORE} puntos`; }
      if(e.key==='-'||e.key==='_' ){ TARGET_SCORE = Math.max(200, TARGET_SCORE-200); document.getElementById('target').innerText = `Meta: ${TARGET_SCORE} puntos`; }
    });

    // Ensure TARGET_SCORE variable reflect in UI
    document.getElementById('target').innerText = `Meta: ${TARGET_SCORE} puntos`;

  </script>
</body>
</html>
