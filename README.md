<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Juego para Zaye</title>
  <style>
    body {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      background: linear-gradient(135deg, #f9f7d9, #fbd1d1);
      font-family: Arial, sans-serif;
    }
    h1 {
      margin-bottom: 10px;
      color: #444;
    }
    canvas {
      border: 3px solid #ff8c94;
      border-radius: 12px;
      background: #fffbea;
      box-shadow: 0 6px 12px rgba(0,0,0,0.2);
    }
    #score {
      margin-top: 10px;
      font-size: 18px;
      font-weight: bold;
      color: #444;
    }
    #progress-container {
      width: 320px;
      height: 20px;
      background: #ddd;
      border-radius: 10px;
      margin-top: 10px;
      overflow: hidden;
    }
    #progress-bar {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #ff8c94, #fbc2eb);
      transition: width 0.3s ease;
    }
    #spotify {
      margin-top: 20px;
      display: none;
      font-size: 16px;
      background: #1db954;
      color: white;
      padding: 10px 15px;
      border-radius: 8px;
      text-decoration: none;
    }
  </style>
</head>
<body>
  <h1>Juego para Zaye</h1>
  <canvas id="gameCanvas" width="320" height="320"></canvas>
  <div id="score">Puntos: 0</div>
  <div id="progress-container">
    <div id="progress-bar"></div>
  </div>
  <a id="spotify" href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr&nd=1&dlsi=2eae3a6453e34fc8" target="_blank">🎵 Escuchar Playlist en Spotify</a>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const rows = 8;
    const cols = 8;
    const cellSize = 40;
    const scoreDisplay = document.getElementById("score");
    const progressBar = document.getElementById("progress-bar");
    const spotify = document.getElementById("spotify");

    const flowers = ["🌻","🌹","🌷","🌼","💮","🏵️"];
    let grid = [];
    let selected = null;
    let score = 0;

    function initGrid() {
      grid = [];
      for (let r = 0; r < rows; r++) {
        let row = [];
        for (let c = 0; c < cols; c++) {
          row.push(randomFlower());
        }
        grid.push(row);
      }
    }

    function randomFlower() {
      return flowers[Math.floor(Math.random() * flowers.length)];
    }

    function drawGrid() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
          const flower = grid[r][c];
          if (flower) {
            ctx.font = "30px Arial";
            ctx.textAlign = "center";
            ctx.textBaseline = "middle";
            ctx.fillText(flower, c * cellSize + cellSize/2, r * cellSize + cellSize/2);
          }
        }
      }
      if (selected) {
        ctx.strokeStyle = "gold";
        ctx.lineWidth = 3;
        ctx.strokeRect(selected.c * cellSize, selected.r * cellSize, cellSize, cellSize);
      }
    }

    function swap(r1,c1,r2,c2) {
      const temp = grid[r1][c1];
      grid[r1][c1] = grid[r2][c2];
      grid[r2][c2] = temp;
    }

    function checkMatch(r,c) {
      const flower = grid[r][c];
      if (!flower) return false;
      let horiz = [[r,c]];
      let vert = [[r,c]];
      // horizontal
      let col = c-1;
      while(col>=0 && grid[r][col]===flower){horiz.push([r,col]); col--;}
      col = c+1;
      while(col<cols && grid[r][col]===flower){horiz.push([r,col]); col++;}
      // vertical
      let row = r-1;
      while(row>=0 && grid[row][c]===flower){vert.push([row,c]); row--;}
      row = r+1;
      while(row<rows && grid[row][c]===flower){vert.push([row,c]); row++;}
      return horiz.length>=3? horiz : vert.length>=3? vert : false;
    }

    function removeMatch(matches) {
      return new Promise(resolve=>{
        let opacity = 1;
        const fade = setInterval(()=>{
          ctx.clearRect(0,0,canvas.width,canvas.height);
          drawGrid();
          ctx.globalAlpha = opacity;
          matches.forEach(([r,c])=>{
            ctx.fillStyle = "#fffbea";
            ctx.fillRect(c*cellSize, r*cellSize, cellSize, cellSize);
          });
          ctx.globalAlpha = 1;
          opacity -= 0.1;
          if(opacity<=0){
            clearInterval(fade);
            matches.forEach(([r,c])=>grid[r][c]=null);
            resolve();
          }
        },50);
      });
    }

    function applyGravity() {
      for(let c=0;c<cols;c++){
        for(let r=rows-1;r>=0;r--){
          if(!grid[r][c]){
            for(let nr=r-1;nr>=0;nr--){
              if(grid[nr][c]){
                grid[r][c]=grid[nr][c];
                grid[nr][c]=null;
                break;
              }
            }
          }
        }
        for(let r=0;r<rows;r++){
          if(!grid[r][c]){
            grid[r][c]=randomFlower();
          }
        }
      }
    }

    canvas.addEventListener("click", e=>{
      const rect = canvas.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      const c = Math.floor(x / cellSize);
      const r = Math.floor(y / cellSize);

      if (selected) {
        if (Math.abs(selected.r-r)+Math.abs(selected.c-c)===1){
          swap(selected.r,selected.c,r,c);
          let match1 = checkMatch(r,c);
          let match2 = checkMatch(selected.r,selected.c);
          if(match1 || match2){
            const match = match1 || match2;
            removeMatch(match).then(()=>{
              score += match.length*10;
              scoreDisplay.textContent = "Puntos: " + score;
              progressBar.style.width = Math.min((score/10000)*100,100)+"%";
              if(score>=10000) spotify.style.display="inline-block";
              applyGravity();
              drawGrid();
            });
          } else {
            swap(selected.r,selected.c,r,c);
          }
        }
        selected=null;
      } else {
        selected={r,c};
      }
      drawGrid();
    });

    initGrid();
    drawGrid();
  </script>
</body>
</html>
