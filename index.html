<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Juego para Zaye</title>
  <style>
    body {
      margin: 0;
      font-family: 'Comic Sans MS', cursive, sans-serif;
      background: linear-gradient(135deg, #ffe6f0, #fff8e7);
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      min-height: 100vh;
      overflow-x: hidden;
    }

    h1 {
      font-size: 2rem;
      color: #e91e63;
      text-align: center;
      margin-bottom: 10px;
      text-shadow: 2px 2px #fff;
      animation: pulse 2s infinite;
    }

    @keyframes pulse {
      0% { transform: scale(1); }
      50% { transform: scale(1.05); }
      100% { transform: scale(1); }
    }

    .scoreboard {
      background: white;
      padding: 15px 25px;
      border-radius: 20px;
      box-shadow: 0 5px 15px rgba(0,0,0,0.2);
      text-align: center;
      margin-bottom: 20px;
      animation: fadeIn 1s ease-in-out;
    }

    .progress-container {
      background: #eee;
      border-radius: 10px;
      overflow: hidden;
      height: 12px;
      margin-top: 10px;
    }

    .progress-bar {
      background: linear-gradient(90deg, #ff4081, #ff9800);
      height: 100%;
      width: 0%;
      transition: width 0.5s ease-in-out;
    }

    .game-board {
      display: grid;
      grid-template-columns: repeat(8, 60px);
      grid-template-rows: repeat(8, 60px);
      gap: 5px;
      margin: 20px auto;
      perspective: 1000px;
    }

    .tile {
      width: 60px;
      height: 60px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 28px;
      background: white;
      border: 3px solid #333;
      border-radius: 12px;
      cursor: pointer;
      user-select: none;
      box-shadow: 0 3px 6px rgba(0,0,0,0.2);
      transition: transform 0.25s ease, box-shadow 0.25s ease;
    }

    .tile:hover {
      transform: scale(1.1);
      box-shadow: 0 6px 12px rgba(0,0,0,0.3);
    }

    .fall {
      animation: fall 0.6s ease forwards;
    }

    @keyframes fall {
      from { transform: translateY(-100px); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }

    .fade-out {
      animation: fadeOut 0.5s forwards;
    }

    @keyframes fadeOut {
      from { opacity: 1; transform: scale(1); }
      to { opacity: 0; transform: scale(0.5); }
    }

    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }

    /* 🌸 Decoración extra */
    .flower-deco {
      position: absolute;
      font-size: 2rem;
      opacity: 0.7;
      animation: float 6s infinite ease-in-out;
    }

    @keyframes float {
      0%, 100% { transform: translateY(0) rotate(0deg); }
      50% { transform: translateY(-20px) rotate(20deg); }
    }

    .winner {
      font-size: 1.5rem;
      margin-top: 20px;
      text-align: center;
      color: #d81b60;
      animation: fadeIn 1s ease-in-out;
    }

  </style>
</head>
<body>
  <h1>🌸 Juego para Zaye 🌸</h1>
  <div class="scoreboard">
    <div>Puntos: <span id="score">0</span> / 10000</div>
    <div class="progress-container">
      <div class="progress-bar" id="progress-bar"></div>
    </div>
  </div>
  
  <div class="game-board" id="game-board"></div>

  <!-- Decoraciones flotantes -->
  <div class="flower-deco" style="top:10%; left:5%;">🌺</div>
  <div class="flower-deco" style="top:20%; right:10%;">🌸</div>
  <div class="flower-deco" style="bottom:15%; left:15%;">🌷</div>
  <div class="flower-deco" style="bottom:20%; right:20%;">🌻</div>

  <div id="winner-message" class="winner"></div>

  <script>
    const boardSize = 8;
    const flowers = ["🌸","🌻","🌷","🌼","💮"];
    const board = document.getElementById("game-board");
    let tiles = [];
    let firstTile = null;
    let score = 0;

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
          if (t1.textContent && t1.textContent === t2.textContent && t2.textContent === t3.textContent) {
            [t1, t2, t3].forEach(t => { 
              t.classList.add("fade-out");
              setTimeout(() => t.textContent = "", 500);
            });
            match = true;
            updateScore(100);
          }
        }
      }

      // columnas
      for (let c = 0; c < boardSize; c++) {
        for (let r = 0; r < boardSize - 2; r++) {
          let t1 = tiles[r][c], t2 = tiles[r+1][c], t3 = tiles[r+2][c];
          if (t1.textContent && t1.textContent === t2.textContent && t2.textContent === t3.textContent) {
            [t1, t2, t3].forEach(t => { 
              t.classList.add("fade-out");
              setTimeout(() => t.textContent = "", 500);
            });
            match = true;
            updateScore(100);
          }
        }
      }

      if (match) {
        setTimeout(dropTiles, 600);
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

      if (score >= 10000) {
        document.getElementById("winner-message").innerHTML = 
          `🎉 ¡Felicidades Zaye! 🎉<br>
          Aquí tienes tu playlist especial: <br>
          <a href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&nd=1&dlsi=2eae3a6453e34fc8" 
             target="_blank">🎵 Escuchar en Spotify</a>`;
      }
    }

    createBoard();
  </script>
</body>
</html>
