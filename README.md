# Sigma-Dungeon
oyun
<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8" />
<title>Sigma Dungeon - Mini Zorluk</title>
<style>
  body { margin:0; overflow:hidden; background:#111; color:#eee; font-family:sans-serif; }
  #gameCanvas { background:#222; display:block; margin:20px auto; border:2px solid #555; }
  #message { text-align:center; font-size:24px; margin-top:10px; }
</style>
</head>
<body>

<canvas id="gameCanvas" width="600" height="400"></canvas>
<div id="message"></div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const player = { x: 300, y: 200, size: 20, color: 'red', speed: 3 };
const enemies = [
  { x: 50, y: 50, size: 20, color: 'blue', speed: 1 },
  { x: 550, y: 50, size: 20, color: 'blue', speed: 1 },
  { x: 300, y: 350, size: 20, color: 'blue', speed: 1 }
];

let keys = {};
let gameOver = false;
let enemySpeedIncreaseInterval = 10000; // 10 saniye
let lastSpeedIncrease = Date.now();

window.addEventListener('keydown', e => keys[e.key.toLowerCase()] = true);
window.addEventListener('keyup', e => keys[e.key.toLowerCase()] = false);

function rectsCollide(a, b) {
  return !(a.x + a.size < b.x || a.x > b.x + b.size || a.y + a.size < b.y || a.y > b.y + b.size);
}

function movePlayer() {
  if (keys['w'] && player.y > 0) player.y -= player.speed;
  if (keys['s'] && player.y + player.size < canvas.height) player.y += player.speed;
  if (keys['a'] && player.x > 0) player.x -= player.speed;
  if (keys['d'] && player.x + player.size < canvas.width) player.x += player.speed;
}

function moveEnemies() {
  enemies.forEach(enemy => {
    let dx = player.x - enemy.x;
    let dy = player.y - enemy.y;
    let dist = Math.sqrt(dx*dx + dy*dy);
    if (dist > 0) {
      enemy.x += (dx/dist) * enemy.speed;
      enemy.y += (dy/dist) * enemy.speed;
    }
  });
}

function checkCollisions() {
  for (let enemy of enemies) {
    if (rectsCollide(player, enemy)) {
      gameOver = true;
      document.getElementById('message').textContent = "Oyun Bitti! Ahmet kral yenildi! 😱";
    }
  }
}

function increaseEnemySpeed() {
  let now = Date.now();
  if (now - lastSpeedIncrease > enemySpeedIncreaseInterval) {
    enemies.forEach(enemy => enemy.speed += 0.5);
    lastSpeedIncrease = now;
  }
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Player
  ctx.fillStyle = player.color;
  ctx.fillRect(player.x, player.y, player.size, player.size);

  // Enemies
  enemies.forEach(enemy => {
    ctx.fillStyle = enemy.color;
    ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);
  });
}

function gameLoop() {
  if (gameOver) return;
  movePlayer();
  moveEnemies();
  checkCollisions();
  increaseEnemySpeed();
  draw();
  requestAnimationFrame(gameLoop);
}

gameLoop();
</script>

</body>
</html>
