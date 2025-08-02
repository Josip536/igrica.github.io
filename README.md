<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Pong Game</title>
  <style>
    body {
      margin: 0;
      background: black;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    canvas {
      border: 3px solid #0f0;
      background: #111;
    }
  </style>
</head>
<body>
  <canvas id="pong" width="600" height="400"></canvas>

  <script>
    const canvas = document.getElementById("pong");
    const ctx = canvas.getContext("2d");

    const paddleWidth = 10, paddleHeight = 100;
    const ballSize = 10;

    let playerY = canvas.height / 2 - paddleHeight / 2;
    let aiY = canvas.height / 2 - paddleHeight / 2;

    let ballX = canvas.width / 2;
    let ballY = canvas.height / 2;
    let ballSpeedX = 5;
    let ballSpeedY = 4;

    document.addEventListener("mousemove", (e) => {
      const rect = canvas.getBoundingClientRect();
      playerY = e.clientY - rect.top - paddleHeight / 2;
    });

    function drawRect(x, y, w, h, color) {
      ctx.fillStyle = color;
      ctx.fillRect(x, y, w, h);
    }

    function drawBall(x, y, r, color) {
      ctx.fillStyle = color;
      ctx.beginPath();
      ctx.arc(x, y, r, 0, Math.PI * 2, false);
      ctx.closePath();
      ctx.fill();
    }

    function resetBall() {
      ballX = canvas.width / 2;
      ballY = canvas.height / 2;
      ballSpeedX *= -1;
      ballSpeedY = 4 * (Math.random() > 0.5 ? 1 : -1);
    }

    function update() {
      ballX += ballSpeedX;
      ballY += ballSpeedY;

      // Wall bounce
      if (ballY <= 0 || ballY + ballSize >= canvas.height) {
        ballSpeedY *= -1;
      }

      // Player paddle collision
      if (
        ballX <= paddleWidth &&
        ballY > playerY &&
        ballY < playerY + paddleHeight
      ) {
        ballSpeedX *= -1;
      }

      // AI paddle collision
      if (
        ballX + ballSize >= canvas.width - paddleWidth &&
        ballY > aiY &&
        ballY < aiY + paddleHeight
      ) {
        ballSpeedX *= -1;
      }

      // AI movement (simple follow)
      const aiCenter = aiY + paddleHeight / 2;
      if (aiCenter < ballY) {
        aiY += 3;
      } else {
        aiY -= 3;
      }

      // Reset if ball goes out
      if (ballX < 0 || ballX > canvas.width) {
        resetBall();
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      drawRect(0, playerY, paddleWidth, paddleHeight, "#0f0"); // Player
      drawRect(canvas.width - paddleWidth, aiY, paddleWidth, paddleHeight, "#f00"); // AI
      drawBall(ballX, ballY, ballSize, "#fff"); // Ball
    }

    function loop() {
      update();
      draw();
      requestAnimationFrame(loop);
    }

    loop();
  </script>
</body>
</html>
