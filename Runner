<!DOCTYPE html>
<html>
<head>
    <title>Tanmaya's Subway Run</title>
    <style>
        body { margin: 0; overflow: hidden; background: #222; font-family: 'Arial Black', sans-serif; }
        canvas { display: block; margin: 0 auto; background: linear-gradient(#444, #222); }
        #overlay { position: absolute; top: 20px; width: 100%; text-align: center; color: yellow; text-shadow: 2px 2px #000; pointer-events: none; }
    </style>
</head>
<body>

<div id="overlay">
    <h1 id="scoreDisplay">SCORE: 0</h1>
    <p>LEFT/RIGHT to move | SPACE to JUMP</p>
</div>
<canvas id="gameCanvas"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = 400;
canvas.height = window.innerHeight;

let score = 0;
let gameRunning = true;
let speed = 8;
let lanes = [80, 200, 320]; // Left, Middle, Right
let currentLane = 1; // Start in middle

const player = {
    x: lanes[1],
    y: canvas.height - 120,
    width: 50,
    height: 80,
    jumpY: 0,
    isJumping: false,
    jumpVelocity: 0
};

const obstacles = [];

// Input handling
window.addEventListener("keydown", (e) => {
    if (e.key === "ArrowLeft" && currentLane > 0) currentLane--;
    if (e.key === "ArrowRight" && currentLane < 2) currentLane++;
    if ((e.key === " " || e.key === "ArrowUp") && !player.isJumping) {
        player.isJumping = true;
        player.jumpVelocity = 20;
    }
});

function spawnObstacle() {
    const laneIdx = Math.floor(Math.random() * 3);
    const isBook = Math.random() > 0.4;
    obstacles.push({
        lane: laneIdx,
        z: 0, // Distance from horizon
        type: isBook ? "BOOK" : "BACKLOG",
        collected: false
    });
}

function update() {
    if (!gameRunning) return;

    // Handle Jump Physics
    if (player.isJumping) {
        player.jumpY += player.jumpVelocity;
        player.jumpVelocity -= 1.2; // Gravity
        if (player.jumpY <= 0) {
            player.jumpY = 0;
            player.isJumping = false;
        }
    }

    // Move items toward the player
    if (Math.random() < 0.04) spawnObstacle();

    obstacles.forEach((obj, index) => {
        obj.z += speed;

        // Collision Check
        if (obj.z > canvas.height - 150 && obj.z < canvas.height - 50) {
            if (obj.lane === currentLane) {
                if (obj.type === "BOOK" && !obj.collected) {
                    score += 50;
                    obj.collected = true;
                } else if (obj.type === "BACKLOG" && player.jumpY < 40) {
                    gameRunning = false;
                    alert("GAME OVER! Tanmaya hit a Backlog!\nFinal Score: " + score);
                    location.reload();
                }
            }
        }

        if (obj.z > canvas.height) obstacles.splice(index, 1);
    });

    speed += 0.002;
    document.getElementById("scoreDisplay").innerText = "SCORE: " + score;
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw 3D Road
    ctx.fillStyle = "#555";
    ctx.beginPath();
    ctx.moveTo(150, 0);
    ctx.lineTo(250, 0);
    ctx.lineTo(400, canvas.height);
    ctx.lineTo(0, canvas.height);
    ctx.fill();

    // Draw Items
    obstacles.forEach(obj => {
        if (obj.collected) return;
        
        // Simple perspective math
        const scale = obj.z / canvas.height;
        const xPos = lanes[obj.lane];
        const visualX = 200 + (xPos - 200) * scale;
        const size = 10 + (40 * scale);

        ctx.fillStyle = obj.type === "BOOK" ? "#FFD700" : "#FF0000";
        if (obj.type === "BOOK") {
            ctx.fillRect(visualX - size/2, obj.z, size, size);
        } else {
            ctx.beginPath();
            ctx.arc(visualX, obj.z, size/2, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = "white";
            ctx.fillText("BACKLOG", visualX - 20, obj.z);
        }
    });

    // Draw Tanmaya
    const playerX = lanes[currentLane];
    ctx.fillStyle = "#00FFCC";
    // Shadow
    ctx.globalAlpha = 0.3;
    ctx.fillStyle = "black";
    ctx.beginPath();
    ctx.ellipse(playerX, player.y + 70, 25, 10, 0, 0, Math.PI * 2);
    ctx.fill();
    ctx.globalAlpha = 1.0;

    // Body
    ctx.fillStyle = "#00FFCC";
    ctx.fillRect(playerX - 25, player.y - player.jumpY, 50, 80);
    ctx.fillStyle = "white";
    ctx.font = "bold 14px Arial";
    ctx.fillText("TANMAYA", playerX - 30, player.y - player.jumpY - 10);

    update();
    requestAnimationFrame(draw);
}

draw();
</script>
</body>
</html>
