# pixel3games
pixel3games
[Uploading deepse<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PixelPlay Mini</title>
    <style>
        body {
            font-family: 'Courier New', monospace;
            background-color: white;
            margin: 0;
            padding: 20px;
            text-align: center;
        }
        
        header {
            margin-bottom: 30px;
        }
        
        h1 {
            font-size: 2.5em;
            color: #333;
        }
        
        .games-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 30px;
            max-width: 1000px;
            margin: 0 auto;
        }
        
        .game-card {
            width: 300px;
            border: 4px solid #333;
            padding: 15px;
            background-color: #f8f8f8;
            box-shadow: 5px 5px 0 #ccc;
            transition: transform 0.2s;
        }
        
        .game-card:hover {
            transform: translate(-2px, -2px);
            box-shadow: 7px 7px 0 #aaa;
        }
        
        .game-title {
            font-size: 1.5em;
            margin: 10px 0;
            color: #222;
        }
        
        .game-screen {
            width: 250px;
            height: 250px;
            margin: 0 auto;
            border: 2px solid #333;
            background-color: white;
            image-rendering: pixelated;
            position: relative;
            overflow: hidden;
        }
        
        canvas {
            display: block;
            background-color: #fff;
            image-rendering: crisp-edges;
        }
        
        .play-btn {
            background-color: #333;
            color: white;
            border: none;
            padding: 8px 20px;
            margin-top: 15px;
            font-family: inherit;
            cursor: pointer;
            font-size: 1em;
        }
        
        .play-btn:hover {
            background-color: #555;
        }
        
        footer {
            margin-top: 40px;
            color: #666;
        }

        .score {
            font-size: 1.2em;
            margin: 10px 0;
        }

        /* Game Focus Mode */
        .game-focus {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: white;
            z-index: 1000;
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .game-focus.active {
            display: flex;
        }

        .game-focus-container {
            position: relative;
            width: 90vmin;
            height: 90vmin;
            max-width: 500px;
            max-height: 500px;
        }

        .close-btn {
            position: absolute;
            top: 10px;
            right: 10px;
            background: #f33;
            color: white;
            border: none;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            font-weight: bold;
            cursor: pointer;
            z-index: 1001;
        }

        .game-controls {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <header>
        <h1>PIXELPLAY MINI</h1>
    </header>
    
    <div class="games-container">
        <!-- Game 1: Pixel Pong -->
        <div class="game-card">
            <div class="game-title">PIXEL PONG</div>
            <div class="score">Player: 0 | CPU: 0</div>
            <div class="game-screen">
                <canvas id="pongCanvas" width="250" height="250"></canvas>
            </div>
            <div class="game-controls">
                <button class="play-btn" id="pongBtn">PLAY</button>
                <button class="play-btn" id="pongFocusBtn">FOCUS MODE</button>
            </div>
        </div>
        
        <!-- Game 2: Snake Quest -->
        <div class="game-card">
            <div class="game-title">SNAKE QUEST</div>
            <div class="score">Score: 0</div>
            <div class="game-screen">
                <canvas id="snakeCanvas" width="250" height="250"></canvas>
            </div>
            <div class="game-controls">
                <button class="play-btn" id="snakeBtn">PLAY</button>
                <button class="play-btn" id="snakeFocusBtn">FOCUS MODE</button>
            </div>
        </div>
        
        <!-- Game 3: Memory Match -->
        <div class="game-card">
            <div class="game-title">MEMORY MATCH</div>
            <div class="score">Matches: 0/8</div>
            <div class="game-screen">
                <canvas id="memoryCanvas" width="250" height="250"></canvas>
            </div>
            <div class="game-controls">
                <button class="play-btn" id="memoryBtn">PLAY</button>
                <button class="play-btn" id="memoryFocusBtn">FOCUS MODE</button>
            </div>
        </div>
    </div>
    
    <footer>
        <p>Simple pixel games for quick fun!</p>
    </footer>

    <!-- Focus Mode Containers -->
    <div class="game-focus" id="pongFocus">
        <div class="game-focus-container">
            <canvas id="pongFocusCanvas" width="400" height="400"></canvas>
            <button class="close-btn" onclick="exitFocusMode('pong')">X</button>
        </div>
    </div>

    <div class="game-focus" id="snakeFocus">
        <div class="game-focus-container">
            <canvas id="snakeFocusCanvas" width="400" height="400"></canvas>
            <button class="close-btn" onclick="exitFocusMode('snake')">X</button>
        </div>
    </div>

    <div class="game-focus" id="memoryFocus">
        <div class="game-focus-container">
            <canvas id="memoryFocusCanvas" width="400" height="400"></canvas>
            <button class="close-btn" onclick="exitFocusMode('memory')">X</button>
        </div>
    </div>

    <script>
        // ======================
        // FOCUS MODE FUNCTIONALITY
        // ======================
        function enterFocusMode(game) {
            document.getElementById(`${game}Focus`).classList.add('active');
            
            // Transfer game state to focus canvas
            if (game === 'pong') {
                initPongFocus();
            } else if (game === 'snake') {
                initSnakeFocus();
            } else if (game === 'memory') {
                initMemoryFocus();
            }
        }

        function exitFocusMode(game) {
            document.getElementById(`${game}Focus`).classList.remove('active');
            
            // Transfer game state back to main canvas
            if (game === 'pong' && pongRunning) {
                drawPong();
            } else if (game === 'snake' && snakeRunning) {
                drawSnake();
            } else if (game === 'memory' && memoryRunning) {
                drawMemoryCards();
            }
        }

        // ======================
        // 1. PIXEL PONG GAME
        // ======================
        const pongCanvas = document.getElementById('pongCanvas');
        const pongCtx = pongCanvas.getContext('2d');
        const pongBtn = document.getElementById('pongBtn');
        const pongFocusBtn = document.getElementById('pongFocusBtn');
        const pongScore = document.querySelector('.game-card:nth-child(1) .score');
        const pongFocusCanvas = document.getElementById('pongFocusCanvas');
        const pongFocusCtx = pongFocusCanvas.getContext('2d');

        let pongRunning = false;
        let playerScore = 0;
        let cpuScore = 0;

        // Game objects
        const ball = {
            x: pongCanvas.width / 2,
            y: pongCanvas.height / 2,
            radius: 5,
            dx: 4,
            dy: 4
        };

        const playerPaddle = {
            x: 10,
            y: pongCanvas.height / 2 - 30,
            width: 10,
            height: 60,
            dy: 0
        };

        const cpuPaddle = {
            x: pongCanvas.width - 20,
            y: pongCanvas.height / 2 - 30,
            width: 10,
            height: 60,
            dy: 2
        };

        function drawPong(ctx = pongCtx, canvas = pongCanvas) {
            // Clear canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Draw ball
            ctx.fillStyle = '#333';
            ctx.beginPath();
            ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
            ctx.fill();
            
            // Draw paddles
            ctx.fillRect(playerPaddle.x, playerPaddle.y, playerPaddle.width, playerPaddle.height);
            ctx.fillRect(cpuPaddle.x, cpuPaddle.y, cpuPaddle.width, cpuPaddle.height);
            
            // Draw center line
            ctx.setLineDash([5, 5]);
            ctx.beginPath();
            ctx.moveTo(canvas.width / 2, 0);
            ctx.lineTo(canvas.width / 2, canvas.height);
            ctx.strokeStyle = '#ccc';
            ctx.stroke();
            ctx.setLineDash([]);
        }

        function updatePong() {
            if (!pongRunning) return;
            
            // Move ball
            ball.x += ball.dx;
            ball.y += ball.dy;
            
            // Wall collision (top/bottom)
            if (ball.y + ball.radius > pongCanvas.height || ball.y - ball.radius < 0) {
                ball.dy = -ball.dy;
            }
            
            // Paddle collision
            if (
                ball.x - ball.radius < playerPaddle.x + playerPaddle.width &&
                ball.y > playerPaddle.y &&
                ball.y < playerPaddle.y + playerPaddle.height
            ) {
                ball.dx = -ball.dx * 1.05; // Increase speed slightly
            }
            
            if (
                ball.x + ball.radius > cpuPaddle.x &&
                ball.y > cpuPaddle.y &&
                ball.y < cpuPaddle.y + cpuPaddle.height
            ) {
                ball.dx = -ball.dx * 1.05;
            }
            
            // Score points
            if (ball.x + ball.radius < 0) {
                cpuScore++;
                resetBall();
            }
            
            if (ball.x - ball.radius > pongCanvas.width) {
                playerScore++;
                resetBall();
            }
            
            // Update score display
            pongScore.textContent = `Player: ${playerScore} | CPU: ${cpuScore}`;
            
            // CPU paddle AI (simple follow)
            if (cpuPaddle.y + cpuPaddle.height/2 < ball.y - 10) {
                cpuPaddle.y += cpuPaddle.dy;
            } else if (cpuPaddle.y + cpuPaddle.height/2 > ball.y + 10) {
                cpuPaddle.y -= cpuPaddle.dy;
            }
            
            // Keep paddles in bounds
            playerPaddle.y = Math.max(0, Math.min(pongCanvas.height - playerPaddle.height, playerPaddle.y + playerPaddle.dy));
            cpuPaddle.y = Math.max(0, Math.min(pongCanvas.height - cpuPaddle.height, cpuPaddle.y));
        }

        function resetBall() {
            ball.x = pongCanvas.width / 2;
            ball.y = pongCanvas.height / 2;
            ball.dx = -ball.dx;
            ball.dy = Math.random() * 8 - 4;
        }

        function gameLoopPong() {
            updatePong();
            drawPong();
            requestAnimationFrame(gameLoopPong);
        }

        function initPongFocus() {
            // Scale game objects for focus canvas
            ball.x = pongFocusCanvas.width / 2;
            ball.y = pongFocusCanvas.height / 2;
            playerPaddle.y = pongFocusCanvas.height / 2 - 30;
            cpuPaddle.y = pongFocusCanvas.height / 2 - 30;
            
            drawPong(pongFocusCtx, pongFocusCanvas);
        }

        // Controls
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowUp') playerPaddle.dy = -6;
            if (e.key === 'ArrowDown') playerPaddle.dy = 6;
        });
        
        document.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowUp' || e.key === 'ArrowDown') playerPaddle.dy = 0;
        });

        pongBtn.addEventListener('click', () => {
            pongRunning = !pongRunning;
            pongBtn.textContent = pongRunning ? 'PAUSE' : 'PLAY';
            if (pongRunning) gameLoopPong();
        });

        pongFocusBtn.addEventListener('click', () => enterFocusMode('pong'));

        // Initialize
        drawPong();

        // ======================
        // 2. SNAKE QUEST GAME (IMPROVED)
        // ======================
        const snakeCanvas = document.getElementById('snakeCanvas');
        const snakeCtx = snakeCanvas.getContext('2d');
        const snakeBtn = document.getElementById('snakeBtn');
        const snakeFocusBtn = document.getElementById('snakeFocusBtn');
        const snakeScore = document.querySelector('.game-card:nth-child(2) .score');
        const snakeFocusCanvas = document.getElementById('snakeFocusCanvas');
        const snakeFocusCtx = snakeFocusCanvas.getContext('2d');

        let snakeRunning = false;
        let snakeGameScore = 0;
        const gridSize = 10;
        const tileCount = snakeCanvas.width / gridSize;

        let snake = [{x: 10, y: 10}];
        let food = {x: 5, y: 5};
        let xVelocity = 0;
        let yVelocity = 0;
        let gameLoopId;
        let lastMoveTime = 0;
        const moveInterval = 150; // milliseconds between moves (controls speed)

        function drawSnake(ctx = snakeCtx, canvas = snakeCanvas) {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Draw border
            ctx.strokeStyle = '#333';
            ctx.lineWidth = 2;
            ctx.strokeRect(0, 0, canvas.width, canvas.height);
            
            // Draw snake
            snake.forEach((part, index) => {
                ctx.fillStyle = index === 0 ? '#333' : '#555';
                ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize-1, gridSize-1);
            });
            
            // Draw food
            ctx.fillStyle = '#f33';
            ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize-1, gridSize-1);
        }

        function updateSnake() {
            if (!snakeRunning) return;
            
            const now = Date.now();
            if (now - lastMoveTime < moveInterval) return;
            lastMoveTime = now;
            
            // Move snake
            const head = {x: snake[0].x + xVelocity, y: snake[0].y + yVelocity};
            snake.unshift(head);
            
            // Check food collision
            if (head.x === food.x && head.y === food.y) {
                snakeGameScore++;
                snakeScore.textContent = `Score: ${snakeGameScore}`;
                generateFood();
            } else {
                snake.pop();
            }
            
            // Check wall collision (game over)
            if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
                gameOver();
                return;
            }
            
            // Check self collision
            if (collision(head, snake.slice(1))) {
                gameOver();
            }
        }

        function generateFood() {
            food = {
                x: Math.floor(Math.random() * tileCount),
                y: Math.floor(Math.random() * tileCount)
            };
            
            // Make sure food doesn't appear on snake
            snake.forEach(part => {
                if (part.x === food.x && part.y === food.y) generateFood();
            });
        }

        function collision(head, array) {
            return array.some(part => part.x === head.x && part.y === head.y);
        }

        function resetSnake() {
            snake = [{x: 10, y: 10}];
            xVelocity = 0;
            yVelocity = 0;
            snakeGameScore = 0;
            snakeScore.textContent = `Score: ${snakeGameScore}`;
            generateFood();
            lastMoveTime = Date.now();
        }

        function gameOver() {
            snakeRunning = false;
            snakeBtn.textContent = 'PLAY';
            alert(`Game Over! Score: ${snakeGameScore}`);
            resetSnake();
            cancelAnimationFrame(gameLoopId);
        }

        function gameLoopSnake() {
            updateSnake();
            drawSnake();
            gameLoopId = requestAnimationFrame(gameLoopSnake);
        }

        function initSnakeFocus() {
            // Scale snake for focus canvas
            const scale = snakeFocusCanvas.width / snakeCanvas.width;
            snake.forEach(part => {
                part.x *= scale;
                part.y *= scale;
            });
            food.x *= scale;
            food.y *= scale;
            
            drawSnake(snakeFocusCtx, snakeFocusCanvas);
        }

        // Controls
        document.addEventListener('keydown', (e) => {
            if (!snakeRunning) return;
            
            // Prevent reverse direction
            switch(e.key) {
                case 'ArrowUp':
                    if (yVelocity !== 1) {
                        xVelocity = 0;
                        yVelocity = -1;
                    }
                    break;
                case 'ArrowDown':
                    if (yVelocity !== -1) {
                        xVelocity = 0;
                        yVelocity = 1;
                    }
                    break;
                case 'ArrowLeft':
                    if (xVelocity !== 1) {
                        xVelocity = -1;
                        yVelocity = 0;
                    }
                    break;
                case 'ArrowRight':
                    if (xVelocity !== -1) {
                        xVelocity = 1;
                        yVelocity = 0;
                    }
                    break;
            }
        });

        snakeBtn.addEventListener('click', () => {
            snakeRunning = !snakeRunning;
            snakeBtn.textContent = snakeRunning ? 'PAUSE' : 'PLAY';
            if (snakeRunning) {
                gameLoopSnake();
            } else {
                cancelAnimationFrame(gameLoopId);
            }
        });

        snakeFocusBtn.addEventListener('click', () => enterFocusMode('snake'));

        // Initialize
        generateFood();
        drawSnake();

        // ======================
        // 3. MEMORY MATCH GAME
        // ======================
        const memoryCanvas = document.getElementById('memoryCanvas');
        const memoryCtx = memoryCanvas.getContext('2d');
        const memoryBtn = document.getElementById('memoryBtn');
        const memoryFocusBtn = document.getElementById('memoryFocusBtn');
        const memoryScore = document.querySelector('.game-card:nth-child(3) .score');
        const memoryFocusCanvas = document.getElementById('memoryFocusCanvas');
        const memoryFocusCtx = memoryFocusCanvas.getContext('2d');

        let memoryRunning = false;
        let matchesFound = 0;
        const cardValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'];
        let cards = [];
        let flippedCards = [];
        let canFlip = true;

        function initMemoryGame() {
            // Shuffle cards
            cards = cardValues
                .sort(() => Math.random() - 0.5)
                .map((value, index) => ({
                    value,
                    x: (index % 4) * 60 + 5,
                    y: Math.floor(index / 4) * 60 + 5,
                    flipped: false,
                    matched: false
                }));
            
            matchesFound = 0;
            memoryScore.textContent = `Matches: ${matchesFound}/8`;
            drawMemoryCards();
        }

        function drawMemoryCards(ctx = memoryCtx, canvas = memoryCanvas) {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            cards.forEach(card => {
                // Draw card back or front
                if (card.flipped || card.matched) {
                    ctx.fillStyle = '#fff';
                    ctx.fillRect(card.x, card.y, 50, 50);
                    ctx.fillStyle = '#333';
                    ctx.font = '24px Courier New';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(card.value, card.x + 25, card.y + 25);
                } else {
                    ctx.fillStyle = '#333';
                    ctx.fillRect(card.x, card.y, 50, 50);
                }
            });
        }

        function handleCardClick(e, ctx = memoryCtx, canvas = memoryCanvas) {
            if (!memoryRunning || !canFlip) return;
            
            const rect = canvas.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;
            
            // Find clicked card
            const clickedCard = cards.find(card => 
                !card.flipped && 
                !card.matched && 
                x >= card.x && x <= card.x + 50 && 
                y >= card.y && y <= card.y + 50
            );
            
            if (!clickedCard || flippedCards.length >= 2) return;
            
            // Flip card
            clickedCard.flipped = true;
            flippedCards.push(clickedCard);
            drawMemoryCards(ctx, canvas);
            
            // Check for match
            if (flippedCards.length === 2) {
                canFlip = false;
                if (flippedCards[0].value === flippedCards[1].value) {
                    // Match found
                    flippedCards[0].matched = true;
                    flippedCards[1].matched = true;
                    matchesFound++;
                    memoryScore.textContent = `Matches: ${matchesFound}/8`;
                    flippedCards = [];
                    canFlip = true;
                    
                    // Check win condition
                    if (matchesFound === 8) {
                        setTimeout(() => {
                            alert('Congratulations! You won!');
                            memoryRunning = false;
                            memoryBtn.textContent = 'PLAY';
                        }, 500);
                    }
                } else {
                    // No match - flip back after delay
                    setTimeout(() => {
                        flippedCards[0].flipped = false;
                        flippedCards[1].flipped = false;
                        flippedCards = [];
                        drawMemoryCards(ctx, canvas);
                        canFlip = true;
                    }, 1000);
                }
            }
        }

        function initMemoryFocus() {
            // Scale cards for focus canvas
            const scale = memoryFocusCanvas.width / memoryCanvas.width;
            cards.forEach(card => {
                card.x *= scale;
                card.y *= scale;
            });
            
            drawMemoryCards(memoryFocusCtx, memoryFocusCanvas);
        }

        memoryBtn.addEventListener('click', () => {
            memoryRunning = !memoryRunning;
            memoryBtn.textContent = memoryRunning ? 'PAUSE' : 'PLAY';
            if (memoryRunning && matchesFound === 0) {
                initMemoryGame();
            }
        });

        memoryFocusBtn.addEventListener('click', () => enterFocusMode('memory'));

        memoryCanvas.addEventListener('click', handleCardClick);
        memoryFocusCanvas.addEventListener('click', (e) => handleCardClick(e, memoryFocusCtx, memoryFocusCanvas));

        // Initialize
        initMemoryGame();
    </script>
</body>
</html>ek_html_20250524_8cef33.html…]()
