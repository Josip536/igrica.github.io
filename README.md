<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Snake Game</title>
  <style>
    body {
      margin: 0;
      background: #111;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      font-family: sans-serif;
    }
    canvas {
      background: #222;
      box-shadow: 0 0 20px #0f0;
    }
    #score {
      position: absolute;
      top: 10px;
      color: #0f0;
      font-size: 20px;
    }
  </style>
</head>
<body>
  <div id="score">Score: 0</div>
  <canvas id="game" width="400" height="400"></canvas>

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
        case 'ArrowLeft':
          if (dx === 0) {
            dx = -1; dy = 0;
          }
          break;
        case 'ArrowRight':
          if (dx === 0) {
            dx = 1; dy = 0;
          }
          break;
        case 'ArrowUp':
          if (dy === 0) {
            dx = 0; dy = -1;
          }
          break;
        case 'ArrowDown':
          if (dy === 0) {
            dx = 0; dy = 1;
          }
          break;
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

      // Game over: Wall collision
      if (
        head.x < 0 || head.x >= tileCount ||
        head.y < 0 || head.y >= tileCount
      ) {
        resetGame();
        return;
      }

      // Game over: Self collision
      for (let i = 0; i < snake.length; i++) {
        if (snake[i].x === head.x && snake[i].y === head.y) {
          resetGame();
          return;
        }
      }

      snake.unshift(head);

      // Eat food
      if (head.x === food.x && head.y === food.y) {
        score++;
        document.getElementById('score').textContent = 'Score: ' + score;
        food = spawnFood();
      } else {
        snake.pop();
      }
    }

    function draw() {
      ctx.fillStyle = '#222';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Draw snake
      ctx.fillStyle = '#0f0';
      for (let part of snake) {
        ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize - 2, gridSize - 2);
      }

      // Draw food
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

    setInterval(gameLoop, 100); // 10 FPS
  </script>
</body>
</html>
