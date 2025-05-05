
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Snake Pro - Version Améliorée</title>
  <style>
    body {
      margin: 0;
      background-color: #0d1117;
      font-family: "Segoe UI", sans-serif;
      text-align: center;
      color: #fff;
    }

    h1 {
      margin-top: 20px;
      color: #00ff88;
    }

    p.instructions {
      font-size: 14px;
      color: #ccc;
      margin-top: -10px;
    }

    #score {
      font-size: 20px;
      margin: 10px 0;
    }

    canvas {
      border: 2px solid #00ff88;
      background-color: #161b22;
      box-shadow: 0 0 10px #00ff88;
    }

    #restartBtn {
      display: none;
      margin-top: 20px;
      padding: 12px 24px;
      background-color: #00ff88;
      color: #000;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: 0.3s;
    }

    #restartBtn:hover {
      background-color: #00cc66;
    }

    #gameOverMessage {
      font-size: 24px;
      color: red;
      font-weight: bold;
      animation: blink 1s infinite;
      margin-top: 20px;
    }

    @keyframes blink {
      0%, 100% { opacity: 1; }
      50% { opacity: 0; }
    }
  </style>
</head>
<body>
  <h1>🐍 Snake Pro</h1>
  <p class="instructions">Utilise les flèches du clavier pour déplacer le serpent. Mange la pomme, évite les murs et ta queue !</p>
  <div id="score">Score : 0</div>
  <canvas id="game" width="400" height="400"></canvas>
  <div id="gameOverMessage" style="display: none;">💀 Game Over</div>
  <button id="restartBtn" onclick="startGame()">🔁 Rejouer</button>

  <audio id="eatSound" src="https://www.soundjay.com/button/beep-07.mp3" preload="auto"></audio>
  <audio id="loseSound" src="https://www.soundjay.com/button/beep-10.mp3" preload="auto"></audio>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const box = 20;
    const rows = canvas.height / box;
    const cols = canvas.width / box;

    let snake, food, dir, gameInterval, speed, score, gameOver;

    const scoreEl = document.getElementById("score");
    const restartBtn = document.getElementById("restartBtn");
    const gameOverMessage = document.getElementById("gameOverMessage");

    const eatSound = document.getElementById("eatSound");
    const loseSound = document.getElementById("loseSound");

    function startGame() {
      snake = [{ x: 10, y: 10 }];
      dir = { x: 1, y: 0 };
      score = 0;
      speed = 150;
      gameOver = false;
      gameOverMessage.style.display = "none";
      restartBtn.style.display = "none";
      placeFood();
      updateScore();
      clearInterval(gameInterval);
      gameInterval = setInterval(update, speed);
    }

    function placeFood() {
      food = {
        x: Math.floor(Math.random() * cols),
        y: Math.floor(Math.random() * rows)
      };
    }

    function updateScore() {
      scoreEl.textContent = "Score : " + score;
    }

    function update() {
      const head = { x: snake[0].x + dir.x, y: snake[0].y + dir.y };

      if (
        head.x < 0 || head.y < 0 ||
        head.x >= cols || head.y >= rows ||
        collision(head)
      ) {
        endGame();
        return;
      }

      snake.unshift(head);

      if (head.x === food.x && head.y === food.y) {
        score++;
        eatSound.play();
        updateScore();
        placeFood();

        if (score % 5 === 0 && speed > 60) {
          speed -= 10;
          clearInterval(gameInterval);
          gameInterval = setInterval(update, speed);
        }
      } else {
        snake.pop();
      }

      draw();
    }

    function collision(pos) {
      return snake.slice(1).some(segment => segment.x === pos.x && segment.y === pos.y);
    }

    function endGame() {
      clearInterval(gameInterval);
      gameOver = true;
      gameOverMessage.style.display = "block";
      restartBtn.style.display = "inline-block";
      loseSound.play();
    }

    function draw() {
      ctx.fillStyle = "#161b22";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.strokeStyle = "#222";
      for (let i = 0; i < cols; i++) {
        for (let j = 0; j < rows; j++) {
          ctx.strokeRect(i * box, j * box, box, box);
        }
      }

      for (let i = 0; i < snake.length; i++) {
        ctx.fillStyle = i === 0 ? "#00ff88" : "#ffffff";
        ctx.fillRect(snake[i].x * box, snake[i].y * box, box, box);
      }

      ctx.fillStyle = "#ff0033";
      ctx.fillRect(food.x * box, food.y * box, box, box);
    }

    document.addEventListener("keydown", e => {
      if (gameOver) return;

      if (e.key === "ArrowUp" && dir.y !== 1) dir = { x: 0, y: -1 };
      else if (e.key === "ArrowDown" && dir.y !== -1) dir = { x: 0, y: 1 };
      else if (e.key === "ArrowLeft" && dir.x !== 1) dir = { x: -1, y: 0 };
      else if (e.key === "ArrowRight" && dir.x !== -1) dir = { x: 1, y: 0 };
    });

    startGame();
  </script>
</body>
</html>
