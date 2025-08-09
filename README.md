# Snake-Xenzia-only-for-Ashley
Creating an interesting game only for my love, Ashley. Hope her happy everyday!


<!DOCTYPE html>
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
        
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
            display: flex;
            justify-content: center;
            align-items: center;
            position: fixed;
            top: 0;
            left: 0;
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
        
        /* 适配不同屏幕比例 */
        @media (min-aspect-ratio: 1/1) {
            #gameContainer {
                width: min(100vw, 60vh);
                height: 100vh;
                border-radius: 0;
                box-shadow: 0 0 30px rgba(0,0,0,0.8);
            }
        }
        
        @media (max-aspect-ratio: 9/16) {
            #gameContainer {
                width: 100vw;
                height: 100vh;
                border-radius: 0;
            }
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
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 60px;
            background: linear-gradient(180deg, rgba(0,0,0,0.9) 0%, rgba(0,0,0,0.6) 70%, transparent 100%);
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
            flex: 1;
        }
        
        .ui-label {
            font-size: 10px;
            opacity: 0.8;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
        }
        
        .ui-value {
            font-size: 16px;
            margin-top: 2px;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.8);
            font-weight: bold;
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
            grid-template-columns: repeat(3, 1fr);
            grid-template-rows: repeat(3, 1fr);
            gap: 8px;
            z-index: 100;
            width: min(240px, 40vw);
            height: min(240px, 40vw);
            max-width: 180px;
            max-height: 180px;
        }
        
        #controls.active { 
            display: grid; 
        }
        
        .control-btn {
            background: rgba(255,255,255,0.15);
            border: 2px solid rgba(255,255,255,0.3);
            border-radius: 15px;
            color: white;
            font-size: min(20px, 4vw);
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.2s ease;
            backdrop-filter: blur(10px);
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
        }
        
        .control-btn:active {
            transform: scale(0.95);
            background: rgba(255,255,255,0.3);
            box-shadow: 0 2px 6px rgba(0,0,0,0.4);
        }
        
        #upBtn { grid-column: 2; grid-row: 1; }
        #leftBtn { grid-column: 1; grid-row: 2; }
        #pauseBtn { 
            grid-column: 2; 
            grid-row: 2; 
            background: rgba(255,107,107,0.4);
            border-color: rgba(255,107,107,0.6);
        }
        #rightBtn { grid-column: 3; grid-row: 2; }
        #downBtn { grid-column: 2; grid-row: 3; }
        
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
            padding: 20px;
            text-align: center;
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
        }
        
        .screen.active { 
            display: flex; 
        }
        
        .screen h1 {
            font-size: clamp(1.8rem, 6vw, 2.8rem);
            font-weight: bold;
            margin: 20px 0 30px 0;
            background: linear-gradient(45deg, #FFD700, #FFA500, #FF6B6B);
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: none;
            text-align: center;
        }
        
        .screen p {
            font-size: clamp(14px, 3.5vw, 16px);
            line-height: 1.6;
            margin-bottom: 15px;
            opacity: 0.9;
        }
        
        /* 按钮样式 */
        .btn {
            background: linear-gradient(145deg, #4CAF50, #45a049);
            color: white;
            border: none;
            padding: clamp(12px, 3vw, 15px) clamp(24px, 6vw, 30px);
            font-size: clamp(14px, 3.5vw, 16px);
            font-weight: bold;
            border-radius: 25px;
            cursor: pointer;
            margin: 8px;
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(76,175,80,0.4);
            min-width: clamp(120px, 30vw, 180px);
            text-transform: none;
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(76,175,80,0.6);
        }
        
        .btn:active {
            transform: translateY(0);
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
        
        /* 关卡选择网格 */
        #levelSelect {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
            gap: 12px;
            max-width: min(90vw, 400px);
            margin: 20px 0;
            width: 100%;
        }
        
        .level-btn {
            aspect-ratio: 1;
            background: rgba(255,255,255,0.1);
            border: 2px solid rgba(255,255,255,0.2);
            border-radius: 15px;
            color: white;
            font-size: clamp(12px, 3vw, 14px);
            font-weight: bold;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.3s ease;
            backdrop-filter: blur(5px);
            position: relative;
            overflow: hidden;
        }
        
        .level-btn::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.1), transparent);
            transform: translateX(-100%);
            transition: transform 0.6s;
        }
        
        .level-btn:hover::before {
            transform: translateX(100%);
        }
        
        .level-btn:hover {
            border-color: #FFD700;
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(255,215,0,0.3);
        }
        
        .level-btn.selected {
            border-color: #FFD700;
            background: rgba(255,215,0,0.2);
            box-shadow: 0 0 20px rgba(255,215,0,0.5);
        }
        
        .level-btn.locked {
            opacity: 0.4;
            cursor: not-allowed;
            filter: grayscale(1);
        }
        
        .level-btn .level-number {
            font-size: clamp(16px, 4vw, 20px);
            margin-bottom: 5px;
        }
        
        .level-btn .level-name {
            font-size: clamp(10px, 2.5vw, 12px);
            opacity: 0.8;
        }
        
        /* 滚动内容 */
        .scrollable-content {
            max-height: 50vh;
            overflow-y: auto;
            padding: 20px;
            margin: 15px 0;
            border-radius: 15px;
            background: rgba(255,255,255,0.1);
            backdrop-filter: blur(5px);
            text-align: left;
            line-height: 1.6;
            width: min(90vw, 500px);
            box-sizing: border-box;
            -webkit-overflow-scrolling: touch;
            border: 1px solid rgba(255,255,255,0.2);
        }
        
        .scrollable-content::-webkit-scrollbar {
            width: 6px;
        }
        
        .scrollable-content::-webkit-scrollbar-track {
            background: rgba(255,255,255,0.1);
            border-radius: 3px;
        }
        
        .scrollable-content::-webkit-scrollbar-thumb {
            background: rgba(255,255,255,0.5);
            border-radius: 3px;
        }
        
        .scrollable-content h3 {
            color: #FFD700;
            margin: 15px 0 10px 0;
            font-size: clamp(16px, 4vw, 18px);
        }
        
        .scrollable-content p {
            margin-bottom: 10px;
            font-size: clamp(13px, 3vw, 15px);
            line-height: 1.5;
        }
        
        /* 关卡信息 */
        #levelInfo {
            text-align: center;
            margin: 20px 0;
            padding: 20px;
            background: rgba(255,255,255,0.1);
            border-radius: 15px;
            min-height: 120px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255,255,255,0.2);
            width: min(90vw, 400px);
        }
        
        #levelInfo h3 {
            color: #FFD700;
            margin-bottom: 10px;
            font-size: clamp(16px, 4vw, 18px);
        }
        
        #levelInfo p {
            font-size: clamp(13px, 3vw, 15px);
            margin: 5px 0;
        }
        
        /* 状态效果指示器 */
        #effectIndicators {
            position: absolute;
            top: 70px;
            right: 10px;
            display: none;
            flex-direction: column;
            gap: 5px;
            z-index: 150;
        }
        
        #effectIndicators.active {
            display: flex;
        }
        
        .effect-indicator {
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 12px;
            display: flex;
            align-items: center;
            gap: 5px;
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255,255,255,0.2);
        }
        
        /* 响应式调整 */
        @media (max-height: 600px) {
            .screen h1 {
                margin: 10px 0 20px 0;
            }
            
            .scrollable-content {
                max-height: 40vh;
            }
            
            #levelInfo {
                min-height: 80px;
                padding: 15px;
            }
        }
        
        @media (max-width: 360px) {
            #controls {
                width: 160px;
                height: 160px;
            }
            
            .control-btn {
                font-size: 16px;
            }
            
            #levelSelect {
                grid-template-columns: repeat(3, 1fr);
                gap: 8px;
            }
        }
        
        /* 高分辨率屏幕优化 */
        @media (min-width: 768px) {
            #gameContainer {
                max-width: 400px;
                border-radius: 20px;
            }
            
            #controls {
                width: 200px;
                height: 200px;
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
            <div id="levelInfo">
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            </div>
            <button class="btn primary" id="startLevelBtn" style="display: none;">开始游戏</button>
            <button class="btn secondary" id="backToMenuBtn">返回主菜单</button>
        </div>
        
        <!-- 游戏说明屏幕 -->
        <div id="instructionsScreen" class="screen">
            <h1>📖 游戏说明</h1>
            <div class="scrollable-content">
                <h3>🎮 控制方式</h3>
                <p>• 在游戏区域滑动控制蛇的移动方向</p>
                <p>• 使用屏幕底部的虚拟方向键</p>
                <p>• 电脑玩家可使用 WASD 或方向键</p>
                <p>• 点击暂停按钮或按空格键暂停游戏</p>
                
                <h3>🍎 普通食物</h3>
                <p>• 🍎 红苹果 (+10分) - 最常见的食物</p>
                <p>• 🍇 紫葡萄 (+20分) - 临时提升移动速度</p>
                <p>• 🍓 草莓 (+30分) - 临时减慢移动速度</p>
                <p>• 🍌 香蕉 (+15分) - 让蛇身增长2格</p>
                
                <h3>💎 特殊道具</h3>
                <p>• 💎 钻石 (+50分) - 高价值道具</p>
                <p>• ⚡ 闪电 (10秒穿墙能力) - 可以穿过墙壁</p>
                <p>• 🛡️ 盾牌 (5秒无敌状态) - 碰到障碍不死</p>
                <p>• 🌟 星星 (15秒双倍得分) - 所有分数翻倍</p>
                <p>• ❄️ 冰块 (8秒时间缓慢) - 游戏速度减半</p>
                <p>• 🔥 火焰 (6秒超高速) - 移动速度大幅提升</p>
                <p>• 🍀 四叶草 (随机好效果) - 触发随机正面效果</p>
                
                <h3>💀 危险道具</h3>
                <p>• 💀 骷髅 (-30分并减少生命) - 避免碰触</p>
                <p>• 🕳️ 黑洞 (传送到随机位置) - 随机传送</p>
                <p>• 🌪️ 龙卷风 (5秒反向操作) - 控制方向相反</p>
                
                <h3>🏋️ 练习场模式</h3>
                <p>• 无限生命，轻松练习操作</p>
                <p>• 所有道具都可能出现</p>
                <p>• 没有目标分数限制</p>
                <p>• 熟悉游戏机制的最佳选择</p>
                
                <h3>🏆 冒险模式</h3>
                <p>• 12个独特设计的关卡</p>
                <p>• 每个关卡都有目标分数</p>
                <p>• 达到目标分数即可解锁下一关</p>
                <p>• 生命有限，需要谨慎操作</p>
                <p>• 不同关卡有不同的地形和挑战</p>
                
                <h3>🎯 关卡特色</h3>
                <p>• 新手村：简单入门</p>
                <p>• 森林迷宫：墙壁障碍</p>
                <p>• 速度狂飙：逐渐加速</p>
                <p>• 十字路口：十字形墙壁</p>
                <p>• 围城之战：四面围墙</p>
                <p>• 螺旋通道：螺旋形迷宫</p>
                <p>• 双龙戏珠：双通道设计</p>
                <p>• 星空漫步：星形图案</p>
                <p>• 终极挑战：最高难度</p>
                
                <h3>💡 游戏技巧</h3>
                <p>• 建议先在练习场熟悉各种道具</p>
                <p>• 合理规划路线避免困住自己</p>
                <p>• 优先收集高分道具和有益效果</p>
                <p>• 善用道具效果的持续时间</p>
                <p>• 在有盾牌效果时可以更激进</p>
                <p>• 注意观察蛇身长度变化</p>
                <p>• 保持冷静，稳中求胜</p>
                
                <h3>🏅 成就系统</h3>
                <p>• 完成所有关卡解锁特殊称号</p>
                <p>• 挑战高分记录</p>
                <p>• 尝试完美通关（无死亡）</p>
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
            <div style="margin: 20px 0;">
                <p>最终分数: <span id="finalScore" style="color: #FFD700; font-weight: bold;">0</span></p>
                <p>存活时间: <span id="survivalTime" style="color: #87CEEB; font-weight: bold;">0</span>秒</p>
                <p id="gameOverReason" style="color: #FF6B6B; margin-top: 10px;"></p>
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
                <p>完成时间: <span id="completionTime" style="color: #87CEEB; font-weight: bold;">0</span>秒</p>
                <p id="levelCompleteBonus" style="color: #98FB98; margin-top: 10px;"></p>
            </div>
            <button class="btn primary" id="nextLevelBtn">下一关</button>
            <button class="btn secondary" id="chooseLevelBtn">选择关卡</button>
            <button class="btn practice" id="backToMenuFromCompleteBtn">返回主菜单</button>
        </div>
    </div>

    <script>
        console.log('🐍 贪食蛇大冒险 - 正在加载...');

        // 全局变量
        let canvas, ctx;
        let gameState = 'menu';
        let selectedLevelId = null;
        let game = null;
        let gameLoop = null;
        
        // 完整的关卡定义（12个关卡）
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
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
                unlocked: false,
                emoji: "👑"
            }
        ];
        
        // 扩展的道具定义
        const powerups = {
            apple: { symbol: '🍎', points: 10, effect: null, rarity: 0.35, name: '红苹果' },
            grape: { symbol: '🍇', points: 20, effect: 'speed', rarity: 0.12, name: '紫葡萄' },
            strawberry: { symbol: '🍓', points: 30, effect: 'slow', rarity: 0.1, name: '草莓' },
            banana: { symbol: '🍌', points: 15, effect: 'growth', rarity: 0.08, name: '香蕉' },
            diamond: { symbol: '💎', points: 50, effect: null, rarity: 0.08, name: '钻石' },
            lightning: { symbol: '⚡', points: 0, effect: 'wallpass', rarity: 0.05, name: '闪电' },
            shield: { symbol: '🛡️', points: 0, effect: 'invincible', rarity: 0.05, name: '盾牌' },
            star: { symbol: '🌟', points: 0, effect: 'doublescore', rarity: 0.04, name: '星星' },
            ice: { symbol: '❄️', points: 0, effect: 'freeze', rarity: 0.04, name: '冰块' },
            fire: { symbol: '🔥', points: 0, effect: 'hyperspeed', rarity: 0.03, name: '火焰' },
            clover: { symbol: '🍀', points: 0, effect: 'random', rarity: 0.03, name: '四叶草' },
            skull: { symbol: '💀', points: -30, effect: 'damage', rarity: 0.02, name: '骷髅' },
            blackhole: { symbol: '🕳️', points: 0, effect: 'teleport', rarity: 0.015, name: '黑洞' },
            tornado: { symbol: '🌪️', points: 0, effect: 'reverse', rarity: 0.01, name: '龙卷风' }
        };
        
        // 游戏类
        class SnakeGame {
            constructor() {
                console.log('🎮 创建游戏实例...');
                this.gridSize = 20;
                this.reset();
                this.lastSpeedIncrease = 0;
            }
            
            reset() {
                console.log('🔄 重置游戏状态...');
                
                // 动态计算网格大小以适应屏幕
                const availableWidth = canvas.width - 40; // 留出边距
                const availableHeight = canvas.height - 120; // 留出UI空间
                
                this.gridSize = Math.min(
                    Math.floor(availableWidth / 20),
                    Math.floor(availableHeight / 20),
                    25 // 最大格子大小
                );
                this.gridSize = Math.max(this.gridSize, 15); // 最小格子大小
                
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
                
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                this.speed = currentLevel?.speed || 150;
                this.baseSpeed = this.speed;
                this.lastSpeedIncrease = 0;
                
                this.activePowerups = new Map();
                this.gameStartTime = Date.now();
                this.isReversed = false;
                
                this.generateFood();
                this.updateUI();
                this.updateEffectIndicators();
                
                console.log(`✅ 游戏重置完成 - 网格: ${this.cols}x${this.rows}, 格子大小: ${this.gridSize}px`);
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
                
                if (attempts >= 100) {
                    console.warn('⚠️ 食物生成位置查找超时');
                }
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
                // 检查是否在蛇身上
                for (const segment of this.snake) {
                    if (segment.x === x && segment.y === y) return true;
                }
                
                // 检查是否在墙壁上
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                if (currentLevel && currentLevel.walls && selectedLevelId !== 0) {
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
                // 处理速度递增关卡
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                if (currentLevel && currentLevel.speedIncrease && this.score > this.lastSpeedIncrease + 100) {
                    this.speed = Math.max(60, this.speed - 5);
                    this.lastSpeedIncrease = this.score;
                    console.log(`🚀 速度提升! 当前速度: ${this.speed}ms`);
                }
                
                this.direction = {...this.nextDirection};
                const head = {...this.snake[0]};
                
                // 应用反向控制效果
                if (this.isReversed) {
                    head.x -= this.direction.x;
                    head.y -= this.direction.y;
                } else {
                    head.x += this.direction.x;
                    head.y += this.direction.y;
                }
                
                // 处理边界碰撞
                if (!this.activePowerups.has('wallpass')) {
                    if (head.x < 0 || head.x >= this.cols || head.y < 0 || head.y >= this.rows) {
                        this.handleCollision('边界碰撞');
                        return;
                    }
                } else {
                    // 穿墙效果
                    head.x = (head.x + this.cols) % this.cols;
                    head.y = (head.y + this.rows) % this.rows;
                }
                
                // 检查碰撞
                if (this.checkCollision(head)) {
                    return;
                }
                
                this.snake.unshift(head);
                
                // 检查是否吃到食物
                if (this.food && head.x === this.food.x && head.y === this.food.y) {
                    this.eatFood();
                } else {
                    this.snake.pop();
                }
            }
            
            checkCollision(head) {
                // 检查自身碰撞
                if (!this.activePowerups.has('invincible')) {
                    for (let i = 1; i < this.snake.length; i++) {
                        if (this.snake[i].x === head.x && this.snake[i].y === head.y) {
                            this.handleCollision('撞到自己');
                            return true;
                        }
                    }
                }
                
                // 检查墙壁碰撞
                if (!this.activePowerups.has('wallpass') && !this.activePowerups.has('invincible')) {
                    const currentLevel = levels.find(l => l.id === selectedLevelId);
                    if (currentLevel && currentLevel.walls && selectedLevelId !== 0) {
                        for (const wall of currentLevel.walls) {
                            if (head.x >= wall.x && head.x < wall.x + wall.width &&
                                head.y >= wall.y && head.y < wall.y + wall.height) {
                                this.handleCollision('撞到墙壁');
                                return true;
                            }
                        }
                    }
                }
                
                return false;
            }
            
            handleCollision(reason = '未知原因') {
                if (this.activePowerups.has('invincible')) {
                    console.log('🛡️ 无敌状态，免疫碰撞');
                    return;
                }
                
                console.log(`💥 碰撞发生: ${reason}`);
                
                if (selectedLevelId !== 0) {
                    this.lives--;
                    console.log(`❤️ 生命值: ${this.lives}`);
                }
                
                if (this.lives <= 0 && selectedLevelId !== 0) {
                    this.gameOver(reason);
                } else {
                    // 重置到中心位置
                    const startCol = Math.floor(this.cols / 2);
                    const startRow = Math.floor(this.rows / 2);
                    this.snake = [{x: startCol, y: startRow}];
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
                
                console.log(`🍎 吃到 ${foodData.name} (+${points}分)`);
                
                this.handlePowerupEffect(this.food.type);
                this.generateFood();
                this.updateUI();
                this.updateEffectIndicators();
                
                // 检查关卡完成
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
                
                const now = Date.now();
                
                switch (effect) {
                    case 'speed':
                        this.speed = Math.max(60, this.speed - 25);
                        setTimeout(() => {
                            this.speed = Math.min(250, this.speed + 25);
                        }, 8000);
                        console.log('🍇 速度提升8秒');
                        break;
                        
                    case 'slow':
                        this.speed = Math.min(300, this.speed + 40);
                        setTimeout(() => {
                            this.speed = Math.max(60, this.speed - 40);
                        }, 8000);
                        console.log('🍓 速度减慢8秒');
                        break;
                        
                    case 'growth':
                        this.snake.push({...this.snake[this.snake.length - 1]});
                        this.snake.push({...this.snake[this.snake.length - 1]});
                        console.log('🍌 蛇身增长2格');
                        break;
                        
                    case 'wallpass':
                        this.activePowerups.set('wallpass', now + 12000);
                        console.log('⚡ 穿墙能力12秒');
                        break;
                        
                    case 'invincible':
                        this.activePowerups.set('invincible', now + 8000);
                        console.log('🛡️ 无敌状态8秒');
                        break;
                        
                    case 'doublescore':
                        this.activePowerups.set('doublescore', now + 20000);
                        console.log('🌟 双倍得分20秒');
                        break;
                        
                    case 'freeze':
                        this.activePowerups.set('freeze', now + 10000);
                        console.log('❄️ 时间缓慢10秒');
                        break;
                        
                    case 'hyperspeed':
                        this.activePowerups.set('hyperspeed', now + 8000);
                        console.log('🔥 超高速8秒');
                        break;
                        
                    case 'random':
                        const goodEffects = ['speed', 'wallpass', 'invincible', 'doublescore', 'freeze'];
                        const randomEffect = goodEffects[Math.floor(Math.random() * goodEffects.length)];
                        this.handlePowerupEffect(randomEffect);
                        console.log('🍀 触发随机效果:', randomEffect);
                        break;
                        
                    case 'damage':
                        if (selectedLevelId !== 0) {
                            this.lives = Math.max(0, this.lives - 1);
                            console.log('💀 生命值-1');
                            if (this.lives <= 0) this.gameOver('生命耗尽');
                        }
                        break;
                        
                    case 'teleport':
                        const newX = Math.floor(Math.random() * this.cols);
                        const newY = Math.floor(Math.random() * this.rows);
                        this.snake[0] = {x: newX, y: newY};
                        console.log('🕳️ 随机传送');
                        break;
                        
                    case 'reverse':
                        this.isReversed = true;
                        setTimeout(() => {
                            this.isReversed = false;
                            console.log('🌪️ 反向控制结束');
                        }, 8000);
                        console.log('🌪️ 反向控制8秒');
                        break;
                }
            }
            
            changeDirection(newDirection) {
                // 防止直接反向
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
                    console.log(`⏰ 效果结束: ${effect}`);
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
                
                // 显示活跃效果
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
                
                // 显示反向控制
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
                    speed *= 2; // 缓慢效果
                }
                if (this.activePowerups.has('hyperspeed')) {
                    speed = Math.max(40, speed - 60); // 超高速效果
                }
                
                return speed;
            }
            
            levelComplete() {
                gameState = 'levelComplete';
                const completionTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                
                document.getElementById('levelScore').textContent = this.score;
                document.getElementById('completionTime').textContent = completionTime;
                
                // 计算通关奖励
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                const bonus = Math.max(0, this.score - currentLevel.targetScore);
                document.getElementById('levelCompleteBonus').textContent = 
                    bonus > 0 ? `超额完成奖励: +${bonus}分` : '完美通关!';
                
                // 解锁下一关
                if (selectedLevelId < levels.length - 1) {
                    levels[selectedLevelId + 1].unlocked = true;
                    console.log(`🔓 解锁关卡 ${selectedLevelId + 1}`);
                }
                
                showScreen('levelCompleteScreen');
                console.log(`🎉 关卡 ${selectedLevelId} 完成! 分数: ${this.score}`);
            }
            
            gameOver(reason = '游戏结束') {
                gameState = 'gameOver';
                const survivalTime = Math.floor((Date.now() - this.gameStartTime) / 1000);
                
                document.getElementById('finalScore').textContent = this.score;
                document.getElementById('survivalTime').textContent = survivalTime;
                document.getElementById('gameOverReason').textContent = `失败原因: ${reason}`;
                
                showScreen('gameOverScreen');
                console.log(`💀 游戏结束: ${reason}, 分数: ${this.score}`);
            }
            
            draw() {
                // 清空画布
                ctx.fillStyle = '#1a1a2e';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                // 绘制网格线（可选）
                if (this.gridSize >= 20) {
                    ctx.strokeStyle = 'rgba(255,255,255,0.05)';
                    ctx.lineWidth = 0.5;
                    for (let x = 0; x <= canvas.width; x += this.gridSize) {
                        ctx.beginPath();
                        ctx.moveTo(x, 0);
                        ctx.lineTo(x, canvas.height);
                        ctx.stroke();
                    }
                    for (let y = 0; y <= canvas.height; y += this.gridSize) {
                        ctx.beginPath();
                        ctx.moveTo(0, y);
                        ctx.lineTo(canvas.width, y);
                        ctx.stroke();
                    }
                }
                
                // 绘制墙壁
                const currentLevel = levels.find(l => l.id === selectedLevelId);
                if (currentLevel && currentLevel.walls && selectedLevelId !== 0) {
                    ctx.fillStyle = '#8B4513';
                    ctx.strokeStyle = '#D2691E';
                    ctx.lineWidth = 2;
                    
                    currentLevel.walls.forEach(wall => {
                        const x = wall.x * this.gridSize;
                        const y = wall.y * this.gridSize;
                        const width = wall.width * this.gridSize;
                        const height = wall.height * this.gridSize;
                        
                        ctx.fillRect(x, y, width, height);
                        ctx.strokeRect(x, y, width, height);
                    });
                }
                
                // 绘制食物
                if (this.food) {
                    const foodData = powerups[this.food.type];
                    const x = this.food.x * this.gridSize;
                    const y = this.food.y * this.gridSize;
                    
                    // 食物发光效果
                    if (foodData.points >= 30) {
                        ctx.shadowColor = '#FFD700';
                        ctx.shadowBlur = 10;
                    }
                    
                    ctx.font = `${Math.floor(this.gridSize * 0.8)}px Arial`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(
                        foodData.symbol,
                        x + this.gridSize/2,
                        y + this.gridSize/2
                    );
                    
                    ctx.shadowBlur = 0;
                }
                
                // 绘制蛇
                this.snake.forEach((segment, index) => {
                    const x = segment.x * this.gridSize;
                    const y = segment.y * this.gridSize;
                    const padding = 2;
                    
                    if (index === 0) {
                        // 蛇头
                        let headColor = '#32CD32';
                        if (this.activePowerups.has('invincible')) headColor = '#FFD700';
                        if (this.activePowerups.has('wallpass')) headColor = '#00BFFF';
                        if (this.isReversed) headColor = '#FF6B6B';
                        
                        ctx.fillStyle = headColor;
                        ctx.fillRect(x + padding, y + padding, this.gridSize - padding*2, this.gridSize - padding*2);
                        
                        // 眼睛
                        ctx.fillStyle = 'white';
                        const eyeSize = Math.max(2, Math.floor(this.gridSize / 8));
                        ctx.fillRect(x + padding + 2, y + padding + 2, eyeSize, eyeSize);
                        ctx.fillRect(x + this.gridSize - padding - eyeSize - 2, y + padding + 2, eyeSize, eyeSize);
                        
                        // 瞳孔
                        ctx.fillStyle = 'black';
                        const pupilSize = Math.max(1, Math.floor(eyeSize / 2));
                        ctx.fillRect(x + padding + 3, y + padding + 3, pupilSize, pupilSize);
                        ctx.fillRect(x + this.gridSize - padding - eyeSize - 1, y + padding + 3, pupilSize, pupilSize);
                    } else {
                        // 蛇身
                        ctx.fillStyle = index % 2 === 1 ? '#90EE90' : '#98FB98';
                        ctx.fillRect(x + padding/2, y + padding/2, this.gridSize - padding, this.gridSize - padding);
                    }
                });
                
                // 特殊效果覆盖
                if (this.activePowerups.has('invincible')) {
                    ctx.fillStyle = 'rgba(255, 215, 0, 0.15)';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                }
                
                if (this.activePowerups.has('wallpass')) {
                    ctx.fillStyle = 'rgba(0, 191, 255, 0.1)';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                }
                
                if (this.isReversed) {
                    ctx.fillStyle = 'rgba(255, 107, 107, 0.15)';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                }
            }
        }
        
        // 屏幕管理函数
        function showScreen(screenId) {
            console.log('📱 显示屏幕:', screenId);
            
            // 隐藏所有屏幕
            document.querySelectorAll('.screen').forEach(screen => {
                screen.classList.remove('active');
            });
            
            // 隐藏游戏UI
            document.getElementById('gameUI').classList.remove('active');
            document.getElementById('controls').classList.remove('active');
            document.getElementById('effectIndicators').classList.remove('active');
            
            // 显示指定屏幕
            if (screenId) {
                const screen = document.getElementById(screenId);
                if (screen) {
                    screen.classList.add('active');
                } else {
                    console.error('❌ 屏幕未找到:', screenId);
                }
            }
        }
        
        function showStartScreen() {
            console.log('🏠 显示主菜单');
            gameState = 'menu';
            selectedLevelId = null;
            showScreen('startScreen');
            
            if (gameLoop) {
                clearInterval(gameLoop);
                gameLoop = null;
            }
        }
        
        function showLevelSelect() {
            console.log('📋 显示关卡选择');
            generateLevelButtons();
            showScreen('levelSelectScreen');
            
            document.getElementById('startLevelBtn').style.display = 'none';
            document.getElementById('levelInfo').innerHTML = `
                <h3>请选择一个关卡</h3>
                <p>点击上方关卡按钮查看详情</p>
            `;
        }
        
        function showInstructions() {
            console.log('📖 显示游戏说明');
            showScreen('instructionsScreen');
        }
        
        function startPractice() {
            console.log('🏋️ 开始练习模式');
            selectedLevelId = 0;
            startGame();
        }
        
        function selectLevel(levelId) {
            console.log('✅ 选择关卡:', levelId);
            const level = levels.find(l => l.id === levelId);
            if (!level || (!level.unlocked && levelId !== 0)) {
                console.log('❌ 关卡未解锁:', levelId);
                return;
            }
            
            selectedLevelId = levelId;
            
            // 更新选中状态
            document.querySelectorAll('.level-btn').forEach(btn => {
                btn.classList.remove('selected');
            });
            const levelBtn = document.querySelector(`[data-level="${levelId}"]`);
            if (levelBtn) {
                levelBtn.classList.add('selected');
            }
            
            // 显示关卡信息
            if (levelId === 0) {
                document.getElementById('levelInfo').innerHTML = `
                    <h3>${level.emoji} ${level.name}</h3>
                    <p>${level.description}</p>
                    <p>无限生命，体验所有道具效果</p>
                    <p>没有目标分数，尽情练习！</p>
                `;
            } else {
                document.getElementById('levelInfo').innerHTML = `
                    <h3>${level.emoji} ${level.name}</h3>
                    <p>${level.description}</p>
                    <p>目标分数: <span style="color: #FFD700;">${level.targetScore}</span></p>
                    <p>初始生命: <span style="color: #FF6B6B;">500</span></p>
                    <p>游戏速度: <span style="color: #87CEEB;">${level.speed}ms</span></p>
                    ${level.speedIncrease ? '<p style="color: #FFA500;">⚠️ 速度会逐渐加快</p>' : ''}
                `;
            }
            
            document.getElementById('startLevelBtn').style.display = 'inline-block';
        }
        
        function startSelectedLevel() {
            console.log('🚀 开始选中的关卡:', selectedLevelId);
            if (selectedLevelId === null) {
                console.error('❌ 未选择关卡');
                return;
            }
            startGame();
        }
        
        function startGame() {
            console.log('🎮 开始游戏，关卡ID:', selectedLevelId);
            
            if (selectedLevelId === null) {
                console.error('❌ 未选择关卡');
                return;
            }
            
            gameState = 'playing';
            
            // 创建或重置游戏实例
            if (!game) {
                game = new SnakeGame();
            } else {
                game.reset();
            }
            
            // 显示游戏界面
            showScreen(null);
            document.getElementById('gameUI').classList.add('active');
            document.getElementById('controls').classList.add('active');
            
            // 启动游戏循环
            if (gameLoop) {
                clearInterval(gameLoop);
            }
            
            function gameLoopFunction() {
                if (gameState === 'playing' && game) {
                    game.updatePowerups();
                    game.move();
                    game.draw();
                    
                    // 动态调整游戏循环速度
                    const currentSpeed = game.getCurrentSpeed();
                    if (currentSpeed !== game.speed) {
                        clearInterval(gameLoop);
                        gameLoop = setInterval(gameLoopFunction, currentSpeed);
                    }
                }
            }
            
            gameLoop = setInterval(gameLoopFunction, game.getCurrentSpeed());
            
            console.log('✅ 游戏开始成功');
        }
        
        function generateLevelButtons() {
            console.log('🔄 生成关卡按钮...');
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
                } else {
                    button.title = '完成前面的关卡才能解锁';
                }
                
                container.appendChild(button);
            });
            
            console.log('✅ 关卡按钮生成完成');
        }
        
        function pauseGame() {
            if (gameState === 'playing') {
                gameState = 'paused';
                showScreen('pauseScreen');
                console.log('⏸️ 游戏暂停');
            }
        }
        
        function resumeGame() {
            gameState = 'playing';
            showScreen(null);
            document.getElementById('gameUI').classList.add('active');
            document.getElementById('controls').classList.add('active');
            document.getElementById('effectIndicators').classList.add('active');
            console.log('▶️ 继续游戏');
        }
        
        function restartLevel() {
            console.log('🔄 重新开始关卡');
            startGame();
        }
        
        function quitToMenu() {
            console.log('🏠 退出到主菜单');
            showStartScreen();
        }
        
        function nextLevel() {
            if (selectedLevelId < levels.length - 1) {
                selectedLevelId++;
                console.log('➡️ 进入下一关:', selectedLevelId);
                startGame();
            } else {
                console.log('🎊 所有关卡完成！');
                showStartScreen();
            }
        }
        
        // 事件监听器设置
        function setupEventListeners() {
            console.log('🔧 设置事件监听器...');
            
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
            function setupControlButton(id, direction) {
                const button = document.getElementById(id);
                if (button) {
                    button.addEventListener('click', (e) => {
                        e.preventDefault();
                        if (game && gameState === 'playing') {
                            game.changeDirection(direction);
                        }
                    });
                } else {
                    console.error(`❌ 按钮未找到: ${id}`);
                }
            }
            
            setupControlButton('upBtn', {x: 0, y: -1});
            setupControlButton('downBtn', {x: 0, y: 1});
            setupControlButton('leftBtn', {x: -1, y: 0});
            setupControlButton('rightBtn', {x: 1, y: 0});
            
            // 暂停按钮
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
                
                // 防止过长时间的滑动
                if (deltaTime > 500) return;
                
                const minSwipeDistance = 30;
                const absDeltaX = Math.abs(deltaX);
                const absDeltaY = Math.abs(deltaY);
                
                if (absDeltaX > minSwipeDistance || absDeltaY > minSwipeDistance) {
                    if (absDeltaX > absDeltaY) {
                        // 水平滑动
                        game.changeDirection(deltaX > 0 ? {x: 1, y: 0} : {x: -1, y: 0});
                    } else {
                        // 垂直滑动
                        game.changeDirection(deltaY > 0 ? {x: 0, y: 1} : {x: 0, y: -1});
                    }
                }
            }, { passive: false });
            
            // 防止滚动
            document.addEventListener('touchmove', (e) => {
                if (e.target === canvas) {
                    e.preventDefault();
                }
            }, { passive: false });
            
            // 窗口大小调整
            let resizeTimeout;
            window.addEventListener('resize', () => {
                clearTimeout(resizeTimeout);
                resizeTimeout = setTimeout(() => {
                    console.log('📐 窗口大小调整');
                    resizeCanvas();
                    if (game && gameState === 'playing') {
                        game.reset();
                    }
                }, 100);
            });
            
            // 页面可见性变化
            document.addEventListener('visibilitychange', () => {
                if (document.hidden && gameState === 'playing') {
                    pauseGame();
                    console.log('⏸️ 页面不可见，自动暂停');
                }
            });
            
            console.log('✅ 事件监听器设置完成');
        }
        
        function resizeCanvas() {
            console.log('📐 调整画布大小...');
            const container = document.getElementById('gameContainer');
            const rect = container.getBoundingClientRect();
            
            // 设置画布尺寸
            canvas.width = rect.width;
            canvas.height = rect.height;
            
            // 设置像素比以支持高分辨率屏幕
            const pixelRatio = window.devicePixelRatio || 1;
            const displayWidth = canvas.clientWidth;
            const displayHeight = canvas.clientHeight;
            
            if (canvas.width !== displayWidth * pixelRatio ||
                canvas.height !== displayHeight * pixelRatio) {
                canvas.width = displayWidth * pixelRatio;
                canvas.height = displayHeight * pixelRatio;
                
                ctx.scale(pixelRatio, pixelRatio);
                canvas.style.width = displayWidth + 'px';
                canvas.style.height = displayHeight + 'px';
            }
            
            console.log(`✅ 画布大小: ${canvas.width}x${canvas.height} (显示: ${displayWidth}x${displayHeight})`);
        }
        
        // 初始化函数
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
            
            // 预加载提示
            console.log('🎮 贪食蛇大冒险已准备就绪！');
            console.log('📱 支持触摸滑动和虚拟按键控制');
            console.log('⌨️ 电脑用户可使用 WASD 或方向键');
            console.log('🎯 共有12个独特关卡等你挑战！');
        }
        
        // 错误处理
        window.addEventListener('error', (e) => {
            console.error('💥 游戏错误:', e.error);
            if (gameState === 'playing') {
                pauseGame();
            }
        });
        
        // 页面加载完成后初始化
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
        
        console.log('📜 贪食蛇大冒险脚本加载完成');
    </script>
</body>
</html>
