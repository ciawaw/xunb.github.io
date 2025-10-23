<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>寻宝大冒险 - VUE版</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #0f1b3a, #2a3f5f);
            color: #fff;
            min-height: 100vh;
            padding: 20px;
            overflow-x: hidden;
        }
        
        .app-container {
            max-width: 1200px;
            margin: 0 auto;
        }
        
        header {
            text-align: center;
            margin-bottom: 20px;
        }
        
        h1 {
            font-size: 2.8rem;
            margin-bottom: 10px;
            text-shadow: 0 2px 10px rgba(0, 0, 0, 0.7);
            background: linear-gradient(to right, #ffd700, #ffed4e, #ffd700);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: titleGlow 3s infinite alternate;
        }
        
        @keyframes titleGlow {
            0% { text-shadow: 0 0 5px rgba(255, 215, 0, 0.5); }
            100% { text-shadow: 0 0 20px rgba(255, 215, 0, 0.8), 0 0 30px rgba(255, 215, 0, 0.6); }
        }
        
        .subtitle {
            font-size: 1.2rem;
            opacity: 0.9;
            margin-bottom: 10px;
        }
        
        /* 导航栏样式 */
        .nav-tabs {
            display: flex;
            justify-content: center;
            margin-bottom: 20px;
            background: rgba(0, 0, 0, 0.6);
            border-radius: 10px;
            padding: 10px;
            flex-wrap: wrap;
        }
        
        .nav-tab {
            padding: 12px 25px;
            margin: 0 5px;
            border: none;
            border-radius: 30px;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .nav-tab.active {
            background: linear-gradient(to right, #FF8C00, #FFA500);
        }
        
        .nav-tab:hover {
            background: rgba(255, 255, 255, 0.2);
        }
        
        /* 页面容器 */
        .page-container {
            background: rgba(0, 0, 0, 0.6);
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            min-height: 500px;
        }
        
        /* 游戏画布容器 */
        .canvas-container {
            position: relative;
            width: 100%;
            height: 400px;
            border-radius: 10px;
            overflow: hidden;
            border: 3px solid #5d4a30;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
            background: #1a1a2e;
            margin-bottom: 20px;
        }
        
        #gameCanvas {
            width: 100%;
            height: 100%;
        }
        
        .scene-label {
            position: absolute;
            top: 10px;
            left: 10px;
            background: rgba(0, 0, 0, 0.7);
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 0.9rem;
        }
        
        /* 进度条 */
        .progress-container {
            width: 100%;
            height: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            margin: 20px 0;
            overflow: hidden;
        }
        
        .progress-bar {
            height: 100%;
            width: 0%;
            background: linear-gradient(to right, #4CAF50, #8BC34A, #CDDC39);
            border-radius: 10px;
            transition: width 0.8s cubic-bezier(0.22, 0.61, 0.36, 1);
            display: flex;
            align-items: center;
            justify-content: center;
            color: #000;
            font-weight: bold;
            font-size: 0.8rem;
        }
        
        /* 控制按钮 */
        .controls {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin: 20px 0;
            flex-wrap: wrap;
        }
        
        button {
            padding: 12px 25px;
            border: none;
            border-radius: 30px;
            background: linear-gradient(to right, #FF8C00, #FFA500);
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1rem;
            position: relative;
            overflow: hidden;
        }
        
        button:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(255, 165, 0, 0.4);
        }
        
        button:disabled {
            background: #666;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }
        
        /* 日志容器 */
        .log-container {
            background: rgba(0, 0, 0, 0.5);
            border-radius: 10px;
            padding: 15px;
            height: 150px;
            overflow-y: auto;
            margin-top: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            font-size: 0.9rem;
        }
        
        .log-entry {
            padding: 8px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            animation: fadeInUp 0.5s ease;
        }
        
        .log-entry.success {
            color: #90EE90;
        }
        
        .log-entry.error {
            color: #FF6B6B;
        }
        
        .log-entry.highlight {
            color: #FFD700;
            font-weight: bold;
        }
        
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        /* 统计信息 */
        .stats {
            display: flex;
            justify-content: space-around;
            width: 100%;
            margin: 15px 0;
            background: rgba(255, 255, 255, 0.05);
            padding: 10px;
            border-radius: 8px;
        }
        
        .stat-item {
            text-align: center;
        }
        
        .stat-value {
            font-size: 1.5rem;
            font-weight: bold;
            color: #FFD700;
        }
        
        .stat-label {
            font-size: 0.8rem;
            opacity: 0.8;
        }
        
        /* 宝藏展示 */
        .treasure-reveal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            opacity: 0;
            pointer-events: none;
            transition: opacity 1s ease;
        }
        
        .treasure-reveal.active {
            opacity: 1;
            pointer-events: all;
        }
        
        .treasure-content {
            text-align: center;
            animation: treasurePulse 2s infinite alternate;
        }
        
        .treasure-icon {
            font-size: 8rem;
            margin-bottom: 20px;
            filter: drop-shadow(0 0 20px gold);
        }
        
        .treasure-message {
            font-size: 2.5rem;
            margin-bottom: 30px;
            background: linear-gradient(to right, #FFD700, #FFA500, #FFD700);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        
        @keyframes treasurePulse {
            0% { transform: scale(1); }
            100% { transform: scale(1.05); }
        }
        
        /* 地图样式 */
        .map-container {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            margin: 20px 0;
        }
        
        .location-card {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 15px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }
        
        .location-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
            background: rgba(255, 255, 255, 0.15);
        }
        
        .location-icon {
            font-size: 3rem;
            margin-bottom: 10px;
        }
        
        .location-name {
            font-size: 1.2rem;
            font-weight: bold;
            margin-bottom: 5px;
        }
        
        .location-desc {
            font-size: 0.9rem;
            opacity: 0.8;
        }
        
        .location-completed {
            position: absolute;
            top: 10px;
            right: 10px;
            background: #4CAF50;
            color: white;
            border-radius: 50%;
            width: 25px;
            height: 25px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.8rem;
        }
        
        /* 用户管理样式 */
        .user-management {
            max-width: 600px;
            margin: 0 auto;
        }
        
        .user-form {
            background: rgba(255, 255, 255, 0.05);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        
        input, select {
            width: 100%;
            padding: 10px;
            border-radius: 5px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            background: rgba(0, 0, 0, 0.5);
            color: white;
        }
        
        .user-list {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 10px;
            padding: 20px;
        }
        
        .user-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .user-info {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: linear-gradient(to right, #FF8C00, #FFA500);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
        }
        
        /* 排行榜样式 */
        .leaderboard {
            max-width: 800px;
            margin: 0 auto;
        }
        
        .leaderboard-table {
            width: 100%;
            border-collapse: collapse;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 10px;
            overflow: hidden;
        }
        
        .leaderboard-table th, .leaderboard-table td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .leaderboard-table th {
            background: rgba(255, 165, 0, 0.2);
            font-weight: bold;
        }
        
        .leaderboard-table tr:last-child td {
            border-bottom: none;
        }
        
        .rank-1 {
            background: rgba(255, 215, 0, 0.2);
        }
        
        .rank-2 {
            background: rgba(192, 192, 192, 0.2);
        }
        
        .rank-3 {
            background: rgba(205, 127, 50, 0.2);
        }
        
        .rank-badge {
            display: inline-block;
            width: 25px;
            height: 25px;
            border-radius: 50%;
            background: #FFD700;
            color: black;
            text-align: center;
            line-height: 25px;
            font-weight: bold;
            margin-right: 10px;
        }
        
        /* 音乐控制 */
        .music-controls {
            position: fixed;
            bottom: 20px;
            right: 20px;
            display: flex;
            gap: 10px;
            z-index: 50;
        }
        
        .music-btn {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: rgba(0, 0, 0, 0.7);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .music-btn:hover {
            background: rgba(0, 0, 0, 0.9);
            transform: scale(1.1);
        }
        
        /* 响应式设计 */
        @media (max-width: 768px) {
            h1 {
                font-size: 2rem;
            }
            
            .canvas-container {
                height: 300px;
            }
            
            .treasure-message {
                font-size: 1.8rem;
            }
            
            .treasure-icon {
                font-size: 5rem;
            }
            
            .map-container {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .nav-tabs {
                flex-direction: column;
                align-items: center;
            }
            
            .nav-tab {
                margin: 5px 0;
                width: 80%;
            }
        }
        
        @media (max-width: 480px) {
            .map-container {
                grid-template-columns: 1fr;
            }
            
            .stats {
                flex-direction: column;
                gap: 10px;
            }
            
            .controls {
                flex-direction: column;
                align-items: center;
            }
            
            button {
                width: 80%;
            }
        }
    </style>
</head>
<body>
    <div id="app">
        <div class="app-container">
            <header>
                <h1>寻宝大冒险</h1>
                <p class="subtitle">跟随线索，寻找失落的宝藏！</p>
            </header>
            
            <nav class="nav-tabs">
                <button 
                    v-for="tab in tabs" 
                    :key="tab.id"
                    class="nav-tab" 
                    :class="{ active: activeTab === tab.id }"
                    @click="activeTab = tab.id"
                >
                    {{ tab.name }}
                </button>
            </nav>
            
            <div class="page-container">
                <!-- 游戏页面 -->
                <div v-if="activeTab === 'game'">
                    <game-canvas 
                        :current-scene="currentScene"
                        :scenes="scenes"
                        :adventurer="adventurer"
                        @move-adventurer="moveAdventurerTo"
                    ></game-canvas>
                    
                    <progress-bar :progress="progress"></progress-bar>
                    
                    <game-stats 
                        :step-count="gameState.currentStep"
                        :time-spent="timeSpent"
                        :treasure-value="gameState.treasureValue"
                    ></game-stats>
                    
                    <game-controls
                        :is-searching="gameState.isSearching"
                        :is-paused="gameState.isPaused"
                        @start="findTreasure"
                        @pause="togglePause"
                        @reset="resetGame"
                    ></game-controls>
                    
                    <game-log :logs="logs"></game-log>
                </div>
                
                <!-- 地图页面 -->
                <div v-if="activeTab === 'map'">
                    <h2 style="text-align: center; margin-bottom: 20px;">探索地点</h2>
                    <div class="map-container">
                        <location-card
                            v-for="location in locations"
                            :key="location.id"
                            :location="location"
                            @click="startGameFromLocation(location)"
                        ></location-card>
                    </div>
                </div>
                
                <!-- 用户管理页面 -->
                <div v-if="activeTab === 'users'">
                    <user-management
                        :users="users"
                        @add-user="addUser"
                        @delete-user="deleteUser"
                    ></user-management>
                </div>
                
                <!-- 排行榜页面 -->
                <div v-if="activeTab === 'leaderboard'">
                    <leaderboard :users="sortedUsers"></leaderboard>
                </div>
            </div>
            
            <!-- 宝藏展示 -->
            <treasure-reveal 
                :show="showTreasure"
                @close="hideTreasure"
            ></treasure-reveal>
            
            <!-- 音乐控制 -->
            <music-controls
                :music-enabled="gameState.musicEnabled"
                :music-volume="gameState.musicVolume"
                @toggle-music="toggleMusic"
                @volume-down="volumeDown"
                @volume-up="volumeUp"
            ></music-controls>
        </div>
    </div>

    <script>
        const { createApp, ref, reactive, computed, onMounted, watch } = Vue;
        
        // 工具函数
        function delay(ms) {
            return new Promise(resolve => setTimeout(resolve, ms));
        }
        
        // 宝藏地图API
        const TreasureMap = {
            async getInitialClue() {
                await delay(1000);
                return "在古老的图书馆里找到了第一个线索：一张泛黄的地图，上面标记着神秘的符号...";
            },
            
            async decodeAncientScript(clue) {
                if (!clue) {
                    throw new Error("没有线索可以解码!");
                }
                
                await delay(1500);
                return "解码成功!地图指向一座隐藏在丛林深处的古老神庙...";
            },
            
            async navigateToTemple(location) {
                if (!location) {
                    throw new Error("没有位置信息!");
                }
                
                await delay(1000);
                const random = Math.random();
                if (random < 0.2) {
                    throw new Error("糟糕!在丛林中迷路了，需要重新确定方向!");
                }
                
                return "成功穿越丛林，找到了神庙的入口!";
            },
            
            async searchTemple(location) {
                if (!location) {
                    throw new Error("没有位置信息!");
                }
                
                await delay(2000);
                const random = Math.random();
                if (random < 0.3) {
                    throw new Error("糟糕!遇到了神庙守卫，需要想办法绕过去!");
                }
                
                return "在神庙深处找到了一个神秘的箱子，上面有复杂的锁...";
            },
            
            async solvePuzzle() {
                await delay(1500);
                const random = Math.random();
                if (random < 0.2) {
                    throw new Error("宝箱的谜题太难了!需要更多时间思考...");
                }
                
                return "成功解开谜题!宝箱的锁打开了...";
            },
            
            async openTreasureBox() {
                await delay(1000);
                return "恭喜!你找到了传说中的宝藏——一颗闪耀的钻石和无数金币!";
            }
        };
        
        // 游戏画布组件
        const GameCanvas = {
            props: ['currentScene', 'scenes', 'adventurer'],
            emits: ['moveAdventurer'],
            template: `
                <div class="canvas-container">
                    <canvas ref="canvas"></canvas>
                    <div class="scene-label">场景: {{ scenes[currentScene].name }}</div>
                </div>
            `,
            mounted() {
                this.canvas = this.$refs.canvas;
                this.ctx = this.canvas.getContext('2d');
                this.resizeCanvas();
                window.addEventListener('resize', this.resizeCanvas);
                this.gameLoop();
            },
            beforeUnmount() {
                window.removeEventListener('resize', this.resizeCanvas);
            },
            methods: {
                resizeCanvas() {
                    const container = this.canvas.parentElement;
                    this.canvas.width = container.clientWidth;
                    this.canvas.height = container.clientHeight;
                    this.drawScene();
                },
                drawScene() {
                    const scene = this.scenes[this.currentScene];
                    
                    // 绘制背景
                    this.ctx.fillStyle = scene.color;
                    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
                    
                    // 绘制场景元素
                    scene.elements.forEach(element => {
                        this.ctx.fillStyle = element.color;
                        
                        if (element.type === "bookshelf") {
                            // 绘制书架
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                            
                            // 书架上的书
                            this.ctx.fillStyle = "#8B0000";
                            for (let i = 0; i < 5; i++) {
                                this.ctx.fillRect(element.x + 5, element.y + 10 + i * 35, element.width - 10, 25);
                            }
                        } else if (element.type === "table") {
                            // 绘制桌子
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                            
                            // 桌子腿
                            this.ctx.fillRect(element.x + 10, element.y + element.height, 10, 40);
                            this.ctx.fillRect(element.x + element.width - 20, element.y + element.height, 10, 40);
                        } else if (element.type === "lamp") {
                            // 绘制灯
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                            this.ctx.beginPath();
                            this.ctx.arc(element.x + element.width/2, element.y - 10, 20, 0, Math.PI * 2);
                            this.ctx.fillStyle = "#F7DC6F";
                            this.ctx.fill();
                        } else if (element.type === "scroll") {
                            // 绘制卷轴
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                            
                            // 卷轴细节
                            this.ctx.fillStyle = "#F9E79F";
                            this.ctx.fillRect(element.x + 10, element.y + 10, element.width - 20, element.height - 20);
                        } else if (element.type === "tree") {
                            // 绘制树
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                            
                            // 树冠
                            this.ctx.beginPath();
                            this.ctx.arc(element.x + element.width/2, element.y - 30, 50, 0, Math.PI * 2);
                            this.ctx.fillStyle = "#145A32";
                            this.ctx.fill();
                        } else if (element.type === "chest") {
                            // 绘制宝箱
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                            
                            // 宝箱细节
                            this.ctx.fillStyle = "#B7950B";
                            this.ctx.fillRect(element.x + 10, element.y + 10, element.width - 20, 10);
                            this.ctx.fillRect(element.x + 10, element.y + element.height - 20, element.width - 20, 10);
                            
                            // 宝箱锁
                            this.ctx.beginPath();
                            this.ctx.arc(element.x + element.width/2, element.y + element.height/2, 10, 0, Math.PI * 2);
                            this.ctx.fillStyle = "#7D6608";
                            this.ctx.fill();
                        } else {
                            // 默认绘制矩形
                            this.ctx.fillRect(element.x, element.y, element.width, element.height);
                        }
                    });
                    
                    // 绘制冒险者
                    this.ctx.fillStyle = this.adventurer.color;
                    this.ctx.fillRect(this.adventurer.x, this.adventurer.y, this.adventurer.width, this.adventurer.height);
                    
                    // 绘制冒险者头部
                    this.ctx.beginPath();
                    this.ctx.arc(this.adventurer.x + this.adventurer.width/2, this.adventurer.y - 10, 15, 0, Math.PI * 2);
                    this.ctx.fillStyle = "#FFEAA7";
                    this.ctx.fill();
                    
                    // 绘制移动目标点（调试用）
                    if (this.adventurer.moving) {
                        this.ctx.beginPath();
                        this.ctx.arc(this.adventurer.targetX, this.adventurer.targetY, 5, 0, Math.PI * 2);
                        this.ctx.fillStyle = "red";
                        this.ctx.fill();
                    }
                },
                updateAdventurer() {
                    if (!this.adventurer.moving) return;
                    
                    const dx = this.adventurer.targetX - this.adventurer.x;
                    const dy = this.adventurer.targetY - this.adventurer.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    
                    if (distance < this.adventurer.speed) {
                        this.adventurer.x = this.adventurer.targetX;
                        this.adventurer.y = this.adventurer.targetY;
                        this.adventurer.moving = false;
                    } else {
                        this.adventurer.x += (dx / distance) * this.adventurer.speed;
                        this.adventurer.y += (dy / distance) * this.adventurer.speed;
                    }
                },
                gameLoop() {
                    this.updateAdventurer();
                    this.drawScene();
                    requestAnimationFrame(this.gameLoop);
                }
            }
        };
        
        // 进度条组件
        const ProgressBar = {
            props: ['progress'],
            template: `
                <div class="progress-container">
                    <div class="progress-bar" :style="{ width: progress + '%' }">{{ progress }}%</div>
                </div>
            `
        };
        
        // 游戏统计组件
        const GameStats = {
            props: ['stepCount', 'timeSpent', 'treasureValue'],
            template: `
                <div class="stats">
                    <div class="stat-item">
                        <div class="stat-value">{{ stepCount }}</div>
                        <div class="stat-label">已完成步骤</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value">{{ timeSpent }}s</div>
                        <div class="stat-label">用时</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value">{{ treasureValue }}</div>
                        <div class="stat-label">宝藏价值</div>
                    </div>
                </div>
            `
        };
        
        // 游戏控制组件
        const GameControls = {
            props: ['isSearching', 'isPaused'],
            emits: ['start', 'pause', 'reset'],
            template: `
                <div class="controls">
                    <button @click="$emit('start')" :disabled="isSearching">开始寻宝</button>
                    <button @click="$emit('pause')" :disabled="!isSearching">{{ isPaused ? '继续' : '暂停' }}</button>
                    <button @click="$emit('reset')" :disabled="!isSearching">重新开始</button>
                </div>
            `
        };
        
        // 游戏日志组件
        const GameLog = {
            props: ['logs'],
            template: `
                <div class="log-container">
                    <div v-for="log in logs" :key="log.id" :class="'log-entry ' + log.type">
                        {{ log.message }}
                    </div>
                </div>
            `
        };
        
        // 地点卡片组件
        const LocationCard = {
            props: ['location'],
            template: `
                <div class="location-card">
                    <div class="location-icon">{{ location.icon }}</div>
                    <div class="location-name">{{ location.name }}</div>
                    <div class="location-desc">{{ location.description }}</div>
                    <div class="location-completed" v-if="location.completed">✓</div>
                </div>
            `
        };
        
        // 用户管理组件
        const UserManagement = {
            props: ['users'],
            emits: ['addUser', 'deleteUser'],
            data() {
                return {
                    newUser: {
                        name: '',
                        level: 1,
                        score: 0
                    }
                };
            },
            template: `
                <div class="user-management">
                    <h2 style="text-align: center; margin-bottom: 20px;">用户管理</h2>
                    
                    <div class="user-form">
                        <h3>添加新用户</h3>
                        <div class="form-group">
                            <label>用户名:</label>
                            <input type="text" v-model="newUser.name" placeholder="输入用户名">
                        </div>
                        <div class="form-group">
                            <label>等级:</label>
                            <select v-model="newUser.level">
                                <option value="1">初级寻宝者</option>
                                <option value="2">中级寻宝者</option>
                                <option value="3">高级寻宝者</option>
                            </select>
                        </div>
                        <button @click="addUser">添加用户</button>
                    </div>
                    
                    <div class="user-list">
                        <h3>用户列表</h3>
                        <div v-for="user in users" :key="user.id" class="user-item">
                            <div class="user-info">
                                <div class="user-avatar">{{ user.name.charAt(0) }}</div>
                                <div>
                                    <div>{{ user.name }}</div>
                                    <div style="font-size: 0.8rem; opacity: 0.7;">
                                        等级: {{ user.level }} | 分数: {{ user.score }}
                                    </div>
                                </div>
                            </div>
                            <button @click="deleteUser(user.id)" style="padding: 5px 10px; font-size: 0.8rem;">删除</button>
                        </div>
                    </div>
                </div>
            `,
            methods: {
                addUser() {
                    if (this.newUser.name.trim()) {
                        this.$emit('addUser', { ...this.newUser });
                        this.newUser.name = '';
                        this.newUser.level = 1;
                    }
                },
                deleteUser(userId) {
                    this.$emit('deleteUser', userId);
                }
            }
        };
        
        // 排行榜组件
        const Leaderboard = {
            props: ['users'],
            template: `
                <div class="leaderboard">
                    <h2 style="text-align: center; margin-bottom: 20px;">寻宝排行榜</h2>
                    
                    <table class="leaderboard-table">
                        <thead>
                            <tr>
                                <th>排名</th>
                                <th>用户</th>
                                <th>等级</th>
                                <th>分数</th>
                                <th>完成时间</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(user, index) in users" :key="user.id" :class="'rank-' + (index + 1)">
                                <td>
                                    <span v-if="index < 3" class="rank-badge">{{ index + 1 }}</span>
                                    <span v-else>{{ index + 1 }}</span>
                                </td>
                                <td>
                                    <div class="user-info">
                                        <div class="user-avatar">{{ user.name.charAt(0) }}</div>
                                        <div>{{ user.name }}</div>
                                    </div>
                                </td>
                                <td>{{ user.level }}</td>
                                <td>{{ user.score }}</td>
                                <td>{{ user.completionTime || '未完成' }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            `
        };
        
        // 宝藏展示组件
        const TreasureReveal = {
            props: ['show'],
            emits: ['close'],
            template: `
                <div class="treasure-reveal" :class="{ active: show }">
                    <div class="treasure-content">
                        <div class="treasure-icon">💎</div>
                        <div class="treasure-message">恭喜！你找到了传说中的宝藏！</div>
                        <button @click="$emit('close')">继续冒险</button>
                    </div>
                </div>
            `
        };
        
        // 音乐控制组件
        const MusicControls = {
            props: ['musicEnabled', 'musicVolume'],
            emits: ['toggleMusic', 'volumeDown', 'volumeUp'],
            template: `
                <div class="music-controls">
                    <div class="music-btn" @click="$emit('toggleMusic')">
                        <span>{{ musicEnabled ? '🔊' : '🔇' }}</span>
                    </div>
                    <div class="music-btn" @click="$emit('volumeDown')">🔉</div>
                    <div class="music-btn" @click="$emit('volumeUp')">🔊</div>
                </div>
            `
        };
        
        // 创建Vue应用
        createApp({
            components: {
                GameCanvas,
                ProgressBar,
                GameStats,
                GameControls,
                GameLog,
                LocationCard,
                UserManagement,
                Leaderboard,
                TreasureReveal,
                MusicControls
            },
            setup() {
                // 游戏状态
                const gameState = reactive({
                    currentStep: 0,
                    totalSteps: 6,
                    isSearching: false,
                    isPaused: false,
                    startTime: null,
                    treasureValue: 0,
                    completedLocations: [],
                    currentLocation: null,
                    musicEnabled: true,
                    musicVolume: 0.5
                });
                
                // 标签页
                const tabs = [
                    { id: 'game', name: '寻宝游戏' },
                    { id: 'map', name: '全景地图' },
                    { id: 'users', name: '用户管理' },
                    { id: 'leaderboard', name: '排行榜' }
                ];
                const activeTab = ref('game');
                
                // 游戏数据
                const currentScene = ref(0);
                const progress = ref(0);
                const showTreasure = ref(false);
                const logs = ref([{ id: 1, message: '寻宝日志开始...点击"开始寻宝"按钮启动冒险！', type: '' }]);
                const timeSpent = ref(0);
                
                // 冒险者
                const adventurer = reactive({
                    x: 50,
                    y: 200,
                    width: 40,
                    height: 60,
                    color: '#3498db',
                    targetX: 50,
                    targetY: 200,
                    speed: 2,
                    moving: false
                });
                
                // 场景数据
                const scenes = ref([
                    { name: "图书馆", color: "#8B4513", elements: [] },
                    { name: "解码室", color: "#2C3E50", elements: [] },
                    { name: "丛林", color: "#27AE60", elements: [] },
                    { name: "神庙入口", color: "#E67E22", elements: [] },
                    { name: "神庙内部", color: "#7D3C98", elements: [] },
                    { name: "宝藏室", color: "#F1C40F", elements: [] }
                ]);
                
                // 地点数据
                const locations = ref([
                    {
                        id: 'library',
                        name: '古老图书馆',
                        icon: '📚',
                        description: '寻找初始线索的神秘图书馆',
                        backgroundMusic: 'library_music.mp3',
                        completed: false
                    },
                    {
                        id: 'decoding-room',
                        name: '解码室',
                        icon: '🔍',
                        description: '分析古代符号和地图',
                        backgroundMusic: 'decoding_music.mp3',
                        completed: false
                    },
                    {
                        id: 'jungle',
                        name: '神秘丛林',
                        icon: '🌴',
                        description: '穿越危险的丛林寻找神庙',
                        backgroundMusic: 'jungle_music.mp3',
                        completed: false
                    },
                    {
                        id: 'temple-entrance',
                        name: '神庙入口',
                        icon: '🏛️',
                        description: '古老神庙的神秘入口',
                        backgroundMusic: 'temple_music.mp3',
                        completed: false
                    },
                    {
                        id: 'temple-interior',
                        name: '神庙内部',
                        icon: '🕍',
                        description: '探索神庙深处的秘密',
                        backgroundMusic: 'temple_music.mp3',
                        completed: false
                    },
                    {
                        id: 'treasure-room',
                        name: '宝藏室',
                        icon: '💎',
                        description: '传说中的宝藏所在地',
                        backgroundMusic: 'treasure_music.mp3',
                        completed: false
                    }
                ]);
                
                // 用户数据
                const users = ref([
                    { id: 1, name: '冒险者小明', level: 3, score: 9500, completionTime: '45分30秒' },
                    { id: 2, name: '宝藏猎人小红', level: 2, score: 7800, completionTime: '52分15秒' },
                    { id: 3, name: '探险家小李', level: 1, score: 6200, completionTime: '1小时05分' },
                    { id: 4, name: '寻宝新手小王', level: 1, score: 4500, completionTime: '未完成' }
                ]);
                
                // 计算属性
                const sortedUsers = computed(() => {
                    return [...users.value].sort((a, b) => b.score - a.score);
                });
                
                // 初始化场景元素
                function initSceneElements() {
                    // 图书馆场景
                    scenes.value[0].elements = [
                        { type: "bookshelf", x: 700, y: 100, width: 80, height: 200, color: "#8B4513" },
                        { type: "table", x: 350, y: 250, width: 120, height: 20, color: "#A0522D" },
                        { type: "lamp", x: 100, y: 80, width: 10, height: 80, color: "#D4AC0D" }
                    ];
                    
                    // 解码室场景
                    scenes.value[1].elements = [
                        { type: "scroll", x: 400, y: 140, width: 80, height: 120, color: "#F7DC6F" },
                        { type: "desk", x: 650, y: 200, width: 120, height: 20, color: "#A0522D" },
                        { type: "symbols", x: 100, y: 100, width: 120, height: 120, color: "#1B4F72" }
                    ];
                    
                    // 丛林场景
                    scenes.value[2].elements = [
                        { type: "tree", x: 650, y: 100, width: 60, height: 200, color: "#27AE60" },
                        { type: "tree", x: 200, y: 150, width: 50, height: 180, color: "#229954" },
                        { type: "bush", x: 450, y: 200, width: 120, height: 60, color: "#145A32" }
                    ];
                    
                    // 神庙入口场景
                    scenes.value[3].elements = [
                        { type: "pillar", x: 100, y: 50, width: 30, height: 200, color: "#BA4A00" },
                        { type: "pillar", x: 670, y: 50, width: 30, height: 200, color: "#BA4A00" },
                        { type: "arch", x: 300, y: 80, width: 200, height: 80, color: "#A04000" }
                    ];
                    
                    // 神庙内部场景
                    scenes.value[4].elements = [
                        { type: "altar", x: 320, y: 160, width: 160, height: 80, color: "#7D3C98" },
                        { type: "torch", x: 80, y: 100, width: 10, height: 60, color: "#F39C12" },
                        { type: "torch", x: 710, y: 100, width: 10, height: 60, color: "#F39C12" }
                    ];
                    
                    // 宝藏室场景
                    scenes.value[5].elements = [
                        { type: "chest", x: 340, y: 160, width: 120, height: 80, color: "#D4AC0D" },
                        { type: "coins", x: 300, y: 260, width: 200, height: 40, color: "#F1C40F" },
                        { type: "gem", x: 380, y: 60, width: 40, height: 40, color: "#9B59B6" }
                    ];
                }
                
                // 移动冒险者
                function moveAdventurerTo(x, y) {
                    adventurer.targetX = x;
                    adventurer.targetY = y;
                    adventurer.moving = true;
                }
                
                // 切换场景
                function changeScene(sceneIndex) {
                    currentScene.value = sceneIndex;
                    
                    // 根据场景设置冒险者初始位置
                    switch(sceneIndex) {
                        case 0: // 图书馆
                            adventurer.x = 50;
                            adventurer.y = 200;
                            break;
                        case 1: // 解码室
                            adventurer.x = 50;
                            adventurer.y = 200;
                            break;
                        case 2: // 丛林
                            adventurer.x = 50;
                            adventurer.y = 200;
                            break;
                        case 3: // 神庙入口
                            adventurer.x = 50;
                            adventurer.y = 200;
                            break;
                        case 4: // 神庙内部
                            adventurer.x = 50;
                            adventurer.y = 200;
                            break;
                        case 5: // 宝藏室
                            adventurer.x = 50;
                            adventurer.y = 200;
                            break;
                    }
                    
                    adventurer.moving = false;
                }
                
                // 添加日志
                function addLogEntry(message, type = '') {
                    logs.value.push({
                        id: Date.now(),
                        message: `[${new Date().toLocaleTimeString()}] ${message}`,
                        type
                    });
                    
                    // 限制日志数量
                    if (logs.value.length > 20) {
                        logs.value.shift();
                    }
                }
                
                // 更新进度
                function updateProgress(percentage) {
                    progress.value = percentage;
                }
                
                // 更新用时
                function updateTimeSpent() {
                    if (gameState.startTime) {
                        timeSpent.value = Math.floor((Date.now() - gameState.startTime) / 1000);
                    }
                }
                
                // 显示宝藏
                function showTreasureFunc() {
                    showTreasure.value = true;
                    gameState.treasureValue = 10000;
                    
                    // 更新用户分数
                    if (users.value.length > 0) {
                        users.value[0].score = Math.max(users.value[0].score, gameState.treasureValue);
                        users.value[0].completionTime = `${timeSpent.value}秒`;
                    }
                }
                
                // 隐藏宝藏
                function hideTreasure() {
                    showTreasure.value = false;
                }
                
                // 重置游戏
                function resetGame() {
                    gameState.currentStep = 0;
                    gameState.isSearching = false;
                    gameState.isPaused = false;
                    gameState.startTime = null;
                    gameState.treasureValue = 0;
                    
                    updateProgress(0);
                    changeScene(0);
                    logs.value = [{ id: 1, message: '寻宝日志开始...点击"开始寻宝"按钮启动冒险！', type: '' }];
                    timeSpent.value = 0;
                    
                    hideTreasure();
                }
                
                // 标记地点完成
                function markLocationCompleted(locationId) {
                    const location = locations.value.find(loc => loc.id === locationId);
                    if (location && !location.completed) {
                        location.completed = true;
                        gameState.completedLocations.push(locationId);
                    }
                }
                
                // 从特定地点开始游戏
                function startGameFromLocation(location) {
                    activeTab.value = 'game';
                    resetGame();
                    
                    // 根据地点设置初始状态
                    switch(location.id) {
                        case 'library':
                            // 默认从图书馆开始
                            break;
                        case 'decoding-room':
                            gameState.currentStep = 1;
                            break;
                        case 'jungle':
                            gameState.currentStep = 2;
                            break;
                        case 'temple-entrance':
                            gameState.currentStep = 3;
                            break;
                        case 'temple-interior':
                            gameState.currentStep = 4;
                            break;
                        case 'treasure-room':
                            gameState.currentStep = 5;
                            break;
                    }
                    
                    // 开始寻宝
                    setTimeout(() => {
                        findTreasure();
                    }, 500);
                }
                
                // 寻宝流程
                async function findTreasure() {
                    if (gameState.isSearching) return;
                    
                    gameState.isSearching = true;
                    gameState.startTime = Date.now();
                    
                    // 启动计时器
                    const timer = setInterval(updateTimeSpent, 1000);
                    
                    try {
                        // 步骤1: 获取初始线索
                        addLogEntry('前往图书馆寻找线索...');
                        changeScene(0);
                        moveAdventurerTo(650, 200);
                        
                        await delay(2000);
                        const clue = await TreasureMap.getInitialClue();
                        addLogEntry(clue, 'success');
                        gameState.currentStep = 1;
                        updateProgress(17);
                        
                        // 标记图书馆为已完成
                        markLocationCompleted('library');
                        
                        // 步骤2: 解码古文字
                        addLogEntry('前往解码室分析线索...', 'highlight');
                        changeScene(1);
                        moveAdventurerTo(440, 200);
                        
                        await delay(2000);
                        const decodedLocation = await TreasureMap.decodeAncientScript(clue);
                        addLogEntry(decodedLocation, 'success');
                        gameState.currentStep = 2;
                        updateProgress(33);
                        
                        // 标记解码室为已完成
                        markLocationCompleted('decoding-room');
                        
                        // 步骤3: 穿越丛林
                        addLogEntry('穿越丛林，寻找神庙...', 'highlight');
                        changeScene(2);
                        moveAdventurerTo(600, 200);
                        
                        await delay(2000);
                        const navigationResult = await TreasureMap.navigateToTemple(decodedLocation);
                        addLogEntry(navigationResult, 'success');
                        gameState.currentStep = 3;
                        updateProgress(50);
                        
                        // 标记丛林为已完成
                        markLocationCompleted('jungle');
                        
                        // 步骤4: 进入神庙
                        addLogEntry('进入神庙，探索内部...', 'highlight');
                        changeScene(3);
                        moveAdventurerTo(400, 200);
                        
                        await delay(1500);
                        changeScene(4);
                        moveAdventurerTo(400, 200);
                        
                        await delay(2000);
                        const searchResult = await TreasureMap.searchTemple(decodedLocation);
                        addLogEntry(searchResult, 'success');
                        gameState.currentStep = 4;
                        updateProgress(67);
                        
                        // 标记神庙入口和内部为已完成
                        markLocationCompleted('temple-entrance');
                        markLocationCompleted('temple-interior');
                        
                        // 步骤5: 解开谜题
                        addLogEntry('尝试解开宝箱的谜题...', 'highlight');
                        changeScene(5);
                        moveAdventurerTo(400, 200);
                        
                        await delay(2000);
                        const puzzleResult = await TreasureMap.solvePuzzle();
                        addLogEntry(puzzleResult, 'success');
                        gameState.currentStep = 5;
                        updateProgress(83);
                        
                        // 步骤6: 打开宝箱
                        addLogEntry('打开宝箱，获取宝藏...', 'highlight');
                        await delay(1500);
                        const treasureResult = await TreasureMap.openTreasureBox();
                        addLogEntry(treasureResult, 'success');
                        gameState.currentStep = 6;
                        updateProgress(100);
                        
                        // 标记宝藏室为已完成
                        markLocationCompleted('treasure-room');
                        
                        // 显示宝藏
                        showTreasureFunc();
                        addLogEntry('寻宝任务完成!', 'highlight');
                        
                    } catch (error) {
                        addLogEntry(error.message, 'error');
                        addLogEntry('寻宝失败，重新尝试...', 'highlight');
                    } finally {
                        gameState.isSearching = false;
                        clearInterval(timer);
                    }
                }
                
                // 暂停/继续游戏
                function togglePause() {
                    gameState.isPaused = !gameState.isPaused;
                }
                
                // 用户管理
                function addUser(user) {
                    users.value.push({
                        id: Date.now(),
                        ...user,
                        score: 0,
                        completionTime: '未完成'
                    });
                }
                
                function deleteUser(userId) {
                    users.value = users.value.filter(user => user.id !== userId);
                }
                
                // 音乐控制
                function toggleMusic() {
                    gameState.musicEnabled = !gameState.musicEnabled;
                }
                
                function volumeDown() {
                    gameState.musicVolume = Math.max(0, gameState.musicVolume - 0.1);
                }
                
                function volumeUp() {
                    gameState.musicVolume = Math.min(1, gameState.musicVolume + 0.1);
                }
                
                // 初始化
                onMounted(() => {
                    initSceneElements();
                    changeScene(0);
                    
                    // 加载保存的游戏状态
                    const savedState = localStorage.getItem('treasureHuntVueState');
                    if (savedState) {
                        const state = JSON.parse(savedState);
                        Object.assign(gameState, state.gameState);
                        
                        // 更新地点完成状态
                        if (state.completedLocations) {
                            locations.value.forEach(location => {
                                if (state.completedLocations.includes(location.id)) {
                                    location.completed = true;
                                }
                            });
                        }
                        
                        updateProgress(Math.floor((gameState.currentStep / gameState.totalSteps) * 100));
                    }
                });
                
                // 保存游戏状态
                watch([gameState, locations], () => {
                    const stateToSave = {
                        gameState: {
                            currentStep: gameState.currentStep,
                            treasureValue: gameState.treasureValue,
                            musicEnabled: gameState.musicEnabled,
                            musicVolume: gameState.musicVolume
                        },
                        completedLocations: locations.value
                            .filter(loc => loc.completed)
                            .map(loc => loc.id)
                    };
                    
                    localStorage.setItem('treasureHuntVueState', JSON.stringify(stateToSave));
                }, { deep: true });
                
                return {
                    tabs,
                    activeTab,
                    gameState,
                    currentScene,
                    progress,
                    showTreasure,
                    logs,
                    timeSpent,
                    adventurer,
                    scenes,
                    locations,
                    users,
                    sortedUsers,
                    moveAdventurerTo,
                    findTreasure,
                    togglePause,
                    resetGame,
                    hideTreasure,
                    startGameFromLocation,
                    addUser,
                    deleteUser,
                    toggleMusic,
                    volumeDown,
                    volumeUp
                };
            }
        }).mount('#app');
    </script>
</body>
</html>
