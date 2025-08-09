# Snake-Xenzia-only-for-Ashley
Creating an interesting game only for my love, Ashley. Hope her happy everyday!


<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>🐍 贪食蛇大冒险</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            overflow: hidden;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        #gameContainer {
            position: relative;
            width: 100vw;
            height: 100vh;
            max-width: 400px;
            max-height: 700px;
            background: linear-gradient(45deg, #1a1a2e, #16213e, #0f3460);
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 20px 40px rgba(0,0,0,0.6);
        }
        
        #gameCanvas {
            width: 100%;
            height: 100%;
            display: block;
            touch-action: none;
            position: absolute;
            top: 0;
            left: 0;
        }
        
        /* 游戏UI */
        #gameUI {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 60px;
            background: linear-gradient(180deg, rgba(0,0,0,0.8) 0%, transparent 100%);
            display: none;
            justify-content: space-around;
            align-items: center;
            padding: 10px 20px;
            z-index: 100;
            color: white;
            font-size: 14px;
            font-weight: bold;
        }
        
        #gameUI.active { 
            display: flex; 
        }
        
        .ui-item {
            text-align: center;
        }
        
        .ui-label {
            font-size: 10px;
            opacity: 0.7;
        }
        
        .ui-value {
            font-size: 16px;
            margin-top: 2px;
        }
        
        #score .ui-value { color: #FFD700; }
        #level .ui-value { color: #00BFFF; }
        #lives .ui-value { color: #FF6B6B; }
        
        /* 控制按钮 */
        #controls {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            display: none;
            grid-template-columns: repeat(3, 60px);
            grid-template-rows: repeat(3, 60px);
            gap: 5px;
            z-index: 100;
        }
        
        #controls.active { 
            display: grid; 
        }
        
        .control-btn {
            background: rgba(255,255,255,0.2);
            border: 2px solid rgba(255,255,255,0.3);
            border-radius: 15px;
            color: white;
            font-size: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.2s;
            backdrop-filter: blur(10px);
        }
        
        .control-btn:active {
            transform: scale(0.9);
            background: rgba(255,255,255,0.4);
        }
        
        #upBtn { grid-column: 2; grid-row: 1; }
        #leftBtn { grid-column: 1; grid-row: 2; }
        #pauseBtn { grid-column: 2; grid-row: 2; background: rgba(255,107,107,0.6); }
        #rightBtn { grid-column: 3; grid-row: 2; }
        #downBtn { grid-column: 2; grid-row: 3; }
        
        /* 屏幕 */
        .screen {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.95);
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 200;
            padding: 20px;
            text-align: center;
        }
        
        .screen.active { 
            display: flex; 
        }
        
        .screen h1 {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 30px;
            background: linear-gradient(45deg, #FFD700, #FFA500, #FF6B6B);
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        
        .btn {
            background: linear-gradient(145deg, #4CAF50, #45a049);
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 25px;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
            box-shadow: 0 5px 15px rgba(76,175,80,0.4);
            min-width: 150px;
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(76,175,80,0.6);
        }
        
        .btn.primary {
            background: linear-gradient(145deg, #FFD700, #FFA500);
            color: #1a1a1a;
            box-shadow: 0 5px 15px rgba(255,215,0,0.4);
        }
        
        .btn.secondary {
            background: linear-gradient(145deg, #6C7CE7, #5A6ACF);
            box-shadow: 0 5px 15px rgba(108,124,231,0.4);
        }
        
        .btn.danger {
            background: linear-gradient(145deg, #FF6B6B, #E55555);
            box-shadow: 0 5px 15px rgba(255,107,107,0.4);
        }
        
        .btn.practice {
            background: linear-gradient(145deg, #9C27B0, #7B1FA2);
            box-shadow: 0 5px 15px rgba(156,39,176,0.4);
        }
        
        /* 关卡选择 */
        #levelSelect {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            max-width: 300px;
            margin: 20px 0;
        }
        
        .level-btn {
            aspect-ratio: 1;
            background: rgba(255,255,255,0.1);
            border: 2px solid rgba(255,255,255,0.2);
            border-radius: 15px;
            color: white;
            font-size: 14px;
            font-weight: bold;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .level-btn:hover {
            border-color: #FFD700;
            transform: scale(1.05);
        }
        
        .level-btn.selected {
            border-color: #FFD700;
            background: rgba(255,215,0,0.2);
        }
        
        .level-btn.locked {
            opacity: 0.3;
            cursor: not-allowed;
        }
        
        /* 滚动内容 */
        .scrollable-content {
            max-height: 60vh;
            overflow-y: auto;
            padding: 20px;
            margin: 10px 0;
            border-radius: 10px;
            background: rgba(255,255,255,0.1);
            text-align: left;
            line-height: 1.6;
            width: 100%;
            box-sizing: border-box;
            -webkit-overflow-scrolling: touch;
        }
        
        .scrollable-content::-webkit-scrollbar {
            width: 8px;
        }
        
        .scrollable-content::-webkit-scrollbar-track {
            background: rgba(255,255,255,0.1);
            border-radius: 4px;
        }
        
        .scrollable-content::-webkit-scrollbar-thumb {
            background: rgba(255,255,255,0.5);
            border-radius: 4px;
        }
        
        .scrollable-content h3 {
            color: #FFD700;
            margin: 15px 0 10px 0;
        }
        
        .scrollable-content p {
            margin-bottom: 10px;
            font-size: 14px;
        }
        
        /* 关卡信息 */
        #levelInfo {
            text-align: center;
            margin: 20px 0;
            padding: 15px;
            background: rgba(255,255,255,0.1);
            border-radius: 10px;
            min-height: 120px;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        
        #levelInfo h3 {
            color: #FFD700;
            margin-bottom: 10px;
        }
        
        /* 响应式 */
        @media (max-width: 480px) {
            #gameContainer {
                border-radius: 0;
                max-width: 100vw;
                max-height: 100vh;
            }
            
            .screen h1 {
                font-size: 2rem;
            }
            
            .scrollable-content {
                max-height: 50vh;
                font-size: 13px;
            }
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <!-- 游戏UI -->
        <div id="gameUI">
            <div id="score" class="ui-item">
                <div class="ui-label">分数</div>
                <div class="ui-value">0</div>
            </div>
            <div id="level" class="ui-item">
                <div class="ui-label">关卡</div>
                <div class="ui-value">1</div>
            </div>
            <div id="lives" class="ui-item">
                <div class="ui-label">生命</div>
                <div class="ui-value">500</div>
            </div>
        </div>
        
        <!-- 游戏画布 -->
        <canvas id="gameCanvas"></canvas>
        
        <!-- 控制按钮 -->
        <div id="controls">
            <button class="control-btn" id="upBtn">↑</button>
            <button class="control-btn" id="leftBtn">←</button>
            <button class="control-btn" id="pauseBtn">⏸</button>
            <button class="control-btn" id="rightBtn">→</button>
            <button class="control-btn" id="downBtn">↓</button>
        </div>
        
        <!-- 开始屏幕 -->
        <div id="startScreen" class="screen active">
            <h1>🐍 贪食蛇大冒险</h1>
            <p style="margin-bottom: 30px;">准备好挑战史上最刺激的贪食蛇游戏了吗？</p>
            <button class="btn practice" id="practiceBtn">🏋️ 练习场</button>
            <button class="btn primary" id="adventureBtn">🚀 开始冒险</button>
            <button class="btn secondary" id="instructionsBtn">📖 游戏说明</button>
        </div>
        
        <!-- 关卡选择屏幕 -->
        <div id="levelSelectScreen" class="screen">
            <h1>选择关卡</h1>
            <div id="levelSelect"></div>
            <div id="levelInfo">
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            </div>
            <button class="btn primary" id="startLevelBtn" style="display: none;">开始游戏</button>
            <button class="btn secondary" id="backToMenuBtn">返回</button>
        </div>
        
        <!-- 游戏说明屏幕 -->
        <div id="instructionsScreen" class="screen">
            <h1>游戏说明</h1>
            <div class="scrollable-content">
                <h3>🎮 控制方式</h3>
                <p>• 滑动屏幕控制蛇的移动方向</p>
                <p>• 使用屏幕底部的方向键</p>
                <p>• 键盘玩家可使用 WASD 或方向键</p>
                
                <h3>🍎 普通食物</h3>
                <p>• 🍎 普通苹果 (+10分)</p>
                <p>• 🍇 葡萄 (+20分，临时加速)</p>
                <p>• 🍓 草莓 (+30分，临时减速)</p>
                
                <h3>💎 特殊道具</h3>
                <p>• 💎 钻石 (+50分)</p>
                <p>• ⚡ 闪电 (10秒穿墙能力)</p>
                <p>• 🛡️ 盾牌 (5秒无敌状态)</p>
                <p>• 🌟 星星 (15秒双倍得分)</p>
                <p>• ❄️ 冰块 (8秒时间缓慢)</p>
                
                <h3>💀 危险道具</h3>
                <p>• 💀 骷髅 (扣分并减少生命)</p>
                <p>• 🕳️ 黑洞 (传送到随机位置)</p>
                
                <h3>🏋️ 练习场</h3>
                <p>• 无限生命，轻松练习</p>
                <p>• 所有道具都会出现</p>
                <p>• 熟悉游戏机制的最佳选择</p>
                
                <h3>🏆 游戏目标</h3>
                <p>• 每个关卡都有目标分数</p>
                <p>• 达到目标分数即可通关</p>
                <p>• 避免撞墙和撞到自己</p>
                <p>• 合理利用道具获得优势</p>
                <p>• 挑战更高分数解锁成就</p>
                
                <h3>💡 游戏技巧</h3>
                <p>• 先在练习场熟悉操作</p>
                <p>• 规划路线避免困住自己</p>
                <p>• 优先收集高分道具</p>
                <p>• 善用道具效果时间</p>
                <p>• 保持冷静，稳中求胜</p>
            </div>
            <button class="btn" id="backFromInstructionsBtn">返回主菜单</button>
        </div>
        
        <!-- 暂停屏幕 -->
        <div id="pauseScreen" class="screen">
            <h1>游戏暂停</h1>
            <button class="btn primary" id="resumeBtn">继续游戏</button>
            <button class="btn secondary" id="restartBtn">重新开始</button>
            <button class="btn danger" id="quitBtn">退出到菜单</button>
        </div>
        
        <!-- 游戏结束屏幕 -->
        <div id="gameOverScreen" class="screen">
            <h1>游戏结束</h1>
            <div style="margin: 20px 0;">
                <p>最终分数: <span id="finalScore" style="color: #FFD700; font-weight: bold;">0</span></p>
                <p>存活时间: <span id="survivalTime" style="color: #87CEEB; font-weight: bold;">0</span>秒</p>
            </div>
            <button class="btn primary" id="restartGameBtn">重新挑战</button>
            <button class="btn secondary" id="selectLevelBtn">选择关卡</button>
            <button class="btn danger" id="returnMenuBtn">返回主菜单</button>
        </div>
        
        <!-- 关卡完成屏幕 -->
        <div id="levelCompleteScreen" class="screen">
            <h1>🎉 关卡完成!</h1>
            <div style="margin: 20px 0;">
                <p>获得分数: <span id="levelScore" style="color: #FFD700; font-weight: bold;">0</span></p>
            </div>
            <button class="btn primary" id="nextLevelBtn">下一关</button>
            <button class="btn secondary" id="chooseLevelBtn">选择关卡</button>
        </div>
    </div>

    <script>
        console.log('脚本开始加载...');

        // 全局变量
        let canvas, ctx;
        let gameState = 'menu';
        let selectedLevelId = null;
        let game = null;
        let gameLoop = null;
        
        // 关卡定义
        const levels = [
            {
                id: 0,
                name: "练习场",
                description: "无限生命，熟悉所有道具",
                targetScore: 0,
                speed: 150,
                walls: [],
                unlocked: true,
                isPractice: true
            },
            {
                id: 1,
                name: "新手村",
                description: "简单的开始，熟悉游戏操作",
                targetScore: 300,
                speed: 180,
                walls: [],
                unlocked: true
            },
            {
                id: 2,
                name: "森林迷宫",
                description: "小心墙壁障碍物",
                targetScore: 500,
                speed: 160,
                walls: [
                    {x: 5, y: 5, width: 2, height: 1},
                    {x: 10, y: 8, width: 1, height: 3}
                ],
                unlocked: false
            },
            {
                id: 3,
                name: "速度狂飙",
                description: "游戏速度逐渐加快",
                targetScore: 800,
                speed: 140,
                walls: [],
                unlocked: false
            }
        ];
        
        // 道具定义
        const powerups = {
            apple: { symbol: '🍎', points: 10, effect: null, rarity: 0.5 },
            grape: { symbol: '🍇', points: 20, effect: 'speed', rarity: 0.15 },
            strawberry: { symbol: '🍓', points: 30, effect: 'slow', rarity: 0.1 },
            diamond: { symbol: '💎', points: 50, effect: null, rarity: 0.08 },
            lightning: { symbol: '⚡', points: 0, effect: 'wallpass', rarity: 0.05 },
            shield: { symbol: '🛡️', points: 0, effect: 'invincible', rarity: 0.05 },
            star: { symbol: '🌟', points: 0, effect: 'doublescore', rarity: 0.04 },
            skull: { symbol: '💀', points: -50, effect: 'damage', rarity: 0.03 }
        };
        
        // 游戏类
        class SnakeGame {
            constructor() {
                console.log('创建游戏实例...');
                this.gridSize = 20;
                this.reset();
            }
            
            reset() {
                console.log('重置游戏状态...');
                this.cols = Math.floor(canvas.width / this.gridSize);
                this.rows = Math.floor(canvas.height / this.gridSize);
                
                const startCol = Math.floor(this.cols / 2);
                const startRow = Math.floor(this.rows / 2);
                
                this.snake = [{x: startCol, y: startRow}];
                this.direction = {x: 1, y: 0};
                this.nextDirection = {x: 1, y: 0};
                this.food = null;
                this.score = 0;
                this.lives = selectedLevelId === 0 ? 999 : 500;
                this.speed = levels.find(l => l.id === selectedLevelId)?.speed || 150;
                this.activePowerups = new Map();
                this.gameStartTime = Date.now();
                
                this.generateFood();
                this.updateUI();
                
                console.log('游戏状态重置完成');
            }
            
            generateFood() {
                let attempts = 0;
                do {
                    this.food = {
                        x: Math.floor(Math.random() * this.cols),
                        y: Math.floor(Math.random() * this.rows),
                        type: this.getRandomFoodType()
                    };
                    attempts++;
                } while (this.isPositionOccupied(this.food.x, this.food.y) && attempts < 100);
            }
            
            getRandomFoodType() {
                const rand = Math.random();
                let cumulative = 0;
                
                for (const [type, data] of Object.entries(powerups)) {
                    cumulative += data.rarity;
                    if (rand <= cumulative) {
                        return type;
                    }
                }
                return 'apple';
            }
            
            isPositionOccupied(x, y) {
                for (const segment of this.snake) {
                    if (segment.x === x && segment.y === y) return true;
                }
                
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                if (currentLevel && currentLevel.walls) {
                    for (const wall of currentLevel.walls) {
                        if (x >= wall.x && x < wall.x + wall.width &&
                            y >= wall.y && y < wall.y + wall.height) {
                            return true;
                        }
                    }
                }
                
                return false;
            }
            
            move() {
                this.direction = {...this.nextDirection};
                const head = {...this.snake[0]};
                head.x += this.direction.x;
                head.y += this.direction.y;
                
                if (!this.activePowerups.has('wallpass')) {
                    if (head.x < 0 || head.x >= this.cols || head.y < 0 || head.y >= this.rows) {
                        this.handleCollision();
                        return;
                    }
                } else {
                    head.x = (head.x + this.cols) % this.cols;
                    head.y = (head.y + this.rows) % this.rows;
                }
                
                if (this.checkCollision(head)) {
                    this.handleCollision();
                    return;
                }
                
                this.snake.unshift(head);
                
                if (this.food && head.x === this.food.x && head.y === this.food.y) {
                    this.eatFood();
                } else {
                    this.snake.pop();
                }
            }
            
            checkCollision(head) {
                if (!this.activePowerups.has('invincible')) {
                    for (let i = 1; i < this.snake.length; i++) {
                        if (this.snake[i].x === head.x && this.snake[i].y === head.y) {
                            return true;
                        }
                    }
                }
                
                if (!this.activePowerups.has('wallpass')) {
                    const currentLevel = levels.find(l => l.id === selectedLevelId);
                    if (currentLevel && currentLevel.walls) {
                        for (const wall of currentLevel.walls) {
                            if (head.x >= wall.x && head.x < wall.x + wall.width &&
                                head.y >= wall.y && head.y < wall.y + wall.height) {
                                return true;
                            }
                        }
                    }
                }
                
                return false;
            }
            
            handleCollision() {
                if (this.activePowerups.has('invincible')) return;
                
                if (selectedLevelId !== 0) {
                    this.lives--;
                }
                
                if (this.lives <= 0 && selectedLevelId !== 0) {
                    this.gameOver();
                } else {
                    const startCol = Math.floor(this.cols / 2);
                    const startRow = Math.floor(this.rows / 2);
                    this.snake = [{x: startCol, y: startRow}];
                    this.direction = {x: 1, y: 0};
                    this.nextDirection = {x: 1, y: 0};
                    this.activePowerups.clear();
                }
                
                this.updateUI();
            }
            
            eatFood() {
                if (!this.food) return;
                
                const foodData = powerups[this.food.type];
                const multiplier = this.activePowerups.has('doublescore') ? 2 : 1;
                this.score += Math.max(0, foodData.points * multiplier);
                
                this.handlePowerupEffect(this.food.type);
                this.generateFood();
                this.updateUI();
                
                if (selectedLevelId !== 0) {
                    const currentLevel = levels.find(l => l.id === selectedLevelId);
                    if (this.score >= currentLevel.targetScore) {
                        this.levelComplete();
                    }
                }
            }
            
            handlePowerupEffect(type) {
                const effect = powerups[type].effect;
                if (!effect) return;
                
                switch (effect) {
                    case 'speed':
                        this.speed = Math.max(80, this.speed - 20);
                        setTimeout(() => {
                            this.speed = Math.min(200, this.speed + 20);
                        }, 5000);
                        break;
                        
                    case 'slow':
                        this.speed = Math.min(250, this.speed + 30);
                        setTimeout(() => {
                            this.speed = Math.max(80, this.speed - 30);
                        }, 5000);
                        break;
                        
                    case 'wallpass':
                        this.activePowerups.set('wallpass', Date.now() + 10000);
                        break;
                        
                    case 'invincible':
                        this.activePowerups.set('invincible', Date.now() + 5000);
                        break;
                        
                    case 'doublescore':
                        this.activePowerups.set('doublescore', Date.now() + 15000);
                        break;
                        
                    case 'damage':
                        if (selectedLevelId !== 0) {
                            this.lives = Math.max(0, this.lives - 1);
                            if (this.lives <= 0) this.gameOver();
                        }
                        break;
                }
            }
            
            changeDirection(newDirection) {
                if (this.direction.x !== -newDirection.x || this.direction.y !== -newDirection.y) {
                    this.nextDirection = newDirection;
                }
            }
            
            updateUI() {
                document.querySelector('#score .ui-value').textContent = this.score;
                document.querySelector('#level .ui-value').textContent = selectedLevelId === 0 ? '练习' : selectedLevelId;
                document.querySelector('#lives .ui-value').textContent = selectedLevelId === 0 ? '∞' : this.lives;
            }
            
            updatePowerups() {
                const now = Date.now();
                for (const [effect, expireTime] of this.activePowerups.entries()) {
                    if (now > expireTime) {
                        this.activePowerups.delete(effect);
                    }
                }
            }
            
            levelComplete() {
                gameState = 'levelComplete';
                document.getElementById('levelScore').textContent = this.score;
                
                if (selectedLevelId < levels.length - 1) {
                    levels[selectedLevelId + 1].unlocked = true;
                }
                
                showScreen('levelCompleteScreen');
            }
            
            gameOver() {
                gameState = 'gameOver';
                const survivalTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                
                document.getElementById('finalScore').textContent = this.score;
                document.getElementById('survivalTime').textContent = survivalTime;
                
                showScreen('gameOverScreen');
            }
            
            draw() {
                ctx.fillStyle = '#1a1a2e';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                if (currentLevel && currentLevel.walls && selectedLevelId !== 0) {
                    ctx.fillStyle = '#8B4513';
                    currentLevel.walls.forEach(wall => {
                        ctx.fillRect(
                            wall.x * this.gridSize,
                            wall.y * this.gridSize,
                            wall.width * this.gridSize,
                            wall.height * this.gridSize
                        );
                    });
                }
                
                if (this.food) {
                    const foodData = powerups[this.food.type];
                    const x = this.food.x * this.gridSize;
                    const y = this.food.y * this.gridSize;
                    
                    ctx.font = `${this.gridSize * 0.8}px Arial`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(
                        foodData.symbol,
                        x + this.gridSize/2,
                        y + this.gridSize/2
                    );
                }
                
                this.snake.forEach((segment, index) => {
                    const x = segment.x * this.gridSize;
                    const y = segment.y * this.gridSize;
                    
                    if (index === 0) {
                        ctx.fillStyle = this.activePowerups.has('invincible') ? '#FFD700' : '#32CD32';
                        ctx.fillRect(x + 2, y + 2, this.gridSize - 4, this.gridSize - 4);
                        
                        ctx.fillStyle = 'white';
                        ctx.fillRect(x + 6, y + 4, 3, 3);
                        ctx.fillRect(x + this.gridSize - 9, y + 4, 3, 3);
                        
                        ctx.fillStyle = 'black';
                        ctx.fillRect(x + 7, y + 5, 1, 1);
                        ctx.fillRect(x + this.gridSize - 8, y + 5, 1, 1);
                    } else {
                        ctx.fillStyle = '#90EE90';
                        ctx.fillRect(x + 1, y + 1, this.gridSize - 2, this.gridSize - 2);
                    }
                });
                
                if (this.activePowerups.has('invincible')) {
                    ctx.fillStyle = 'rgba(255, 215, 0, 0.2)';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                }
            }
        }
        
        // 屏幕管理函数
        function showScreen(screenId) {
            console.log('显示屏幕:', screenId);
            
            document.querySelectorAll('.screen').forEach(screen => {
                screen.classList.remove('active');
            });
            
            document.getElementById('gameUI').classList.remove('active');
            document.getElementById('controls').classList.remove('active');
            
            if (screenId) {
                const screen = document.getElementById(screenId);
                if (screen) {
                    screen.classList.add('active');
                } else {
                    console.error('屏幕未找到:', screenId);
                }
            }
        }
        
        function showStartScreen() {
            console.log('显示开始屏幕');
            gameState = 'menu';
            selectedLevelId = null;
            showScreen('startScreen');
            
            if (gameLoop) {
                clearInterval(gameLoop);
                gameLoop = null;
            }
        }
        
        function showLevelSelect() {
            console.log('显示关卡选择');
            generateLevelButtons();
            showScreen('levelSelectScreen');
            
            document.getElementById('startLevelBtn').style.display = 'none';
            document.getElementById('levelInfo').innerHTML = `
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            `;
        }
        
        function showInstructions() {
            console.log('显示游戏说明');
            showScreen('instructionsScreen');
        }
        
        function startPractice() {
            console.log('开始练习模式');
            selectedLevelId = 0;
            startGame();
        }
        
        function selectLevel(levelId) {
            console.log('选择关卡:', levelId);
            const level = levels.find(l => l.id === levelId);
            if (!level || !level.unlocked) return;
            
            selectedLevelId = levelId;
            
            document.querySelectorAll('.level-btn').forEach(btn => {
                btn.classList.remove('selected');
            });
            const levelBtn = document.querySelector(`[data-level="${levelId}"]`);
            if (levelBtn) {
                levelBtn.classList.add('selected');
            }
            
            if (levelId === 0) {
                document.getElementById('levelInfo').innerHTML = `
                    <h3>${level.name}</h3>
                    <p>${level.description}</p>
                    <p>无限生命，体验所有道具效果</p>
                `;
            } else {
                document.getElementById('levelInfo').innerHTML = `
                    <h3>${level.name}</h3>
                    <p>${level.description}</p>
                    <p>目标分数: <span style="color: #FFD700;">${level.targetScore}</span></p>
                    <p>初始生命: <span style="color: #FF6B6B;">500</span></p>
                `;
            }
            
            document.getElementById('startLevelBtn').style.display = 'inline-block';
        }
        
        function startSelectedLevel() {
            console.log('开始选中的关卡:', selectedLevelId);
            if (selectedLevelId === null) return;
            startGame();
        }
        
        function startGame() {
            console.log('开始游戏，关卡ID:', selectedLevelId);
            
            if (selectedLevelId === null) {
                console.error('未选择关卡');
                return;
            }
            
            gameState = 'playing';
            
            if (!game) {
                game = new SnakeGame();
            } else {
                game.reset();
            }
            
            showScreen(null);
            document.getElementById('gameUI').classList.add('active');
            document.getElementById('controls').classList.add('active');
            
            if (gameLoop) {
                clearInterval(gameLoop);
            }
            
            gameLoop = setInterval(() => {
                if (gameState === 'playing') {
                    game.updatePowerups();
                    game.move();
                    game.draw();
                }
            }, game.speed);
            
            console.log('游戏开始成功');
        }
        
        function generateLevelButtons() {
            const container = document.getElementById('levelSelect');
            container.innerHTML = '';
            
            levels.forEach(level => {
                const button = document.createElement('button');
                button.className = `level-btn ${level.unlocked ? 'unlocked' : 'locked'}`;
                button.setAttribute('data-level', level.id);
                
                if (level.id === 0) {
                    button.innerHTML = `
                        <div style="font-size: 18px; margin-bottom: 5px;">🏋️</div>
                        <div style="font-size: 12px;">${level.name}</div>
                    `;
                } else {
                    button.innerHTML = `
                        <div style="font-size: 18px; margin-bottom: 5px;">${level.id}</div>
                        <div style="font-size: 12px;">${level.name}</div>
                    `;
                }
                
                if (level.unlocked) {
                    button.addEventListener('click', () => selectLevel(level.id));
                }
                
                container.appendChild(button);
            });
        }
        
        function pauseGame() {
            if (gameState === 'playing') {
                gameState = 'paused';
                showScreen('pauseScreen');
            }
        }
        
        function resumeGame() {
            gameState = 'playing';
            showScreen(null);
            document.getElementById('gameUI').classList.add('active');
            document.getElementById('controls').classList.add('active');
        }
        
        function restartLevel() {
            startGame();
        }
        
        function quitToMenu() {
            showStartScreen();
        }
        
        function nextLevel() {
            if (selectedLevelId < levels.length - 1) {
                selectedLevelId++;
                startGame();
            } else {
                showStartScreen();
            }
        }
        
        // 事件监听器设置
        function setupEventListeners() {
            console.log('设置事件监听器...');
            
            // 主菜单按钮
            document.getElementById('practiceBtn').addEventListener('click', startPractice);
            document.getElementById('adventureBtn').addEventListener('click', showLevelSelect);
            document.getElementById('instructionsBtn').addEventListener('click', showInstructions);
            
            // 关卡选择按钮
            document.getElementById('startLevelBtn').addEventListener('click', startSelectedLevel);
            document.getElementById('backToMenuBtn').addEventListener('click', showStartScreen);
            
            // 说明页按钮
            document.getElementById('backFromInstructionsBtn').addEventListener('click', showStartScreen);
            
            // 暂停页按钮
            document.getElementById('resumeBtn').addEventListener('click', resumeGame);
            document.getElementById('restartBtn').addEventListener('click', restartLevel);
            document.getElementById('quitBtn').addEventListener('click', quitToMenu);
            
            // 游戏结束页按钮
            document.getElementById('restartGameBtn').addEventListener('click', restartLevel);
            document.getElementById('selectLevelBtn').addEventListener('click', showLevelSelect);
            document.getElementById('returnMenuBtn').addEventListener('click', showStartScreen);
            
            // 关卡完成页按钮
            document.getElementById('nextLevelBtn').addEventListener('click', nextLevel);
            document.getElementById('chooseLevelBtn').addEventListener('click', showLevelSelect);
            
            // 游戏控制按钮
            document.getElementById('upBtn').addEventListener('click', (e) => {
                e.preventDefault();
                if (game && gameState === 'playing') game.changeDirection({x: 0, y: -1});
            });
            
            document.getElementById('downBtn').addEventListener('click', (e) => {
                e.preventDefault();
                if (game && gameState === 'playing') game.changeDirection({x: 0, y: 1});
            });
            
            document.getElementById('leftBtn').addEventListener('click', (e) => {
                e.preventDefault();
                if (game && gameState === 'playing') game.changeDirection({x: -1, y: 0});
            });
            
            document.getElementById('rightBtn').addEventListener('click', (e) => {
                e.preventDefault();
                if (game && gameState === 'playing') game.changeDirection({x: 1, y: 0});
            });
            
            document.getElementById('pauseBtn').addEventListener('click', (e) => {
                e.preventDefault();
                if (gameState === 'playing') {
                    pauseGame();
                } else if (gameState === 'paused') {
                    resumeGame();
                }
            });
            
            // 键盘控制
            document.addEventListener('keydown', (e) => {
                if (gameState !== 'playing' || !game) return;
                
                switch (e.key) {
                    case 'ArrowUp':
                    case 'w':
                    case 'W':
                        e.preventDefault();
                        game.changeDirection({x: 0, y: -1});
                        break;
                    case 'ArrowDown':
                    case 's':
                    case 'S':
                        e.preventDefault();
                        game.changeDirection({x: 0, y: 1});
                        break;
                    case 'ArrowLeft':
                    case 'a':
                    case 'A':
                        e.preventDefault();
                        game.changeDirection({x: -1, y: 0});
                        break;
                    case 'ArrowRight':
                    case 'd':
                    case 'D':
                        e.preventDefault();
                        game.changeDirection({x: 1, y: 0});
                        break;
                    case ' ':
                        e.preventDefault();
                        pauseGame();
                        break;
                }
            });
            
            // 触摸滑动
            let touchStartX = 0;
            let touchStartY = 0;
            
            canvas.addEventListener('touchstart', (e) => {
                e.preventDefault();
                touchStartX = e.touches[0].clientX;
                touchStartY = e.touches[0].clientY;
            });
            
            canvas.addEventListener('touchend', (e) => {
                e.preventDefault();
                
                if (gameState !== 'playing' || !game) return;
                
                const touchEndX = e.changedTouches[0].clientX;
                const touchEndY = e.changedTouches[0].clientY;
                const deltaX = touchEndX - touchStartX;
                const deltaY = touchEndY - touchStartY;
                const minSwipeDistance = 30;
                
                if (Math.abs(deltaX) > Math.abs(deltaY)) {
                    if (Math.abs(deltaX) > minSwipeDistance) {
                        if (deltaX > 0) {
                            game.changeDirection({x: 1, y: 0});
                        } else {
                            game.changeDirection({x: -1, y: 0});
                        }
                    }
                } else {
                    if (Math.abs(deltaY) > minSwipeDistance) {
                        if (deltaY > 0) {
                            game.changeDirection({x: 0, y: 1});
                        } else {
                            game.changeDirection({x: 0, y: -1});
                        }
                    }
                }
            });
            
            // 窗口大小调整
            window.addEventListener('resize', resizeCanvas);
            
            console.log('事件监听器设置完成');
        }
        
        function resizeCanvas() {
            console.log('调整画布大小...');
            const container = document.getElementById('gameContainer');
            const rect = container.getBoundingClientRect();
            
            canvas.width = rect.width;
            canvas.height = rect.height;
            
            if (game) {
                game.reset();
            }
        }
        
        // 初始化函数
        function init() {
            console.log('开始初始化游戏...');
            
            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');
            
            if (!canvas || !ctx) {
                console.error('无法获取画布或上下文');
                return;
            }
            
            resizeCanvas();
            setupEventListeners();
            
            console.log('游戏初始化完成！');
        }
        
        // 页面加载完成后初始化
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
        
        console.log('脚本加载完成');
    </script>
</body>
</html>
