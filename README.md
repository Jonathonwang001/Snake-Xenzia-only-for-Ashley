# Snake-Xenzia-only-for-Ashley
Creating an interesting game only for my love, Ashley. Hope her happy everyday!

<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, maximum-scale=1.0, minimum-scale=1.0">
    <title>🐍 贪食蛇大冒险</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
        }
        
        html, body {
            height: 100%;
            overflow: hidden;
            font-family: 'Arial', sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
        }
        
        #gameContainer {
            position: relative;
            width: 100vw;
            height: 100vh;
            background: linear-gradient(45deg, #1a1a2e, #16213e, #0f3460);
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }
        
        #gameCanvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: block;
            touch-action: none;
            background: transparent;
        }
        
        /* 游戏UI - 顶部信息栏 */
        #gameUI {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            height: 40px;
            background: linear-gradient(180deg, rgba(0,0,0,0.9) 0%, rgba(0,0,0,0.6) 70%, transparent 100%);
            display: none;
            justify-content: space-around;
            align-items: center;
            padding: 5px 10px;
            z-index: 100;
            color: white;
            font-size: 12px;
            font-weight: bold;
        }
        
        #gameUI.active { 
            display: flex; 
        }
        
        .ui-item {
            text-align: center;
            flex: 1;
        }
        
        .ui-label {
            font-size: 9px;
            opacity: 0.8;
        }
        
        .ui-value {
            font-size: 12px;
            margin-top: 2px;
            font-weight: bold;
        }
        
        #score .ui-value { color: #FFD700; }
        #level .ui-value { color: #00BFFF; }
        #lives .ui-value { color: #FF6B6B; }
        
        /* 控制按钮 - 移动到底部 */
        #controls {
            position: fixed;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            display: none;
            flex-direction: row;
            gap: 12px;
            z-index: 100;
        }
        
        #controls.active { 
            display: flex; 
        }
        
        .control-btn {
            background: rgba(0,0,0,0.8);
            border: 1px solid rgba(255,255,255,0.3);
            border-radius: 8px;
            color: white;
            font-size: 11px;
            padding: 6px 10px;
            cursor: pointer;
            transition: all 0.2s ease;
            backdrop-filter: blur(10px);
            box-shadow: 0 2px 8px rgba(0,0,0,0.5);
            font-weight: bold;
            min-width: 50px;
            text-align: center;
        }
        
        .control-btn:active {
            transform: scale(0.95);
            background: rgba(0,0,0,0.9);
        }
        
        .control-btn.pause {
            background: rgba(255,107,107,0.8);
        }
        
        /* 状态效果指示器 - 右上角 */
        #effectIndicators {
            position: fixed;
            top: 45px;
            right: 5px;
            display: none;
            flex-direction: column;
            gap: 3px;
            z-index: 150;
            max-width: 120px;
        }
        
        #effectIndicators.active {
            display: flex;
        }
        
        .effect-indicator {
            background: rgba(0,0,0,0.8);
            color: white;
            padding: 3px 6px;
            border-radius: 10px;
            font-size: 9px;
            display: flex;
            align-items: center;
            gap: 3px;
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255,255,255,0.2);
        }
        
        /* 屏幕通用样式 */
        .screen {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, rgba(26,26,46,0.95), rgba(22,33,62,0.95));
            backdrop-filter: blur(10px);
            display: none;
            flex-direction: column;
            justify-content: flex-start;
            align-items: center;
            z-index: 200;
            padding: 10px;
            text-align: center;
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
        }
        
        .screen.active { 
            display: flex; 
        }
        
        .screen h1 {
            font-size: clamp(1.2rem, 4vw, 1.8rem);
            font-weight: bold;
            margin: 10px 0 15px 0;
            background: linear-gradient(45deg, #FFD700, #FFA500, #FF6B6B);
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
            text-align: center;
        }
        
        .screen p {
            font-size: clamp(11px, 2.5vw, 13px);
            line-height: 1.4;
            margin-bottom: 10px;
            opacity: 0.9;
        }
        
        /* 按钮样式 */
        .btn {
            background: linear-gradient(145deg, #4CAF50, #45a049);
            color: white;
            border: none;
            padding: clamp(8px, 2vw, 10px) clamp(16px, 4vw, 20px);
            font-size: clamp(11px, 2.5vw, 13px);
            font-weight: bold;
            border-radius: 15px;
            cursor: pointer;
            margin: 4px;
            transition: all 0.3s ease;
            min-width: clamp(80px, 20vw, 120px);
        }
        
        .btn:active {
            transform: scale(0.98);
        }
        
        .btn.primary {
            background: linear-gradient(145deg, #FFD700, #FFA500);
            color: #1a1a1a;
        }
        
        .btn.secondary {
            background: linear-gradient(145deg, #6C7CE7, #5A6ACF);
        }
        
        .btn.danger {
            background: linear-gradient(145deg, #FF6B6B, #E55555);
        }
        
        .btn.practice {
            background: linear-gradient(145deg, #9C27B0, #7B1FA2);
        }
        
        /* 关卡选择网格 */
        #levelSelect {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 6px;
            max-width: min(90vw, 300px);
            margin: 10px 0;
            width: 100%;
        }
        
        .level-btn {
            aspect-ratio: 1;
            background: rgba(255,255,255,0.1);
            border: 2px solid rgba(255,255,255,0.2);
            border-radius: 10px;
            color: white;
            font-size: clamp(9px, 2.2vw, 11px);
            font-weight: bold;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.3s ease;
            backdrop-filter: blur(5px);
        }
        
        .level-btn:hover,
        .level-btn:active {
            border-color: #FFD700;
            transform: scale(1.05);
        }
        
        .level-btn.selected {
            border-color: #FFD700;
            background: rgba(255,215,0,0.2);
        }
        
        .level-btn .level-number {
            font-size: clamp(12px, 3vw, 14px);
            margin-bottom: 2px;
        }
        
        .level-btn .level-name {
            font-size: clamp(7px, 1.8vw, 9px);
            opacity: 0.8;
        }
        
        /* 关卡信息和按钮容器 */
        #levelInfoContainer {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            gap: 10px;
        }
        
        #levelInfo {
            text-align: center;
            padding: 12px;
            background: rgba(255,255,255,0.1);
            border-radius: 10px;
            min-height: 60px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255,255,255,0.2);
            width: min(90vw, 300px);
        }
        
        #levelInfo h3 {
            color: #FFD700;
            margin-bottom: 6px;
            font-size: clamp(12px, 3vw, 14px);
        }
        
        #levelInfo p {
            font-size: clamp(10px, 2.5vw, 12px);
            margin: 2px 0;
        }
        
        /* 关卡选择按钮容器 */
        #levelSelectButtons {
            display: flex;
            flex-direction: row;
            gap: 8px;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 5px;
        }
        
        /* 滚动内容 */
        .scrollable-content {
            max-height: 35vh;
            overflow-y: auto;
            padding: 12px;
            margin: 8px 0;
            border-radius: 10px;
            background: rgba(255,255,255,0.1);
            backdrop-filter: blur(5px);
            text-align: left;
            line-height: 1.4;
            width: min(90vw, 400px);
            -webkit-overflow-scrolling: touch;
            border: 1px solid rgba(255,255,255,0.2);
        }
        
        .scrollable-content::-webkit-scrollbar {
            width: 3px;
        }
        
        .scrollable-content::-webkit-scrollbar-track {
            background: rgba(255,255,255,0.1);
        }
        
        .scrollable-content::-webkit-scrollbar-thumb {
            background: rgba(255,255,255,0.5);
            border-radius: 2px;
        }
        
        .scrollable-content h3 {
            color: #FFD700;
            margin: 10px 0 6px 0;
            font-size: clamp(12px, 3vw, 14px);
        }
        
        .scrollable-content p {
            margin-bottom: 6px;
            font-size: clamp(10px, 2.5vw, 12px);
            line-height: 1.3;
        }
        
        /* 响应式调整 */
        @media (max-height: 600px) {
            .screen h1 {
                margin: 5px 0 10px 0;
            }
            
            .scrollable-content {
                max-height: 30vh;
                padding: 10px;
            }
            
            #levelInfo {
                min-height: 50px;
                padding: 10px;
            }
            
            #gameUI {
                height: 35px;
            }
            
            #effectIndicators {
                top: 40px;
            }
        }
        
        @media (max-height: 500px) {
            .screen h1 {
                font-size: clamp(1rem, 3vh, 1.4rem);
                margin: 3px 0 8px 0;
            }
            
            .btn {
                padding: clamp(6px, 1.5vw, 8px) clamp(12px, 3vw, 16px);
                margin: 3px;
            }
        }
        
        @media (max-width: 360px) {
            #levelSelect {
                gap: 4px;
            }
            
            .level-btn {
                border-radius: 8px;
            }
            
            #controls {
                gap: 8px;
            }
        }
        
        /* 横屏优化 */
        @media (orientation: landscape) and (max-height: 500px) {
            .screen {
                padding: 8px;
            }
            
            .screen h1 {
                font-size: clamp(0.9rem, 2.5vh, 1.2rem);
                margin: 3px 0 6px 0;
            }
            
            .scrollable-content {
                max-height: 22vh;
            }
            
            #levelSelect {
                grid-template-columns: repeat(4, 1fr);
                gap: 4px;
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
        
        <!-- 状态效果指示器 -->
        <div id="effectIndicators"></div>
        
        <!-- 游戏画布 -->
        <canvas id="gameCanvas"></canvas>
        
        <!-- 控制按钮 - 移动到底部 -->
        <div id="controls">
            <button class="control-btn pause" id="pauseBtn">⏸ 暂停</button>
            <button class="control-btn" id="menuBtn">📋 菜单</button>
        </div>
        
        <!-- 开始屏幕 -->
        <div id="startScreen" class="screen active">
            <h1>🐍 贪食蛇大冒险</h1>
            <p>准备好挑战史上最刺激的贪食蛇游戏了吗？</p>
            <p>12个独特关卡等你征服！</p>
            <button class="btn practice" id="practiceBtn">🏋️ 练习场</button>
            <button class="btn primary" id="adventureBtn">🚀 开始冒险</button>
            <button class="btn secondary" id="instructionsBtn">📖 游戏说明</button>
        </div>
        
        <!-- 关卡选择屏幕 -->
        <div id="levelSelectScreen" class="screen">
            <h1>选择关卡</h1>
            <div id="levelSelect"></div>
            <div id="levelInfoContainer">
                <div id="levelInfo">
                    <h3>请选择一个关卡</h3>
                    <p>点击上方关卡按钮查看详情</p>
                </div>
                <div id="levelSelectButtons">
                    <button class="btn primary" id="startLevelBtn" style="display: none;">开始游戏</button>
                    <button class="btn secondary" id="backToMenuBtn">返回主菜单</button>
                </div>
            </div>
        </div>
        
        <!-- 游戏说明屏幕 -->
        <div id="instructionsScreen" class="screen">
            <h1>📖 游戏说明</h1>
            <div class="scrollable-content">
                <h3>🎮 控制方式</h3>
                <p>• 在游戏区域滑动控制蛇的移动方向</p>
                <p>• 电脑玩家可使用 WASD 或方向键</p>
                <p>• 点击暂停按钮暂停游戏</p>
                
                <h3>🍎 普通食物</h3>
                <p>• 🍎 红苹果 (+10分) - 最常见的食物</p>
                <p>• 🍇 紫葡萄 (+20分) - 临时提升移动速度</p>
                <p>• 🍓 草莓 (+30分) - 临时减慢移动速度</p>
                <p>• 🍌 香蕉 (+15分) - 让蛇身增长2格</p>
                
                <h3>💎 增益道具 (可选择吃取)</h3>
                <p>• 💎 钻石 (+50分) - 高价值道具</p>
                <p>• ⚡ 闪电 (12秒穿墙能力) - 可以穿过墙壁</p>
                <p>• 🛡️ 盾牌 (8秒无敌状态) - 碰到障碍不死</p>
                <p>• 🌟 星星 (20秒双倍得分) - 所有分数翻倍</p>
                <p>• ❄️ 冰块 (10秒时间缓慢) - 游戏速度减半</p>
                <p>• 🔥 火焰 (8秒超高速) - 移动速度大幅提升</p>
                <p>• 🍀 四叶草 (随机好效果) - 触发随机正面效果</p>
                
                <h3>💀 危险障碍 (必须避开)</h3>
                <p>• 💀 骷髅 (-30分并减少生命) - 避免碰触</p>
                <p>• 🕳️ 黑洞 (传送到随机位置) - 随机传送</p>
                <p>• 🌪️ 龙卷风 (8秒反向操作) - 控制方向相反</p>
                
                <h3>🏋️ 练习场模式</h3>
                <p>• 无限生命，轻松练习操作</p>
                <p>• 所有道具都可能出现</p>
                <p>• 没有目标分数限制</p>
                <p>• 熟悉游戏机制的最佳选择</p>
                
                <h3>🏆 冒险模式</h3>
                <p>• 12个独特设计的关卡，全部开放选择</p>
                <p>• 每个关卡都有目标分数</p>
                <p>• 生命有限，需要谨慎操作</p>
                <p>• 不同关卡有不同的地形和挑战</p>
            </div>
            <button class="btn secondary" id="backFromInstructionsBtn">返回主菜单</button>
        </div>
        
        <!-- 暂停屏幕 -->
        <div id="pauseScreen" class="screen">
            <h1>⏸️ 游戏暂停</h1>
            <p>休息一下，准备继续挑战！</p>
            <button class="btn primary" id="resumeBtn">继续游戏</button>
            <button class="btn secondary" id="restartBtn">重新开始</button>
            <button class="btn danger" id="quitBtn">退出到菜单</button>
        </div>
        
        <!-- 游戏结束屏幕 -->
        <div id="gameOverScreen" class="screen">
            <h1>💀 游戏结束</h1>
            <div style="margin: 12px 0;">
                <p>最终分数: <span id="finalScore" style="color: #FFD700; font-weight: bold;">0</span></p>
                <p>存活时间: <span id="survivalTime" style="color: #87CEEB; font-weight: bold;">0</span>秒</p>
                <p id="gameOverReason" style="color: #FF6B6B; margin-top: 6px;"></p>
            </div>
            <button class="btn primary" id="restartGameBtn">重新挑战</button>
            <button class="btn secondary" id="selectLevelBtn">选择关卡</button>
            <button class="btn danger" id="returnMenuBtn">返回主菜单</button>
        </div>
        
        <!-- 关卡完成屏幕 -->
        <div id="levelCompleteScreen" class="screen">
            <h1>🎉 关卡完成!</h1>
            <div style="margin: 12px 0;">
                <p>获得分数: <span id="levelScore" style="color: #FFD700; font-weight: bold;">0</span></p>
                <p>完成时间: <span id="completionTime" style="color: #87CEEB; font-weight: bold;">0</span>秒</p>
                <p id="levelCompleteBonus" style="color: #98FB98; margin-top: 6px;"></p>
            </div>
            <button class="btn primary" id="nextLevelBtn">下一关</button>
            <button class="btn secondary" id="chooseLevelBtn">选择关卡</button>
            <button class="btn practice" id="backToMenuFromCompleteBtn">返回主菜单</button>
        </div>
    </div>

    <script>
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
                description: "无限生命，熟悉所有道具效果",
                targetScore: 0,
                speed: 150,
                walls: [],
                unlocked: true,
                isPractice: true,
                emoji: "🏋️"
            },
            {
                id: 1,
                name: "新手村",
                description: "简单的开始，熟悉基本操作",
                targetScore: 300,
                speed: 180,
                walls: [],
                unlocked: true,
                emoji: "🌱"
            },
            {
                id: 2,
                name: "森林迷宫",
                description: "小心墙壁障碍物",
                targetScore: 500,
                speed: 160,
                walls: [
                    {x: 5, y: 5, width: 3, height: 1},
                    {x: 10, y: 8, width: 1, height: 4},
                    {x: 15, y: 3, width: 2, height: 2}
                ],
                unlocked: true,
                emoji: "🌲"
            },
            {
                id: 3,
                name: "速度狂飙",
                description: "游戏速度逐渐加快，考验反应",
                targetScore: 800,
                speed: 140,
                speedIncrease: true,
                walls: [],
                unlocked: true,
                emoji: "🏃"
            },
            {
                id: 4,
                name: "十字路口",
                description: "十字形障碍，规划路线",
                targetScore: 700,
                speed: 150,
                walls: [
                    {x: 9, y: 0, width: 2, height: 8},
                    {x: 9, y: 12, width: 2, height: 8},
                    {x: 0, y: 9, width: 8, height: 2},
                    {x: 12, y: 9, width: 8, height: 2}
                ],
                unlocked: true,
                emoji: "➕"
            },
            {
                id: 5,
                name: "围城之战",
                description: "四面围墙，空间有限",
                targetScore: 600,
                speed: 160,
                walls: [
                    {x: 2, y: 2, width: 16, height: 1},
                    {x: 2, y: 17, width: 16, height: 1},
                    {x: 2, y: 2, width: 1, height: 16},
                    {x: 17, y: 2, width: 1, height: 16}
                ],
                unlocked: true,
                emoji: "🏰"
            },
            {
                id: 6,
                name: "螺旋通道",
                description: "螺旋形迷宫，考验走位",
                targetScore: 900,
                speed: 140,
                walls: [
                    {x: 3, y: 3, width: 14, height: 1},
                    {x: 16, y: 3, width: 1, height: 10},
                    {x: 6, y: 12, width: 11, height: 1},
                    {x: 6, y: 6, width: 1, height: 7},
                    {x: 6, y: 6, width: 8, height: 1},
                    {x: 13, y: 6, width: 1, height: 4}
                ],
                unlocked: true,
                emoji: "🌀"
            },
            {
                id: 7,
                name: "双龙戏珠",
                description: "双通道设计，选择路线",
                targetScore: 800,
                speed: 155,
                walls: [
                    {x: 9, y: 2, width: 2, height: 6},
                    {x: 9, y: 12, width: 2, height: 6},
                    {x: 4, y: 8, width: 4, height: 1},
                    {x: 12, y: 10, width: 4, height: 1}
                ],
                unlocked: true,
                emoji: "🐉"
            },
            {
                id: 8,
                name: "星空漫步",
                description: "星形图案障碍",
                targetScore: 1000,
                speed: 145,
                walls: [
                    {x: 10, y: 4, width: 1, height: 4},
                    {x: 10, y: 12, width: 1, height: 4},
                    {x: 6, y: 8, width: 4, height: 1},
                    {x: 11, y: 8, width: 4, height: 1},
                    {x: 7, y: 5, width: 2, height: 1},
                    {x: 12, y: 5, width: 2, height: 1},
                    {x: 7, y: 11, width: 2, height: 1},
                    {x: 12, y: 11, width: 2, height: 1}
                ],
                unlocked: true,
                emoji: "⭐"
            },
            {
                id: 9,
                name: "迷宫探险",
                description: "复杂迷宫，考验耐心",
                targetScore: 1100,
                speed: 130,
                walls: [
                    {x: 1, y: 1, width: 18, height: 1},
                    {x: 1, y: 18, width: 18, height: 1},
                    {x: 1, y: 1, width: 1, height: 18},
                    {x: 18, y: 1, width: 1, height: 18},
                    {x: 5, y: 5, width: 1, height: 5},
                    {x: 10, y: 3, width: 1, height: 7},
                    {x: 15, y: 8, width: 1, height: 6},
                    {x: 3, y: 12, width: 5, height: 1},
                    {x: 11, y: 15, width: 4, height: 1}
                ],
                unlocked: true,
                emoji: "🗝️"
            },
            {
                id: 10,
                name: "极速挑战",
                description: "超高速度，极限反应",
                targetScore: 900,
                speed: 80,
                walls: [
                    {x: 8, y: 8, width: 4, height: 4}
                ],
                unlocked: true,
                emoji: "⚡"
            },
            {
                id: 11,
                name: "终极试炼",
                description: "集合所有挑战的最终关卡",
                targetScore: 1500,
                speed: 120,
                speedIncrease: true,
                walls: [
                    {x: 0, y: 0, width: 20, height: 1},
                    {x: 0, y: 19, width: 20, height: 1},
                    {x: 0, y: 0, width: 1, height: 20},
                    {x: 19, y: 0, width: 1, height: 20},
                    {x: 5, y: 5, width: 2, height: 2},
                    {x: 13, y: 5, width: 2, height: 2},
                    {x: 5, y: 13, width: 2, height: 2},
                    {x: 13, y: 13, width: 2, height: 2},
                    {x: 9, y: 9, width: 2, height: 2}
                ],
                unlocked: true,
                emoji: "👑"
            }
        ];
        
        // 道具定义
        const powerups = {
            // 普通食物
            apple: { symbol: '🍎', points: 10, effect: null, rarity: 0.4, name: '红苹果', type: 'food' },
            grape: { symbol: '🍇', points: 20, effect: 'speed', rarity: 0.15, name: '紫葡萄', type: 'food' },
            strawberry: { symbol: '🍓', points: 30, effect: 'slow', rarity: 0.1, name: '草莓', type: 'food' },
            banana: { symbol: '🍌', points: 15, effect: 'growth', rarity: 0.1, name: '香蕉', type: 'food' },
            
            // 增益道具
            diamond: { symbol: '💎', points: 50, effect: null, rarity: 0.08, name: '钻石', type: 'bonus' },
            lightning: { symbol: '⚡', points: 0, effect: 'wallpass', rarity: 0.05, name: '闪电', type: 'bonus' },
            shield: { symbol: '🛡️', points: 0, effect: 'invincible', rarity: 0.05, name: '盾牌', type: 'bonus' },
            star: { symbol: '🌟', points: 0, effect: 'doublescore', rarity: 0.04, name: '星星', type: 'bonus' },
            ice: { symbol: '❄️', points: 0, effect: 'freeze', rarity: 0.04, name: '冰块', type: 'bonus' },
            fire: { symbol: '🔥', points: 0, effect: 'hyperspeed', rarity: 0.03, name: '火焰', type: 'bonus' },
            clover: { symbol: '🍀', points: 0, effect: 'random', rarity: 0.03, name: '四叶草', type: 'bonus' },
        };

        // 危险障碍
        const obstacles = {
            skull: { symbol: '💀', effect: 'damage', rarity: 0.3, name: '骷髅' },
            blackhole: { symbol: '🕳️', effect: 'teleport', rarity: 0.4, name: '黑洞' },
            tornado: { symbol: '🌪️', effect: 'reverse', rarity: 0.3, name: '龙卷风' }
        };
        
        // 游戏类
        class SnakeGame {
            constructor() {
                console.log('🎮 创建游戏实例...');
                this.reset();
            }
            
            reset() {
                console.log('🔄 重置游戏状态...');
                
                // 计算游戏区域
                const uiHeight = 40;
                const controlHeight = 40;
                const padding = 20;
                
                const gameAreaHeight = canvas.height - uiHeight - controlHeight - padding * 2;
                const gameAreaWidth = canvas.width - padding * 2;
                
                // 计算网格大小
                this.gridSize = Math.min(
                    Math.floor(gameAreaWidth / 20),
                    Math.floor(gameAreaHeight / 20),
                    25
                );
                this.gridSize = Math.max(this.gridSize, 10);
                
                this.cols = Math.floor(gameAreaWidth / this.gridSize);
                this.rows = Math.floor(gameAreaHeight / this.gridSize);
                
                this.cols = Math.max(this.cols, 15);
                this.rows = Math.max(this.rows, 15);
                
                // 游戏区域位置
                this.gameOffsetX = (canvas.width - this.cols * this.gridSize) / 2;
                this.gameOffsetY = uiHeight + (gameAreaHeight - this.rows * this.gridSize) / 2;
                
                const startCol = Math.floor(this.cols / 2);
                const startRow = Math.floor(this.rows / 2);
                
                this.snake = [{x: startCol, y: startRow}];
                this.direction = {x: 1, y: 0};
                this.nextDirection = {x: 1, y: 0};
                this.food = null;
                this.bonusItems = [];
                this.obstacleItems = [];
                this.score = 0;
                this.lives = selectedLevelId === 0 ? 999 : 500;
                
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                this.speed = currentLevel?.speed || 150;
                this.baseSpeed = this.speed;
                this.lastSpeedIncrease = 0;
                
                this.activePowerups = new Map();
                this.gameStartTime = Date.now();
                this.isReversed = false;
                
                // 初始化可达区域
                this.initializeReachableAreas();
                
                this.generateFood();
                this.updateUI();
                this.updateEffectIndicators();
                
                console.log(`✅ 游戏重置完成 - 网格: ${this.cols}x${this.rows}, 格子大小: ${this.gridSize}px`);
            }
            
            // 修复的可达区域计算
            initializeReachableAreas() {
                console.log('🗺️ 初始化可达区域...');
                
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                
                // 创建网格地图
                this.grid = Array(this.rows).fill(null).map(() => Array(this.cols).fill(false));
                
                // 标记墙壁
                if (currentLevel && currentLevel.walls && selectedLevelId !== 0) {
                    for (const wall of currentLevel.walls) {
                        for (let y = wall.y; y < wall.y + wall.height; y++) {
                            for (let x = wall.x; x < wall.x + wall.width; x++) {
                                if (y >= 0 && y < this.rows && x >= 0 && x < this.cols) {
                                    this.grid[y][x] = true; // true表示墙壁
                                }
                            }
                        }
                    }
                }
                
                // 使用BFS找到所有可达位置
                this.reachablePositions = [];
                const visited = new Set();
                const startPos = {x: Math.floor(this.cols / 2), y: Math.floor(this.rows / 2)};
                
                // 确保起始位置不在墙上
                if (this.grid[startPos.y] && this.grid[startPos.y][startPos.x]) {
                    // 如果起始位置在墙上，找一个不在墙上的位置
                    for (let y = 0; y < this.rows; y++) {
                        for (let x = 0; x < this.cols; x++) {
                            if (!this.grid[y][x]) {
                                startPos.x = x;
                                startPos.y = y;
                                break;
                            }
                        }
                        if (!this.grid[startPos.y][startPos.x]) break;
                    }
                }
                
                const queue = [startPos];
                
                while (queue.length > 0) {
                    const {x, y} = queue.shift();
                    const key = `${x},${y}`;
                    
                    // 检查边界
                    if (x < 0 || x >= this.cols || y < 0 || y >= this.rows) continue;
                    
                    // 检查是否已访问或是墙壁
                    if (visited.has(key) || this.grid[y][x]) continue;
                    
                    visited.add(key);
                    this.reachablePositions.push({x, y});
                    
                    // 添加四个方向的相邻位置
                    queue.push({x: x + 1, y});
                    queue.push({x: x - 1, y});
                    queue.push({x, y: y + 1});
                    queue.push({x, y: y - 1});
                }
                
                console.log(`✅ 可达区域计算完成: ${this.reachablePositions.length}/${this.cols * this.rows} 个位置可达`);
                
                if (this.reachablePositions.length === 0) {
                    console.error('❌ 没有可达位置！使用全地图作为备选');
                    for (let x = 0; x < this.cols; x++) {
                        for (let y = 0; y < this.rows; y++) {
                            this.reachablePositions.push({x, y});
                        }
                    }
                }
            }
            
            getRandomReachablePosition() {
                if (this.reachablePositions.length === 0) {
                    console.error('❌ 没有可达位置！');
                    return {x: Math.floor(this.cols / 2), y: Math.floor(this.rows / 2)};
                }
                
                // 过滤掉被占用的位置
                const availablePositions = this.reachablePositions.filter(pos => 
                    !this.isPositionOccupied(pos.x, pos.y)
                );
                
                if (availablePositions.length === 0) {
                    console.warn('⚠️ 所有可达位置都被占用！随机选择一个');
                    return this.reachablePositions[Math.floor(Math.random() * this.reachablePositions.length)];
                }
                
                return availablePositions[Math.floor(Math.random() * availablePositions.length)];
            }
            
            generateFood() {
                const position = this.getRandomReachablePosition();
                
                this.food = {
                    x: position.x,
                    y: position.y,
                    type: this.getRandomFoodType()
                };
                
                console.log(`🍎 生成食物: ${this.food.type} 在 (${this.food.x}, ${this.food.y})`);
                
                // 生成增益道具
                if (Math.random() < 0.3) {
                    this.generateBonusItem();
                }
                
                // 生成危险障碍
                if (Math.random() < 0.2) {
                    this.generateObstacle();
                }
            }
            
            getRandomFoodType() {
                const foodTypes = Object.keys(powerups).filter(key => powerups[key].type === 'food');
                const rand = Math.random();
                let cumulative = 0;
                
                for (const type of foodTypes) {
                    cumulative += powerups[type].rarity;
                    if (rand <= cumulative) {
                        return type;
                    }
                }
                return 'apple';
            }
            
            generateBonusItem() {
                const bonusTypes = Object.keys(powerups).filter(key => powerups[key].type === 'bonus');
                const type = bonusTypes[Math.floor(Math.random() * bonusTypes.length)];
                
                const position = this.getRandomReachablePosition();
                
                this.bonusItems.push({
                    x: position.x,
                    y: position.y,
                    type: type,
                    timer: 10000
                });
            }
            
            generateObstacle() {
                const obstacleTypes = Object.keys(obstacles);
                const type = obstacleTypes[Math.floor(Math.random() * obstacleTypes.length)];
                
                const position = this.getRandomReachablePosition();
                
                this.obstacleItems.push({
                    x: position.x,
                    y: position.y,
                    type: type,
                    timer: 8000
                });
            }
            
            isPositionOccupied(x, y) {
                // 检查蛇身
                for (const segment of this.snake) {
                    if (segment.x === x && segment.y === y) return true;
                }
                
                // 检查食物
                if (this.food && this.food.x === x && this.food.y === y) return true;
                
                // 检查增益道具
                for (const item of this.bonusItems) {
                    if (item.x === x && item.y === y) return true;
                }
                
                // 检查障碍物
                for (const item of this.obstacleItems) {
                    if (item.x === x && item.y === y) return true;
                }
                
                // 检查墙壁
                if (this.grid && this.grid[y] && this.grid[y][x]) return true;
                
                return false;
            }
            
            move() {
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                if (currentLevel && currentLevel.speedIncrease && this.score > this.lastSpeedIncrease + 100) {
                    this.speed = Math.max(60, this.speed - 5);
                    this.lastSpeedIncrease = this.score;
                }
                
                this.direction = {...this.nextDirection};
                const head = {...this.snake[0]};
                
                if (this.isReversed) {
                    head.x -= this.direction.x;
                    head.y -= this.direction.y;
                } else {
                    head.x += this.direction.x;
                    head.y += this.direction.y;
                }
                
                // 边界处理
                if (!this.activePowerups.has('wallpass')) {
                    if (head.x < 0 || head.x >= this.cols || head.y < 0 || head.y >= this.rows) {
                        this.handleCollision('边界碰撞');
                        return;
                    }
                } else {
                    head.x = (head.x + this.cols) % this.cols;
                    head.y = (head.y + this.rows) % this.rows;
                }
                
                if (this.checkCollision(head)) {
                    return;
                }
                
                this.snake.unshift(head);
                
                let ate = false;
                if (this.food && head.x === this.food.x && head.y === this.food.y) {
                    this.eatFood();
                    ate = true;
                }
                
                const hitBonus = this.bonusItems.find(item => item.x === head.x && item.y === head.y);
                if (hitBonus) {
                    this.eatBonusItem(hitBonus);
                    this.bonusItems = this.bonusItems.filter(item => item !== hitBonus);
                    ate = true;
                }
                
                if (!ate) {
                    this.snake.pop();
                }
                
                const hitObstacle = this.obstacleItems.find(item => item.x === head.x && item.y === head.y);
                if (hitObstacle) {
                    this.handleObstacleCollision(hitObstacle);
                    this.obstacleItems = this.obstacleItems.filter(item => item !== hitObstacle);
                }
                
                this.updateItemTimers();
            }
            
            checkCollision(head) {
                // 自身碰撞
                if (!this.activePowerups.has('invincible')) {
                    for (let i = 1; i < this.snake.length; i++) {
                        if (this.snake[i].x === head.x && this.snake[i].y === head.y) {
                            this.handleCollision('撞到自己');
                            return true;
                        }
                    }
                }
                
                // 墙壁碰撞
                if (!this.activePowerups.has('wallpass') && !this.activePowerups.has('invincible')) {
                    if (this.grid && this.grid[head.y] && this.grid[head.y][head.x]) {
                        this.handleCollision('撞到墙壁');
                        return true;
                    }
                }
                
                return false;
            }
            
            updateItemTimers() {
                this.bonusItems = this.bonusItems.filter(item => {
                    item.timer -= this.getCurrentSpeed();
                    return item.timer > 0;
                });
                
                this.obstacleItems = this.obstacleItems.filter(item => {
                    item.timer -= this.getCurrentSpeed();
                    return item.timer > 0;
                });
            }
            
            handleCollision(reason = '未知原因') {
                if (this.activePowerups.has('invincible')) {
                    return;
                }
                
                if (selectedLevelId !== 0) {
                    this.lives--;
                }
                
                if (this.lives <= 0 && selectedLevelId !== 0) {
                    this.gameOver(reason);
                } else {
                    // 重置到安全位置
                    const safePosition = this.getRandomReachablePosition();
                    this.snake = [{x: safePosition.x, y: safePosition.y}];
                    this.direction = {x: 1, y: 0};
                    this.nextDirection = {x: 1, y: 0};
                    this.activePowerups.clear();
                    this.isReversed = false;
                    this.updateEffectIndicators();
                }
                
                this.updateUI();
            }
            
            eatFood() {
                if (!this.food) return;
                
                const foodData = powerups[this.food.type];
                const multiplier = this.activePowerups.has('doublescore') ? 2 : 1;
                const points = Math.max(0, foodData.points * multiplier);
                this.score += points;
                
                this.handlePowerupEffect(this.food.type);
                this.generateFood();
                this.updateUI();
                this.updateEffectIndicators();
                
                if (selectedLevelId !== 0) {
                    const currentLevel = levels.find(l => l.id === selectedLevelId);
                    if (this.score >= currentLevel.targetScore) {
                        this.levelComplete();
                    }
                }
            }
            
            eatBonusItem(item) {
                const itemData = powerups[item.type];
                const multiplier = this.activePowerups.has('doublescore') ? 2 : 1;
                const points = Math.max(0, itemData.points * multiplier);
                this.score += points;
                
                this.handlePowerupEffect(item.type);
                this.updateUI();
                this.updateEffectIndicators();
            }
            
            handleObstacleCollision(item) {
                const obstacleData = obstacles[item.type];
                this.handleObstacleEffect(obstacleData.effect);
            }
            
            handlePowerupEffect(type) {
                const effect = powerups[type]?.effect;
                if (!effect) return;
                
                const now = Date.now();
                
                switch (effect) {
                    case 'speed':
                        this.speed = Math.max(60, this.speed - 25);
                        setTimeout(() => {
                            this.speed = Math.min(250, this.speed + 25);
                        }, 8000);
                        break;
                        
                    case 'slow':
                        this.speed = Math.min(300, this.speed + 40);
                        setTimeout(() => {
                            this.speed = Math.max(60, this.speed - 40);
                        }, 8000);
                        break;
                        
                    case 'growth':
                        this.snake.push({...this.snake[this.snake.length - 1]});
                        this.snake.push({...this.snake[this.snake.length - 1]});
                        break;
                        
                    case 'wallpass':
                        this.activePowerups.set('wallpass', now + 12000);
                        break;
                        
                    case 'invincible':
                        this.activePowerups.set('invincible', now + 8000);
                        break;
                        
                    case 'doublescore':
                        this.activePowerups.set('doublescore', now + 20000);
                        break;
                        
                    case 'freeze':
                        this.activePowerups.set('freeze', now + 10000);
                        break;
                        
                    case 'hyperspeed':
                        this.activePowerups.set('hyperspeed', now + 8000);
                        break;
                        
                    case 'random':
                        const goodEffects = ['speed', 'wallpass', 'invincible', 'doublescore', 'freeze'];
                        const randomEffect = goodEffects[Math.floor(Math.random() * goodEffects.length)];
                        this.handlePowerupEffect({type: randomEffect});
                        break;
                }
            }
            
            handleObstacleEffect(effect) {
                switch (effect) {
                    case 'damage':
                        if (selectedLevelId !== 0) {
                            this.lives = Math.max(0, this.lives - 1);
                            this.score = Math.max(0, this.score - 30);
                            if (this.lives <= 0) this.gameOver('生命耗尽');
                        }
                        break;
                        
                    case 'teleport':
                        const position = this.getRandomReachablePosition();
                        this.snake[0] = {x: position.x, y: position.y};
                        break;
                        
                    case 'reverse':
                        this.isReversed = true;
                        setTimeout(() => {
                            this.isReversed = false;
                        }, 8000);
                        break;
                }
                
                this.updateUI();
                this.updateEffectIndicators();
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
                const toRemove = [];
                
                for (const [effect, expireTime] of this.activePowerups.entries()) {
                    if (now > expireTime) {
                        toRemove.push(effect);
                    }
                }
                
                for (const effect of toRemove) {
                    this.activePowerups.delete(effect);
                }
                
                if (toRemove.length > 0) {
                    this.updateEffectIndicators();
                }
            }
            
            updateEffectIndicators() {
                const container = document.getElementById('effectIndicators');
                container.innerHTML = '';
                
                if (this.activePowerups.size === 0 && !this.isReversed) {
                    container.classList.remove('active');
                    return;
                }
                
                container.classList.add('active');
                
                for (const [effect, expireTime] of this.activePowerups.entries()) {
                    const timeLeft = Math.ceil((expireTime - Date.now()) / 1000);
                    const indicator = document.createElement('div');
                    indicator.className = 'effect-indicator';
                    
                    const effectNames = {
                        wallpass: '⚡ 穿墙',
                        invincible: '🛡️ 无敌',
                        doublescore: '🌟 双倍',
                        freeze: '❄️ 缓慢',
                        hyperspeed: '🔥 极速'
                    };
                    
                    indicator.innerHTML = `${effectNames[effect] || effect} ${timeLeft}s`;
                    container.appendChild(indicator);
                }
                
                if (this.isReversed) {
                    const indicator = document.createElement('div');
                    indicator.className = 'effect-indicator';
                    indicator.innerHTML = '🌪️ 反向控制';
                    container.appendChild(indicator);
                }
            }
            
            getCurrentSpeed() {
                let speed = this.speed;
                
                if (this.activePowerups.has('freeze')) {
                    speed *= 2;
                }
                if (this.activePowerups.has('hyperspeed')) {
                    speed = Math.max(40, speed - 60);
                }
                
                return speed;
            }
            
            levelComplete() {
                gameState = 'levelComplete';
                const completionTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                
                document.getElementById('levelScore').textContent = this.score;
                document.getElementById('completionTime').textContent = completionTime;
                
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                const bonus = Math.max(0, this.score - currentLevel.targetScore);
                document.getElementById('levelCompleteBonus').textContent = 
                    bonus > 0 ? `超额完成奖励: +${bonus}分` : '完美通关!';
                
                showScreen('levelCompleteScreen');
            }
            
            gameOver(reason = '游戏结束') {
                gameState = 'gameOver';
                const survivalTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                
                document.getElementById('finalScore').textContent = this.score;
                document.getElementById('survivalTime').textContent = survivalTime;
                document.getElementById('gameOverReason').textContent = `失败原因: ${reason}`;
                
                showScreen('gameOverScreen');
            }
            
            draw() {
                // 清空画布
                ctx.fillStyle = '#1a1a2e';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                // 绘制游戏边界
                ctx.strokeStyle = 'rgba(255,255,255,0.2)';
                ctx.lineWidth = 1;
                ctx.strokeRect(this.gameOffsetX - 1, this.gameOffsetY - 1, 
                             this.cols * this.gridSize + 2, this.rows * this.gridSize + 2);
                
                // 绘制墙壁
                if (this.grid) {
                    ctx.fillStyle = '#8B4513';
                    ctx.strokeStyle = '#D2691E';
                    ctx.lineWidth = 1;
                    
                    for (let y = 0; y < this.rows; y++) {
                        for (let x = 0; x < this.cols; x++) {
                            if (this.grid[y][x]) {
                                const drawX = this.gameOffsetX + x * this.gridSize;
                                const drawY = this.gameOffsetY + y * this.gridSize;
                                ctx.fillRect(drawX, drawY, this.gridSize, this.gridSize);
                                ctx.strokeRect(drawX, drawY, this.gridSize, this.gridSize);
                            }
                        }
                    }
                }
                
                // 绘制食物
                if (this.food) {
                    const foodData = powerups[this.food.type];
                    const x = this.gameOffsetX + this.food.x * this.gridSize;
                    const y = this.gameOffsetY + this.food.y * this.gridSize;
                    
                    ctx.font = `${Math.floor(this.gridSize * 0.7)}px Arial`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(
                        foodData.symbol,
                        x + this.gridSize/2,
                        y + this.gridSize/2
                    );
                }
                
                // 绘制增益道具
                this.bonusItems.forEach(item => {
                    const itemData = powerups[item.type];
                    const x = this.gameOffsetX + item.x * this.gridSize;
                    const y = this.gameOffsetY + item.y * this.gridSize;
                    
                    ctx.fillStyle = 'rgba(0, 255, 0, 0.3)';
                    ctx.fillRect(x, y, this.gridSize, this.gridSize);
                    ctx.strokeStyle = 'rgba(0, 255, 0, 0.8)';
                    ctx.lineWidth = 1;
                    ctx.strokeRect(x, y, this.gridSize, this.gridSize);
                    
                    ctx.font = `${Math.floor(this.gridSize * 0.7)}px Arial`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillStyle = 'white';
                    ctx.fillText(
                        itemData.symbol,
                        x + this.gridSize/2,
                        y + this.gridSize/2
                    );
                });
                
                // 绘制危险障碍
                this.obstacleItems.forEach(item => {
                    const itemData = obstacles[item.type];
                    const x = this.gameOffsetX + item.x * this.gridSize;
                    const y = this.gameOffsetY + item.y * this.gridSize;
                    
                    ctx.fillStyle = 'rgba(255, 0, 0, 0.4)';
                    ctx.fillRect(x, y, this.gridSize, this.gridSize);
                    ctx.strokeStyle = 'rgba(255, 0, 0, 0.8)';
                    ctx.lineWidth = 1;
                    ctx.strokeRect(x, y, this.gridSize, this.gridSize);
                    
                    ctx.font = `${Math.floor(this.gridSize * 0.7)}px Arial`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillStyle = 'white';
                    ctx.fillText(
                        itemData.symbol,
                        x + this.gridSize/2,
                        y + this.gridSize/2
                    );
                });
                
                // 绘制蛇
                this.snake.forEach((segment, index) => {
                    const x = this.gameOffsetX + segment.x * this.gridSize;
                    const y = this.gameOffsetY + segment.y * this.gridSize;
                    const padding = Math.max(1, Math.floor(this.gridSize * 0.1));
                    
                    if (index === 0) {
                        // 蛇头
                        let headColor = '#32CD32';
                        if (this.activePowerups.has('invincible')) headColor = '#FFD700';
                        if (this.activePowerups.has('wallpass')) headColor = '#00BFFF';
                        if (this.isReversed) headColor = '#FF6B6B';
                        
                        ctx.fillStyle = headColor;
                        ctx.fillRect(x + padding, y + padding, this.gridSize - padding*2, this.gridSize - padding*2);
                        
                        // 眼睛
                        if (this.gridSize >= 12) {
                            ctx.fillStyle = 'white';
                            const eyeSize = Math.max(1, Math.floor(this.gridSize / 8));
                            ctx.fillRect(x + padding + 2, y + padding + 2, eyeSize, eyeSize);
                            ctx.fillRect(x + this.gridSize - padding - eyeSize - 2, y + padding + 2, eyeSize, eyeSize);
                        }
                    } else {
                        // 蛇身
                        ctx.fillStyle = index % 2 === 1 ? '#90EE90' : '#98FB98';
                        ctx.fillRect(x + padding/2, y + padding/2, this.gridSize - padding, this.gridSize - padding);
                    }
                });
                
                // 特殊效果覆盖
                if (this.activePowerups.has('invincible')) {
                    ctx.fillStyle = 'rgba(255, 215, 0, 0.1)';
                    ctx.fillRect(this.gameOffsetX, this.gameOffsetY, 
                               this.cols * this.gridSize, this.rows * this.gridSize);
                }
                
                if (this.activePowerups.has('wallpass')) {
                    ctx.fillStyle = 'rgba(0, 191, 255, 0.08)';
                    ctx.fillRect(this.gameOffsetX, this.gameOffsetY, 
                               this.cols * this.gridSize, this.rows * this.gridSize);
                }
                
                if (this.isReversed) {
                    ctx.fillStyle = 'rgba(255, 107, 107, 0.1)';
                    ctx.fillRect(this.gameOffsetX, this.gameOffsetY, 
                               this.cols * this.gridSize, this.rows * this.gridSize);
                }
            }
        }
        
        // 屏幕管理函数
        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(screen => {
                screen.classList.remove('active');
            });
            
            document.getElementById('gameUI').classList.remove('active');
            document.getElementById('controls').classList.remove('active');
            document.getElementById('effectIndicators').classList.remove('active');
            
            if (screenId) {
                const screen = document.getElementById(screenId);
                if (screen) {
                    screen.classList.add('active');
                }
            }
        }
        
        function showStartScreen() {
            gameState = 'menu';
            selectedLevelId = null;
            showScreen('startScreen');
            
            if (gameLoop) {
                clearInterval(gameLoop);
                gameLoop = null;
            }
        }
        
        function showLevelSelect() {
            generateLevelButtons();
            showScreen('levelSelectScreen');
            
            document.getElementById('startLevelBtn').style.display = 'none';
            document.getElementById('levelInfo').innerHTML = `
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            `;
        }
        
        function showInstructions() {
            showScreen('instructionsScreen');
        }
        
        function startPractice() {
            selectedLevelId = 0;
            startGame();
        }
        
        function selectLevel(levelId) {
            const level = levels.find(l => l.id === levelId);
            if (!level || !level.unlocked) {
                return;
            }
            
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
                    <h3>${level.emoji} ${level.name}</h3>
                    <p>${level.description}</p>
                    <p>无限生命，体验所有道具效果</p>
                `;
            } else {
                document.getElementById('levelInfo').innerHTML = `
                    <h3>${level.emoji} ${level.name}</h3>
                    <p>${level.description}</p>
                    <p>目标分数: <span style="color: #FFD700;">${level.targetScore}</span></p>
                    <p>初始生命: <span style="color: #FF6B6B;">500</span></p>
                `;
            }
            
            document.getElementById('startLevelBtn').style.display = 'inline-block';
        }
        
        function startSelectedLevel() {
            if (selectedLevelId === null) {
                return;
            }
            startGame();
        }
        
        function startGame() {
            if (selectedLevelId === null) {
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
            
            function gameLoopFunction() {
                if (gameState === 'playing' && game) {
                    game.updatePowerups();
                    game.move();
                    game.draw();
                    
                    const currentSpeed = game.getCurrentSpeed();
                    if (currentSpeed !== game.speed) {
                        clearInterval(gameLoop);
                        gameLoop = setInterval(gameLoopFunction, currentSpeed);
                    }
                }
            }
            
            gameLoop = setInterval(gameLoopFunction, game.getCurrentSpeed());
        }
        
        function generateLevelButtons() {
            const container = document.getElementById('levelSelect');
            container.innerHTML = '';
            
            levels.forEach(level => {
                const button = document.createElement('button');
                button.className = `level-btn ${level.unlocked ? 'unlocked' : 'locked'}`;
                button.setAttribute('data-level', level.id);
                
                const numberDiv = document.createElement('div');
                numberDiv.className = 'level-number';
                numberDiv.textContent = level.id === 0 ? level.emoji : level.id;
                
                const nameDiv = document.createElement('div');
                nameDiv.className = 'level-name';
                nameDiv.textContent = level.name;
                
                button.appendChild(numberDiv);
                button.appendChild(nameDiv);
                
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
            document.getElementById('effectIndicators').classList.add('active');
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
            document.getElementById('backToMenuFromCompleteBtn').addEventListener('click', showStartScreen);
            
            // 游戏控制按钮
            document.getElementById('pauseBtn').addEventListener('click', (e) => {
                e.preventDefault();
                if (gameState === 'playing') {
                    pauseGame();
                } else if (gameState === 'paused') {
                    resumeGame();
                }
            });
            
            document.getElementById('menuBtn').addEventListener('click', (e) => {
                e.preventDefault();
                quitToMenu();
            });
            
            // 键盘控制
            document.addEventListener('keydown', (e) => {
                if (gameState !== 'playing' || !game) return;
                
                const keyMap = {
                    'ArrowUp': {x: 0, y: -1},
                    'w': {x: 0, y: -1},
                    'W': {x: 0, y: -1},
                    'ArrowDown': {x: 0, y: 1},
                    's': {x: 0, y: 1},
                    'S': {x: 0, y: 1},
                    'ArrowLeft': {x: -1, y: 0},
                    'a': {x: -1, y: 0},
                    'A': {x: -1, y: 0},
                    'ArrowRight': {x: 1, y: 0},
                    'd': {x: 1, y: 0},
                    'D': {x: 1, y: 0}
                };
                
                if (keyMap[e.key]) {
                    e.preventDefault();
                    game.changeDirection(keyMap[e.key]);
                } else if (e.key === ' ') {
                    e.preventDefault();
                    pauseGame();
                }
            });
            
            // 触摸滑动控制
            let touchStartX = 0;
            let touchStartY = 0;
            let touchStartTime = 0;
            
            canvas.addEventListener('touchstart', (e) => {
                e.preventDefault();
                const touch = e.touches[0];
                touchStartX = touch.clientX;
                touchStartY = touch.clientY;
                touchStartTime = Date.now();
            }, { passive: false });
            
            canvas.addEventListener('touchend', (e) => {
                e.preventDefault();
                
                if (gameState !== 'playing' || !game) return;
                
                const touch = e.changedTouches[0];
                const touchEndX = touch.clientX;
                const touchEndY = touch.clientY;
                const touchEndTime = Date.now();
                
                const deltaX = touchEndX - touchStartX;
                const deltaY = touchEndY - touchStartY;
                const deltaTime = touchEndTime - touchStartTime;
                
                if (deltaTime > 500) return;
                
                const minSwipeDistance = 30;
                const absDeltaX = Math.abs(deltaX);
                const absDeltaY = Math.abs(deltaY);
                
                if (absDeltaX > minSwipeDistance || absDeltaY > minSwipeDistance) {
                    if (absDeltaX > absDeltaY) {
                        game.changeDirection(deltaX > 0 ? {x: 1, y: 0} : {x: -1, y: 0});
                    } else {
                        game.changeDirection(deltaY > 0 ? {x: 0, y: 1} : {x: 0, y: -1});
                    }
                }
            }, { passive: false });
            
            document.addEventListener('touchmove', (e) => {
                if (e.target === canvas) {
                    e.preventDefault();
                }
            }, { passive: false });
            
            let resizeTimeout;
            window.addEventListener('resize', () => {
                clearTimeout(resizeTimeout);
                resizeTimeout = setTimeout(() => {
                    resizeCanvas();
                    if (game && gameState === 'playing') {
                        game.reset();
                    }
                }, 100);
            });
            
            document.addEventListener('visibilitychange', () => {
                if (document.hidden && gameState === 'playing') {
                    pauseGame();
                }
            });
        }
        
        function resizeCanvas() {
            console.log('📐 调整画布大小...');
            const container = document.getElementById('gameContainer');
            
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
            
            console.log(`✅ 画布大小: ${canvas.width}x${canvas.height}`);
        }
        
        function init() {
            console.log('🚀 开始初始化游戏...');
            
            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');
            
            if (!canvas || !ctx) {
                console.error('❌ 无法获取画布或上下文');
                alert('游戏初始化失败，请刷新页面重试');
                return;
            }
            
            resizeCanvas();
            setupEventListeners();
            
            console.log('🎮 贪食蛇大冒险已准备就绪！');
        }
        
        window.addEventListener('error', (e) => {
            console.error('💥 游戏错误:', e.error);
            if (gameState === 'playing') {
                pauseGame();
            }
        });
        
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
    </script>
</body>
</html>
