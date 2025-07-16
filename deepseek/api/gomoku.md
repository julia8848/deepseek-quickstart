# 五子棋游戏

我将为您创建一个完整的五子棋游戏，所有代码都在一个HTML文件中。这个游戏包含棋盘绘制、黑白棋交替落子、胜负判断和游戏重置功能。

## 设计思路
- 使用Canvas绘制棋盘和棋子
- 实现鼠标点击落子功能
- 设计简洁优雅的游戏界面
- 添加游戏状态显示和控制面板
- 实现胜负判断算法

下面是完整的实现代码：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>五子棋游戏</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Microsoft YaHei', sans-serif;
        }
        
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #1a2a6c);
            padding: 20px;
        }
        
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(255, 255, 255, 0.2);
            max-width: 800px;
            width: 100%;
        }
        
        h1 {
            color: #fff;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.7);
            margin-bottom: 20px;
            font-size: 2.5rem;
        }
        
        .game-area {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 30px;
            width: 100%;
        }
        
        .board-container {
            position: relative;
            background: #e8c48a;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.4);
        }
        
        canvas {
            display: block;
            background: #e8c48a;
            border: 2px solid #8b4513;
            border-radius: 5px;
            cursor: pointer;
            box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.2);
        }
        
        .game-info {
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            padding: 25px;
            width: 300px;
            color: white;
        }
        
        .status {
            text-align: center;
            padding: 15px;
            background: rgba(0, 0, 0, 0.4);
            border-radius: 10px;
            margin-bottom: 20px;
            font-size: 1.3rem;
            font-weight: bold;
            min-height: 70px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .player-turn {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin: 20px 0;
            padding: 10px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
        }
        
        .player {
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 10px;
            border-radius: 8px;
            width: 45%;
        }
        
        .player.active {
            background: rgba(255, 255, 255, 0.2);
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.4);
        }
        
        .player-icon {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            margin-bottom: 8px;
        }
        
        .black .player-icon {
            background: radial-gradient(circle at 30% 30%, #666, #000);
            box-shadow: 0 0 8px rgba(0, 0, 0, 0.8);
        }
        
        .white .player-icon {
            background: radial-gradient(circle at 30% 30%, #fff, #ddd);
            box-shadow: 0 0 8px rgba(255, 255, 255, 0.8);
        }
        
        .controls {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        
        button {
            padding: 12px;
            border: none;
            border-radius: 8px;
            background: linear-gradient(to right, #4a00e0, #8e2de2);
            color: white;
            font-size: 1.1rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }
        
        button:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
            background: linear-gradient(to right, #8e2de2, #4a00e0);
        }
        
        button:active {
            transform: translateY(1px);
        }
        
        .game-rules {
            margin-top: 25px;
            background: rgba(0, 0, 0, 0.3);
            padding: 20px;
            border-radius: 15px;
            width: 100%;
        }
        
        .game-rules h2 {
            color: #fff;
            margin-bottom: 15px;
            text-align: center;
        }
        
        .rules-list {
            color: #ddd;
            padding-left: 20px;
        }
        
        .rules-list li {
            margin-bottom: 10px;
            line-height: 1.5;
        }
        
        @media (max-width: 768px) {
            .game-area {
                flex-direction: column;
                align-items: center;
            }
            
            .game-info {
                width: 100%;
                max-width: 500px;
            }
        }
        
        .winning-message {
            font-size: 1.8rem;
            text-shadow: 0 0 10px rgba(255, 215, 0, 0.8);
            color: gold;
            animation: pulse 1.5s infinite;
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>五子棋游戏</h1>
        
        <div class="game-area">
            <div class="board-container">
                <canvas id="chessboard" width="500" height="500"></canvas>
            </div>
            
            <div class="game-info">
                <div class="status" id="status">黑方先行</div>
                
                <div class="player-turn">
                    <div class="player black active">
                        <div class="player-icon"></div>
                        <div>黑棋</div>
                    </div>
                    <div class="player white">
                        <div class="player-icon"></div>
                        <div>白棋</div>
                    </div>
                </div>
                
                <div class="controls">
                    <button id="restartBtn">重新开始</button>
                    <button id="undoBtn">悔棋</button>
                    <button id="hintBtn">提示</button>
                </div>
            </div>
        </div>
        
        <div class="game-rules">
            <h2>游戏规则</h2>
            <ul class="rules-list">
                <li>黑棋先行，双方交替在棋盘上放置棋子</li>
                <li>先在横向、纵向或对角线方向连成五个或以上连续同色棋子的一方获胜</li>
                <li>棋子放置在棋盘的交叉点上，不可移动或移除</li>
                <li>棋盘大小为15×15，共225个落子点</li>
            </ul>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const canvas = document.getElementById('chessboard');
            const ctx = canvas.getContext('2d');
            const statusDisplay = document.getElementById('status');
            const restartBtn = document.getElementById('restartBtn');
            const undoBtn = document.getElementById('undoBtn');
            const hintBtn = document.getElementById('hintBtn');
            const blackPlayer = document.querySelector('.player.black');
            const whitePlayer = document.querySelector('.player.white');
            
            // 游戏常量
            const BOARD_SIZE = 15;
            const CELL_SIZE = canvas.width / (BOARD_SIZE + 1);
            const STONE_RADIUS = CELL_SIZE * 0.4;
            
            // 游戏状态
            let board = Array(BOARD_SIZE).fill().map(() => Array(BOARD_SIZE).fill(0));
            let currentPlayer = 1; // 1: 黑棋, 2: 白棋
            let gameActive = true;
            let moveHistory = [];
            
            // 绘制棋盘
            function drawBoard() {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                
                // 绘制棋盘背景
                ctx.fillStyle = '#e8c48a';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                // 绘制网格线
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 1;
                
                for (let i = 0; i < BOARD_SIZE; i++) {
                    // 横线
                    ctx.beginPath();
                    ctx.moveTo(CELL_SIZE, CELL_SIZE * (i + 1));
                    ctx.lineTo(canvas.width - CELL_SIZE, CELL_SIZE * (i + 1));
                    ctx.stroke();
                    
                    // 竖线
                    ctx.beginPath();
                    ctx.moveTo(CELL_SIZE * (i + 1), CELL_SIZE);
                    ctx.lineTo(CELL_SIZE * (i + 1), canvas.height - CELL_SIZE);
                    ctx.stroke();
                }
                
                // 绘制天元和星位
                const starPoints = [3, 7, 11];
                ctx.fillStyle = '#000';
                
                for (let i = 0; i < starPoints.length; i++) {
                    for (let j = 0; j < starPoints.length; j++) {
                        const x = CELL_SIZE * (starPoints[i] + 1);
                        const y = CELL_SIZE * (starPoints[j] + 1);
                        
                        ctx.beginPath();
                        ctx.arc(x, y, 4, 0, Math.PI * 2);
                        ctx.fill();
                    }
                }
                
                // 绘制棋子
                for (let row = 0; row < BOARD_SIZE; row++) {
                    for (let col = 0; col < BOARD_SIZE; col++) {
                        if (board[row][col] !== 0) {
                            drawStone(col, row, board[row][col]);
                        }
                    }
                }
            }
            
            // 绘制棋子
            function drawStone(x, y, player) {
                const centerX = CELL_SIZE * (x + 1);
                const centerY = CELL_SIZE * (y + 1);
                
                ctx.beginPath();
                ctx.arc(centerX, centerY, STONE_RADIUS, 0, Math.PI * 2);
                
                if (player === 1) { // 黑棋
                    const gradient = ctx.createRadialGradient(
                        centerX - 3, centerY - 3, 1,
                        centerX, centerY, STONE_RADIUS
                    );
                    gradient.addColorStop(0, '#666');
                    gradient.addColorStop(1, '#000');
                    ctx.fillStyle = gradient;
                } else { // 白棋
                    const gradient = ctx.createRadialGradient(
                        centerX - 2, centerY - 2, 1,
                        centerX, centerY, STONE_RADIUS
                    );
                    gradient.addColorStop(0, '#fff');
                    gradient.addColorStop(1, '#ddd');
                    ctx.fillStyle = gradient;
                }
                
                ctx.fill();
                ctx.strokeStyle = '#333';
                ctx.lineWidth = 1;
                ctx.stroke();
            }
            
            // 处理落子
            function placeStone(event) {
                if (!gameActive) return;
                
                const rect = canvas.getBoundingClientRect();
                const x = event.clientX - rect.left;
                const y = event.clientY - rect.top;
                
                // 计算棋盘坐标
                const col = Math.round((x - CELL_SIZE) / CELL_SIZE);
                const row = Math.round((y - CELL_SIZE) / CELL_SIZE);
                
                // 检查是否在有效范围内
                if (row >= 0 && row < BOARD_SIZE && col >= 0 && col < BOARD_SIZE && board[row][col] === 0) {
                    // 保存历史记录
                    moveHistory.push({row, col, player: currentPlayer});
                    
                    // 落子
                    board[row][col] = currentPlayer;
                    drawBoard();
                    
                    // 检查是否获胜
                    if (checkWin(row, col)) {
                        gameActive = false;
                        const winner = currentPlayer === 1 ? '黑棋' : '白棋';
                        statusDisplay.innerHTML = `<span class="winning-message">${winner}获胜！</span>`;
                        return;
                    }
                    
                    // 切换玩家
                    currentPlayer = currentPlayer === 1 ? 2 : 1;
                    updateStatus();
                }
            }
            
            // 更新游戏状态显示
            function updateStatus() {
                const player = currentPlayer === 1 ? '黑棋' : '白棋';
                statusDisplay.textContent = `${player}回合`;
                
                // 更新活动玩家指示
                if (currentPlayer === 1) {
                    blackPlayer.classList.add('active');
                    whitePlayer.classList.remove('active');
                } else {
                    whitePlayer.classList.add('active');
                    blackPlayer.classList.remove('active');
                }
            }
            
            // 检查获胜条件
            function checkWin(row, col) {
                const player = board[row][col];
                const directions = [
                    [0, 1],  // 水平
                    [1, 0],  // 垂直
                    [1, 1],  // 右下
                    [1, -1]  // 右上
                ];
                
                for (const [dx, dy] of directions) {
                    let count = 1;
                    
                    // 正向检查
                    for (let i = 1; i <= 4; i++) {
                        const newRow = row + dx * i;
                        const newCol = col + dy * i;
                        
                        if (newRow >= 0 && newRow < BOARD_SIZE && 
                            newCol >= 0 && newCol < BOARD_SIZE && 
                            board[newRow][newCol] === player) {
                            count++;
                        } else {
                            break;
                        }
                    }
                    
                    // 反向检查
                    for (let i = 1; i <= 4; i++) {
                        const newRow = row - dx * i;
                        const newCol = col - dy * i;
                        
                        if (newRow >= 0 && newRow < BOARD_SIZE && 
                            newCol >= 0 && newCol < BOARD_SIZE && 
                            board[newRow][newCol] === player) {
                            count++;
                        } else {
                            break;
                        }
                    }
                    
                    if (count >= 5) {
                        return true;
                    }
                }
                
                return false;
            }
            
            // 重新开始游戏
            function restartGame() {
                board = Array(BOARD_SIZE).fill().map(() => Array(BOARD_SIZE).fill(0));
                currentPlayer = 1;
                gameActive = true;
                moveHistory = [];
                drawBoard();
                updateStatus();
            }
            
            // 悔棋
            function undoMove() {
                if (moveHistory.length === 0 || !gameActive) return;
                
                // 移除最后一步
                const lastMove = moveHistory.pop();
                board[lastMove.row][lastMove.col] = 0;
                
                // 如果不是第一步，切换玩家
                if (moveHistory.length > 0) {
                    currentPlayer = moveHistory[moveHistory.length - 1].player === 1 ? 2 : 1;
                } else {
                    currentPlayer = 1;
                }
                
                drawBoard();
                updateStatus();
            }
            
            // 提示功能
            function showHint() {
                if (!gameActive) return;
                
                // 简单的提示：在棋盘上随机选择一个空位
                const emptyCells = [];
                for (let row = 0; row < BOARD_SIZE; row++) {
                    for (let col = 0; col < BOARD_SIZE; col++) {
                        if (board[row][col] === 0) {
                            emptyCells.push({row, col});
                        }
                    }
                }
                
                if (emptyCells.length > 0) {
                    const randomIndex = Math.floor(Math.random() * emptyCells.length);
                    const hintCell = emptyCells[randomIndex];
                    
                    // 绘制提示标记
                    const centerX = CELL_SIZE * (hintCell.col + 1);
                    const centerY = CELL_SIZE * (hintCell.row + 1);
                    
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, STONE_RADIUS * 0.4, 0, Math.PI * 2);
                    ctx.fillStyle = currentPlayer === 1 ? 'rgba(0, 0, 0, 0.5)' : 'rgba(255, 255, 255, 0.5)';
                    ctx.fill();
                    
                    // 3秒后移除提示
                    setTimeout(drawBoard, 1000);
                }
            }
            
            // 事件监听
            canvas.addEventListener('click', placeStone);
            restartBtn.addEventListener('click', restartGame);
            undoBtn.addEventListener('click', undoMove);
            hintBtn.addEventListener('click', showHint);
            
            // 初始化游戏
            drawBoard();
            updateStatus();
        });
    </script>
</body>
</html>
```

## 游戏功能说明

1. **棋盘设计**：
   - 15×15标准五子棋棋盘
   - 天元和星位标记
   - 木质纹理棋盘背景

2. **游戏功能**：
   - 黑白棋交替落子（黑棋先行）
   - 实时显示当前玩家
   - 胜负判定（横向、纵向、对角线五连）
   - 获胜时显示胜利信息

3. **控制面板**：
   - 重新开始按钮：重置游戏
   - 悔棋按钮：撤销上一步操作
   - 提示按钮：随机显示一个推荐落子位置

4. **游戏规则说明**：
   - 清晰展示五子棋基本规则
   - 帮助新玩家快速了解玩法

5. **响应式设计**：
   - 适配不同屏幕尺寸
   - 在移动设备上自动调整布局

## 使用说明

1. 黑棋玩家先行，点击棋盘交叉点放置棋子
2. 玩家轮流落子，先连成五子者获胜
3. 使用控制按钮管理游戏进程
4. 游戏结束后点击"重新开始"开始新游戏

您可以直接复制此代码到HTML文件中运行，无需任何外部依赖。