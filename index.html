<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Juego para Zaye 🌸</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to bottom, #ffe4f2, #fff8e7);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
    }
    h1 {
      color: #ff69b4;
      margin-bottom: 10px;
    }
    #game {
      display: grid;
      grid-template-columns: repeat(8, 60px);
      grid-template-rows: repeat(8, 60px);
      gap: 5px;
      background: #fff;
      padding: 10px;
      border-radius: 15px;
      box-shadow: 0 8px 20px rgba(0,0,0,0.2);
    }
    .cell {
      width: 60px;
      height: 60px;
      font-size: 32px;
      text-align: center;
      line-height: 60px;
      cursor: pointer;
      user-select: none;
      border-radius: 12px;
      background: #f9f9f9;
      box-shadow: 0 3px 6px rgba(0,0,0,0.15);
      transition: transform 0.25s ease, opacity 0.25s ease;
    }
    .cell.match {
      transform: scale(1.3);
      opacity: 0;
    }
    #score-container {
      margin: 15px;
      font-size: 18px;
      color: #333;
    }
    #progress {
      width: 400px;
      height: 25px;
      background: #ddd;
      border-radius: 12px;
      overflow: hidden;
      margin-top: 5px;
    }
    #progress-bar {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #ff69b4, #ffa500);
      transition: width 0.5s ease;
    }
    #win-message {
      display: none;
      font-size: 20px;
      color: #28a745;
      margin-top: 20px;
    }
    #win-message a {
      color: #007bff;
      text-decoration: none;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>🌸 Juego para Zaye 🌻</h1>
  <div id="score-container">
    Puntos: <span id="score">0</span> / 10000
    <div id="progress"><div id="progress-bar"></div></div>
  </div>
  <div id="game"></div>
  <div id="win-message">
    🎉 ¡Felicidades, Zaye! 🌹 Has completado el juego.<br>
    Aquí está tu sorpresa:  
    <a href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr" target="_blank">Tu playlist especial 💖</a>
  </div>

  <script>
    const flowers = ["🌻","🌹","🌷","🌼","🌺","🌸"];
    const size = 8;
    const goal = 10000;
    let score = 0;
    let board = [];

    const game = document.getElementById("game");
    const scoreEl = document.getElementById("score");
    const progressBar = document.getElementById("progress-bar");
    const winMessage = document.getElementById("win-message");

    // Inicializar tablero
    function initBoard() {
      board = [];
      game.innerHTML = "";
      for (let r = 0; r < size; r++) {
        const row = [];
        for (let c = 0; c < size; c++) {
          const flower = randomFlower();
          row.push(flower);
          const cell = createCell(r, c, flower);
          game.appendChild(cell);
        }
        board.push(row);
      }
    }

    function randomFlower() {
      return flowers[Math.floor(Math.random() * flowers.length)];
    }

    function createCell(r, c, flower) {
      const cell = document.createElement("div");
      cell.className = "cell";
      cell.dataset.row = r;
      cell.dataset.col = c;
      cell.textContent = flower;
      cell.addEventListener("click", () => selectCell(r, c));
      return cell;
    }

    let firstCell = null;
    function selectCell(r, c) {
      const cell = document.querySelector(`[data-row='${r}'][data-col='${c}']`);
      if (!firstCell) {
        firstCell = cell;
        cell.style.transform = "scale(1.2)";
      } else {
        swapCells(firstCell, cell);
        firstCell.style.transform = "scale(1)";
        firstCell = null;
      }
    }

    function swapCells(cell1, cell2) {
      const r1 = +cell1.dataset.row;
      const c1 = +cell1.dataset.col;
      const r2 = +cell2.dataset.row;
      const c2 = +cell2.dataset.col;
      if (Math.abs(r1-r2)+Math.abs(c1-c2) !== 1) return;

      [board[r1][c1], board[r2][c2]] = [board[r2][c2], board[r1][c1]];
      updateBoard();
      if (!checkMatches()) {
        [board[r1][c1], board[r2][c2]] = [board[r2][c2], board[r1][c1]];
        updateBoard();
      }
    }

    function updateBoard() {
      for (let r = 0; r < size; r++) {
        for (let c = 0; c < size; c++) {
          const cell = document.querySelector(`[data-row='${r}'][data-col='${c}']`);
          cell.textContent = board[r][c];
        }
      }
    }

    function checkMatches() {
      let matches = [];
      // Horizontal
      for (let r = 0; r < size; r++) {
        for (let c = 0; c < size-2; c++) {
          let f = board[r][c];
          if (f && f === board[r][c+1] && f === board[r][c+2]) {
            let length = 3;
            while (c+length < size && board[r][c+length] === f) length++;
            matches.push({r, c, length, horizontal: true});
            c += length-1;
          }
        }
      }
      // Vertical
      for (let c = 0; c < size; c++) {
        for (let r = 0; r < size-2; r++) {
          let f = board[r][c];
          if (f && f === board[r+1][c] && f === board[r+2][c]) {
            let length = 3;
            while (r+length < size && board[r+length][c] === f) length++;
            matches.push({r, c, length, horizontal: false});
            r += length-1;
          }
        }
      }
      if (matches.length > 0) {
        removeMatches(matches);
        return true;
      }
      return false;
    }

    function removeMatches(matches) {
      let points = 0;
      matches.forEach(m => {
        const basePoints = 110;
        const multiplier = m.length - 2;
        points += basePoints * multiplier;
        for (let i = 0; i < m.length; i++) {
          let r = m.r + (m.horizontal ? 0 : i);
          let c = m.c + (m.horizontal ? i : 0);
          const cell = document.querySelector(`[data-row='${r}'][data-col='${c}']`);
          cell.classList.add("match");
          setTimeout(() => { board[r][c] = null; }, 250);
        }
      });
      score += points;
      scoreEl.textContent = score;
      progressBar.style.width = Math.min(100, (score/goal)*100) + "%";

      if (score >= goal) {
        winMessage.style.display = "block";
      }

      setTimeout(dropFlowers, 300);
    }

    function dropFlowers() {
      for (let c = 0; c < size; c++) {
        let empty = 0;
        for (let r = size-1; r >= 0; r--) {
          if (!board[r][c]) {
            empty++;
          } else if (empty > 0) {
            board[r+empty][c] = board[r][c];
            board[r][c] = null;
          }
        }
        for (let r = 0; r < empty; r++) {
          board[r][c] = randomFlower();
        }
      }
      updateBoard();
      setTimeout(() => { if (checkMatches()) return; }, 300);
    }

    initBoard();
  </script>
</body>
</html>
