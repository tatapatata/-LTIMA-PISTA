<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Juego para Zaye</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      background: linear-gradient(135deg, #ffe6f0, #e6f7ff);
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      overflow: hidden;
    }
    h1 {
      font-size: 2.5rem;
      font-weight: bold;
      color: #ff66b2;
      text-shadow: 2px 2px 6px rgba(0,0,0,0.15);
    }
    canvas {
      background: white;
      border-radius: 20px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.2);
      display: block;
      margin: auto;
    }
    #progress-container {
      width: 80%;
      height: 30px;
      background: #ddd;
      border-radius: 15px;
      margin: 20px auto;
      overflow: hidden;
      box-shadow: inset 0 4px 8px rgba(0,0,0,0.2);
    }
    #progress-bar {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #ff66b2, #ffcc00);
      transition: width 0.5s ease-in-out;
    }
    .decor {
      position: absolute;
      font-size: 2rem;
      opacity: 0.6;
      pointer-events: none;
    }
    .fade-out {
      animation: fadeOut 0.6s forwards;
    }
    @keyframes fadeOut {
      from { opacity: 1; transform: scale(1); }
      to { opacity: 0; transform: scale(0.6); }
    }
  </style>
</head>
<body>
  <div class="text-center mt-6">
    <h1>🌸 Juego para Zaye 🌸</h1>
  </div>
  <div id="progress-container"><div id="progress-bar"></div></div>
  <canvas id="gameCanvas" width="480" height="480"></canvas>
  
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const rows = 8, cols = 8, size = 60;
    const flowers = ["🌻","🌹","🌷","🌼","💐","🌸"];
    let board = [];
    let selected = null;
    let score = 0;
    const target = 10000;

    // Crear decoración flotante
    function createDecor() {
      const decor = document.createElement("div");
      decor.className = "decor";
      decor.innerText = flowers[Math.floor(Math.random()*flowers.length)];
      decor.style.left = Math.random()*window.innerWidth + "px";
      decor.style.top = "-50px";
      document.body.appendChild(decor);

      let y = -50;
      const fall = setInterval(() => {
        y += 2;
        decor.style.top = y+"px";
        if(y > window.innerHeight+50){
          clearInterval(fall);
          decor.remove();
        }
      }, 50);
    }
    setInterval(createDecor, 2000);

    function randomFlower(){ return flowers[Math.floor(Math.random()*flowers.length)]; }

    function initBoard(){
      board = [];
      for(let r=0;r<rows;r++){
        board[r] = [];
        for(let c=0;c<cols;c++){
          board[r][c] = randomFlower();
        }
      }
    }

    function drawBoard(){
      ctx.clearRect(0,0,canvas.width,canvas.height);
      for(let r=0;r<rows;r++){
        for(let c=0;c<cols;c++){
          const flower = board[r][c];
          if(flower){
            ctx.font="40px Segoe UI Emoji";
            ctx.textAlign="center";
            ctx.textBaseline="middle";
            ctx.fillText(flower, c*size+size/2, r*size+size/2);
          }
        }
      }
    }

    function swap(r1,c1,r2,c2){
      const temp = board[r1][c1];
      board[r1][c1] = board[r2][c2];
      board[r2][c2] = temp;
    }

    function findMatches(){
      let matches = [];
      // horizontales
      for(let r=0;r<rows;r++){
        for(let c=0;c<cols-2;c++){
          let f=board[r][c];
          if(f && f===board[r][c+1] && f===board[r][c+2]){
            let m=[[r,c],[r,c+1],[r,c+2]];
            c+=2;
            while(c+1<cols && board[r][c+1]===f){
              m.push([r,c+1]); c++;
            }
            matches.push(m);
          }
        }
      }
      // verticales
      for(let c=0;c<cols;c++){
        for(let r=0;r<rows-2;r++){
          let f=board[r][c];
          if(f && f===board[r+1][c] && f===board[r+2][c]){
            let m=[[r,c],[r+1,c],[r+2,c]];
            r+=2;
            while(r+1<rows && board[r+1][c]===f){
              m.push([r+1,c]); r++;
            }
            matches.push(m);
          }
        }
      }
      return matches;
    }

    function removeMatches(matches){
      matches.forEach(m=>{
        score += m.length*110;
        m.forEach(([r,c])=>{
          board[r][c]=null;
        });
      });
      updateProgress();
    }

    function collapse(){
      for(let c=0;c<cols;c++){
        let empty=[];
        for(let r=rows-1;r>=0;r--){
          if(board[r][c]==null) empty.push(r);
          else if(empty.length>0){
            let er=empty.shift();
            board[er][c]=board[r][c];
            board[r][c]=null;
            empty.push(r);
          }
        }
      }
    }

    function refill(){
      for(let r=0;r<rows;r++){
        for(let c=0;c<cols;c++){
          if(board[r][c]==null){
            board[r][c]=randomFlower();
          }
        }
      }
    }

    function updateProgress(){
      let percent=Math.min(100,(score/target)*100);
      document.getElementById("progress-bar").style.width=percent+"%";
      if(score>=target){
        setTimeout(()=>{
          alert("🎉 ¡Felicidades Zaye! 🌸 Has ganado 🌸\nAquí está tu sorpresa:\nhttps://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw");
        },500);
      }
    }

    canvas.addEventListener("click",(e)=>{
      const rect=canvas.getBoundingClientRect();
      const x=e.clientX-rect.left;
      const y=e.clientY-rect.top;
      const r=Math.floor(y/size), c=Math.floor(x/size);
      if(selected){
        const [r0,c0]=selected;
        if(Math.abs(r-r0)+Math.abs(c-c0)===1){
          swap(r0,c0,r,c);
          let matches=findMatches();
          if(matches.length>0){
            processMatches(matches);
          } else {
            swap(r0,c0,r,c);
          }
          selected=null;
        } else selected=[r,c];
      } else {
        selected=[r,c];
      }
      drawBoard();
    });

    function processMatches(matches){
      removeMatches(matches);
      drawBoard();
      setTimeout(()=>{
        collapse();
        refill();
        let newMatches=findMatches();
        if(newMatches.length>0){
          processMatches(newMatches);
        } else drawBoard();
      },400);
    }

    initBoard();
    drawBoard();
  </script>
</body>
</html>
