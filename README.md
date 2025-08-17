<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Juego de Flores 🌻</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f9f9f9;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    h1 {
      margin: 10px;
    }
    #board {
      display: grid;
      grid-template-columns: repeat(8, 60px);
      grid-template-rows: repeat(8, 60px);
      gap: 5px;
      margin-top: 20px;
    }
    .tile {
      width: 60px;
      height: 60px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 32px;
      cursor: pointer;
      transition: transform 0.25s ease, top 0.3s ease;
      position: relative;
    }
    #score {
      margin-top: 15px;
      font-size: 20px;
    }
    #win {
      margin-top: 20px;
      font-size: 22px;
      color: green;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>🌸 Match de Flores 🌸</h1>
  <div id="board"></div>
  <div id="score">Puntos: 0 / 10000</div>
  <div id="win"></div>

<script>
const boardSize = 8;
const flowers = ["🌻","🌹","🌷","🌼","💮","🌺"];
let board = [];
let score = 0;
const goal = 10000;

function createBoard() {
  const boardDiv = document.getElementById("board");
  boardDiv.innerHTML = "";
  board = [];
  for (let row = 0; row < boardSize; row++) {
    const rowArr = [];
    for (let col = 0; col < boardSize; col++) {
      const tile = document.createElement("div");
      tile.classList.add("tile");
      tile.setAttribute("data-row", row);
      tile.setAttribute("data-col", col);
      tile.innerText = flowers[Math.floor(Math.random()*flowers.length)];
      tile.addEventListener("click", tileClick);
      boardDiv.appendChild(tile);
      rowArr.push(tile);
    }
    board.push(rowArr);
  }
}

let firstTile = null;
function tileClick(e) {
  const tile = e.target;
  if (!firstTile) {
    firstTile = tile;
    tile.style.transform = "scale(1.2)";
  } else if (tile === firstTile) {
    firstTile.style.transform = "scale(1)";
    firstTile = null;
  } else {
    swapTiles(firstTile, tile);
    firstTile.style.transform = "scale(1)";
    firstTile = null;
  }
}

function swapTiles(t1, t2) {
  const r1 = parseInt(t1.getAttribute("data-row"));
  const c1 = parseInt(t1.getAttribute("data-col"));
  const r2 = parseInt(t2.getAttribute("data-row"));
  const c2 = parseInt(t2.getAttribute("data-col"));

  if (Math.abs(r1-r2)+Math.abs(c1-c2) !== 1) return; // solo adyacentes

  // Intercambio
  const temp = t1.innerText;
  t1.innerText = t2.innerText;
  t2.innerText = temp;

  if (checkMatches()) {
    processMatches();
  } else {
    // revertir si no hay match
    setTimeout(()=>{
      const tempBack = t1.innerText;
      t1.innerText = t2.innerText;
      t2.innerText = tempBack;
    }, 300);
  }
}

function checkMatches() {
  let found = false;
  // filas
  for (let r=0;r<boardSize;r++) {
    for (let c=0;c<boardSize-2;c++) {
      const t1 = board[r][c].innerText;
      const t2 = board[r][c+1].innerText;
      const t3 = board[r][c+2].innerText;
      if (t1 === t2 && t2 === t3) found = true;
    }
  }
  // columnas
  for (let c=0;c<boardSize;c++) {
    for (let r=0;r<boardSize-2;r++) {
      const t1 = board[r][c].innerText;
      const t2 = board[r+1][c].innerText;
      const t3 = board[r+2][c].innerText;
      if (t1 === t2 && t2 === t3) found = true;
    }
  }
  return found;
}

function processMatches() {
  let matches = [];

  // filas
  for (let r=0;r<boardSize;r++) {
    for (let c=0;c<boardSize-2;c++) {
      const t1 = board[r][c];
      const t2 = board[r][c+1];
      const t3 = board[r][c+2];
      if (t1.innerText && t1.innerText===t2.innerText && t2.innerText===t3.innerText) {
        let combo = [t1,t2,t3];
        let k=c+3;
        while (k<boardSize && board[r][k].innerText===t1.innerText){
          combo.push(board[r][k]);
          k++;
        }
        matches.push(combo);
        c=k-1;
      }
    }
  }

  // columnas
  for (let c=0;c<boardSize;c++) {
    for (let r=0;r<boardSize-2;r++) {
      const t1 = board[r][c];
      const t2 = board[r+1][c];
      const t3 = board[r+2][c];
      if (t1.innerText && t1.innerText===t2.innerText && t2.innerText===t3.innerText) {
        let combo = [t1,t2,t3];
        let k=r+3;
        while (k<boardSize && board[k][c].innerText===t1.innerText){
          combo.push(board[k][c]);
          k++;
        }
        matches.push(combo);
        r=k-1;
      }
    }
  }

  if (matches.length>0) {
    matches.forEach(combo=>{
      let basePoints = 110;
      let multiplier = combo.length-2; // 3=1x, 4=2x, etc.
      score += Math.floor(basePoints*multiplier);
      combo.forEach(tile=>tile.innerText="");
    });
    updateScore();
    applyGravity();
    setTimeout(()=>{
      refillBoard();
      if (checkMatches()) processMatches();
    },300);
  }
}

function applyGravity() {
  for (let c=0;c<boardSize;c++) {
    for (let r=boardSize-1;r>=0;r--) {
      if (board[r][c].innerText==="") {
        for (let k=r-1;k>=0;k--) {
          if (board[k][c].innerText!=="") {
            board[r][c].innerText = board[k][c].innerText;
            board[k][c].innerText = "";
            break;
          }
        }
      }
    }
  }
}

function refillBoard() {
  for (let r=0;r<boardSize;r++) {
    for (let c=0;c<boardSize;c++) {
      if (board[r][c].innerText==="") {
        board[r][c].innerText = flowers[Math.floor(Math.random()*flowers.length)];
      }
    }
  }
}

function updateScore() {
  document.getElementById("score").innerText = `Puntos: ${score} / ${goal}`;
  if (score>=goal) {
    document.getElementById("win").innerHTML = 
      `🎉 ¡Ganaste! <a href="https://open.spotify.com/" target="_blank">Escucha tu premio aquí</a>`;
  }
}

createBoard();
</script>
</body>
</html>
