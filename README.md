# Snake-Xenzia-only-for-Ashley
Creating an interesting game only for my love, Ashley. Hope her happy everyday!


<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, maximum-scale=1.0, minimum-scale=1.0">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="贪食蛇大冒险">
    <title>🐍 贪食蛇大冒险 - 终极版</title>
    <link rel="apple-touch-icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><circle cx='50' cy='50' r='40' fill='%2332CD32'/><circle cx='35' cy='35' r='5' fill='white'/><circle cx='32' cy='32' r='2' fill='black'/></svg>">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&display=swap');
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
            -webkit-touch-callout: none;
            -webkit-user-select: none;
            user-select: none;
        }
        
        body {
            font-family: 'Orbitron', monospace;
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
            background: radial-gradient(circle at 30% 30%, rgba(255,255,255,0.1) 0%, transparent 50%);
            touch-action: none;
        }
        
        /* 头部UI */
        #gameUI {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 80px;
            background: linear-gradient(180deg, rgba(0,0,0,0.8) 0%, transparent 100%);
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            z-index: 100;
        }
        
        .ui-item {
            text-align: center;
            flex: 1;
        }
        
        .ui-label {
            font-size: 10px;
            opacity: 0.7;
            margin-bottom: 2px;
        }
        
        .ui-value {
            font-size: 16px;
            font-weight: 700;
            text-shadow: 0 0 10px rgba(255,255,255,0.5);
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
            display: grid;
            grid-template-columns: repeat(3, 60px);
            grid-template-rows: repeat(3, 60px);
            gap: 5px;
            z-index: 100;
        }
        
        .control-btn {
            background: linear-gradient(145deg, rgba(255,255,255,0.2), rgba(255,255,255,0.05));
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
            user-select: none;
            -webkit-user-select: none;
        }
        
        .control-btn:active {
            transform: scale(0.9);
            background: linear-gradient(145deg, rgba(255,255,255,0.4), rgba(255,255,255,0.1));
            box-shadow: inset 0 2px 10px rgba(0,0,0,0.3);
        }
        
        #upBtn { grid-column: 2; grid-row: 1; }
        #leftBtn { grid-column: 1; grid-row: 2; }
        #pauseBtn { grid-column: 2; grid-row: 2; background: linear-gradient(145deg, #FF6B6B, #E55555); }
        #rightBtn { grid-column: 3; grid-row: 2; }
        #downBtn { grid-column: 2; grid-row: 3; }
        
        /* 屏幕和弹窗 */
        .screen {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, rgba(0,0,0,0.95), rgba(26,26,46,0.95));
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 200;
            backdrop-filter: blur(20px);
            padding: 20px;
        }
        
        .screen h1 {
            font-size: 2.5rem;
            font-weight: 900;
            margin-bottom: 30px;
            background: linear-gradient(45deg, #FFD700, #FFA500, #FF6B6B);
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0 0 20px rgba(255,215,0,0.5);
            text-align: center;
        }
        
        /* 修复游戏说明滚动问题 */
        .scrollable-content {
            max-height: 60vh;
            overflow-y: auto;
            padding: 10px;
            margin: 10px 0;
            border-radius: 10px;
            background: rgba(255,255,255,0.1);
            backdrop-filter: blur(10px);
            -webkit-overflow-scrolling: touch;
        }
        
        .scrollable-content::-webkit-scrollbar {
            width: 6px;
        }
        
        .scrollable-content::-webkit-scrollbar-track {
            background: rgba(255,255,255,0.1);
            border-radius: 3px;
        }
        
        .scrollable-content::-webkit-scrollbar-thumb {
            background: rgba(255,255,255,0.4);
            border-radius: 3px;
        }
        
        .btn {
            background: linear-gradient(145deg, #4CAF50, #45a049);
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            font-weight: 700;
            border-radius: 25px;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
            box-shadow: 0 5px 15px rgba(76,175,80,0.4);
            font-family: 'Orbitron', monospace;
            min-width: 150px;
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(76,175,80,0.6);
        }
        
        .btn:active {
            transform: translateY(0);
        }
        
        .btn.danger {
            background: linear-gradient(145deg, #FF6B6B, #E55555);
            box-shadow: 0 5px 15px rgba(255,107,107,0.4);
        }
        
        .btn.secondary {
            background: linear-gradient(145deg, #6C7CE7, #5A6ACF);
            box-shadow: 0 5px 15px rgba(108,124,231,0.4);
        }
        
        .btn.primary {
            background: linear-gradient(145deg, #FFD700, #FFA500);
            color: #1a1a1a;
            box-shadow: 0 5px 15px rgba(255,215,0,0.4);
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
            background: linear-gradient(145deg, rgba(255,255,255,0.1), rgba(255,255,255,0.05));
            border: 2px solid rgba(255,255,255,0.2);
            border-radius: 15px;
            color: white;
            font-size: 14px;
            font-weight: 700;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.3s;
            backdrop-filter: blur(10px);
            position: relative;
        }
        
        .level-btn:hover, .level-btn.unlocked:hover {
            border-color: #FFD700;
            transform: scale(1.05);
            box-shadow: 0 0 20px rgba(255,215,0,0.3);
        }
        
        .level-btn.locked {
            opacity: 0.3;
            cursor: not-allowed;
        }
        
        .level-btn.selected {
            border-color: #FFD700;
            background: linear-gradient(145deg, rgba(255,215,0,0.3), rgba(255,215,0,0.1));
            transform: scale(1.05);
            box-shadow: 0 0 20px rgba(255,215,0,0.5);
        }
        
        .level-btn.completed::after {
            content: '✓';
            position: absolute;
            top: 5px;
            right: 5px;
            color: #4CAF50;
            font-size: 16px;
        }
        
        /* 关卡信息显示 */
        #levelInfo {
            text-align: center;
            margin: 20px 0;
            padding: 15px;
            background: rgba(255,255,255,0.1);
            border-radius: 10px;
            backdrop-filter: blur(10px);
            min-height: 100px;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        
        #levelInfo h3 {
            color: #FFD700;
            margin-bottom: 10px;
            font-size: 1.2rem;
        }
        
        #levelInfo p {
            opacity: 0.8;
            line-height: 1.4;
            font-size: 0.9rem;
        }
        
        /* 道具显示 */
        #powerupsUI {
            position: absolute;
            top: 90px;
            right: 10px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 100;
        }
        
        .powerup-indicator {
            width: 40px;
            height: 40px;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 18px;
            background: linear-gradient(145deg, rgba(255,255,255,0.2), rgba(255,255,255,0.05));
            border: 2px solid rgba(255,255,255,0.3);
            backdrop-filter: blur(10px);
            animation: pulse 2s infinite;
        }
        
        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.1); }
        }
        
        /* 粒子效果 */
        .particle {
            position: absolute;
            pointer-events: none;
            border-radius: 50%;
            animation: particleFloat 2s ease-out forwards;
        }
        
        @keyframes particleFloat {
            0% {
                transform: scale(1) translate(0, 0);
                opacity: 1;
            }
            100% {
                transform: scale(0) translate(var(--dx), var(--dy));
                opacity: 0;
            }
        }
        
        /* 响应式设计 */
        @media (max-width: 480px) {
            #gameContainer {
                border-radius: 0;
                max-width: 100vw;
                max-height: 100vh;
            }
            
            .screen h1 {
                font-size: 2rem;
            }
            
            #controls {
                bottom: 30px;
            }
            
            .control-btn {
                width: 55px;
                height: 55px;
                font-size: 18px;
            }
            
            .scrollable-content {
                max-height: 50vh;
            }
        }
        
        @media (orientation: landscape) and (max-height: 500px) {
            #controls {
                right: 20px;
                bottom: 50%;
                transform: translateY(50%);
                left: auto;
            }
            
            .screen h1 {
                font-size: 1.8rem;
                margin-bottom: 20px;
            }
            
            .scrollable-content {
                max-height: 40vh;
            }
        }
        
        /* 加载动画 */
        .loading {
            animation: spin 1s linear infinite;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        /* 成就通知 */
        .achievement {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(145deg, #FFD700, #FFA500);
            color: #1a1a1a;
            padding: 20px;
            border-radius: 15px;
            font-weight: 700;
            text-align: center;
            z-index: 300;
            animation: achievementPop 3s ease-out forwards;
            box-shadow: 0 10px 30px rgba(255,215,0,0.5);
        }
        
        @keyframes achievementPop {
            0% {
                transform: translate(-50%, -50%) scale(0);
                opacity: 0;
            }
            20% {
                transform: translate(-50%, -50%) scale(1.1);
                opacity: 1;
            }
            80% {
                transform: translate(-50%, -50%) scale(1);
                opacity: 1;
            }
            100% {
                transform: translate(-50%, -50%) scale(0.8);
                opacity: 0;
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
                <div class="ui-value">3</div>
            </div>
        </div>
        
        <!-- 道具指示器 -->
        <div id="powerupsUI"></div>
        
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
        <div id="startScreen" class="screen">
            <h1>🐍 贪食蛇大冒险</h1>
            <p style="text-align: center; margin-bottom: 20px;">准备好挑战史上最刺激的贪食蛇游戏了吗？</p>
            <div style="margin: 20px 0; text-align: center; line-height: 1.6;">
                <div>🍎 收集食物获得分数</div>
                <div>💎 特殊道具带来惊喜</div>
                <div>🏆 解锁更多关卡</div>
            </div>
            <button class="btn primary" onclick="showLevelSelect()">开始冒险</button>
            <button class="btn secondary" onclick="showInstructions()">游戏说明</button>
        </div>
        
        <!-- 关卡选择 -->
        <div id="levelSelectScreen" class="screen" style="display: none;">
            <h1>选择关卡</h1>
            <div id="levelSelect">
                <!-- 关卡按钮将通过JS生成 -->
            </div>
            <div id="levelInfo">
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            </div>
            <div>
                <button class="btn primary" id="startLevelBtn" onclick="startSelectedLevel()" style="display: none;">开始游戏</button>
                <button class="btn secondary" onclick="showStartScreen()">返回</button>
            </div>
        </div>
        
        <!-- 游戏说明 -->
        <div id="instructionsScreen" class="screen" style="display: none;">
            <h1>游戏说明</h1>
            <div class="scrollable-content">
                <div style="text-align: left; line-height: 1.8; font-size: 14px;">
                    <h3 style="color: #FFD700; margin: 15px 0 10px 0;">🎮 控制方式</h3>
                    <p style="margin-bottom: 15px;">• 滑动屏幕控制蛇的移动方向</p>
                    <p style="margin-bottom: 15px;">• 使用屏幕底部的方向键</p>
                    <p style="margin-bottom: 15px;">• 键盘玩家可使用 WASD 或方向键</p>
                    
                    <h3 style="color: #FFD700; margin: 15px 0 10px 0;">🍎 普通食物</h3>
                    <p style="margin-bottom: 10px;">• 🍎 普通苹果 (+10分)</p>
                    <p style="margin-bottom: 10px;">• 🍇 葡萄 (+20分，临时加速)</p>
                    <p style="margin-bottom: 15px;">• 🍓 草莓 (+30分，临时减速)</p>
                    
                    <h3 style="color: #FFD700; margin: 15px 0 10px 0;">💎 特殊道具</h3>
                    <p style="margin-bottom: 10px;">• 💎 钻石 (+50分)</p>
                    <p style="margin-bottom: 10px;">• ⚡ 闪电 (10秒穿墙能力)</p>
                    <p style="margin-bottom: 10px;">• 🛡️ 盾牌 (5秒无敌状态)</p>
                    <p style="margin-bottom: 10px;">• 🌟 星星 (15秒双倍得分)</p>
                    <p style="margin-bottom: 15px;">• ❄️ 冰块 (8秒时间缓慢)</p>
                    
                    <h3 style="color: #FF6B6B; margin: 15px 0 10px 0;">💀 危险道具</h3>
                    <p style="margin-bottom: 10px;">• 💀 骷髅 (扣除50分并减少一条生命)</p>
                    <p style="margin-bottom: 15px;">• 🕳️ 黑洞 (传送到随机位置)</p>
                    
                    <h3 style="color: #FFD700; margin: 15px 0 10px 0;">🏆 游戏目标</h3>
                    <p style="margin-bottom: 10px;">• 每个关卡都有目标分数</p>
                    <p style="margin-bottom: 10px;">• 达到目标分数即可通关</p>
                    <p style="margin-bottom: 10px;">• 避免撞墙和撞到自己</p>
                    <p style="margin-bottom: 10px;">• 合理利用道具获得优势</p>
                    <p style="margin-bottom: 15px;">• 挑战更高分数解锁成就</p>
                    
                    <h3 style="color: #FFD700; margin: 15px 0 10px 0;">🎯 关卡特色</h3>
                    <p style="margin-bottom: 10px;">• 新手村：基础玩法练习</p>
                    <p style="margin-bottom: 10px;">• 森林迷宫：增加墙壁障碍</p>
                    <p style="margin-bottom: 10px;">• 速度狂飙：游戏速度逐渐加快</p>
                    <p style="margin-bottom: 10px;">• 传送门：神秘传送挑战</p>
                    <p style="margin-bottom: 15px;">• Boss关卡：终极挑战等你征服</p>
                    
                    <h3 style="color: #4CAF50; margin: 15px 0 10px 0;">💡 游戏技巧</h3>
                    <p style="margin-bottom: 10px;">• 规划路线避免困住自己</p>
                    <p style="margin-bottom: 10px;">• 优先收集高分道具</p>
                    <p style="margin-bottom: 10px;">• 善用道具效果时间</p>
                    <p style="margin-bottom: 10px;">• 注意观察危险道具</p>
                    <p style="margin-bottom: 20px;">• 保持冷静，稳中求胜</p>
                </div>
            </div>
            <button class="btn" onclick="showStartScreen()">返回主菜单</button>
        </div>
        
        <!-- 暂停屏幕 -->
        <div id="pauseScreen" class="screen" style="display: none;">
            <h1>游戏暂停</h1>
            <button class="btn primary" onclick="resumeGame()">继续游戏</button>
            <button class="btn secondary" onclick="restartLevel()">重新开始</button>
            <button class="btn danger" onclick="quitToMenu()">退出到菜单</button>
        </div>
        
        <!-- 游戏结束 -->
        <div id="gameOverScreen" class="screen" style="display: none;">
            <h1>游戏结束</h1>
            <div id="gameOverStats" style="margin: 20px 0; text-align: center;">
                <div style="margin: 10px 0;">最终分数: <span id="finalScore" style="color: #FFD700; font-weight: bold;">0</span></div>
                <div style="margin: 10px 0;">存活时间: <span id="survivalTime" style="color: #87CEEB; font-weight: bold;">0</span>秒</div>
                <div style="margin: 10px 0;">最大长度: <span id="maxLength" style="color: #32CD32; font-weight: bold;">0</span></div>
            </div>
            <button class="btn primary" onclick="restartLevel()">重新挑战</button>
            <button class="btn secondary" onclick="showLevelSelect()">选择关卡</button>
            <button class="btn danger" onclick="showStartScreen()">返回主菜单</button>
        </div>
        
        <!-- 关卡完成 -->
        <div id="levelCompleteScreen" class="screen" style="display: none;">
            <h1>🎉 关卡完成!</h1>
            <div id="levelCompleteStats" style="margin: 20px 0; text-align: center;">
                <div style="margin: 10px 0;">获得分数: <span id="levelScore" style="color: #FFD700; font-weight: bold;">0</span></div>
                <div style="margin: 10px 0;">时间奖励: <span id="timeBonus" style="color: #87CEEB; font-weight: bold;">0</span></div>
                <div style="margin: 10px 0;">完美奖励: <span id="perfectBonus" style="color: #32CD32; font-weight: bold;">0</span></div>
            </div>
            <button class="btn primary" onclick="nextLevel()">下一关</button>
            <button class="btn secondary" onclick="showLevelSelect()">选择关卡</button>
        </div>
    </div>

    <script>
        // 游戏全局变量
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        let gameState = 'menu'; // menu, playing, paused, gameOver, levelComplete
        let selectedLevelId = null;
        
        // 音频上下文初始化
        let audioContext;
        
        function initAudio() {
            if (!audioContext) {
                audioContext = new (window.AudioContext || window.webkitAudioContext)();
            }
        }
        
        // 音效生成器
        class SoundGenerator {
            static playTone(frequency, duration, type = 'sine', volume = 0.1) {
                if (!audioContext) return;
                
                const oscillator = audioContext.createOscillator();
                const gainNode = audioContext.createGain();
                
                oscillator.connect(gainNode);
                gainNode.connect(audioContext.destination);
                
                oscillator.frequency.setValueAtTime(frequency, audioContext.currentTime);
                oscillator.type = type;
                
                gainNode.gain.setValueAtTime(volume, audioContext.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.001, audioContext.currentTime + duration);
                
                oscillator.start(audioContext.currentTime);
                oscillator.stop(audioContext.currentTime + duration);
            }
            
            static eat() {
                this.playTone(800, 0.1, 'square', 0.3);
                setTimeout(() => this.playTone(1000, 0.1, 'square', 0.2), 50);
            }
            
            static powerup() {
                this.playTone(523, 0.1, 'sine', 0.3);
                setTimeout(() => this.playTone(659, 0.1, 'sine', 0.3), 100);
                setTimeout(() => this.playTone(784, 0.1, 'sine', 0.3), 200);
            }
            
            static death() {
                this.playTone(200, 0.3, 'sawtooth', 0.4);
                setTimeout(() => this.playTone(150, 0.3, 'sawtooth', 0.3), 200);
                setTimeout(() => this.playTone(100, 0.5, 'sawtooth', 0.2), 400);
            }
            
            static levelComplete() {
                for (let i = 0; i < 5; i++) {
                    setTimeout(() => {
                        this.playTone(523 + i * 100, 0.2, 'triangle', 0.3);
                    }, i * 100);
                }
            }
            
            static warning() {
                this.playTone(300, 0.1, 'square', 0.2);
            }
        }
        
        // 画布大小调整
        function resizeCanvas() {
            const container = document.getElementById('gameContainer');
            const rect = container.getBoundingClientRect();
            
            canvas.width = rect.width;
            canvas.height = rect.height;
            
            // 更新游戏网格
            if (typeof game !== 'undefined') {
                game.updateGrid();
            }
        }
        
        // 粒子系统
        class ParticleSystem {
            constructor() {
                this.particles = [];
            }
            
            createExplosion(x, y, color, count = 10) {
                for (let i = 0; i < count; i++) {
                    this.particles.push({
                        x: x,
                        y: y,
                        vx: (Math.random() - 0.5) * 10,
                        vy: (Math.random() - 0.5) * 10,
                        life: 1,
                        decay: 0.02,
                        size: Math.random() * 4 + 2,
                        color: color
                    });
                }
            }
            
            update() {
                this.particles = this.particles.filter(particle => {
                    particle.x += particle.vx;
                    particle.y += particle.vy;
                    particle.life -= particle.decay;
                    particle.vx *= 0.98;
                    particle.vy *= 0.98;
                    particle.size *= 0.99;
                    
                    return particle.life > 0;
                });
            }
            
            draw(ctx) {
                this.particles.forEach(particle => {
                    ctx.save();
                    ctx.globalAlpha = particle.life;
                    ctx.fillStyle = particle.color;
                    ctx.beginPath();
                    ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.restore();
                });
            }
        }
        
        // 关卡定义
        const levels = [
            {
                id: 1,
                name: "新手村",
                description: "简单的开始，熟悉游戏操作",
                targetScore: 500,
                speed: 150,
                walls: [],
                specialRules: null,
                bgColor: "#1a1a2e",
                unlocked: true
            },
            {
                id: 2,
                name: "森林迷宫",
                description: "小心墙壁障碍物",
                targetScore: 800,
                speed: 130,
                walls: [
                    {x: 5, y: 5, width: 3, height: 1},
                    {x: 10, y: 8, width: 1, height: 4},
                    {x: 15, y: 3, width: 2, height: 6}
                ],
                specialRules: null,
                bgColor: "#0f3460"
            },
            {
                id: 3,
                name: "速度狂飙",
                description: "游戏速度越来越快",
                targetScore: 1000,
                speed: 100,
                walls: [],
                specialRules: "speedUp",
                bgColor: "#16213e"
            },
            {
                id: 4,
                name: "传送门",
                description: "神秘的空间传送",
                targetScore: 1200,
                speed: 140,
                walls: [],
                portals: [
                    {x1: 3, y1: 3, x2: 17, y2: 17},
                    {x1: 17, y1: 3, x2: 3, y2: 17}
                ],
                specialRules: "portals",
                bgColor: "#2d1b69"
            },
            {
                id: 5,
                name: "Boss关卡",
                description: "终极挑战，征服一切",
                targetScore: 2000,
                speed: 120,
                walls: [
                    {x: 0, y: 7, width: 8, height: 1},
                    {x: 12, y: 7, width: 8, height: 1},
                    {x: 7, y: 0, width: 1, height: 6},
                    {x: 7, y: 9, width: 1, height: 6}
                ],
                specialRules: "boss",
                bgColor: "#4a0e2d"
            }
        ];
        
        // 道具定义
        const powerups = {
            apple: { symbol: '🍎', points: 10, effect: null, rarity: 0.7 },
            grape: { symbol: '🍇', points: 20, effect: 'speed', rarity: 0.15 },
            strawberry: { symbol: '🍓', points: 30, effect: 'slow', rarity: 0.1 },
            diamond: { symbol: '💎', points: 50, effect: null, rarity: 0.05 },
            lightning: { symbol: '⚡', points: 0, effect: 'wallpass', rarity: 0.03 },
            shield: { symbol: '🛡️', points: 0, effect: 'invincible', rarity: 0.03 },
            star: { symbol: '🌟', points: 0, effect: 'doublescore', rarity: 0.02 },
            ice: { symbol: '❄️', points: 0, effect: 'timeslow', rarity: 0.02 },
            skull: { symbol: '💀', points: -50, effect: 'damage', rarity: 0.02 },
            blackhole: { symbol: '🕳️', points: 0, effect: 'teleport', rarity: 0.01 }
        };
        
        // 主游戏类
        class SnakeGame {
            constructor() {
                this.gridSize = 20;
                this.updateGrid();
                this.reset();
                this.particles = new ParticleSystem();
                this.activePowerups = new Map();
                this.gameStartTime = Date.now();
                this.maxLength = 3;
            }
            
            updateGrid() {
                if (canvas.width > 0 && canvas.height > 0) {
                    this.cols = Math.floor(canvas.width / this.gridSize);
                    this.rows = Math.floor(canvas.height / this.gridSize);
                }
            }
            
            reset() {
                this.snake = [
                    {x: Math.floor(this.cols/2), y: Math.floor(this.rows/2)}
                ];
                this.direction = {x: 1, y: 0};
                this.nextDirection = {x: 1, y: 0};
                this.food = null;
                this.score = 0;
                this.level = selectedLevelId || 1;
                this.lives = 3;
                this.speed = 150;
                this.gameStartTime = Date.now();
                this.maxLength = 3;
                this.activePowerups.clear();
                this.generateFood();
                this.updateUI();
            }
            
            generateFood() {
                if (!this.cols || !this.rows) return;
                
                let attempts = 0;
                do {
                    this.food = {
                        x: Math.floor(Math.random() * this.cols),
                        y: Math.floor(Math.random() * this.rows),
                        type: this.getRandomFoodType(),
                        animation: 0
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
                // 检查蛇身
                for (const segment of this.snake) {
                    if (segment.x === x && segment.y === y) return true;
                }
                
                // 检查墙壁
                const currentLevel = levels.find(l => l.id === this.level);
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
                
                // 处理边界
                if (!this.activePowerups.has('wallpass')) {
                    if (head.x < 0 || head.x >= this.cols || head.y < 0 || head.y >= this.rows) {
                        this.handleCollision();
                        return;
                    }
                } else {
                    head.x = (head.x + this.cols) % this.cols;
                    head.y = (head.y + this.rows) % this.rows;
                }
                
                // 检查碰撞
                if (this.checkCollision(head)) {
                    this.handleCollision();
                    return;
                }
                
                this.snake.unshift(head);
                
                // 检查食物
                if (this.food && head.x === this.food.x && head.y === this.food.y) {
                    this.eatFood();
                } else {
                    this.snake.pop();
                }
                
                this.maxLength = Math.max(this.maxLength, this.snake.length);
            }
            
            checkCollision(head) {
                // 检查自身碰撞
                if (!this.activePowerups.has('invincible')) {
                    for (let i = 1; i < this.snake.length; i++) {
                        if (this.snake[i].x === head.x && this.snake[i].y === head.y) {
                            return true;
                        }
                    }
                }
                
                // 检查墙壁碰撞
                if (!this.activePowerups.has('wallpass')) {
                    const currentLevel = levels.find(l => l.id === this.level);
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
                
                this.lives--;
                SoundGenerator.death();
                
                // 创建死亡粒子效果
                const head = this.snake[0];
                this.particles.createExplosion(
                    head.x * this.gridSize + this.gridSize/2,
                    head.y * this.gridSize + this.gridSize/2,
                    '#FF6B6B',
                    15
                );
                
                if (this.lives <= 0) {
                    this.gameOver();
                } else {
                    // 重置蛇的位置
                    this.snake = [
                        {x: Math.floor(this.cols/2), y: Math.floor(this.rows/2)}
                    ];
                    this.direction = {x: 1, y: 0};
                    this.nextDirection = {x: 1, y: 0};
                    this.activePowerups.clear();
                }
                
                this.updateUI();
            }
            
            eatFood() {
                if (!this.food) return;
                
                const foodType = powerups[this.food.type];
                const multiplier = this.activePowerups.has('doublescore') ? 2 : 1;
                this.score += Math.max(0, foodType.points * multiplier);
                
                SoundGenerator.eat();
                
                // 创建吃食物粒子效果
                this.particles.createExplosion(
                    this.food.x * this.gridSize + this.gridSize/2,
                    this.food.y * this.gridSize + this.gridSize/2,
                    '#FFD700',
                    8
                );
                
                // 处理特殊效果
                this.handlePowerupEffect(this.food.type);
                
                this.generateFood();
                this.updateUI();
                
                // 检查关卡完成条件
                const currentLevel = levels.find(l => l.id === this.level);
                if (this.score >= currentLevel.targetScore) {
                    this.levelComplete();
                }
            }
            
            handlePowerupEffect(type) {
                const effect = powerups[type].effect;
                if (!effect) return;
                
                switch (effect) {
                    case 'speed':
                        this.speed = Math.max(80, this.speed - 20);
                        this.showPowerupIndicator('⚡', '#FFD700');
                        setTimeout(() => {
                            this.speed = Math.min(200, this.speed + 20);
                        }, 5000);
                        break;
                        
                    case 'slow':
                        this.speed = Math.min(250, this.speed + 30);
                        this.showPowerupIndicator('🐌', '#87CEEB');
                        setTimeout(() => {
                            this.speed = Math.max(80, this.speed - 30);
                        }, 5000);
                        break;
                        
                    case 'wallpass':
                        this.activePowerups.set('wallpass', Date.now() + 10000);
                        this.showPowerupIndicator('🌟', '#00BFFF');
                        SoundGenerator.powerup();
                        break;
                        
                    case 'invincible':
                        this.activePowerups.set('invincible', Date.now() + 5000);
                        this.showPowerupIndicator('🛡️', '#4CAF50');
                        SoundGenerator.powerup();
                        break;
                        
                    case 'doublescore':
                        this.activePowerups.set('doublescore', Date.now() + 15000);
                        this.showPowerupIndicator('⭐', '#FFD700');
                        SoundGenerator.powerup();
                        break;
                        
                    case 'timeslow':
                        this.activePowerups.set('timeslow', Date.now() + 8000);
                        this.showPowerupIndicator('❄️', '#87CEEB');
                        SoundGenerator.powerup();
                        break;
                        
                    case 'damage':
                        this.lives = Math.max(0, this.lives - 1);
                        this.particles.createExplosion(
                            this.snake[0].x * this.gridSize + this.gridSize/2,
                            this.snake[0].y * this.gridSize + this.gridSize/2,
                            '#FF0000',
                            12
                        );
                        SoundGenerator.warning();
                        if (this.lives <= 0) this.gameOver();
                        break;
                        
                    case 'teleport':
                        const head = this.snake[0];
                        let newX, newY;
                        do {
                            newX = Math.floor(Math.random() * this.cols);
                            newY = Math.floor(Math.random() * this.rows);
                        } while (this.isPositionOccupied(newX, newY));
                        
                        this.particles.createExplosion(
                            head.x * this.gridSize + this.gridSize/2,
                            head.y * this.gridSize + this.gridSize/2,
                            '#800080',
                            10
                        );
                        
                        this.snake[0] = {x: newX, y: newY};
                        
                        this.particles.createExplosion(
                            newX * this.gridSize + this.gridSize/2,
                            newY * this.gridSize + this.gridSize/2,
                            '#800080',
                            10
                        );
                        break;
                }
            }
            
            showPowerupIndicator(emoji, color) {
                const indicator = document.createElement('div');
                indicator.className = 'powerup-indicator';
                indicator.textContent = emoji;
                indicator.style.background = `linear-gradient(145deg, ${color}44, ${color}22)`;
                indicator.style.borderColor = color;
                
                document.getElementById('powerupsUI').appendChild(indicator);
                
                setTimeout(() => {
                    if (indicator.parentNode) {
                        indicator.parentNode.removeChild(indicator);
                    }
                }, 5000);
            }
            
            updatePowerups() {
                const now = Date.now();
                for (const [effect, expireTime] of this.activePowerups.entries()) {
                    if (now > expireTime) {
                        this.activePowerups.delete(effect);
                    }
                }
            }
            
            changeDirection(newDirection) {
                // 防止反向移动
                if (this.direction.x !== -newDirection.x || this.direction.y !== -newDirection.y) {
                    this.nextDirection = newDirection;
                }
            }
            
            updateUI() {
                document.querySelector('#score .ui-value').textContent = this.score;
                document.querySelector('#level .ui-value').textContent = this.level;
                document.querySelector('#lives .ui-value').textContent = this.lives;
            }
            
            levelComplete() {
                gameState = 'levelComplete';
                SoundGenerator.levelComplete();
                
                const survivalTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                const timeBonus = Math.max(0, 300 - survivalTime) * 5;
                const perfectBonus = this.lives === 3 ? 200 : 0;
                
                document.getElementById('levelScore').textContent = this.score;
                document.getElementById('timeBonus').textContent = timeBonus;
                document.getElementById('perfectBonus').textContent = perfectBonus;
                
                this.score += timeBonus + perfectBonus;
                
                // 解锁下一关
                if (this.level < levels.length) {
                    levels[this.level].unlocked = true;
                    localStorage.setItem('snakeGameProgress', JSON.stringify(levels.map(l => ({id: l.id, unlocked: l.unlocked}))));
                }
                
                showScreen('levelCompleteScreen');
                
                // 显示成就
                if (perfectBonus > 0) {
                    this.showAchievement('🏆 完美通关！');
                }
                if (this.maxLength >= 20) {
                    this.showAchievement('🐍 超长蛇王！');
                }
            }
            
            gameOver() {
                gameState = 'gameOver';
                
                const survivalTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                
                document.getElementById('finalScore').textContent = this.score;
                document.getElementById('survivalTime').textContent = survivalTime;
                document.getElementById('maxLength').textContent = this.maxLength;
                
                showScreen('gameOverScreen');
                
                // 保存最高分
                const highScore = localStorage.getItem('snakeHighScore') || 0;
                if (this.score > highScore) {
                    localStorage.setItem('snakeHighScore', this.score);
                    this.showAchievement('🎉 新纪录！');
                }
            }
            
            showAchievement(text) {
                const achievement = document.createElement('div');
                achievement.className = 'achievement';
                achievement.textContent = text;
                document.getElementById('gameContainer').appendChild(achievement);
                
                setTimeout(() => {
                    if (achievement.parentNode) {
                        achievement.parentNode.removeChild(achievement);
                    }
                }, 3000);
            }
            
            draw() {
                if (!canvas.width || !canvas.height) return;
                
                // 清空画布
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                
                // 绘制背景
                const currentLevel = levels.find(l => l.id === this.level);
                if (currentLevel) {
                    const gradient = ctx.createRadialGradient(
                        canvas.width/2, canvas.height/2, 0,
                        canvas.width/2, canvas.height/2, Math.max(canvas.width, canvas.height)/2
                    );
                    gradient.addColorStop(0, currentLevel.bgColor + '44');
                    gradient.addColorStop(1, currentLevel.bgColor + '88');
                    ctx.fillStyle = gradient;
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                }
                
                // 绘制墙壁
                if (currentLevel && currentLevel.walls) {
                    ctx.fillStyle = '#8B4513';
                    currentLevel.walls.forEach(wall => {
                        ctx.fillRect(
                            wall.x * this.gridSize,
                            wall.y * this.gridSize,
                            wall.width * this.gridSize,
                            wall.height * this.gridSize
                        );
                        
                        // 添加阴影效果
                        ctx.fillStyle = 'rgba(0,0,0,0.3)';
                        ctx.fillRect(
                            wall.x * this.gridSize + 2,
                            wall.y * this.gridSize + 2,
                            wall.width * this.gridSize,
                            wall.height * this.gridSize
                        );
                        ctx.fillStyle = '#8B4513';
                    });
                }
                
                // 绘制传送门
                if (currentLevel && currentLevel.portals) {
                    currentLevel.portals.forEach((portal, index) => {
                        const colors = ['#FF00FF', '#00FFFF'];
                        ctx.save();
                        
                        // 绘制传送门动画效果
                        const time = Date.now() / 1000;
                        const pulse = Math.sin(time * 4) * 0.3 + 0.7;
                        
                        ctx.globalAlpha = pulse;
                        ctx.fillStyle = colors[index % 2];
                        
                        // 入口
                        ctx.fillRect(
                            portal.x1 * this.gridSize,
                            portal.y1 * this.gridSize,
                            this.gridSize,
                            this.gridSize
                        );
                        
                        // 出口
                        ctx.fillRect(
                            portal.x2 * this.gridSize,
                            portal.y2 * this.gridSize,
                            this.gridSize,
                            this.gridSize
                        );
                        
                        ctx.restore();
                    });
                }
                
                // 绘制食物
                if (this.food) {
                    const foodData = powerups[this.food.type];
                    const x = this.food.x * this.gridSize;
                    const y = this.food.y * this.gridSize;
                    
                    // 食物动画
                    this.food.animation += 0.1;
                    const scale = 1 + Math.sin(this.food.animation) * 0.1;
                    const rotation = this.food.animation * 0.1;
                    
                    ctx.save();
                    ctx.translate(x + this.gridSize/2, y + this.gridSize/2);
                    ctx.scale(scale, scale);
                    ctx.rotate(rotation);
                    
                    // 绘制光晕
                    const gradient = ctx.createRadialGradient(0, 0, 0, 0, 0, this.gridSize);
                    gradient.addColorStop(0, 'rgba(255,255,255,0.3)');
                    gradient.addColorStop(1, 'rgba(255,255,255,0)');
                    ctx.fillStyle = gradient;
                    ctx.fillRect(-this.gridSize/2, -this.gridSize/2, this.gridSize, this.gridSize);
                    
                    // 绘制食物emoji
                    ctx.font = `${this.gridSize * 0.8}px Arial`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(foodData.symbol, 0, 0);
                    
                    ctx.restore();
                }
                
                // 绘制蛇
                this.snake.forEach((segment, index) => {
                    const x = segment.x * this.gridSize;
                    const y = segment.y * this.gridSize;
                    
                    ctx.save();
                    
                    if (index === 0) {
                        // 蛇头
                        const gradient = ctx.createRadialGradient(
                            x + this.gridSize/2, y + this.gridSize/2, 0,
                            x + this.gridSize/2, y + this.gridSize/2, this.gridSize/2
                        );
                        
                        if (this.activePowerups.has('invincible')) {
                            gradient.addColorStop(0, '#FFD700');
                            gradient.addColorStop(1, '#FFA500');
                        } else {
                            gradient.addColorStop(0, '#32CD32');
                            gradient.addColorStop(1, '#228B22');
                        }
                        
                        ctx.fillStyle = gradient;
                        ctx.fillRect(x + 2, y + 2, this.gridSize - 4, this.gridSize - 4);
                        
                        // 蛇眼睛
                        ctx.fillStyle = 'white';
                        const eyeSize = 3;
                        ctx.fillRect(x + 6, y + 4, eyeSize, eyeSize);
                        ctx.fillRect(x + this.gridSize - 9, y + 4, eyeSize, eyeSize);
                        
                        ctx.fillStyle = 'black';
                        ctx.fillRect(x + 7, y + 5, 1, 1);
                        ctx.fillRect(x + this.gridSize - 8, y + 5, 1, 1);
                        
                    } else {
                        // 蛇身
                        const alpha = 1 - (index / this.snake.length) * 0.3;
                        ctx.globalAlpha = alpha;
                        
                        const gradient = ctx.createLinearGradient(x, y, x + this.gridSize, y + this.gridSize);
                        gradient.addColorStop(0, '#90EE90');
                        gradient.addColorStop(1, '#32CD32');
                        
                        ctx.fillStyle = gradient;
                        ctx.fillRect(x + 1, y + 1, this.gridSize - 2, this.gridSize - 2);
                        
                        // 蛇身纹理
                        ctx.fillStyle = 'rgba(255,255,255,0.1)';
                        ctx.fillRect(x + 3, y + 3, this.gridSize - 6, 2);
                    }
                    
                    ctx.restore();
                });
                
                // 绘制粒子效果
                this.particles.draw(ctx);
                
                // 绘制道具效果指示
                if (this.activePowerups.has('invincible')) {
                    ctx.save();
                    ctx.globalAlpha = 0.3;
                    ctx.fillStyle = '#FFD700';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                    ctx.restore();
                }
                
                if (this.activePowerups.has('timeslow')) {
                    ctx.save();
                    ctx.globalAlpha = 0.2;
                    ctx.fillStyle = '#87CEEB';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                    ctx.restore();
                }
            }
            
            update() {
                this.updatePowerups();
                this.particles.update();
                
                // 根据道具调整速度
                let currentSpeed = this.speed;
                if (this.activePowerups.has('timeslow')) {
                    currentSpeed *= 2;
                }
                
                // 特殊关卡规则
                const currentLevel = levels.find(l => l.id === this.level);
                if (currentLevel && currentLevel.specialRules === 'speedUp') {
                    currentSpeed = Math.max(80, this.speed - Math.floor(this.score / 100) * 10);
                }
                
                return currentSpeed;
            }
        }
        
        // 游戏实例
        let game;
        let gameLoop;
        let lastTime = 0;
        
        function initGame() {
            game = new SnakeGame();
        }
        
        function startGameLoop() {
            if (gameLoop) clearInterval(gameLoop);
            
            function loop() {
                const now = Date.now();
                const deltaTime = now - lastTime;
                const speed = game.update();
                
                if (deltaTime >= speed) {
                    if (gameState === 'playing') {
                        game.move();
                    }
                    lastTime = now;
                }
                
                game.draw();
                
                if (gameState === 'playing' || gameState === 'paused') {
                    requestAnimationFrame(loop);
                }
            }
            
            requestAnimationFrame(loop);
        }
        
        // 屏幕管理
        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(screen => {
                screen.style.display = 'none';
            });
            document.getElementById(screenId).style.display = 'flex';
        }
        
        function showStartScreen() {
            gameState = 'menu';
            selectedLevelId = null;
            showScreen('startScreen');
        }
        
        function showLevelSelect() {
            generateLevelButtons();
            showScreen('levelSelectScreen');
            
            // 隐藏开始按钮和重置关卡信息
            document.getElementById('startLevelBtn').style.display = 'none';
            document.getElementById('levelInfo').innerHTML = `
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            `;
        }
        
        function showInstructions() {
            showScreen('instructionsScreen');
        }
        
        function selectLevel(levelId) {
            const level = levels.find(l => l.id === levelId);
            if (!level || !level.unlocked) return;
            
            selectedLevelId = levelId;
            
            // 更新选中状态
            document.querySelectorAll('.level-btn').forEach(btn => {
                btn.classList.remove('selected');
            });
            document.querySelector(`[data-level="${levelId}"]`).classList.add('selected');
            
            // 显示关卡信息
            document.getElementById('levelInfo').innerHTML = `
                <h3>${level.name}</h3>
                <p>${level.description}</p>
                <p style="margin-top: 10px;">目标分数: <span style="color: #FFD700;">${level.targetScore}</span></p>
            `;
            
            // 显示开始按钮
            document.getElementById('startLevelBtn').style.display = 'inline-block';
        }
        
        function startSelectedLevel() {
            if (!selectedLevelId) return;
            
            initAudio();
            
            if (!game) {
                initGame();
            }
            
            const level = levels.find(l => l.id === selectedLevelId);
            game.level = selectedLevelId;
            game.speed = level.speed;
            game.reset();
            
            gameState = 'playing';
            document.querySelectorAll('.screen').forEach(screen => {
                screen.style.display = 'none';
            });
            
            startGameLoop();
        }
        
        function generateLevelButtons() {
            const container = document.getElementById('levelSelect');
            container.innerHTML = '';
            
            levels.forEach((level, index) => {
                const button = document.createElement('button');
                button.className = `level-btn ${level.unlocked ? 'unlocked' : 'locked'}`;
                button.setAttribute('data-level', level.id);
                button.innerHTML = `
                    <div style="font-size: 18px; margin-bottom: 5px;">${level.id}</div>
                    <div style="font-size: 12px; opacity: 0.8;">${level.name}</div>
                `;
                
                if (level.unlocked) {
                    button.onclick = () => selectLevel(level.id);
                }
                
                // 检查是否完成
                const progress = JSON.parse(localStorage.getItem('snakeGameProgress') || '[]');
                const levelProgress = progress.find(p => p.id === level.id);
                if (levelProgress && levelProgress.completed) {
                    button.classList.add('completed');
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
            document.querySelectorAll('.screen').forEach(screen => {
                screen.style.display = 'none';
            });
            startGameLoop();
        }
        
        function restartLevel() {
            if (!game) return;
            
            game.reset();
            gameState = 'playing';
            document.querySelectorAll('.screen').forEach(screen => {
                screen.style.display = 'none';
            });
            startGameLoop();
        }
        
        function quitToMenu() {
            gameState = 'menu';
            showStartScreen();
        }
        
        function nextLevel() {
            if (game.level < levels.length) {
                selectedLevelId = game.level + 1;
                startSelectedLevel();
            } else {
                showStartScreen();
            }
        }
        
        // 控制事件
        document.getElementById('upBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (game) game.changeDirection({x: 0, y: -1});
        });
        
        document.getElementById('downBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (game) game.changeDirection({x: 0, y: 1});
        });
        
        document.getElementById('leftBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (game) game.changeDirection({x: -1, y: 0});
        });
        
        document.getElementById('rightBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (game) game.changeDirection({x: 1, y: 0});
        });
        
        document.getElementById('pauseBtn').addEventListener('touchstart', (e) => {
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
        
        // 触摸滑动控制
        let touchStartX = 0;
        let touchStartY = 0;
        
        canvas.addEventListener('touchstart', (e) => {
            e.preventDefault();
            touchStartX = e.touches[0].clientX;
            touchStartY = e.touches[0].clientY;
        }, { passive: false });
        
        canvas.addEventListener('touchmove', (e) => {
            e.preventDefault();
        }, { passive: false });
        
        canvas.addEventListener('touchend', (e) => {
            e.preventDefault();
            
            if (gameState !== 'playing' || !game) return;
            
            const touchEndX = e.changedTouches[0].clientX;
            const touchEndY = e.changedTouches[0].clientY;
            const deltaX = touchEndX - touchStartX;
            const deltaY = touchEndY - touchStartY;
            const minSwipeDistance = 30;
            
            if (Math.abs(deltaX) > Math.abs(deltaY)) {
                // 水平滑动
                if (Math.abs(deltaX) > minSwipeDistance) {
                    if (deltaX > 0) {
                        game.changeDirection({x: 1, y: 0}); // 右
                    } else {
                        game.changeDirection({x: -1, y: 0}); // 左
                    }
                }
            } else {
                // 垂直滑动
                if (Math.abs(deltaY) > minSwipeDistance) {
                    if (deltaY > 0) {
                        game.changeDirection({x: 0, y: 1}); // 下
                    } else {
                        game.changeDirection({x: 0, y: -1}); // 上
                    }
                }
            }
        }, { passive: false });
        
        // 防止页面滚动
        document.addEventListener('touchmove', (e) => {
            e.preventDefault();
        }, { passive: false });
        
        // 窗口大小改变处理
        window.addEventListener('resize', () => {
            resizeCanvas();
            if (gameState === 'playing' && game) {
                game.draw();
            }
        });
        
        // 页面可见性改变处理
        document.addEventListener('visibilitychange', () => {
            if (document.hidden && gameState === 'playing') {
                pauseGame();
            }
        });
        
        // 加载游戏进度
        function loadProgress() {
            const progress = JSON.parse(localStorage.getItem('snakeGameProgress') || '[]');
            progress.forEach(p => {
                const level = levels.find(l => l.id === p.id);
                if (level) {
                    level.unlocked = p.unlocked;
                }
            });
        }
        
        // 初始化游戏
        window.addEventListener('load', () => {
            resizeCanvas();
            loadProgress();
            
            // 显示开始屏幕
            showStartScreen();
            
            // 预加载音频
            setTimeout(initAudio, 1000);
        });
        
        // PWA支持
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('data:text/javascript;base64,');
        }
    </script>
</body>
</html>
