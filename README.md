# Snake-Xenzia-only-for-Ashley
Creating an interesting game only for my love, Ashley. Hope her happy everyday!


<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>贪吃蛇游戏</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: white;
            overflow: hidden;
        }

        .game-container {
            text-align: center;
            max-width: 100vw;
            max-height: 100vh;
            width: 100%;
            position: relative;
        }

        .menu {
            background: rgba(0, 0, 0, 0.8);
            border-radius: 20px;
            padding: 30px;
            margin: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }

        h1 {
            font-size: clamp(24px, 8vw, 48px);
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }

        .level-selection {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }

        .level-btn {
            background: linear-gradient(45deg, #4CAF50, #45a049);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 10px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .level-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }

        .level-btn:active {
            transform: translateY(0);
        }

        .game-screen {
            display: none;
            position: relative;
            width: 100vw;
            height: 100vh;
            background: #2c3e50;
        }

        .game-header {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            background: rgba(0, 0, 0, 0.8);
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 100;
            font-size: 18px;
            height: 60px;
        }

        .game-info {
            display: flex;
            gap: 20px;
            align-items: center;
        }

        .control-buttons {
            display: flex;
            gap: 10px;
        }

        .control-btn {
            background: #e74c3c;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 14px;
            transition: background 0.3s ease;
        }

        .control-btn:hover {
            background: #c0392b;
        }

        .control-btn.pause {
            background: #f39c12;
        }

        .control-btn.pause:hover {
            background: #e67e22;
        }

        #gameCanvas {
            position: absolute;
            top: 60px;
            left: 0;
            background: #34495e;
            border: 2px solid #2c3e50;
            width: 100vw;
            height: calc(100vh - 60px);
        }

        .game-over {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            z-index: 200;
            display: none;
        }

        .game-over h2 {
            font-size: 32px;
            margin-bottom: 20px;
            color: #e74c3c;
        }

        .game-over button {
            background: #3498db;
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            margin: 5px;
            transition: background 0.3s ease;
        }

        .game-over button:hover {
            background: #2980b9;
        }

        .pause-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 150;
        }

        .pause-menu {
            background: rgba(0, 0, 0, 0.9);
            padding: 40px;
            border-radius: 20px;
            text-align: center;
        }

        /* 移动端优化 */
        @media (max-width: 768px) {
            .menu {
                margin: 10px;
                padding: 20px;
            }

            .game-header {
                font-size: 14px;
                padding: 5px 10px;
                height: 50px;
            }

            #gameCanvas {
                top: 50px;
                height: calc(100vh - 50px);
            }

            .game-info {
                gap: 10px;
            }

            .control-btn {
                padding: 6px 12px;
                font-size: 12px;
            }
        }

        .instructions {
            margin-top: 20px;
            font-size: 14px;
            opacity: 0.8;
            line-height: 1.5;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <!-- 主菜单 -->
        <div id="mainMenu" class="menu">
            <h1>🐍 贪吃蛇游戏</h1>
            <div class="level-selection">
                <button class="level-btn" onclick="startGame(1)">简单模式</button>
                <button class="level-btn" onclick="startGame(2)">普通模式</button>
                <button class="level-btn" onclick="startGame(3)">困难模式</button>
                <button class="level-btn" onclick="startGame(4)">地狱模式</button>
                <button class="level-btn" onclick="startGame(5)">极限模式</button>
            </div>
            <div class="instructions">
                📱 移动端：滑动屏幕控制方向<br>
                🍎 红色食物：+10分<br>
                🟢 绿色道具：增益效果（可选择吃取）<br>
                🔴 红色障碍：负面效果（需要避开）
            </div>
        </div>

        <!-- 游戏屏幕 -->
        <div id="gameScreen" class="game-screen">
            <div class="game-header">
                <div class="game-info">
                    <span>分数: <span id="score">0</span></span>
                    <span>生命: <span id="lives">3</span></span>
                    <span>等级: <span id="level">1</span></span>
                </div>
                <div class="control-buttons">
                    <button class="control-btn pause" onclick="togglePause()">暂停</button>
                    <button class="control-btn" onclick="backToMenu()">菜单</button>
                </div>
            </div>
            <canvas id="gameCanvas"></canvas>
        </div>

        <!-- 暂停菜单 -->
        <div id="pauseOverlay" class="pause-overlay">
            <div class="pause-menu">
                <h2>游戏暂停</h2>
                <button onclick="togglePause()">继续游戏</button>
                <button onclick="backToMenu()">返回菜单</button>
            </div>
        </div>

        <!-- 游戏结束菜单 -->
        <div id="gameOverMenu" class="game-over">
            <h2>游戏结束</h2>
            <p>最终分数: <span id="finalScore">0</span></p>
            <button onclick="restartGame()">重新开始</button>
            <button onclick="backToMenu()">返回菜单</button>
        </div>
    </div>

    <script>
        class SnakeGame {
            constructor() {
                this.canvas = document.getElementById('gameCanvas');
                this.ctx = this.canvas.getContext('2d');
                this.init();
            }

            init() {
                this.resizeCanvas();
                this.reset();
                this.setupTouchControls();
                window.addEventListener('resize', () => this.resizeCanvas());
            }

            resizeCanvas() {
                this.canvas.width = window.innerWidth;
                this.canvas.height = window.innerHeight - 60; // 减去header高度
                this.gridSize = Math.max(15, Math.min(25, Math.min(this.canvas.width, this.canvas.height) / 25));
                this.cols = Math.floor(this.canvas.width / this.gridSize);
                this.rows = Math.floor(this.canvas.height / this.gridSize);
            }

            reset() {
                this.snake = [{
                    x: Math.floor(this.cols / 2),
                    y: Math.floor(this.rows / 2)
                }];
                this.direction = { x: 1, y: 0 };
                this.food = this.generateFood();
                this.powerUps = [];
                this.obstacles = [];
                this.score = 0;
                this.lives = 3;
                this.gameRunning = false;
                this.gamePaused = false;
                this.currentLevel = 1;
                this.updateUI();
            }

            generateFood() {
                let pos;
                do {
                    pos = {
                        x: Math.floor(Math.random() * this.cols),
                        y: Math.floor(Math.random() * this.rows)
                    };
                } while (this.isSnakePosition(pos));
                return pos;
            }

            generatePowerUp() {
                if (Math.random() < 0.3) { // 30%概率生成道具
                    let pos;
                    do {
                        pos = {
                            x: Math.floor(Math.random() * this.cols),
                            y: Math.floor(Math.random() * this.rows)
                        };
                    } while (this.isSnakePosition(pos) || this.isPosition(pos, this.food));

                    const types = ['score', 'life', 'slow'];
                    const type = types[Math.floor(Math.random() * types.length)];
                    
                    this.powerUps.push({
                        x: pos.x,
                        y: pos.y,
                        type: type,
                        timer: 300 // 5秒后消失
                    });
                }
            }

            generateObstacle() {
                if (this.currentLevel > 2 && Math.random() < 0.2) { // 困难模式后生成障碍物
                    let pos;
                    do {
                        pos = {
                            x: Math.floor(Math.random() * this.cols),
                            y: Math.floor(Math.random() * this.rows)
                        };
                    } while (this.isSnakePosition(pos) || this.isPosition(pos, this.food) || 
                             this.powerUps.some(p => this.isPosition(pos, p)));

                    this.obstacles.push({
                        x: pos.x,
                        y: pos.y,
                        timer: 400 // 6.7秒后消失
                    });
                }
            }

            isSnakePosition(pos) {
                return this.snake.some(segment => segment.x === pos.x && segment.y === pos.y);
            }

            isPosition(pos1, pos2) {
                return pos1.x === pos2.x && pos1.y === pos2.y;
            }

            setupTouchControls() {
                let startX, startY;
                
                this.canvas.addEventListener('touchstart', (e) => {
                    e.preventDefault();
                    const touch = e.touches[0];
                    startX = touch.clientX;
                    startY = touch.clientY;
                });

                this.canvas.addEventListener('touchmove', (e) => {
                    e.preventDefault();
                });

                this.canvas.addEventListener('touchend', (e) => {
                    e.preventDefault();
                    if (!startX || !startY) return;

                    const touch = e.changedTouches[0];
                    const endX = touch.clientX;
                    const endY = touch.clientY;

                    const deltaX = endX - startX;
                    const deltaY = endY - startY;

                    if (Math.abs(deltaX) > Math.abs(deltaY)) {
                        // 水平滑动
                        if (deltaX > 30 && this.direction.x === 0) {
                            this.direction = { x: 1, y: 0 };
                        } else if (deltaX < -30 && this.direction.x === 0) {
                            this.direction = { x: -1, y: 0 };
                        }
                    } else {
                        // 垂直滑动
                        if (deltaY > 30 && this.direction.y === 0) {
                            this.direction = { x: 0, y: 1 };
                        } else if (deltaY < -30 && this.direction.y === 0) {
                            this.direction = { x: 0, y: -1 };
                        }
                    }

                    startX = startY = null;
                });

                // 键盘控制（PC端）
                document.addEventListener('keydown', (e) => {
                    if (!this.gameRunning || this.gamePaused) return;

                    switch(e.key) {
                        case 'ArrowUp':
                            if (this.direction.y === 0) this.direction = { x: 0, y: -1 };
                            break;
                        case 'ArrowDown':
                            if (this.direction.y === 0) this.direction = { x: 0, y: 1 };
                            break;
                        case 'ArrowLeft':
                            if (this.direction.x === 0) this.direction = { x: -1, y: 0 };
                            break;
                        case 'ArrowRight':
                            if (this.direction.x === 0) this.direction = { x: 1, y: 0 };
                            break;
                    }
                });
            }

            update() {
                if (!this.gameRunning || this.gamePaused) return;

                // 移动蛇
                const head = { 
                    x: this.snake[0].x + this.direction.x, 
                    y: this.snake[0].y + this.direction.y 
                };

                // 边界检查
                if (head.x < 0 || head.x >= this.cols || head.y < 0 || head.y >= this.rows) {
                    this.loseLife();
                    return;
                }

                // 自身碰撞检查
                if (this.isSnakePosition(head)) {
                    this.loseLife();
                    return;
                }

                // 障碍物碰撞检查
                const hitObstacle = this.obstacles.find(obs => this.isPosition(head, obs));
                if (hitObstacle) {
                    this.loseLife();
                    return;
                }

                this.snake.unshift(head);

                // 食物检查
                if (this.isPosition(head, this.food)) {
                    this.score += 10;
                    this.food = this.generateFood();
                    this.generatePowerUp();
                    this.generateObstacle();
                } else {
                    this.snake.pop();
                }

                // 道具检查
                const hitPowerUp = this.powerUps.find(powerUp => this.isPosition(head, powerUp));
                if (hitPowerUp) {
                    this.applyPowerUp(hitPowerUp);
                    this.powerUps = this.powerUps.filter(p => p !== hitPowerUp);
                }

                // 更新道具和障碍物计时器
                this.powerUps = this.powerUps.filter(p => --p.timer > 0);
                this.obstacles = this.obstacles.filter(o => --o.timer > 0);

                this.updateUI();
            }

            applyPowerUp(powerUp) {
                switch(powerUp.type) {
                    case 'score':
                        this.score += 50;
                        break;
                    case 'life':
                        this.lives = Math.min(this.lives + 1, 5);
                        break;
                    case 'slow':
                        // 临时减速效果
                        this.slowEffect = 60; // 1秒减速
                        break;
                }
            }

            loseLife() {
                this.lives--;
                if (this.lives <= 0) {
                    this.gameOver();
                } else {
                    // 重置蛇的位置
                    this.snake = [{
                        x: Math.floor(this.cols / 2),
                        y: Math.floor(this.rows / 2)
                    }];
                    this.direction = { x: 1, y: 0 };
                }
            }

            gameOver() {
                this.gameRunning = false;
                document.getElementById('finalScore').textContent = this.score;
                document.getElementById('gameOverMenu').style.display = 'block';
            }

            render() {
                this.ctx.fillStyle = '#34495e';
                this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

                // 绘制蛇
                this.ctx.fillStyle = '#2ecc71';
                this.snake.forEach((segment, index) => {
                    if (index === 0) {
                        // 蛇头
                        this.ctx.fillStyle = '#27ae60';
                    } else {
                        this.ctx.fillStyle = '#2ecc71';
                    }
                    this.ctx.fillRect(
                        segment.x * this.gridSize,
                        segment.y * this.gridSize,
                        this.gridSize - 1,
                        this.gridSize - 1
                    );
                });

                // 绘制食物
                this.ctx.fillStyle = '#e74c3c';
                this.ctx.fillRect(
                    this.food.x * this.gridSize,
                    this.food.y * this.gridSize,
                    this.gridSize - 1,
                    this.gridSize - 1
                );

                // 绘制道具
                this.powerUps.forEach(powerUp => {
                    switch(powerUp.type) {
                        case 'score':
                            this.ctx.fillStyle = '#f1c40f';
                            break;
                        case 'life':
                            this.ctx.fillStyle = '#e67e22';
                            break;
                        case 'slow':
                            this.ctx.fillStyle = '#9b59b6';
                            break;
                    }
                    this.ctx.fillRect(
                        powerUp.x * this.gridSize,
                        powerUp.y * this.gridSize,
                        this.gridSize - 1,
                        this.gridSize - 1
                    );
                });

                // 绘制障碍物
                this.ctx.fillStyle = '#c0392b';
                this.obstacles.forEach(obstacle => {
                    this.ctx.fillRect(
                        obstacle.x * this.gridSize,
                        obstacle.y * this.gridSize,
                        this.gridSize - 1,
                        this.gridSize - 1
                    );
                });
            }

            updateUI() {
                document.getElementById('score').textContent = this.score;
                document.getElementById('lives').textContent = this.lives;
                document.getElementById('level').textContent = this.currentLevel;
            }

            start(level) {
                this.currentLevel = level;
                this.gameRunning = true;
                this.gamePaused = false;
                
                // 根据等级调整游戏速度
                const speeds = [200, 150, 100, 80, 60];
                this.gameSpeed = speeds[level - 1] || 60;
                
                this.gameLoop();
            }

            gameLoop() {
                this.update();
                this.render();

                if (this.gameRunning) {
                    const speed = this.slowEffect ? this.gameSpeed * 2 : this.gameSpeed;
                    if (this.slowEffect) this.slowEffect--;
                    setTimeout(() => this.gameLoop(), speed);
                }
            }

            pause() {
                this.gamePaused = !this.gamePaused;
                document.getElementById('pauseOverlay').style.display = 
                    this.gamePaused ? 'flex' : 'none';
                
                if (!this.gamePaused && this.gameRunning) {
                    this.gameLoop();
                }
            }
        }

        let game = new SnakeGame();

        function startGame(level) {
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('gameScreen').style.display = 'block';
            document.getElementById('gameOverMenu').style.display = 'none';
            
            game.reset();
            game.start(level);
        }

        function backToMenu() {
            game.gameRunning = false;
            game.gamePaused = false;
            document.getElementById('mainMenu').style.display = 'block';
            document.getElementById('gameScreen').style.display = 'none';
            document.getElementById('pauseOverlay').style.display = 'none';
            document.getElementById('gameOverMenu').style.display = 'none';
        }

        function restartGame() {
            document.getElementById('gameOverMenu').style.display = 'none';
            game.reset();
            game.start(game.currentLevel);
        }

        function togglePause() {
            if (game.gameRunning) {
                game.pause();
            }
        }

        // 防止页面滚动
        document.addEventListener('touchmove', function(e) {
            e.preventDefault();
        }, { passive: false });

        // 防止双击缩放
        let lastTouchEnd = 0;
        document.addEventListener('touchend', function(event) {
            let now = (new Date()).getTime();
            if (now - lastTouchEnd <= 300) {
                event.preventDefault();
            }
            lastTouchEnd = now;
        }, false);
    </script>
</body>
</html>
