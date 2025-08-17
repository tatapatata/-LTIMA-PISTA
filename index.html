<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Juego para Zaye</title>
  <style>
    body {
      font-family: 'Arial Rounded MT Bold', sans-serif;
      background: linear-gradient(135deg, #f9f7f1, #f1f7f9);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
    }

    h1 {
      color: #ff69b4;
      text-shadow: 2px 2px #fff;
    }

    #game {
      display: grid;
      grid-template-columns: repeat(8, 60px);
      grid-template-rows: repeat(8, 60px);
      gap: 5px;
      margin: 20px 0;
    }

    .cell {
      width: 60px;
      height: 60px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 28px;
      cursor: pointer;
      transition: transform 0.25s ease, opacity 0.4s ease;
    }

    .cell.fading {
      opacity: 0;
      transform: scale(0.6);
    }

    #score {
      font-size: 20px;
      margin: 10px;
    }

    #progress-container {
      width: 500px;
      height: 25px;
      background: #ddd;
      border-radius: 12px;
      overflow: hidden;
      margin-bottom: 20px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.15);
    }

    #progress-bar {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #ff69b4, #ffa500);
      transition: width 0.4s ease;
    }

    #decor {
      font-size: 40px;
      margin-top: 10px;
      animation: float 3s ease-in-out infinite;
    }

    @keyframes float {
      0%, 100% { transform: translateY(0); }
      50% { transform: translateY(-8px); }
    }

    #spotify {
      display: none;
      margin-top: 20px;
      font-size: 18px;
    }

    #spotify a {
      color: #1DB954;
      font-weight: bold;
      text-decoration: none;
    }

  </style>
</head>
<body>
  <h1>🌻 Juego para Zaye 🌻</h1>
  <div id="score">Puntaje: 0</div>
  <div id="progress-container"><div id="progress-bar"></div></div>
  <div id="game"></div>
  <div id="decor">🌸🌼🌺</div>
  <div id="spotify">
    🎶 ¡Felicidades! Has desbloqueado la playlist: 
    <a href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr&nd=1&dlsi=2eae3a6453e34fc8" target="_blank">
      Escúchala aquí 💖
    </a>
  </div>

  <script>
    const game = document.getElementById("game");
    const scoreDisplay = document.getElementById("score");
    const progressBar = document.getElementById("progress-bar");
    const spotify = document.getElementById("spotify");

    const flowers = ["🌻","🌸","🌼","🌺","🌷","💐"];
    const size = 8;
    let grid = [];
    let score = 0;
    const goal = 10000;

    function createBoard() {
      grid = [];
      game.innerHTML = "";
      for (let i = 0; i < size * size; i++) {
        const cell = document.createElement("div");
        cell.classList.add("cell");
        const flower = flowers[Math.floor(Math.random() * flowers.length)];
        cell.textContent = flower;
        cell.dataset.index = i;
        cell.addEventListener("click", () => selectCell(i));
        game.appendChild(cell);
        grid.push(cell);
      }
    }

    let firstSelection = null;
    function selectCell(index) {
      if (firstSelection === null) {
        firstSelection = index;
        grid[index].style.transform = "scale(1.2)";
      } else {
        swap(firstSelection, index);
        grid[firstSelection].style.transform = "scale(1)";
        firstSelection = null;
      }
    }

    function swap(i, j) {
      const temp = grid[i].textContent;
      grid[i].textContent = grid[j].textContent;
      grid[j].textContent = temp;
      checkMatches();
    }

    function checkMatches() {
      let matches = [];
      // Horizontal
      for (let r = 0; r < size; r++) {
        for (let c = 0; c < size - 2; c++) {
          let i = r * size + c;
          let f = grid[i].textContent;
          if (f && f === grid[i+1].textContent && f === grid[i+2].textContent) {
            matches.push(i, i+1, i+2);
          }
        }
      }
      // Vertical
      for (let c = 0; c < size; c++) {
        for (let r = 0; r < size - 2; r++) {
          let i = r * size + c;
          let f = grid[i].textContent;
          if (f && f === grid[i+size].textContent && f === grid[i+2*size].textContent) {
            matches.push(i, i+size, i+2*size);
          }
        }
      }

      if (matches.length > 0) {
        removeMatches([...new Set(matches)]);
      }
    }

    function removeMatches(matches) {
      matches.forEach(i => {
        grid[i].classList.add("fading");
      });
      setTimeout(() => {
        matches.forEach(i => {
          grid[i].classList.remove("fading");
          grid[i].textContent = flowers[Math.floor(Math.random() * flowers.length)];
        });
        updateScore(matches.length);
        checkMatches();
      }, 400); // tiempo del fade
    }

    function updateScore(count) {
      let points = 110 * Math.floor(count/3);
      score += points;
      score = Math.floor(score); // asegura enteros
      scoreDisplay.textContent = "Puntaje: " + score;
      progressBar.style.width = Math.min((score/goal)*100, 100) + "%";

      if (score >= goal) {
        spotify.style.display = "block";
      }
    }

    createBoard();
  </script>
</body>
</html>
