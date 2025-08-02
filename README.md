<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Snake Game (Mobile Friendly)</title>
  <style>
    body {
      margin: 0;
      background: #000;
      display: flex;
      flex-direction: column;
      align-items: center;
      font-family: sans-serif;
      color: #0f0;
    }

    canvas {
      background: #111;
      margin-top: 10px;
      box-shadow: 0 0 20px #0f0;
    }

    #score {
      font-size: 20px;
      margin-top: 10px;
    }

    .controls {
      display: grid;
      grid-template-columns: 60px 60px 60px;
      grid-template-rows: 60px 60px;
      gap: 10px;
      margin: 20px;
      justify-content: center;
    }

    .btn {
      width: 60px;
      height: 60px;
      background: #0f0;
      color: #000;
      font-weight: bold;
      font-size: 18px;
      border: none;
      border-radius: 10px;
      box-shadow: 0 0 5px #0f0;
    }

    .blank {
      background: transparent;
      box-shadow: none;
    }
  </style>
</head>
<body>

  <div id="score">Score: 0</div>
  <canvas id="game" width="400" height="400"></canvas>

  <div class="controls">
    <div class="blank"></div>
    <button class="btn" onclick="setDirection('up')">↑</button>
    <div class="blank"></div>
    <button class="btn" onclick="setDirection('left')">←</button>
    <button class="btn" onclick="setDirection('down')">↓</button>
    <button class="btn" onclick="setDirection('right')">→</button>
  </div>

  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');

    const gridSize = 20;
    const tileCount = canvas.width / gridSize;

    let snake = [{ x: 10, y: 10 }];
    let dx = 0;
    let dy = 0;
    let food = spawnFood();
    let score = 0;

    document.addEventListener('keydown', keyDown);

    function keyDown(e) {
      switch (e.key) {
        case 'ArrowLeft': setDirection('left'); break;
        case 'ArrowRight': setDirection('right'); break;
        case 'ArrowUp': setDirection('up'); break;
        case 'ArrowDown': setDirection('down'); break;
      }
    }

    function setDirection(dir) {
      if (dir === 'left' && dx === 0) {
        dx = -1; dy = 0;
      } else if (dir === 'right' && dx === 0) {
        dx = 1; dy = 0;
      } else if (dir === 'up' && dy === 0) {
        dx = 0; dy = -1;
      } else if (dir === 'down' && dy === 0) {
        dx = 0; dy = 1;
      }
    }

    function spawnFood() {
      return {
        x: Math.floor(Math.random() * tileCount),
        y: Math.floor(Math.random() * tileCount)
      };
    }

    function update() {
      const head = { x: snake[0].x + dx, y: snake[0].y + dy };

      if (
        head.x < 0 || head.x >= tileCount ||
        head.y < 0 || head.y >= tileCount
      ) {
        resetGame();
        return;
      }

      for (let i = 0; i < snake.length; i++) {
        if (snake[i].x === head.x && snake[i].y === head.y) {
          resetGame();
          return;
        }
      }

      snake.unshift(head);

      if (head.x === food.x && head.y === food.y) {
        score++;
        document.getElementById('score').textContent = 'Score: ' + score;
        food = spawnFood();
      } else {
        snake.pop();
      }
    }

    function draw() {
      ctx.fillStyle = '#111';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = '#0f0';
      for (let part of snake) {
        ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize - 2, gridSize - 2);
      }

      ctx.fillStyle = 'red';
      ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize - 2, gridSize - 2);
    }

    function resetGame() {
      snake = [{ x: 10, y: 10 }];
      dx = dy = 0;
      food = spawnFood();
      score = 0;
      document.getElementById('score').textContent = 'Score: 0';
    }

    function gameLoop() {
      update();
      draw();
    }

    setInterval(gameLoop, 100);
  </script>

</body>
</html>
