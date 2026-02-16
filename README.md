# Block-Crosh
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Block Crush - Preview bilan</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            -webkit-touch-callout: none;
        }

        html, body {
            width: 100%;
            height: 100%;
            overflow: hidden;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            touch-action: none;
        }

        body {
            background: linear-gradient(135deg, #c79a63 0%, #b58b5a 100%);
            font-family: 'Segoe UI', Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 10px;
        }

        /* Bosh menyu */
        .menu {
            background: #8b5a2b;
            padding: min(40px, 8vw);
            border-radius: 40px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
            width: min(450px, 95%);
            text-align: center;
            border: 4px solid gold;
        }

        .menu h1 {
            color: gold;
            font-size: clamp(36px, 12vw, 52px);
            margin-bottom: 30px;
            text-shadow: 4px 4px 0 #4b2e16;
        }

        .menu button {
            background: gold;
            color: #4b2e16;
            border: none;
            padding: 18px 35px;
            margin: 12px;
            font-size: clamp(20px, 6vw, 26px);
            font-weight: bold;
            border-radius: 60px;
            cursor: pointer;
            border: 4px solid #4b2e16;
            transition: all 0.2s;
            width: min(220px, 85%);
            box-shadow: 0 5px 0 #4b2e16;
        }

        .menu button:active {
            transform: translateY(4px);
            box-shadow: 0 1px 0 #4b2e16;
        }

        /* O'yin qutisi */
        .game-container {
            background: #8b5a2b;
            padding: min(15px, 3vw);
            border-radius: 40px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
            width: min(550px, 98%);
            max-height: 98vh;
            display: none;
            border: 4px solid gold;
            position: relative;
            touch-action: none;
        }

        h1 {
            margin: 5px 0;
            color: gold;
            font-size: clamp(28px, 8vw, 36px);
            text-shadow: 2px 2px 0 #4b2e16;
        }

        .score-panel {
            background: #4b2e16;
            color: gold;
            font-size: clamp(22px, 6vw, 26px);
            padding: 10px 25px;
            border-radius: 50px;
            margin: 10px auto;
            display: inline-block;
            font-weight: bold;
            border: 3px solid gold;
            box-shadow: inset 0 -3px 0 #2b1a0e;
        }

        .board {
            width: min(340px, 75vw);
            height: min(340px, 75vw);
            margin: 10px auto;
            display: grid;
            grid-template-columns: repeat(10, 1fr);
            grid-template-rows: repeat(10, 1fr);
            gap: 3px;
            background: #4b2e16;
            padding: 8px;
            border-radius: 20px;
            border: 4px solid gold;
            touch-action: none;
            box-shadow: inset 0 -5px 0 #2b1a0e;
            position: relative;
        }

        .cell {
            background: #6b3f1f;
            border-radius: 6px;
            transition: all 0.15s;
            box-shadow: inset 0 -3px 0 #3a2a1a;
            aspect-ratio: 1;
            border: 1px solid #8b5a2b;
            position: relative;
            z-index: 1;
        }

        .cell.filled {
            background: gold;
            animation: pop 0.2s ease;
            box-shadow: inset 0 -3px 0 #b8860b;
        }

        .cell.filled.red { background: #ff4444; }
        .cell.filled.blue { background: #4444ff; }
        .cell.filled.green { background: #44ff44; }
        .cell.filled.purple { background: #aa44ff; }
        .cell.filled.orange { background: #ff8844; }

        /* PREVIEW SOYA - MUHIM QISM */
        .cell.preview {
            background: rgba(255, 215, 0, 0.3);
            border: 2px dashed gold;
            box-shadow: 0 0 15px gold;
            animation: pulse 1s infinite;
            z-index: 10;
        }

        .cell.preview.red { background: rgba(255, 68, 68, 0.3); border-color: #ff4444; }
        .cell.preview.blue { background: rgba(68, 68, 255, 0.3); border-color: #4444ff; }
        .cell.preview.green { background: rgba(46, 204, 113, 0.3); border-color: #2ecc71; }
        .cell.preview.purple { background: rgba(155, 89, 182, 0.3); border-color: #9b59b6; }
        .cell.preview.orange { background: rgba(230, 126, 34, 0.3); border-color: #e67e22; }

        @keyframes pulse {
            0% { opacity: 0.5; transform: scale(1); }
            50% { opacity: 1; transform: scale(1.05); }
            100% { opacity: 0.5; transform: scale(1); }
        }

        @keyframes pop {
            0% { transform: scale(1); }
            50% { transform: scale(1.15); }
            100% { transform: scale(1); }
        }

        .pieces {
            display: flex;
            justify-content: center;
            gap: min(12px, 2.5vw);
            margin: 15px 0 5px 0;
            padding: min(15px, 3vw);
            background: #4b2e16;
            border-radius: 30px;
            border: 4px solid gold;
            flex-wrap: wrap;
            touch-action: none;
            min-height: 120px;
            box-shadow: inset 0 -5px 0 #2b1a0e;
        }

        .piece {
            display: grid;
            gap: 2px;
            background: #6b3f1f;
            padding: min(8px, 2vw);
            border-radius: 15px;
            border: 3px solid gold;
            touch-action: none;
            cursor: grab;
            -webkit-user-drag: element;
            box-shadow: 0 5px 0 #3a2a1a;
            transition: all 0.1s;
        }

        .piece:active {
            transform: translateY(3px) scale(0.98);
            box-shadow: 0 2px 0 #3a2a1a;
            cursor: grabbing;
        }

        .piece.dragging {
            opacity: 0.3;
        }

        .block {
            width: min(24px, 5.5vw);
            height: min(24px, 5.5vw);
            border-radius: 5px;
            box-shadow: 0 3px 0 #3a2a1a;
        }

        .block.green { background: #2ecc71; }
        .block.blue { background: #3498db; }
        .block.purple { background: #9b59b6; }
        .block.red { background: #e74c3c; }
        .block.orange { background: #e67e22; }

        .block.empty {
            visibility: hidden;
        }

        /* Modal oynalar */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.85);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 2000;
            backdrop-filter: blur(5px);
        }

        .modal-content {
            background: #8b5a2b;
            padding: min(40px, 8vw);
            border-radius: 40px;
            text-align: center;
            border: 5px solid gold;
            width: min(400px, 90%);
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
        }

        .modal-content h2 {
            color: gold;
            font-size: clamp(36px, 10vw, 48px);
            margin-bottom: 20px;
            text-shadow: 3px 3px 0 #4b2e16;
        }

        .modal-content p {
            color: white;
            font-size: clamp(20px, 6vw, 26px);
            margin: 20px 0;
        }

        #finalScore {
            color: gold;
            font-size: clamp(48px, 12vw, 60px);
            font-weight: bold;
            text-shadow: 3px 3px 0 #4b2e16;
        }

        .modal-content button {
            background: gold;
            color: #4b2e16;
            border: none;
            padding: 18px 35px;
            margin: 12px;
            font-size: clamp(18px, 5vw, 22px);
            font-weight: bold;
            border-radius: 60px;
            cursor: pointer;
            border: 4px solid #4b2e16;
            transition: all 0.2s;
            width: min(220px, 85%);
            box-shadow: 0 5px 0 #4b2e16;
        }

        .modal-content button:active {
            transform: translateY(4px);
            box-shadow: 0 1px 0 #4b2e16;
        }

        .modal-content .menu-btn {
            background: #e74c3c;
            color: white;
        }

        .rules-text {
            color: white;
            font-size: clamp(16px, 4.5vw, 20px);
            text-align: left;
            margin: 20px 0;
            line-height: 2;
            padding: 0 15px;
            background: #6b3f1f;
            border-radius: 20px;
            border: 2px solid gold;
        }

        .rules-text p {
            margin: 15px 0;
            color: white;
            display: flex;
            align-items: center;
            gap: 10px;
        }
    </style>
</head>
<body>
    <!-- Bosh menyu -->
    <div class="menu" id="menu">
        <h1>🧩 BLOCK CRUSH</h1>
        <button id="startBtn">🎮 O'YNASH</button>
        <button id="howBtn">📋 QOIDALAR</button>
    </div>

    <!-- O'yin qutisi -->
    <div class="game-container" id="gameContainer">
        <h1>🧩 BLOCK CRUSH</h1>
        
        <div class="score-panel" id="score">
            Ball: 0
        </div>

        <div class="board" id="board"></div>

        <div class="pieces" id="pieces"></div>
    </div>

    <!-- O'yin tugadi oynasi -->
    <div class="modal" id="gameOver">
        <div class="modal-content">
            <h2>GAME OVER</h2>
            <p>Yakuniy ball:</p>
            <p id="finalScore">0</p>
            <button id="playAgainBtn">🔄 QAYTA BOSHLASH</button>
            <button class="menu-btn" id="gameOverMenuBtn">🏠 MENYU</button>
        </div>
    </div>

    <!-- Qoidalar oynasi -->
    <div class="modal" id="howToPlay">
        <div class="modal-content">
            <h2>QOIDALAR</h2>
            <div class="rules-text">
                <p>👉 Kubiklarni barmoq bilan surib maydonga tashlang</p>
                <p>⬜ To'liq qator yoki ustun hosil qiling</p>
                <p>💰 Har bir kubik uchun +10 ball</p>
                <p>🎯 To'liq qator/ustun uchun +100 ball</p>
                <p>⚠️ Joy qolmasa o'yin tugaydi</p>
            </div>
            <button id="closeHowBtn">✅ TUSHUNDIM</button>
        </div>
    </div>

    <script>
        // Elementlarni olish
        const menu = document.getElementById('menu');
        const gameContainer = document.getElementById('gameContainer');
        const board = document.getElementById('board');
        const piecesContainer = document.getElementById('pieces');
        const scoreDisplay = document.getElementById('score');
        const gameOverElement = document.getElementById('gameOver');
        const finalScoreElement = document.getElementById('finalScore');
        const howToPlay = document.getElementById('howToPlay');

        // O'yin o'zgaruvchilari
        let grid = [];
        let score = 0;
        let currentPieces = [];
        let draggedPiece = null;
        let draggedElement = null;
        let previewRow = -1;
        let previewCol = -1;

        // Figuralar
        const shapes = [
            { pattern: [[1,1,1,1]], color: "red" },           // I
            { pattern: [[1,0,0],[1,1,1]], color: "green" },   // L
            { pattern: [[0,1,0],[1,1,1]], color: "blue" },    // T
            { pattern: [[1,1],[1,1]], color: "purple" },      // O
            { pattern: [[1,1,0],[0,1,1]], color: "orange" },  // S
            { pattern: [[0,1,1],[1,1,0]], color: "green" },   // Z
            { pattern: [[0,0,1],[1,1,1]], color: "blue" }     // J
        ];

        // Bosh menyu tugmalari
        document.getElementById('startBtn').addEventListener('click', () => {
            menu.style.display = 'none';
            gameContainer.style.display = 'block';
            restartGame();
        });

        document.getElementById('howBtn').addEventListener('click', () => {
            howToPlay.style.display = 'flex';
        });

        document.getElementById('closeHowBtn').addEventListener('click', () => {
            howToPlay.style.display = 'none';
        });

        // 10x10 board yaratish
        function createBoard() {
            board.innerHTML = '';
            grid = [];
            for (let i = 0; i < 100; i++) {
                const cell = document.createElement('div');
                cell.classList.add('cell');
                cell.dataset.index = i;
                cell.dataset.row = Math.floor(i / 10);
                cell.dataset.col = i % 10;
                board.appendChild(cell);
                grid.push(cell);
            }
        }

        // Preview tozalash
        function clearPreview() {
            grid.forEach(cell => {
                cell.classList.remove('preview', 'red', 'blue', 'green', 'purple', 'orange');
            });
        }

        // Preview ko'rsatish
        function showPreview(piece, row, col) {
            clearPreview();
            
            if (!piece) return;
            
            // Tekshirish
            for (let r = 0; r < piece.pattern.length; r++) {
                for (let c = 0; c < piece.pattern[0].length; c++) {
                    if (piece.pattern[r][c] === 1) {
                        const newRow = row + r;
                        const newCol = col + c;
                        
                        if (newRow >= 0 && newRow < 10 && newCol >= 0 && newCol < 10) {
                            if (!grid[newRow * 10 + newCol].classList.contains('filled')) {
                                grid[newRow * 10 + newCol].classList.add('preview', piece.color);
                            }
                        }
                    }
                }
            }
        }

        // Tasodifiy figura
        function getRandomPiece() {
            return JSON.parse(JSON.stringify(shapes[Math.floor(Math.random() * shapes.length)]));
        }

        // Figura elementini yaratish
        function createPieceElement(piece) {
            const pieceDiv = document.createElement('div');
            pieceDiv.classList.add('piece');
            
            const rows = piece.pattern.length;
            const cols = piece.pattern[0].length;
            
            pieceDiv.style.gridTemplateColumns = `repeat(${cols}, min(28px, 6vw))`;
            pieceDiv.style.gridTemplateRows = `repeat(${rows}, min(28px, 6vw))`;

            for (let r = 0; r < rows; r++) {
                for (let c = 0; c < cols; c++) {
                    const block = document.createElement('div');
                    block.classList.add('block');
                    
                    if (piece.pattern[r][c] === 1) {
                        block.classList.add(piece.color);
                    } else {
                        block.classList.add('empty');
                    }
                    
                    pieceDiv.appendChild(block);
                }
            }

            // TOUCH EVENTLARI - PREVIEW BILAN
            pieceDiv.addEventListener('touchstart', (e) => {
                e.preventDefault();
                const touch = e.touches[0];
                draggedPiece = piece;
                draggedElement = pieceDiv;
                
                pieceDiv.classList.add('dragging');
                previewRow = -1;
                previewCol = -1;
            });

            pieceDiv.addEventListener('touchmove', (e) => {
                e.preventDefault();
                
                if (draggedPiece) {
                    const touch = e.touches[0];
                    
                    // Maydon ustidami?
                    const rect = board.getBoundingClientRect();
                    
                    if (touch.clientX >= rect.left && touch.clientX <= rect.right && 
                        touch.clientY >= rect.top && touch.clientY <= rect.bottom) {
                        
                        const cellSize = rect.width / 10;
                        const col = Math.floor((touch.clientX - rect.left) / cellSize);
                        const row = Math.floor((touch.clientY - rect.top) / cellSize);
                        
                        if (row >= 0 && row < 10 && col >= 0 && col < 10) {
                            // Preview ko'rsatish
                            if (previewRow !== row || previewCol !== col) {
                                previewRow = row;
                                previewCol = col;
                                showPreview(draggedPiece, row, col);
                            }
                        } else {
                            clearPreview();
                            previewRow = -1;
                            previewCol = -1;
                        }
                    } else {
                        clearPreview();
                        previewRow = -1;
                        previewCol = -1;
                    }
                }
            });

            pieceDiv.addEventListener('touchend', (e) => {
                e.preventDefault();
                
                if (draggedPiece && draggedElement) {
                    const touch = e.changedTouches[0];
                    
                    // Maydonga tushirish
                    const rect = board.getBoundingClientRect();
                    
                    if (touch.clientX >= rect.left && touch.clientX <= rect.right && 
                        touch.clientY >= rect.top && touch.clientY <= rect.bottom) {
                        
                        const cellSize = rect.width / 10;
                        const col = Math.floor((touch.clientX - rect.left) / cellSize);
                        const row = Math.floor((touch.clientY - rect.top) / cellSize);
                        
                        if (row >= 0 && row < 10 && col >= 0 && col < 10) {
                            if (placePiece(draggedPiece, row, col)) {
                                clearLines();
                                
                                // Birinchi figurani o'chirish
                                if (piecesContainer.children.length > 0) {
                                    piecesContainer.removeChild(piecesContainer.children[0]);
                                    currentPieces.shift();
                                    
                                    // Yangi figura qo'shish
                                    const newPiece = getRandomPiece();
                                    currentPieces.push(newPiece);
                                    piecesContainer.appendChild(createPieceElement(newPiece));
                                }
                                
                                // O'yin tugaganligini tekshirish
                                setTimeout(() => {
                                    if (checkGameOver()) {
                                        gameOver();
                                    }
                                }, 100);
                            }
                        }
                    }
                    
                    // Tozalash
                    draggedElement.classList.remove('dragging');
                    draggedPiece = null;
                    draggedElement = null;
                    clearPreview();
                    previewRow = -1;
                    previewCol = -1;
                }
            });

            pieceDiv.addEventListener('touchcancel', (e) => {
                e.preventDefault();
                if (draggedElement) {
                    draggedElement.classList.remove('dragging');
                }
                draggedPiece = null;
                draggedElement = null;
                clearPreview();
                previewRow = -1;
                previewCol = -1;
            });

            return pieceDiv;
        }

        // 3 ta figura yaratish
        function generatePieces() {
            piecesContainer.innerHTML = '';
            currentPieces = [];
            
            for (let i = 0; i < 3; i++) {
                const piece = getRandomPiece();
                currentPieces.push(piece);
                piecesContainer.appendChild(createPieceElement(piece));
            }
        }

        // Figura qo'yish mumkinmi?
        function canPlacePiece(piece, row, col) {
            for (let r = 0; r < piece.pattern.length; r++) {
                for (let c = 0; c < piece.pattern[0].length; c++) {
                    if (piece.pattern[r][c] === 1) {
                        const newRow = row + r;
                        const newCol = col + c;
                        
                        if (newRow >= 10 || newCol >= 10) return false;
                        if (grid[newRow * 10 + newCol].classList.contains('filled')) return false;
                    }
                }
            }
            return true;
        }

        // Figura qo'yish
        function placePiece(piece, row, col) {
            if (!canPlacePiece(piece, row, col)) return false;

            for (let r = 0; r < piece.pattern.length; r++) {
                for (let c = 0; c < piece.pattern[0].length; c++) {
                    if (piece.pattern[r][c] === 1) {
                        const index = (row + r) * 10 + (col + c);
                        grid[index].classList.add('filled', piece.color);
                    }
                }
            }

            score += 10;
            updateScore();
            return true;
        }

        // To'liq qator va ustunlarni tozalash
        function clearLines() {
            let linesCleared = 0;

            // Qatorlarni tekshirish
            for (let r = 0; r < 10; r++) {
                let fullRow = true;
                for (let c = 0; c < 10; c++) {
                    if (!grid[r * 10 + c].classList.contains('filled')) {
                        fullRow = false;
                        break;
                    }
                }
                
                if (fullRow) {
                    for (let c = 0; c < 10; c++) {
                        grid[r * 10 + c].classList.remove('filled', 'red', 'blue', 'green', 'purple', 'orange');
                    }
                    linesCleared++;
                }
            }

            // Ustunlarni tekshirish
            for (let c = 0; c < 10; c++) {
                let fullCol = true;
                for (let r = 0; r < 10; r++) {
                    if (!grid[r * 10 + c].classList.contains('filled')) {
                        fullCol = false;
                        break;
                    }
                }
                
                if (fullCol) {
                    for (let r = 0; r < 10; r++) {
                        grid[r * 10 + c].classList.remove('filled', 'red', 'blue', 'green', 'purple', 'orange');
                    }
                    linesCleared++;
                }
            }

            if (linesCleared > 0) {
                score += linesCleared * 100;
                updateScore();
            }
        }

        // O'yin tugaganligini tekshirish
        function checkGameOver() {
            for (let i = 0; i < currentPieces.length; i++) {
                const piece = currentPieces[i];
                
                for (let r = 0; r <= 10 - piece.pattern.length; r++) {
                    for (let c = 0; c <= 10 - piece.pattern[0].length; c++) {
                        if (canPlacePiece(piece, r, c)) {
                            return false;
                        }
                    }
                }
            }
            return true;
        }

        // Ballni yangilash
        function updateScore() {
            scoreDisplay.textContent = `Ball: ${score}`;
        }

        // O'yin tugadi
        function gameOver() {
            finalScoreElement.textContent = score;
            gameOverElement.style.display = 'flex';
        }

        // O'yinni qayta boshlash
        function restartGame() {
            score = 0;
            updateScore();
            
            grid.forEach(cell => {
                cell.classList.remove('filled', 'red', 'blue', 'green', 'purple', 'orange', 'preview');
            });
            
            generatePieces();
            gameOverElement.style.display = 'none';
        }

        // GAME OVER tugmalari
        document.getElementById('playAgainBtn').addEventListener('click', () => {
            gameOverElement.style.display = 'none';
            restartGame();
        });

        document.getElementById('gameOverMenuBtn').addEventListener('click', () => {
            gameOverElement.style.display = 'none';
            gameContainer.style.display = 'none';
            menu.style.display = 'block';
        });

        // O'yinni boshlash
        createBoard();
        generatePieces();

        // Ekran aylanishini oldini olish
        document.body.addEventListener('touchmove', (e) => {
            e.preventDefault();
        }, { passive: false });
    </script>
</body>
</html>
