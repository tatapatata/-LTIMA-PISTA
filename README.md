<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Juego de Flores — Para Zaye</title>
<style>
  :root{
    --tile-size:64;
    --gap:8;
    --rows:8;
    --cols:8;
  }
  html,body{height:100%;margin:0;font-family:system-ui,Arial;background:linear-gradient(180deg,#fff0f6,#fff9ec);display:flex;align-items:center;justify-content:center}
  .container{width:calc(var(--cols) * (var(--tile-size)px) + (var(--cols) - 1) * var(--gap)px + 40px);padding:20px;border-radius:14px;box-shadow:0 12px 30px rgba(0,0,0,0.12);background:linear-gradient(180deg,#fff,#fff8f9)}
  h1{margin:0 0 10px 0;color:#c2185b;text-align:center}
  #hud{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px;gap:12px}
  #scoreText{font-weight:600;color:#333}
  #progress{flex:1;height:18px;background:#eee;border-radius:9px;margin-left:12px;overflow:hidden;box-shadow:inset 0 -2px 6px rgba(0,0,0,0.06)}
  #progressBar{height:100%;width:0%;background:linear-gradient(90deg,#ff7ab6,#ffb86b);transition:width 450ms cubic-bezier(.2,.9,.3,1)}
  /* board wrapper: position relative to host absolutely positioned tiles */
  #boardWrap{width:calc(var(--cols) * (var(--tile-size)px) + (var(--cols) - 1) * var(--gap)px);height:calc(var(--rows) * (var(--tile-size)px) + (var(--rows) - 1) * var(--gap)px);background:linear-gradient(180deg,#ffffff,#fff7fb);border-radius:12px;padding:10px;box-sizing:border-box;margin:0 auto;position:relative}
  .tile{
    position:absolute;
    width:calc(var(--tile-size)px);
    height:calc(var(--tile-size)px);
    display:flex;align-items:center;justify-content:center;
    font-size:30px;line-height:1;
    border-radius:12px;
    background:#fff;
    box-shadow:0 6px 12px rgba(0,0,0,0.12);
    border:3px solid rgba(0,0,0,0.85); /* borde oscuro para accesibilidad */
    transform-origin:center;
    user-select:none;
    touch-action:none;
  }
  .tile.selected{box-shadow:0 12px 20px rgba(0,0,0,0.18);transform:scale(1.08)}
  /* pop (match) animation via web-animations in JS, but keep fallback */
  .invisible{opacity:0}
  #winBox{display:none;margin-top:14px;text-align:center}
  #winBox.show{display:block}
  #winBox a{display:inline-block;margin-top:8px;padding:8px 12px;background:linear-gradient(90deg,#1db954,#0ca84a);color:white;border-radius:8px;text-decoration:none}
  /* small helper to avoid pointer events while animating */
  .no-pointer{pointer-events:none;opacity:0.98}
  /* responsive */
  @media(max-width:760px){:root{--tile-size:48;--gap:6}.container{width:95vw}}
</style>
</head>
<body>
  <div class="container">
    <h1>🌻 Juego de Flores — Para Zaye</h1>
    <div id="hud">
      <div id="scoreText">Puntos: <span id="score">0</span> / <strong id="goalText">10000</strong></div>
      <div id="progress"><div id="progressBar"></div></div>
    </div>

    <div id="boardWrap" aria-hidden="false"></div>

    <div id="winBox"><div>🎉 <strong>¡Lo lograste!</strong></div>
      <div style="margin-top:6px">Has completado la meta.</div>
      <a id="spotifyLink" href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr" target="_blank" rel="noopener">Abrir playlist especial</a>
    </div>
  </div>

<script>
/* Juego reescrito: tiles absolutamente posicionadas, animaciones con Web Animations API,
   físicas simuladas: caída con duración proporcional a distancia + bounce, pop para match,
   chain reactions soportadas.
   Puntos: siempre enteros -> puntos por grupo = Math.round(110 * (L / 3))
*/

const ROWS = 8, COLS = 8;
const TILE = parseInt(getComputedStyle(document.documentElement).getPropertyValue('--tile-size')) || 64;
const GAP = parseInt(getComputedStyle(document.documentElement).getPropertyValue('--gap')) || 8;
const GOAL = 10000;
const BASE_POINTS_PER_3 = 110; // aprox para 3 -> 110

const flowers = ["🌻","🌹","🌷","🌼","🌺","🌸"]; // formas distintas para daltónicos
let score = 0;
const boardWrap = document.getElementById('boardWrap');
const scoreEl = document.getElementById('score');
const progressBar = document.getElementById('progressBar');
const winBox = document.getElementById('winBox');
document.getElementById('goalText').textContent = GOAL;

let tilesById = new Map(); // id -> tileData {id,type,row,col,el}
let board = []; // board[row][col] = id or null
let idCounter = 1;
let selectedId = null;
let animating = false;

// UTIL: position in pixels
function posFor(rc){ // returns {left, top}
  return {
    left: rc.col * (TILE + GAP),
    top: rc.row * (TILE + GAP)
  };
}

function randomFlowerType(){ return Math.floor(Math.random() * flowers.length); }

function createTile(row, col, type=null){
  const id = idCounter++;
  if(type===null) type = randomFlowerType();
  const el = document.createElement('div');
  el.className = 'tile';
  el.dataset.id = id;
  el.setAttribute('role','button');
  el.style.left = (col * (TILE + GAP)) + 'px';
  el.style.top = (row * (TILE + GAP)) + 'px';
  el.style.width = TILE + 'px';
  el.style.height = TILE + 'px';
  el.style.fontSize = Math.round(TILE * 0.48) + 'px';
  el.innerHTML = `<span style="filter: drop-shadow(0 1px 0 rgba(0,0,0,0.15));">${flowers[type]}</span>`;
  boardWrap.appendChild(el);

  const tile = { id, type, row, col, el };
  tilesById.set(id, tile);

  // events
  el.addEventListener('pointerdown', (e)=>{
    if(animating) return;
    handleSelect(id);
  });

  return tile;
}

function buildInitialBoard(){
  board = Array.from({length:ROWS}, ()=>Array(COLS).fill(null));
  tilesById.clear();
  boardWrap.innerHTML = '';
  idCounter = 1;
  // fill randomly then remove any direct starting matches
  for(let r=0;r<ROWS;r++){
    for(let c=0;c<COLS;c++){
      const t = createTile(r,c);
      board[r][c] = t.id;
    }
  }
  // ensure no initial matches by reshuffling tiles that create matches
  let safety = 0;
  while(true){
    const matches = findMatchesInternal();
    if(matches.length===0) break;
    // replace matched tiles with new random ones
    for(const group of matches){
      for(const pos of group){
        const r=pos.r,c=pos.c;
        // remove old tile element
        const oldId = board[r][c];
        const oldTile = tilesById.get(oldId);
        if(oldTile){ oldTile.el.remove(); tilesById.delete(oldId); }
        const nt = createTile(r,c);
        board[r][c] = nt.id;
      }
    }
    if(++safety>10) break;
  }
}

// Find matches - returns array of groups: each group = [{r,c},...]
function findMatchesInternal(){
  const groups = [];
  // horizontal
  for(let r=0;r<ROWS;r++){
    let c=0;
    while(c < COLS){
      const id = board[r][c];
      if(!id){ c++; continue; }
      const t = tilesById.get(id);
      let len=1;
      while(c+len < COLS){
        const id2 = board[r][c+len];
        if(!id2) break;
        if(tilesById.get(id2).type === t.type) len++; else break;
      }
      if(len >= 3){
        const group = [];
        for(let k=0;k<len;k++) group.push({r, c: c+k});
        groups.push(group);
      }
      c += Math.max(1,len);
    }
  }
  // vertical
  for(let c=0;c<COLS;c++){
    let r=0;
    while(r < ROWS){
      const id = board[r][c];
      if(!id){ r++; continue; }
      const t = tilesById.get(id);
      let len=1;
      while(r+len < ROWS){
        const id2 = board[r+len][c];
        if(!id2) break;
        if(tilesById.get(id2).type === t.type) len++; else break;
      }
      if(len >= 3){
        const group = [];
        for(let k=0;k<len;k++) group.push({r: r+k, c});
        groups.push(group);
      }
      r += Math.max(1,len);
    }
  }
  // groups may overlap; we should merge overlapping positions into distinct union groups
  // create set of positions -> group id
  const posKey = p => `${p.r},${p.c}`;
  const allPos = new Map(); // key -> true
  groups.forEach(g => g.forEach(p => allPos.set(posKey(p), true)));
  // Reconstruct disjoint groups: flood fill on positions in allPos using adjacency (4-neigh)
  const remaining = new Set(allPos.keys());
  const finalGroups = [];
  while(remaining.size){
    const it = remaining.values().next().value;
    const queue = [it];
    remaining.delete(it);
    const comp = [];
    while(queue.length){
      const key = queue.shift();
      const [r,c] = key.split(',').map(Number);
      comp.push({r,c});
      const neigh = [[r-1,c],[r+1,c],[r,c-1],[r,c+1]];
      for(const n of neigh){
        const nk = `${n[0]},${n[1]}`;
        if(remaining.has(nk)){
          remaining.delete(nk);
          queue.push(nk);
        }
      }
    }
    finalGroups.push(comp);
  }
  return finalGroups;
}

// selects / swaps
function handleSelect(id){
  if(selectedId === id){
    // deselect
    const t = tilesById.get(id);
    if(t) t.el.classList.remove('selected');
    selectedId = null;
    return;
  }
  if(!selectedId){
    selectedId = id;
    const t = tilesById.get(id);
    if(t) t.el.classList.add('selected');
    return;
  }
  // try swap with selectedId
  const a = tilesById.get(selectedId);
  const b = tilesById.get(id);
  if(!a || !b) { clearSelection(); return; }
  // check adjacency
  if(Math.abs(a.row - b.row) + Math.abs(a.col - b.col) !== 1){
    // not adjacent: change selection
    a.el.classList.remove('selected');
    selectedId = id;
    b.el.classList.add('selected');
    return;
  }
  // perform swap animation and logic
  clearSelection();
  doSwap(a,b).catch(err=>console.error(err));
}

function clearSelection(){
  if(selectedId){
    const t = tilesById.get(selectedId);
    if(t) t.el.classList.remove('selected');
  }
  selectedId = null;
}

async function doSwap(a,b){
  if(animating) return;
  animating = true;
  // animate visually swap
  await animateSwap(a,b);
  // swap in board structure
  [board[a.row][a.col], board[b.row][b.col]] = [board[b.row][b.col], board[a.row][a.col]];
  // swap their row/col values
  const ar=a.row, ac=a.col, br=b.row, bc=b.col;
  a.row = br; a.col = bc;
  b.row = ar; b.col = ac;
  // after swap check matches
  let groups = findMatchesInternal();
  if(groups.length===0){
    // revert swap
    await animateSwap(a,b);
    // revert in board
    [board[a.row][a.col], board[b.row][b.col]] = [board[b.row][b.col], board[a.row][a.col]];
    // restore coords
    a.row = ar; a.col = ac; b.row = br; b.col = bc;
    animating = false;
    return;
  }
  // process matches chains until none
  await processAllMatches();
  animating = false;
}

// animate swap between two tiles (their .el), swapping left/top css
function animateSwap(tA, tB){
  return new Promise(resolve=>{
    const aEl = tA.el, bEl = tB.el;
    const aPos = posFor(tA), bPos = posFor(tB);
    // duration small for smoothness
    const dur = 220;
    const animA = aEl.animate([
      { transform: 'translate(0,0)' },
      { transform: `translate(${bPos.left - aPos.left}px, ${bPos.top - aPos.top}px)` }
    ], {duration:dur, easing:'cubic-bezier(.2,.9,.3,1)', fill:'forwards'});
    const animB = bEl.animate([
      { transform: 'translate(0,0)' },
      { transform: `translate(${aPos.left - bPos.left}px, ${aPos.top - bPos.top}px)` }
    ], {duration:dur, easing:'cubic-bezier(.2,.9,.3,1)', fill:'forwards'});
    animA.onfinish = animB.onfinish = ()=>{
      // after finishing, clear transforms and set absolute positions to new places
      aEl.style.transform = '';
      bEl.style.transform = '';
      aEl.style.left = bPos.left + 'px';
      aEl.style.top = bPos.top + 'px';
      bEl.style.left = aPos.left + 'px';
      bEl.style.top = aPos.top + 'px';
      resolve();
    };
  });
}

// core: process matches loop (remove -> drop -> refill -> repeat)
async function processAllMatches(){
  while(true){
    const groups = findMatchesInternal();
    if(groups.length===0) break;
    // compute points for each group and remove animation
    let gained = 0;
    const allRemovedIds = new Set();
    for(const g of groups){
      const L = g.length;
      // points approx: 3 -> 110 ; for L use linear scaling approx: points = round(110 * (L/3))
      const pts = Math.round(BASE_POINTS_PER_3 * (L / 3));
      gained += pts;
      // mark removed
      g.forEach(p => {
        const id = board[p.r][p.c];
        if(id) allRemovedIds.add(id);
      });
    }
    // animate pop for all removed
    await animatePopAndRemove(Array.from(allRemovedIds));
    // update score
    score += gained;
    scoreEl.textContent = score;
    progressBar.style.width = Math.min(100, Math.round((score / GOAL) * 100)) + '%';
    if(score >= GOAL){
      winBox.classList.add('show');
      winBox.classList.add('show'); // ensure displayed
    }
    // drop existing tiles and fill new ones with animation
    await dropAndFill();
    // loop to check new matches (chain)
  }
}

// animate pop for removed ids, then remove elements and clear board references
function animatePopAndRemove(ids){
  return new Promise(resolve=>{
    if(ids.length===0){ resolve(); return; }
    const anims = [];
    for(const id of ids){
      const tile = tilesById.get(id);
      if(!tile) continue;
      const el = tile.el;
      // small scale+fade
      const a = el.animate([
        { transform: 'scale(1)', opacity: 1 },
        { transform: 'scale(1.5)', opacity: 0.65, offset: 0.6 },
        { transform: 'scale(0)', opacity: 0 }
      ], { duration: 300, easing: 'cubic-bezier(.23,1,.32,1)', fill:'forwards' });
      anims.push(a);
    }
    // after all complete, remove elements and clear board coordinates
    Promise.all(anims.map(a => a.finished)).then(()=>{
      for(const id of ids){
        const t = tilesById.get(id);
        if(!t) continue;
        t.el.remove();
        // set board cell to null
        if(typeof t.row === 'number' && typeof t.col === 'number') board[t.row][t.col] = null;
        tilesById.delete(id);
      }
      resolve();
    }).catch(()=>{ resolve(); });
  });
}

// drop existing tiles down column by column and create new tiles above
async function dropAndFill(){
  // We'll animate each tile moving to its new row position with a bounce.
  const animations = [];
  // For each column compute stack
  for(let c=0;c<COLS;c++){
    // collect existing tiles in this column (top -> bottom)
    const stack = [];
    for(let r=0;r<ROWS;r++){
      const id = board[r][c];
      if(id) stack.push(tilesById.get(id));
    }
    // number of new tiles needed
    const missing = ROWS - stack.length;
    // place tiles at bottom: their new row = missing + index
    for(let i=0;i<stack.length;i++){
      const tile = stack[i];
      const targetRow = missing + i;
      if(tile.row !== targetRow){
        // animate tile from current top to target top
        const fromTop = tile.row * (TILE + GAP);
        const toTop = targetRow * (TILE + GAP);
        // duration proportional to distance
        const distanceRows = Math.abs(tile.row - targetRow);
        const duration = 180 + distanceRows * 60; // ms
        // keyframes with slight overshoot/bounce
        const anim = tile.el.animate([
          { transform: `translateY(0px)` },
          { transform: `translateY(${(toTop - fromTop) + 10}px)`, offset: 0.8 },
          { transform: `translateY(${(toTop - fromTop)}px)` }
        ], { duration, easing: 'cubic-bezier(.2,.9,.3,1)', fill:'forwards' });
        animations.push(anim.finished.then(()=> {
          // finalize position
          tile.el.style.transform = '';
          tile.el.style.top = toTop + 'px';
          tile.row = targetRow;
          board[targetRow][c] = tile.id;
        }));
        // clear old location in board (will be set when finalized)
        board[tile.row][c] = null;
      } else {
        // unchanged, ensure board mapping consistent
        board[tile.row][c] = tile.id;
      }
    }
    // create missing tiles at negative rows (above board) and animate falling to rows 0..missing-1
    for(let k=0;k<missing;k++){
      const rFinal = k;
      const type = randomFlowerType();
      const nt = createTile(- (missing - k), c, type); // start above
      // place initial top style (already set by createTile with negative row)
      // actually createTile placed with row value -N? We set row param negative so posFor will compute negative; ensure top calc works.
      // animate falling into rFinal
      const fromTop = nt.row * (TILE + GAP); // negative
      const toTop = rFinal * (TILE + GAP);
      const distanceRows = Math.abs(nt.row - rFinal);
      const duration = 200 + distanceRows * 70;
      const anim = nt.el.animate([
        { transform: `translateY(0px)` },
        { transform: `translateY(${(toTop - fromTop) + 14}px)`, offset: 0.78 },
        { transform: `translateY(${(toTop - fromTop)}px)` }
      ], { duration, easing: 'cubic-bezier(.15,.9,.35,1)', fill:'forwards' });
      animations.push(anim.finished.then(()=>{
        nt.el.style.transform = '';
        nt.el.style.top = toTop + 'px';
        nt.row = rFinal;
        board[rFinal][c] = nt.id;
      }));
    }
  }
  // wait all animations finish
  try{ await Promise.all(animations); }catch(e){}
}

// Initialize and start
function startGame(){
  buildInitialBoard();
  // update score UI
  score = 0;
  scoreEl.textContent = score;
  progressBar.style.width = '0%';
  winBox.classList.remove('show');
}

startGame();

// keyboard: reset with R
window.addEventListener('keydown', (e)=>{ if(e.key.toLowerCase()==='r') startGame(); });

</script>
</body>
</html>
