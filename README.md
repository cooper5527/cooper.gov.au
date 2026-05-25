# cooper.gov.au
Fun games
[snow_rider_game.html](https://github.com/user-attachments/files/28212644/snow_rider_game.html)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snow Rider 3D</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            overflow: hidden;
            background: linear-gradient(to bottom, #87CEEB, #E0F6FF);
        }

        #gameCanvas {
            display: block;
            touch-action: none;
        }

        #ui {
            position: absolute;
            top: 20px;
            left: 20px;
            color: white;
            font-size: 24px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.8);
            z-index: 10;
        }

        #score {
            font-weight: bold;
            margin-bottom: 10px;
        }

        #distance {
            margin-bottom: 10px;
        }

        #speed {
            margin-bottom: 10px;
        }

        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            color: white;
            display: none;
            z-index: 100;
        }

        #gameOver h1 {
            font-size: 48px;
            margin-bottom: 20px;
            color: #ff4444;
        }

        #gameOver button {
            font-size: 24px;
            padding: 15px 40px;
            margin: 10px;
            border: none;
            border-radius: 10px;
            background: #4CAF50;
            color: white;
            cursor: pointer;
            transition: all 0.3s;
        }

        #gameOver button:hover {
            background: #45a049;
            transform: scale(1.05);
        }

        #devPanel {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 10px;
            color: white;
            font-size: 14px;
            max-width: 300px;
            display: none;
            z-index: 50;
        }

        #devPanel h3 {
            margin-bottom: 15px;
            color: #4CAF50;
        }

        #devPanel label {
            display: block;
            margin: 10px 0 5px 0;
        }

        #devPanel input[type="checkbox"] {
            margin-right: 10px;
        }

        #devPanel button {
            margin-top: 10px;
            padding: 8px 15px;
            background: #2196F3;
            border: none;
            border-radius: 5px;
            color: white;
            cursor: pointer;
        }

        #devPanel button:hover {
            background: #0b7dda;
        }

        .locked {
            opacity: 0.5;
            pointer-events: none;
        }

        #flightPasscode {
            width: 100%;
            padding: 5px;
            margin-top: 5px;
            border-radius: 3px;
            border: 1px solid #666;
        }

        #devLockScreen {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.9);
            padding: 30px;
            border-radius: 10px;
            color: white;
            font-size: 14px;
            max-width: 300px;
            display: none;
            z-index: 50;
            text-align: center;
        }

        #devLockScreen h3 {
            margin-bottom: 20px;
            color: #FF4444;
        }

        #devLockScreen input {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            border: 2px solid #666;
            font-size: 16px;
        }

        #devLockScreen button {
            width: 100%;
            padding: 10px;
            margin-top: 10px;
            background: #4CAF50;
            border: none;
            border-radius: 5px;
            color: white;
            cursor: pointer;
            font-size: 16px;
        }

        #devLockScreen button:hover {
            background: #45a049;
        }

        #devToggle {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.6);
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            cursor: pointer;
            z-index: 60;
        }

        #controls {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            color: white;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.8);
            text-align: center;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    
    <div id="ui">
        <div id="score">Score: 0</div>
        <div id="distance">Distance: 0m</div>
        <div id="speed">Speed: 0 km/h</div>
    </div>

    <div id="controls">
        ← → Arrow Keys or A/D to steer | Space to Jump | Shift to Boost
    </div>

    <button id="devToggle">DEV PANEL</button>

    <div id="devLockScreen">
        <h3>🔒 Developer Panel Locked</h3>
        <p>Enter passcode to access developer features</p>
        <input type="password" id="masterPasscode" placeholder="Enter master passcode">
        <button id="unlockDevPanel">UNLOCK</button>
    </div>

    <div id="devPanel">
        <h3>🛠️ Developer Panel</h3>
        
        <label>
            <input type="checkbox" id="godMode"> God Mode (Invincible)
        </label>
        
        <label>
            <input type="checkbox" id="unlimitedBoost"> Unlimited Boost
        </label>
        
        <label>
            <input type="checkbox" id="superSpeed"> Super Speed
        </label>
        
        <label>
            <input type="checkbox" id="noObstacles"> Remove All Obstacles
        </label>
        
        <label>
            <input type="checkbox" id="flightMode"> Flight Mode ✈️
        </label>
        
        <button id="addScore">+1000 Score</button>
        <button id="resetGame">Reset Game</button>
        <button id="lockDevPanel">Lock Panel</button>
    </div>

    <div id="gameOver">
        <h1>GAME OVER</h1>
        <p id="finalScore" style="font-size: 32px; margin: 20px 0;"></p>
        <p id="finalDistance" style="font-size: 24px; margin: 20px 0;"></p>
        <button id="restartBtn">RESTART</button>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // Game state
        let gameState = {
            playing: true,
            score: 0,
            distance: 0,
            speed: 0,
            baseSpeed: 8,
            maxSpeed: 20,
            playerX: 0,
            playerY: 0,
            playerZ: 0,
            velocityY: 0,
            isJumping: false,
            isBoosting: false,
            boostEnergy: 100,
            rotation: 0,
            
            // Dev panel features
            godMode: false,
            unlimitedBoost: false,
            superSpeed: false,
            noObstacles: false,
            flightMode: false
        };

        const MASTER_PASSCODE = "bobisthebest";

        // Obstacles and collectibles
        let obstacles = [];
        let trees = [];
        let coins = [];
        let ramps = [];

        let devPanelUnlocked = false;

        // Input handling
        const keys = {};
        let spacePressed = false;
        
        window.addEventListener('keydown', (e) => {
            keys[e.key] = true;
            if (e.key === ' ') {
                e.preventDefault();
                if (!spacePressed && !gameState.isJumping && gameState.playing) {
                    jump();
                    spacePressed = true;
                }
            }
        });

        window.addEventListener('keyup', (e) => {
            keys[e.key] = false;
            if (e.key === ' ') {
                spacePressed = false;
            }
        });

        // Dev panel setup
        document.getElementById('devToggle').addEventListener('click', () => {
            if (!devPanelUnlocked) {
                document.getElementById('devLockScreen').style.display = 'block';
                document.getElementById('devPanel').style.display = 'none';
            } else {
                const panel = document.getElementById('devPanel');
                panel.style.display = panel.style.display === 'none' ? 'block' : 'none';
                document.getElementById('devLockScreen').style.display = 'none';
            }
        });

        document.getElementById('unlockDevPanel').addEventListener('click', () => {
            const passcode = document.getElementById('masterPasscode').value;
            if (passcode === MASTER_PASSCODE) {
                devPanelUnlocked = true;
                document.getElementById('devLockScreen').style.display = 'none';
                document.getElementById('devPanel').style.display = 'block';
                document.getElementById('masterPasscode').value = '';
            } else {
                alert('❌ Incorrect passcode!');
                document.getElementById('masterPasscode').value = '';
            }
        });

        document.getElementById('lockDevPanel').addEventListener('click', () => {
            devPanelUnlocked = false;
            document.getElementById('devPanel').style.display = 'none';
            alert('🔒 Dev Panel locked!');
        });

        // Allow Enter key to submit passcode
        document.getElementById('masterPasscode').addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                document.getElementById('unlockDevPanel').click();
            }
        });

        document.getElementById('godMode').addEventListener('change', (e) => {
            gameState.godMode = e.target.checked;
        });

        document.getElementById('unlimitedBoost').addEventListener('change', (e) => {
            gameState.unlimitedBoost = e.target.checked;
        });

        document.getElementById('superSpeed').addEventListener('change', (e) => {
            gameState.superSpeed = e.target.checked;
        });

        document.getElementById('noObstacles').addEventListener('change', (e) => {
            gameState.noObstacles = e.target.checked;
        });

        document.getElementById('flightMode').addEventListener('change', (e) => {
            gameState.flightMode = e.target.checked;
        });

        document.getElementById('addScore').addEventListener('click', () => {
            gameState.score += 1000;
        });

        document.getElementById('resetGame').addEventListener('click', () => {
            resetGame();
        });

        document.getElementById('restartBtn').addEventListener('click', () => {
            document.getElementById('gameOver').style.display = 'none';
            resetGame();
        });

        function resetGame() {
            gameState.playing = true;
            gameState.score = 0;
            gameState.distance = 0;
            gameState.speed = 0;
            gameState.playerX = 0;
            gameState.playerY = 0;
            gameState.playerZ = 0;
            gameState.velocityY = 0;
            gameState.isJumping = false;
            gameState.isBoosting = false;
            gameState.boostEnergy = 100;
            gameState.rotation = 0;
            obstacles = [];
            trees = [];
            coins = [];
            ramps = [];
            initializeObjects();
        }

        function jump() {
            if (gameState.flightMode) {
                gameState.velocityY = 15;
            } else if (!gameState.isJumping) {
                gameState.velocityY = 12;
                gameState.isJumping = true;
            }
        }

        function initializeObjects() {
            // Generate initial obstacles
            for (let i = 0; i < 20; i++) {
                spawnObstacle(i * 150 + 500);
            }
            
            // Generate trees
            for (let i = 0; i < 50; i++) {
                trees.push({
                    x: (Math.random() - 0.5) * 1500,
                    z: i * 100 + Math.random() * 50,
                    size: 30 + Math.random() * 20
                });
            }
            
            // Generate coins
            for (let i = 0; i < 30; i++) {
                coins.push({
                    x: (Math.random() - 0.5) * 600,
                    y: 20 + Math.random() * 50,
                    z: i * 200 + 300,
                    collected: false,
                    rotation: 0
                });
            }
        }

        function spawnObstacle(zPos) {
            const type = Math.random();
            if (type < 0.4) {
                // Rock
                obstacles.push({
                    type: 'rock',
                    x: (Math.random() - 0.5) * 500,
                    y: 0,
                    z: zPos,
                    size: 30 + Math.random() * 20
                });
            } else if (type < 0.7) {
                // Tree stump
                obstacles.push({
                    type: 'stump',
                    x: (Math.random() - 0.5) * 500,
                    y: 0,
                    z: zPos,
                    size: 25 + Math.random() * 15
                });
            } else {
                // Ramp
                ramps.push({
                    x: (Math.random() - 0.5) * 400,
                    y: 0,
                    z: zPos,
                    width: 80,
                    height: 40
                });
            }
        }

        function update() {
            if (!gameState.playing) return;

            // Movement
            const turnSpeed = 8;
            if (keys['ArrowLeft'] || keys['a'] || keys['A']) {
                gameState.playerX -= turnSpeed;
            }
            if (keys['ArrowRight'] || keys['d'] || keys['D']) {
                gameState.playerX += turnSpeed;
            }

            // Boost
            if (keys['Shift'] && (gameState.boostEnergy > 0 || gameState.unlimitedBoost)) {
                gameState.isBoosting = true;
                if (!gameState.unlimitedBoost) {
                    gameState.boostEnergy -= 1;
                }
            } else {
                gameState.isBoosting = false;
                if (gameState.boostEnergy < 100) {
                    gameState.boostEnergy += 0.5;
                }
            }

            // Speed calculation
            let targetSpeed = gameState.baseSpeed;
            if (gameState.superSpeed) {
                targetSpeed = 30;
            } else if (gameState.isBoosting) {
                targetSpeed = gameState.maxSpeed;
            }
            
            gameState.speed += (targetSpeed - gameState.speed) * 0.1;

            // Flight mode
            if (gameState.flightMode) {
                if (keys['ArrowUp'] || keys['w'] || keys['W']) {
                    gameState.playerY += 5;
                }
                if (keys['ArrowDown'] || keys['s'] || keys['S']) {
                    gameState.playerY -= 5;
                }
                gameState.velocityY = 0;
            } else {
                // Gravity
                gameState.velocityY -= 0.6;
                gameState.playerY += gameState.velocityY;

                // Ground collision
                if (gameState.playerY <= 0) {
                    gameState.playerY = 0;
                    gameState.velocityY = 0;
                    gameState.isJumping = false;
                }
            }

            // Constrain player position
            gameState.playerX = Math.max(-400, Math.min(400, gameState.playerX));
            if (!gameState.flightMode) {
                gameState.playerY = Math.max(0, gameState.playerY);
            }

            // Update distance
            gameState.distance += gameState.speed * 0.1;
            gameState.score += Math.floor(gameState.speed * 0.05);

            // Update rotation for tricks
            if (gameState.isJumping) {
                gameState.rotation += 5;
            } else {
                gameState.rotation *= 0.9;
            }

            // Move obstacles
            obstacles.forEach((obs, index) => {
                obs.z -= gameState.speed;
                
                // Respawn obstacles
                if (obs.z < -100) {
                    obstacles.splice(index, 1);
                    spawnObstacle(Math.max(...obstacles.map(o => o.z)) + 150 + Math.random() * 100);
                }
                
                // Collision detection
                if (!gameState.godMode && !gameState.noObstacles) {
                    const dx = obs.x - gameState.playerX;
                    const dy = obs.y - gameState.playerY;
                    const dz = obs.z;
                    const distance = Math.sqrt(dx*dx + dy*dy + dz*dz);
                    
                    if (distance < obs.size + 20 && Math.abs(dz) < 50) {
                        gameOver();
                    }
                }
            });

            // Update coins
            coins.forEach(coin => {
                coin.z -= gameState.speed;
                coin.rotation += 5;
                
                if (!coin.collected) {
                    const dx = coin.x - gameState.playerX;
                    const dy = coin.y - gameState.playerY;
                    const dz = coin.z;
                    const distance = Math.sqrt(dx*dx + dy*dy + dz*dz);
                    
                    if (distance < 40 && Math.abs(dz) < 50) {
                        coin.collected = true;
                        gameState.score += 100;
                    }
                }
                
                // Respawn coins
                if (coin.z < -100) {
                    coin.z = Math.max(...coins.map(c => c.z)) + 200;
                    coin.x = (Math.random() - 0.5) * 600;
                    coin.collected = false;
                }
            });

            // Update ramps
            ramps.forEach(ramp => {
                ramp.z -= gameState.speed;
                
                // Check if player hits ramp - only when on ground and not already jumping
                const dx = Math.abs(ramp.x - gameState.playerX);
                const dz = Math.abs(ramp.z);
                
                if (dx < ramp.width && dz < 40 && gameState.playerY <= 5 && !gameState.isJumping) {
                    gameState.velocityY = 15;
                    gameState.isJumping = true;
                }
                
                // Respawn ramps
                if (ramp.z < -100) {
                    ramp.z = Math.max(...ramps.map(r => r.z), ...obstacles.map(o => o.z)) + 200;
                    ramp.x = (Math.random() - 0.5) * 400;
                }
            });
        }

        function draw() {
            // Clear canvas
            ctx.fillStyle = '#87CEEB';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw snowy ground with perspective
            const horizon = canvas.height * 0.3;
            const groundGradient = ctx.createLinearGradient(0, horizon, 0, canvas.height);
            groundGradient.addColorStop(0, '#E0E0E0');
            groundGradient.addColorStop(1, '#FFFFFF');
            ctx.fillStyle = groundGradient;
            ctx.fillRect(0, horizon, canvas.width, canvas.height - horizon);

            // Draw grid lines for depth
            ctx.strokeStyle = 'rgba(200, 200, 200, 0.3)';
            ctx.lineWidth = 2;
            for (let i = 0; i < 10; i++) {
                const y = horizon + (canvas.height - horizon) * (i / 10);
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(canvas.width, y);
                ctx.stroke();
            }

            const centerX = canvas.width / 2;
            const centerY = canvas.height * 0.7;

            // Draw trees (background)
            trees.forEach(tree => {
                const relZ = tree.z - gameState.distance;
                if (relZ > -100 && relZ < 2000) {
                    const scale = 1000 / (relZ + 1000);
                    const screenX = centerX + (tree.x - gameState.playerX) * scale;
                    const screenY = centerY - scale * 100;
                    const size = tree.size * scale;

                    // Tree trunk
                    ctx.fillStyle = '#8B4513';
                    ctx.fillRect(screenX - size * 0.15, screenY, size * 0.3, size * 0.8);

                    // Tree foliage
                    ctx.fillStyle = '#2D5016';
                    ctx.beginPath();
                    ctx.moveTo(screenX, screenY - size * 0.4);
                    ctx.lineTo(screenX - size * 0.5, screenY + size * 0.2);
                    ctx.lineTo(screenX + size * 0.5, screenY + size * 0.2);
                    ctx.closePath();
                    ctx.fill();
                }
            });

            // Draw ramps
            ramps.forEach(ramp => {
                const relZ = ramp.z - gameState.distance;
                if (relZ > -100 && relZ < 1000) {
                    const scale = 1000 / (relZ + 1000);
                    const screenX = centerX + (ramp.x - gameState.playerX) * scale;
                    const screenY = centerY - scale * ramp.y;
                    const width = ramp.width * scale;
                    const height = ramp.height * scale;

                    ctx.fillStyle = '#CD853F';
                    ctx.beginPath();
                    ctx.moveTo(screenX - width / 2, screenY);
                    ctx.lineTo(screenX + width / 2, screenY);
                    ctx.lineTo(screenX + width / 2, screenY - height);
                    ctx.closePath();
                    ctx.fill();

                    ctx.strokeStyle = '#8B4513';
                    ctx.lineWidth = 2;
                    ctx.stroke();
                }
            });

            // Draw obstacles
            if (!gameState.noObstacles) {
                obstacles.forEach(obs => {
                    const relZ = obs.z - gameState.distance;
                    if (relZ > -100 && relZ < 1000) {
                        const scale = 1000 / (relZ + 1000);
                        const screenX = centerX + (obs.x - gameState.playerX) * scale;
                        const screenY = centerY - scale * obs.y;
                        const size = obs.size * scale;

                        if (obs.type === 'rock') {
                            ctx.fillStyle = '#696969';
                            ctx.beginPath();
                            ctx.arc(screenX, screenY, size, 0, Math.PI * 2);
                            ctx.fill();
                            
                            ctx.fillStyle = '#505050';
                            ctx.beginPath();
                            ctx.arc(screenX - size * 0.2, screenY - size * 0.2, size * 0.6, 0, Math.PI * 2);
                            ctx.fill();
                        } else if (obs.type === 'stump') {
                            ctx.fillStyle = '#8B4513';
                            ctx.fillRect(screenX - size * 0.6, screenY - size * 0.4, size * 1.2, size * 0.8);
                            
                            ctx.fillStyle = '#D2691E';
                            ctx.beginPath();
                            ctx.arc(screenX, screenY, size * 0.7, 0, Math.PI * 2);
                            ctx.fill();
                        }
                    }
                });
            }

            // Draw coins
            coins.forEach(coin => {
                if (!coin.collected) {
                    const relZ = coin.z - gameState.distance;
                    if (relZ > -100 && relZ < 1000) {
                        const scale = 1000 / (relZ + 1000);
                        const screenX = centerX + (coin.x - gameState.playerX) * scale;
                        const screenY = centerY - scale * coin.y;
                        const size = 15 * scale;

                        ctx.save();
                        ctx.translate(screenX, screenY);
                        ctx.rotate(coin.rotation * Math.PI / 180);
                        
                        ctx.fillStyle = '#FFD700';
                        ctx.beginPath();
                        ctx.ellipse(0, 0, size, size * 0.3, 0, 0, Math.PI * 2);
                        ctx.fill();
                        
                        ctx.strokeStyle = '#FFA500';
                        ctx.lineWidth = 2;
                        ctx.stroke();
                        
                        ctx.restore();
                    }
                }
            });

            // Draw player (snowboarder)
            const playerScreenY = centerY - gameState.playerY * 2;
            
            ctx.save();
            ctx.translate(centerX, playerScreenY);
            ctx.rotate(gameState.rotation * Math.PI / 180);

            // Snowboard
            ctx.fillStyle = '#FF4444';
            ctx.fillRect(-30, -5, 60, 10);
            ctx.strokeStyle = '#CC0000';
            ctx.lineWidth = 2;
            ctx.strokeRect(-30, -5, 60, 10);

            // Player body
            ctx.fillStyle = '#0066CC';
            ctx.fillRect(-15, -40, 30, 35);

            // Player head
            ctx.fillStyle = '#FFD4A3';
            ctx.beginPath();
            ctx.arc(0, -50, 12, 0, Math.PI * 2);
            ctx.fill();

            // Helmet
            ctx.fillStyle = '#FFD700';
            ctx.beginPath();
            ctx.arc(0, -55, 13, Math.PI, Math.PI * 2);
            ctx.fill();

            ctx.restore();

            // Draw boost bar
            if (!gameState.unlimitedBoost) {
                const barWidth = 200;
                const barHeight = 20;
                const barX = canvas.width - barWidth - 20;
                const barY = canvas.height - barHeight - 20;

                ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
                ctx.fillRect(barX, barY, barWidth, barHeight);

                const boostWidth = (gameState.boostEnergy / 100) * barWidth;
                const gradient = ctx.createLinearGradient(barX, barY, barX + barWidth, barY);
                gradient.addColorStop(0, '#FF4444');
                gradient.addColorStop(1, '#FFAA00');
                ctx.fillStyle = gradient;
                ctx.fillRect(barX, barY, boostWidth, barHeight);

                ctx.strokeStyle = '#FFFFFF';
                ctx.lineWidth = 2;
                ctx.strokeRect(barX, barY, barWidth, barHeight);

                ctx.fillStyle = '#FFFFFF';
                ctx.font = '14px Arial';
                ctx.textAlign = 'center';
                ctx.fillText('BOOST', barX + barWidth / 2, barY - 5);
            }

            // Update UI
            document.getElementById('score').textContent = `Score: ${Math.floor(gameState.score)}`;
            document.getElementById('distance').textContent = `Distance: ${Math.floor(gameState.distance)}m`;
            document.getElementById('speed').textContent = `Speed: ${Math.floor(gameState.speed * 10)} km/h`;
        }

        function gameOver() {
            gameState.playing = false;
            document.getElementById('gameOver').style.display = 'block';
            document.getElementById('finalScore').textContent = `Final Score: ${Math.floor(gameState.score)}`;
            document.getElementById('finalDistance').textContent = `Distance: ${Math.floor(gameState.distance)}m`;
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Initialize and start game
        initializeObjects();
        gameLoop();

        // Handle window resize
        window.addEventListener('resize', () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        });
    </script>
</body>
</html>
