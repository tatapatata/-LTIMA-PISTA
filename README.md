<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>juego para zaye</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/addons/p5.dom.min.js"></script>
  <style>
    body {
      background: linear-gradient(135deg, #fceabb, #f8b500);
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      font-family: 'Comic Sans MS', cursive;
    }
    canvas {
      border: 8px solid #fff;
      border-radius: 20px;
      box-shadow: 0px 0px 30px rgba(255, 182, 193, 0.8);
    }
    h1 {
      position: absolute;
      top: 10px;
      color: #fff;
      text-shadow: 2px 2px 4px #000;
    }
    #spotify {
      display: none;
      position: absolute;
      bottom: 20px;
      font-size: 20px;
      background: #1db954;
      padding: 10px 20px;
      border-radius: 15px;
      text-decoration: none;
      color: white;
      font-weight: bold;
      box-shadow: 0px 0px 15px rgba(0,0,0,0.4);
    }
    #spotify:hover {
      background: #1ed760;
    }
  </style>
</head>
<body>
  <h1>🌸 juego para zaye 🌸</h1>
  <a id="spotify" href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr&nd=1&dlsi=2eae3a6453e34fc8" target="_blank">
    🎶 Abrir Playlist en Spotify
  </a>
  <script>
    let grid = [];
    let cols = 6;
    let rows = 8;
    let cellSize = 70;
    let flowers = ["🌹", "🌻", "🌷", "🌼", "💮"];
    let score = 0;
    let goal = 10000;
    let selected = [];
    let falling = [];
    let fading = [];

    function setup() {
      createCanvas(cols * cellSize, rows * cellSize + 60);
      initGrid();
    }

    function initGrid() {
      for (let x = 0; x < cols; x++) {
        grid[x] = [];
        for (let y = 0; y < rows; y++) {
          grid[x][y] = random(flowers);
        }
      }
    }

    function draw() {
      background(255, 182, 193);

      // Decoración flotante 🌸
      for (let i = 0; i < 20; i++) {
        let x = (frameCount * 0.5 + i * 50) % width;
        let y = (i * 60 + frameCount * 0.3) % height;
        textSize(20);
        text("🌸", x, y);
      }

      // Dibujar tablero
      textAlign(CENTER, CENTER);
      textSize(40);
      for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows; y++) {
          let flower = grid[x][y];
          if (flower) {
            if (fading[x + "," + y]) {
              push();
              let alpha = fading[x + "," + y];
              fill(0, alpha);
              text(flower, x * cellSize + cellSize / 2, y * cellSize + cellSize / 2);
              pop();
              fading[x + "," + y] -= 5;
              if (fading[x + "," + y] <= 0) {
                grid[x][y] = null;
                delete fading[x + "," + y];
              }
            } else {
              text(flower, x * cellSize + cellSize / 2, y * cellSize + cellSize / 2);
            }
          }
        }
      }

      // Caída de flores
      for (let x = 0; x < cols; x++) {
        for (let y = rows - 1; y >= 0; y--) {
          if (!grid[x][y]) {
            for (let k = y - 1; k >= 0; k--) {
              if (grid[x][k]) {
                grid[x][y] = grid[x][k];
                grid[x][k] = null;
                break;
              }
            }
            if (!grid[x][y]) {
              grid[x][y] = random(flowers);
            }
          }
        }
      }

      // Barra de progreso
      fill(255);
      rect(10, height - 40, width - 20, 20, 10);
      let progress = map(score, 0, goal, 0, width - 20);
      fill(50, 205, 50);
      rect(10, height - 40, progress, 20, 10);

      // Puntos
      fill(0);
      textSize(20);
      text("Puntos: " + score, width / 2, height - 55);

      // Mostrar Spotify si llega a meta
      if (score >= goal) {
        document.getElementById("spotify").style.display = "block";
      }
    }

    function mousePressed() {
      let x = floor(mouseX / cellSize);
      let y = floor(mouseY / cellSize);
      if (x >= 0 && x < cols && y >= 0 && y < rows) {
        selected.push({x, y});
        if (selected.length == 2) {
          swap(selected[0], selected[1]);
          selected = [];
        }
      }
    }

    function swap(a, b) {
      let temp = grid[a.x][a.y];
      grid[a.x][a.y] = grid[b.x][b.y];
      grid[b.x][b.y] = temp;

      let matches = checkMatches();
      if (matches.length == 0) {
        // Revertir si no hay match
        grid[b.x][b.y] = grid[a.x][a.y];
        grid[a.x][a.y] = temp;
      } else {
        // Fade out en las coincidencias
        for (let m of matches) {
          fading[m.x + "," + m.y] = 255;
        }
        score += matches.length * 110;
      }
    }

    function checkMatches() {
      let matches = [];
      // Horizontal
      for (let y = 0; y < rows; y++) {
        for (let x = 0; x < cols - 2; x++) {
          if (grid[x][y] && grid[x][y] === grid[x + 1][y] && grid[x][y] === grid[x + 2][y]) {
            matches.push({x, y}, {x: x + 1, y}, {x: x + 2, y});
          }
        }
      }
      // Vertical
      for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows - 2; y++) {
          if (grid[x][y] && grid[x][y] === grid[x][y + 1] && grid[x][y] === grid[x][y + 2]) {
            matches.push({x, y}, {x, y: y + 1}, {x, y: y + 2});
          }
        }
      }
      return matches;
    }
  </script>
</body>
</html>
