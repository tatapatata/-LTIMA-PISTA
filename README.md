<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Candy Crush de Flores para Zaye</title>
  <style>
    body {
      margin: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background: linear-gradient(135deg, #fdfbfb 0%, #ebedee 100%);
      font-family: Arial, sans-serif;
    }
    h1 {
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
      box-shadow: 0 8px 16px rgba(0,0,0,0.2);
    }
    .cell {
      width: 60px;
      height: 60px;
      font-size: 32px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      border: 3px solid #000; /* Bordes negros para contraste */
      border-radius: 12px;
      background: #f9f9f9;
      box-shadow: 0 4px 6px rgba(0,0,0,0.3);
      transition: transform 0.2s ease;
    }
    .cell:hover {
      transform: scale(1.1);
    }
    .matched {
      animation: pop 0.4s forwards;
    }
    @keyframes pop {
      0% { transform: scale(1); opacity: 1; }
      50% { transform: scale(1.5); opacity: 0.6; }
      100% { transform: scale(0); opacity: 0; }
    }
    .fall {
      animation: fall 0.5s ease-out;
    }
    @keyframes fall {
      0% { transform: translateY(-80px); }
      70% { transform: translateY(10px); }
      100% { transform: translateY(0); }
    }
    #scoreboard {
      margin-top: 15px;
      width: 80%;
      background: #ddd;
      border-radius: 10px;
      overflow: hidden;
    }
    #progress {
      height: 20px;
      background: linear-gradient(to right, #ff7e5f, #feb47b);
      width: 0%;
      transition: width 0.5s ease;
    }
    #victory {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.7);
      display: flex;
      align-items: center;
      justify-content: center;
    }
    #victoryContent {
      background: white;
      padding: 20px;
      border-radius: 12px;
      text-align: center;
    }
    #victoryContent a {
      display: inline-block;
      margin-top: 10px;
      padding: 10px 15px;
      background: #ff7e5f;
      color: #fff;
      border-radius: 8px;
      text-decoration: none;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>🌻 Candy Crush de Flores para Zaye 🌷</h1>
  <div id="game"></div>
  <div id="scoreboard">
    <div id="progress"></div>
  </div>
  <div id="victory">
    <div id="victoryContent">
      <h2>🎉 ¡Felicidades Zaye! 🎉</h2>
      <p>Has completado el reto floral 🌸🌻🌹</p>
      <a href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr" target="_blank">Abrir Playlist Especial 💖</a>
    </div>
  </div>

  <script>
    const game = document.getElementById('game');
    const progress = document.getElementById('progress');
    const victory = document.getElementById('victory');
    const width = 8;
    const squares = [];
    const flowers = ["🌻","🌹","🌷","🌼","🌺","🌸"];

    let score = 0;
    const targetScore = 10000;

    // Crear tablero
    function createBoard() {
      for (let i=0; i < width*width; i++) {
        const cell = document.createElement('div');
        cell.setAttribute('draggable', true);
        cell.setAttribute('id', i);
        cell.classList.add('cell');
        cell.textContent = flowers[Math.floor(Math.random()*flowers.length)];
        game.appendChild(cell);
        squares.push(cell);
      }
    }
    createBoard();

    let colorBeingDragged, colorBeingReplaced, squareIdBeingDragged, squareIdBeingReplaced;

    squares.forEach(square => square.addEventListener('dragstart', dragStart));
    squares.forEach(square => square.addEventListener('dragend', dragEnd));
    squares.forEach(square => square.addEventListener('dragover', dragOver));
    squares.forEach(square => square.addEventListener('dragenter', dragEnter));
    squares.forEach(square => square.addEventListener('dragleave', dragLeave));
    squares.forEach(square => square.addEventListener('drop', dragDrop));

    function dragStart() {
      colorBeingDragged = this.textContent;
      squareIdBeingDragged = parseInt(this.id);
    }
    function dragOver(e) { e.preventDefault(); }
    function dragEnter(e) { e.preventDefault(); }
    function dragLeave() {}
    function dragDrop() {
      colorBeingReplaced = this.textContent;
      squareIdBeingReplaced = parseInt(this.id);
      this.textContent = colorBeingDragged;
      squares[squareIdBeingDragged].textContent = colorBeingReplaced;
    }
    function dragEnd() {
      let validMoves = [squareIdBeingDragged-1, squareIdBeingDragged+1,
                        squareIdBeingDragged-width, squareIdBeingDragged+width];
      let validMove = validMoves.includes(squareIdBeingReplaced);
      if (squareIdBeingReplaced && validMove) {
        squareIdBeingReplaced = null;
      } else if (squareIdBeingReplaced && !validMove) {
        squares[squareIdBeingReplaced].textContent = colorBeingReplaced;
        squares[squareIdBeingDragged].textContent = colorBeingDragged;
      } else squares[squareIdBeingDragged].textContent = colorBeingDragged;
    }

    function checkRowForThree() {
      for (let i=0; i<61; i++) {
        let rowOfThree = [i, i+1, i+2];
        let decidedFlower = squares[i].textContent;
        const isBlank = decidedFlower === '';
        const notValid = [6,7,14,15,22,23,30,31,38,39,46,47,54,55];
        if (notValid.includes(i)) continue;

        if(rowOfThree.every(index => squares[index].textContent === decidedFlower && !isBlank)) {
          rowOfThree.forEach(index => {
            squares[index].classList.add('matched');
            setTimeout(()=> squares[index].textContent='', 400);
          });
          updateScore(110);
        }
      }
    }

    function checkColumnForThree() {
      for (let i=0; i<47; i++) {
        let columnOfThree = [i, i+width, i+width*2];
        let decidedFlower = squares[i].textContent;
        const isBlank = decidedFlower === '';
        if(columnOfThree.every(index => squares[index].textContent === decidedFlower && !isBlank)) {
          columnOfThree.forEach(index => {
            squares[index].classList.add('matched');
            setTimeout(()=> squares[index].textContent='', 400);
          });
          updateScore(110);
        }
      }
    }

    function moveDown() {
      for (let i=0; i<55; i++) {
        if(squares[i+width].textContent === '') {
          squares[i+width].textContent = squares[i].textContent;
          squares[i].textContent = '';
          squares[i+width].classList.add('fall');
        }
        if (i < width && squares[i].textContent === '') {
          squares[i].textContent = flowers[Math.floor(Math.random()*flowers.length)];
        }
      }
    }

    function updateScore(points) {
      score += points;
      let percent = Math.min((score/targetScore)*100,100);
      progress.style.width = percent + '%';
      if(score >= targetScore) {
        victory.style.display = 'flex';
      }
    }

    window.setInterval(function(){
      checkRowForThree();
      checkColumnForThree();
      moveDown();
    }, 200);
  </script>
</body>
</html>
