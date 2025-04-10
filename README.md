<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>밥밥 게임</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      overflow: hidden;
      height: 100%;
      touch-action: manipulation;
    }
    canvas {
      display: block;
      margin: auto;
      background-color: #fff;
      width: 100vw;
      height: 100vh;
    }
    #score {
      position: absolute;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      font-size: 24px;
      font-family: 'Arial', sans-serif;
      font-weight: bold;
      color: #333;
      z-index: 10;
    }
  </style>
</head>
<body>
<div id="score">잡은 빵: 0개</div>
<canvas id="gameCanvas"></canvas>
<script>
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

  const scoreDisplay = document.getElementById('score');
  let score = 0;

  const bg = new Image();
  bg.src = '피크닉배경.jpg'; // 배경 이미지

  const character = new Image();
  character.src = '호두캐릭터.png'; // 캐릭터 이미지

  const breadImgs = ['bread1.png', 'bread2.png', 'bread3.png'];
  const breads = [];

  const loadedBreadImgs = breadImgs.map(src => {
    const img = new Image();
    img.src = src;
    return img;
  });

  const characterPos = {
    x: canvas.width / 2 - 50,
    y: canvas.height - 150,
    width: 100,
    height: 100,
    speed: 20
  };

  function createBread() {
    const i = Math.floor(Math.random() * loadedBreadImgs.length);
    breads.push({
      img: loadedBreadImgs[i],
      x: Math.random() * (canvas.width - 60),
      y: -60,
      width: 60,
      height: 60,
      speed: 3 + Math.random() * 3
    });
  }

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.drawImage(bg, 0, 0, canvas.width, canvas.height);
    ctx.drawImage(character, characterPos.x, characterPos.y, characterPos.width, characterPos.height);

    breads.forEach((b, i) => {
      b.y += b.speed;
      ctx.drawImage(b.img, b.x, b.y, b.width, b.height);

      if (
        b.x < characterPos.x + characterPos.width &&
        b.x + b.width > characterPos.x &&
        b.y < characterPos.y + characterPos.height &&
        b.y + b.height > characterPos.y
      ) {
        breads.splice(i, 1);
        score++;
        scoreDisplay.textContent = `잡은 빵: ${score}개`;
      }

      if (b.y > canvas.height) breads.splice(i, 1);
    });

    requestAnimationFrame(draw);
  }

  // PC
  document.addEventListener('keydown', e => {
    if (e.key === 'ArrowLeft') characterPos.x -= characterPos.speed;
    if (e.key === 'ArrowRight') characterPos.x += characterPos.speed;
  });

  // Mobile Touch Support
  let startX = null;
  canvas.addEventListener('touchstart', e => {
    startX = e.touches[0].clientX;
  });

  canvas.addEventListener('touchmove', e => {
    if (!startX) return;
    const currentX = e.touches[0].clientX;
    const diff = currentX - startX;
    characterPos.x += diff * 0.2;
    startX = currentX;
  });

  setInterval(createBread, 1000);
  draw();
</script>
</body>
</html>
