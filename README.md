<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Block Blast Game</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      background: #4267b2;
      font-family: 'Segoe UI', sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }
    #homeScreen, #gameScreen { text-align: center; }
    #homeScreen { display: block; }
    #gameScreen { display: none; flex-direction: column; align-items: center; }
    .btn {
      padding: 10px 20px;
      background-color: #f0c419;
      color: black;
      font-weight: bold;
      border: none;
      border-radius: 10px;
      margin-top: 20px;
      font-size: 20px;
      cursor: pointer;
    }
    #grid {
      display: grid;
      grid-template-columns: repeat(9, 30px);
      grid-template-rows: repeat(9, 30px);
      gap: 2px;
      margin: 20px auto;
    }
    .cell {
      width: 30px;
      height: 30px;
      background-color: #2e3b55;
      border-radius: 4px;
    }
    .filled { background-color: #f5c400; }
    #pieces {
      display: flex;
      justify-content: center;
      gap: 20px;
      margin-top: 20px;
    }
    .piece {
      display: grid;
      gap: 2px;
      background-color: #333;
      padding: 5px;
      border-radius: 8px;
      cursor: grab;
    }
    #scoreLevel {
      color: white;
      font-size: 20px;
      margin: 10px;
    }
    #levelUpMessage {
      font-size: 30px;
      font-weight: bold;
      color: #fff700;
      display: none;
      animation: fade 2s ease-in-out;
    }
    @keyframes fade {
      0% { opacity: 0; }
      50% { opacity: 1; }
      100% { opacity: 0; }
    }
  </style>
</head>
<body>
  <div id="homeScreen">
    <h1 style="color: white; margin-bottom: 20px;">Block Blast</h1>
    <button class="btn" onclick="startGame()">Start Game</button>
  </div>
  <div id="gameScreen">
    <div id="scoreLevel">
      Score: <span id="score">0</span> | Level: <span id="level">1</span>
    </div>
    <div id="levelUpMessage">Level Up!</div>
    <div id="grid"></div>
    <div id="pieces"></div>
  </div>

  <audio id="placeSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_5c715f6fbf.mp3?filename=click-124467.mp3"></audio>
  <audio id="levelSound" src="https://cdn.pixabay.com/download/audio/2021/08/04/audio_2bfc5b3d4d.mp3?filename=success-1-6297.mp3"></audio>

  <script>
    const homeScreen = document.getElementById('homeScreen');
    const gameScreen = document.getElementById('gameScreen');
    const grid = document.getElementById('grid');
    const pieces = document.getElementById('pieces');
    const scoreEl = document.getElementById('score');
    const levelEl = document.getElementById('level');
    const levelUpMessage = document.getElementById('levelUpMessage');
    const placeSound = document.getElementById('placeSound');
    const levelSound = document.getElementById('levelSound');

    let score = 0;
    let level = 1;
    let piecesPlaced = 0;
    const blockShapes = [
      [[1,1],[1,1]],
      [[1,1,1,1]],
      [[1],[1],[1],[1]],
      [[1,1,1]],
      [[1],[1],[1]],
      [[1,1],[1,0]],
      [[1,0],[1,1]],
      [[1,1,0],[0,1,1]]
    ];

    function startGame() {
      homeScreen.style.display = 'none';
      gameScreen.style.display = 'flex';
      drawGrid();
      drawPieces();
    }

    function drawGrid() {
      grid.innerHTML = '';
      for (let i = 0; i < 81; i++) {
        const cell = document.createElement('div');
        cell.className = 'cell';
        cell.dataset.index = i;
        grid.appendChild(cell);
      }
    }

    function drawPieces() {
      pieces.innerHTML = '';
      for (let i = 0; i < 3; i++) {
        const shape = blockShapes[Math.floor(Math.random() * blockShapes.length)];
        const piece = document.createElement('div');
        piece.className = 'piece';
        piece.style.gridTemplateColumns = `repeat(${shape[0].length}, 20px)`;
        piece.dataset.shape = JSON.stringify(shape);
        shape.forEach(row => {
          row.forEach(cell => {
            const cellDiv = document.createElement('div');
            cellDiv.style.width = '20px';
            cellDiv.style.height = '20px';
            cellDiv.style.backgroundColor = cell ? '#f5c400' : 'transparent';
            piece.appendChild(cellDiv);
          });
        });
        piece.onclick = () => placePiece(shape, piece);
        pieces.appendChild(piece);
      }
    }

    function placePiece(shape, piece) {
      piecesPlaced++;
      score += 100;
      scoreEl.textContent = score;
      placeSound.play();
      piece.remove();

      if (piecesPlaced >= 3) {
        drawPieces();
        piecesPlaced = 0;
      }
      if (score >= level * 500) {
        level++;
        levelEl.textContent = level;
        levelUpMessage.style.display = 'block';
        levelSound.play();
        setTimeout(() => {
          levelUpMessage.style.display = 'none';
        }, 2000);
      }
    }
  </script>
</body>
</html>
