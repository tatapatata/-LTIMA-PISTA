<script>
const boardSize = 8;
const flowers = ["🌸","🌻","🌷","🌼","💮"];
const board = document.getElementById("game-board");
let tiles = [];
let firstTile = null;
let score = 0;
let linkShown = false; // para que no aparezca varias veces

function createBoard() {
  tiles = [];
  board.innerHTML = "";
  for (let r = 0; r < boardSize; r++) {
    let row = [];
    for (let c = 0; c < boardSize; c++) {
      let tile = document.createElement("div");
      tile.classList.add("tile");
      tile.dataset.row = r;
      tile.dataset.col = c;
      tile.textContent = flowers[Math.floor(Math.random() * flowers.length)];
      tile.addEventListener("click", () => selectTile(tile));
      board.appendChild(tile);
      row.push(tile);
    }
    tiles.push(row);
  }
}

function selectTile(tile) {
  if (!firstTile) {
    firstTile = tile;
    tile.style.transform = "scale(1.2)";
    return;
  }
  if (firstTile === tile) {
    firstTile.style.transform = "scale(1)";
    firstTile = null;
    return;
  }
  swapTiles(firstTile, tile);
  if (!checkMatches()) {
    swapTiles(firstTile, tile); // 🔄 revertir si no hay match
  }
  firstTile.style.transform = "scale(1)";
  firstTile = null;
}

function swapTiles(tile1, tile2) {
  const temp = tile1.textContent;
  tile1.textContent = tile2.textContent;
  tile2.textContent = temp;
}

function checkMatches() {
  let match = false;
  // filas
  for (let r = 0; r < boardSize; r++) {
    for (let c = 0; c < boardSize - 2; c++) {
      let t1 = tiles[r][c], t2 = tiles[r][c+1], t3 = tiles[r][c+2];
      if (t1.textContent === t2.textContent && t2.textContent === t3.textContent) {
        [t1, t2, t3].forEach(t => { t.textContent = ""; });
        match = true;
        updateScore(100);
      }
    }
  }
  // columnas
  for (let c = 0; c < boardSize; c++) {
    for (let r = 0; r < boardSize - 2; r++) {
      let t1 = tiles[r][c], t2 = tiles[r+1][c], t3 = tiles[r+2][c];
      if (t1.textContent === t2.textContent && t2.textContent === t3.textContent) {
        [t1, t2, t3].forEach(t => { t.textContent = ""; });
        match = true;
        updateScore(100);
      }
    }
  }
  if (match) {
    setTimeout(dropTiles, 400);
  }
  return match;
}

function dropTiles() {
  for (let c = 0; c < boardSize; c++) {
    let empty = [];
    for (let r = boardSize - 1; r >= 0; r--) {
      if (tiles[r][c].textContent === "") {
        empty.push(r);
      } else if (empty.length > 0) {
        let newR = empty.shift();
        tiles[newR][c].textContent = tiles[r][c].textContent;
        tiles[r][c].textContent = "";
        empty.push(r);
      }
    }
    // rellenar arriba
    for (let r = 0; r < boardSize; r++) {
      if (tiles[r][c].textContent === "") {
        tiles[r][c].textContent = flowers[Math.floor(Math.random() * flowers.length)];
        tiles[r][c].classList.add("fall");
        setTimeout(() => tiles[r][c].classList.remove("fall"), 600);
      }
    }
  }
  setTimeout(checkMatches, 400);
}

function updateScore(points) {
  score += points;
  document.getElementById("score").textContent = score;
  const progress = Math.min((score / 10000) * 100, 100);
  document.getElementById("progress-bar").style.width = progress + "%";

  // 🎵 Mostrar link cuando llegue a 10000
  if (score >= 10000 && !linkShown) {
    linkShown = true;
    const link = document.createElement("a");
    link.href = "https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr&nd=1&dlsi=2eae3a6453e34fc8";
    link.textContent = "🎶 ¡Felicidades! Aquí tienes tu playlist 🎶";
    link.target = "_blank";
    link.style.display = "block";
    link.style.marginTop = "20px";
    link.style.fontSize = "1.2rem";
    link.style.color = "#e91e63";
    link.style.textDecoration = "none";
    link.style.fontWeight = "bold";
    document.body.appendChild(link);
  }
}

createBoard();
</script>
