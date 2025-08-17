<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>juego para zaye</title>
  <style>
    body {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background: linear-gradient(135deg, #f9f7f7, #dbe9f4);
      font-family: 'Comic Sans MS', cursive, sans-serif;
      overflow: hidden;
    }

    h1 {
      margin-bottom: 10px;
      color: #333;
      text-shadow: 2px 2px 4px rgba(0,0,0,0.2);
    }

    #gameBoard {
      display: grid;
      grid-template-columns: repeat(8, 60px);
      grid-template-rows: repeat(8, 60px);
      gap: 5px;
      background: rgba(255,255,255,0.8);
      padding: 10px;
      border-radius: 20px;
      box-shadow: 0px 4px 12px rgba(0,0,0,0.2);
      position: relative;
    }

    .flower {
      width: 60px;
      height: 60px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 32px;
      cursor: pointer;
      user-select: none;
      transition: transform 0.2s ease;
    }

    .flower:hover {
      transform: scale(1.1);
    }

    .fade-out {
      animation: fadeOut 0.6s forwards;
    }

    @keyframes fadeOut {
      from { opacity: 1; transform: scale(1); }
      to { opacity: 0; transform: scale(0.6); }
    }

    #scoreBoard {
      margin-top: 10px;
      font-size: 20px;
      font-weight: bold;
    }

    #progressContainer {
      margin-top: 10px;
      width: 500px;
      height: 25px;
      background: #ddd;
      border-radius: 15px;
      overflow: hidden;
      box-shadow: inset 2px 2px 6px rgba(0,0,0,0.2);
    }

    #progressBar {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #f9a825, #f57c00);
      border-radius: 15px;
      transition: width 0.5s ease;
    }

    #confetti {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
      overflow: hidden;
    }

    .confetti-piece {
      position: absolute;
      width: 10px;
      height: 10px;
      background-color: hsl(var(--hue), 70%, 50%);
      top: -10px;
      animation: fall 3s linear forwards;
    }

    @keyframes fall {
      to {
        transform: translateY(100vh) rotate(720deg);
      }
    }
  </style>
</head>
<body>
  <h1>🌻 juego para zaye 🌻</h1>
  <div id="gameBoard"></div>
  <div id="scoreBoard">Puntos: 0</div>

  <div id="progressContainer">
    <div id="progressBar"></div>
  </div>

  <div id="confetti"></div>

  <script>
    const board = document.getElementById("gameBoard");
    const scoreBoard = document.getElementById("scoreBoard");
    const progressBar = document.getElementById("progressBar");
    const confettiContainer = document.getElementById("confetti");
    const width = 8;
    const flowers = ["🌻","🌸","🌹","🌷","🌼","💮"]; // distintos tipos para accesibilidad
    let squares = [];
    let score = 0;
    const targetScore = 10000;

    // Crear tablero
    function createBoard() {
      for (let i = 0; i < width*width; i++) {
        const square = document.createElement("div");
        square.setAttribute("draggable", true);
        square.setAttribute("id", i);
        square.classList.add("flower");
        let randomFlower = flowers[Math.floor(Math.random() * flowers.length)];
        square.textContent = randomFlower;
        board.appendChild(square);
        squares.push(square);
      }
    }
    createBoard();

    // Drag and drop
    let colorBeingDragged;
    let colorBeingReplaced;
    let squareIdBeingDragged;
    let squareIdBeingReplaced;

    squares.forEach(square => square.addEventListener("dragstart", dragStart));
    squares.forEach(square => square.addEventListener("dragend", dragEnd));
    squares.forEach(square => square.addEventListener("dragover", e => e.preventDefault()));
    squares.forEach(square => square.addEventListener("dragenter", e => e.preventDefault()));
    squares.forEach(square => square.addEventListener("drop", dragDrop));

    function dragStart(){
      colorBeingDragged = this.textContent;
      squareIdBeingDragged = parseInt(this.id);
    }

    function dragDrop(){
      colorBeingReplaced = this.textContent;
      squareIdBeingReplaced = parseInt(this.id);
      squares[squareIdBeingDragged].textContent = colorBeingReplaced;
      squares[squareIdBeingReplaced].textContent = colorBeingDragged;
    }

    function dragEnd(){
      let validMoves = [
        squareIdBeingDragged -1,
        squareIdBeingDragged -width,
        squareIdBeingDragged +1,
        squareIdBeingDragged +width
      ];
      let validMove = validMoves.includes(squareIdBeingReplaced);
      if (squareIdBeingReplaced && validMove){
        squareIdBeingReplaced = null;
      } else if (squareIdBeingReplaced && !validMove){
        squares[squareIdBeingReplaced].textContent = colorBeingReplaced;
        squares[squareIdBeingDragged].textContent = colorBeingDragged;
      }
    }

    // Revisar combinaciones
    function checkMatches(){
      for (let i = 0; i < squares.length; i++){
        let rowOfThree = [i, i+1, i+2];
        let decidedFlower = squares[i].textContent;
        const isBlank = decidedFlower === "";

        if (i % width < 6){
          if (rowOfThree.every(index => squares[index].textContent === decidedFlower && !isBlank)){
            rowOfThree.forEach(index => {
              squares[index].classList.add("fade-out");
              setTimeout(()=> squares[index].textContent = "", 600);
            });
            score += 110;
          }
        }

        let columnOfThree = [i, i+width, i+width*2];
        if (i < 48){
          if (columnOfThree.every(index => squares[index].textContent === decidedFlower && !isBlank)){
            columnOfThree.forEach(index => {
              squares[index].classList.add("fade-out");
              setTimeout(()=> squares[index].textContent = "", 600);
            });
            score += 110;
          }
        }
      }
      scoreBoard.textContent = "Puntos: " + score;
      progressBar.style.width = (score/targetScore*100) + "%";
      if (score >= targetScore){
        launchConfetti();
        setTimeout(()=>{
          window.open("https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr&nd=1&dlsi=2eae3a6453e34fc8", "_blank");
        }, 1500);
      }
    }

    // Hacer que caigan las flores
    function moveDown(){
      for (let i = 0; i < 56; i++){
        if (squares[i + width].textContent === ""){
          squares[i + width].textContent = squares[i].textContent;
          squares[i].textContent = "";
        }
      }
      for (let i = 0; i < width; i++){
        if (squares[i].textContent === ""){
          let randomFlower = flowers[Math.floor(Math.random() * flowers.length)];
          squares[i].textContent = randomFlower;
        }
      }
    }

    // Confetti
    function launchConfetti(){
      for(let i=0; i<100; i++){
        const piece = document.createElement("div");
        piece.classList.add("confetti-piece");
        piece.style.left = Math.random()*100 + "vw";
        piece.style.setProperty("--hue", Math.floor(Math.random()*360));
        confettiContainer.appendChild(piece);
        setTimeout(()=> piece.remove(), 3000);
      }
    }

    window.setInterval(function(){
      checkMatches();
      moveDown();
    }, 200);

  </script>
</body>
</html>
