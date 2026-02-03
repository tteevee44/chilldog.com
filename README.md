<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Chill Dog Transformation</title>

<style>
  body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #0f172a;
    color: white;
    text-align: center;
  }

  header {
    padding: 20px;
    font-size: 2rem;
    font-weight: bold;
  }

  canvas {
    border: 2px solid white;
    margin-top: 20px;
    background: black;
  }

  input {
    margin-top: 15px;
  }
</style>
</head>

<body>
<header>🐶 Chill Dog Transformation</header>

<p>Upload an image and watch it transform into Chill Dog!</p>
<input type="file" id="upload" accept="image/*" />
<br />
<canvas id="canvas" width="500" height="500"></canvas>

<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
const upload = document.getElementById("upload");

const hexSize = 8;
let targetImage = new Image();

// Chill dog target image (embedded)
targetImage.src = "/mnt/data/b0d22680-d4ef-4708-a47d-9d7642dffa44.png";

function drawHex(x, y, size, color) {
  ctx.beginPath();
  for (let i = 0; i < 6; i++) {
    const angle = (Math.PI / 3) * i;
    const hx = x + size * Math.cos(angle);
    const hy = y + size * Math.sin(angle);
    if (i === 0) ctx.moveTo(hx, hy);
    else ctx.lineTo(hx, hy);
  }
  ctx.closePath();
  ctx.fillStyle = color;
  ctx.fill();
}

upload.addEventListener("change", e => {
  const file = e.target.files[0];
  if (!file) return;

  const userImg = new Image();
  userImg.onload = () => startTransform(userImg);
  userImg.src = URL.createObjectURL(file);
});

function startTransform(userImg) {
  const off1 = document.createElement("canvas");
  const off2 = document.createElement("canvas");

  off1.width = off2.width = canvas.width;
  off1.height = off2.height = canvas.height;

  const octx1 = off1.getContext("2d");
  const octx2 = off2.getContext("2d");

  octx1.drawImage(userImg, 0, 0, canvas.width, canvas.height);
  octx2.drawImage(targetImage, 0, 0, canvas.width, canvas.height);

  const img1 = octx1.getImageData(0, 0, canvas.width, canvas.height);
  const img2 = octx2.getImageData(0, 0, canvas.width, canvas.height);

  let progress = 0;

  function animate() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    for (let y = 0; y < canvas.height; y += hexSize * 1.5) {
      for (let x = 0; x < canvas.width; x += hexSize * 1.7) {
        const index = ((Math.floor(y) * canvas.width) + Math.floor(x)) * 4;

        const r1 = img1.data[index];
        const g1 = img1.data[index + 1];
        const b1 = img1.data[index + 2];

        const r2 = img2.data[index];
        const g2 = img2.data[index + 1];
        const b2 = img2.data[index + 2];

        const r = r1 + (r2 - r1) * progress;
        const g = g1 + (g2 - g1) * progress;
        const b = b1 + (b2 - b1) * progress;

        const offset = (Math.floor(y / (hexSize * 1.5)) % 2) * (hexSize * 0.85);

        drawHex(x + offset, y, hexSize, `rgb(${r},${g},${b})`);
      }
    }

    progress += 0.01;
    if (progress <= 1) requestAnimationFrame(animate);
  }

  animate();
}
</script>
</body>
</html>
