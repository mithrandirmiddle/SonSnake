# SonSnake
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Yüksek Çözünürlüklü Yılan Oyunu</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            font-family: Arial, sans-serif;
        }
        #gameContainer {
            text-align: center;
        }
        canvas {
            border: 2px solid #333;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        #score {
            font-size: 24px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <canvas id="gameCanvas" width="800" height="800"></canvas>
        <div id="score">Skor: 0</div>
    </div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');

        const gridSize = 20;
        const tileCount = canvas.width / gridSize;

        let snake = [
            {x: 10, y: 10},
        ];
        let food = {x: 15, y: 15};
        let dx = 0;
        let dy = 0;
        let score = 0;

        function drawGame() {
            clearCanvas();
            moveSnake();
            drawSnake();
            drawFood();
            checkCollision();
            updateScore();
        }

        function clearCanvas() {
            ctx.fillStyle = '#e8f5e9';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }

        function moveSnake() {
            const head = {x: snake[0].x + dx, y: snake[0].y + dy};
            
            if (head.x < 0) head.x = tileCount - 1;
            if (head.x >= tileCount) head.x = 0;
            if (head.y < 0) head.y = tileCount - 1;
            if (head.y >= tileCount) head.y = 0;

            snake.unshift(head);

            if (head.x === food.x && head.y === food.y) {
                score++;
                generateFood();
            } else {
                snake.pop();
            }
        }

        function drawSnake() {
            snake.forEach((segment, index) => {
                const gradient = ctx.createRadialGradient(
                    (segment.x + 0.5) * gridSize, (segment.y + 0.5) * gridSize, 2,
                    (segment.x + 0.5) * gridSize, (segment.y + 0.5) * gridSize, gridSize / 2
                );
                gradient.addColorStop(0, '#4caf50');
                gradient.addColorStop(1, '#2e7d32');
                
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.arc((segment.x + 0.5) * gridSize, (segment.y + 0.5) * gridSize, gridSize / 2 - 1, 0, 2 * Math.PI);
                ctx.fill();

                if (index === 0) {
                    // Gözler
                    ctx.fillStyle = 'white';
                    ctx.beginPath();
                    ctx.arc((segment.x + 0.3) * gridSize, (segment.y + 0.3) * gridSize, gridSize / 10, 0, 2 * Math.PI);
                    ctx.arc((segment.x + 0.7) * gridSize, (segment.y + 0.3) * gridSize, gridSize / 10, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.fillStyle = 'black';
                    ctx.beginPath();
                    ctx.arc((segment.x + 0.3) * gridSize, (segment.y + 0.3) * gridSize, gridSize / 20, 0, 2 * Math.PI);
                    ctx.arc((segment.x + 0.7) * gridSize, (segment.y + 0.3) * gridSize, gridSize / 20, 0, 2 * Math.PI);
                    ctx.fill();
                }
            });
        }

        function drawFood() {
            const gradient = ctx.createRadialGradient(
                (food.x + 0.5) * gridSize, (food.y + 0.5) * gridSize, 2,
                (food.x + 0.5) * gridSize, (food.y + 0.5) * gridSize, gridSize / 2
            );
            gradient.addColorStop(0, '#ff5722');
            gradient.addColorStop(1, '#e64a19');

            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.arc((food.x + 0.5) * gridSize, (food.y + 0.5) * gridSize, gridSize / 2 - 1, 0, 2 * Math.PI);
            ctx.fill();
        }

        function generateFood() {
            food.x = Math.floor(Math.random() * tileCount);
            food.y = Math.floor(Math.random() * tileCount);
        }

        function checkCollision() {
            const head = snake[0];

            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    resetGame();
                    return;
                }
            }
        }

        function resetGame() {
            snake = [{x: 10, y: 10}];
            dx = 0;
            dy = 0;
            score = 0;
            generateFood();
        }

        function updateScore() {
            scoreElement.textContent = `Skor: ${score}`;
        }

        document.addEventListener('keydown', (e) => {
            switch(e.key) {
                case 'ArrowUp':
                    if (dy === 0) { dx = 0; dy = -1; }
                    break;
                case 'ArrowDown':
                    if (dy === 0) { dx = 0; dy = 1; }
                    break;
                case 'ArrowLeft':
                    if (dx === 0) { dx = -1; dy = 0; }
                    break;
                case 'ArrowRight':
                    if (dx === 0) { dx = 1; dy = 0; }
                    break;
            }
        });

        setInterval(drawGame, 100);
    </script>
</body>
</html>
