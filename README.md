# Gift-Box
# Gift box photo carousel
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Gift Surprise with Framed Photo Carousel</title>
<style>
  :root{
    --carousel-width: 340px;   /* portrait layout width */
    --carousel-height: 450px;  /* tall photo space */
    --bottom-extension: 340px; /* tall enough for full message */
    --photo-border: 14px;      /* thick white border */
  }

  body {
    margin: 0;
    min-height: 100vh;
    background: linear-gradient(135deg,#ffe4ec,#fff0f5);
    display:flex;
    align-items:center;
    justify-content:center;
    flex-direction:column;
    font-family: "Segoe UI", system-ui, -apple-system, "Helvetica Neue", Arial;
    overflow-x:hidden;
  }

  /* Gift container */
  .gift-container {
    position: relative;
    width: 220px;
    height: 220px;
    cursor: pointer;
    perspective: 1000px;
    transition: opacity 0.5s ease;
  }

  .box {
    width: 220px;
    height: 160px;
    position:absolute;
    bottom:0;
    border-radius:0 0 15px 15px;
    background: repeating-linear-gradient(45deg,#ffdc2c,#ffdc2c 20px,#ffdc2c 20px,#ffdc2c 40px);
    border:4px solid #1a01ff;
    box-shadow:0 10px 18px rgba(0,0,0,0.25);
  }

  .lid {
    width:240px;
    height:60px;
    position:absolute;
    top: -11.7px; left:-10px;
    border-radius:15px 15px 0 0;
    background: repeating-linear-gradient(45deg,#ffdc2c,#ffdc2c 20px,#ffdc2c 20px,#ffdc2c 40px);
    border:4px solid #1a01ff;
    transform-origin: bottom center;
    transition: transform .6s ease;
    box-shadow:0 6px 12px rgba(0,0,0,0.2);
  }

  .ribbon-vertical{
    position:absolute; top:0; left:50%; transform:translateX(-50%);
    width:30px; height:100%;
    background:linear-gradient(#f00606,#bd0606);
    box-shadow: inset 0 0 5px rgba(0,0,0,.2);
  }
  .ribbon-horizontal{
    position:absolute; top:50%; left:0; height:20px; width:100%;
    transform:translateY(-50%);
    background:linear-gradient(#ff0000,#ff0000);
    box-shadow: inset 0 0 5px rgba(0,0,0,.2);
  }
  .bow{ position:absolute; top:-25px; left:50%; transform:translateX(-50%); font-size:40px; color:gold; text-shadow:0 2px 5px rgba(0,0,0,.3); }

  .tap-text { margin-top:20px; font-size:1.3em; color:#d6336c; font-weight:700; animation:pulse 1.2s infinite; }
  @keyframes pulse { 0%,100%{ transform:scale(1);} 50%{ transform:scale(1.05);} }

  @keyframes bounceLid {
    0%   { transform: rotateX(0deg); }
    30%  { transform: rotateX(-20deg); }
    50%  { transform: rotateX(0deg); }
    70%  { transform: rotateX(-10deg); }
    100% { transform: rotateX(0deg); }
  }

  .confetti { position:absolute; width:8px; height:8px; background:gold; opacity:0; animation: fall 3s linear forwards; }
  @keyframes fall { 0%{ transform:translateY(0) rotate(0); opacity:1;} 100%{ transform:translateY(400px) rotate(720deg); opacity:0;} }

  .flower { position:absolute; font-size:20px; opacity:0; animation: floatUp 4s ease-out forwards; }
  @keyframes floatUp { 0%{ transform:translateY(0) scale(.5); opacity:1;} 100%{ transform:translateY(-250px) scale(1.2); opacity:0;} }

  .surprise { margin-top:20px; max-width: 760px; width: calc(var(--carousel-width)); text-align:center; display:none; animation: fadeIn 1.2s ease forwards; }
  @keyframes fadeIn { from{ opacity:0; transform:translateY(18px);} to{ opacity:1; transform:translateY(0);} }

  /* ---------- CAROUSEL + FRAMED PHOTO ---------- */
  .carousel {
    position: relative;
    width: var(--carousel-width);
    height: calc(var(--carousel-height) + var(--bottom-extension));
    margin: 0 auto;
    overflow: visible;
    border-radius: 14px;
    display:flex;
    align-items:flex-start;
    justify-content:center;
  }

  .photo-card {
    position: relative;
    width: 100%;
    height: calc(var(--carousel-height) + var(--bottom-extension));
    display:block;
    user-select:none;
  }

  /* Portrait frame */
  .photo-frame {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: var(--carousel-height);
    padding: var(--photo-border);
    box-sizing: border-box;
    background: white;
    border-radius: 16px 16px 0 0;
    box-shadow: 0 8px 18px rgba(0,0,0,0.18);
    z-index: 2;
  }

  /* Portrait photo styling */
  .photo-frame img {
    width:100%;
    height:100%;
    object-fit:cover;
    display:none;
    border-radius: 8px;
    box-shadow: 0 4px 10px rgba(0,0,0,0.18);
    background: #eee;
  }
  .photo-frame img.active { display:block; }

  .photo-bottom {
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    height: var(--bottom-extension);
    background: white;
    border-radius: 0 0 16px 16px;
    box-shadow: 0 6px 14px rgba(0,0,0,0.08);
    z-index: 1;
    padding: 20px 24px;
    box-sizing: border-box;
    display:flex;
    align-items:flex-start;
    justify-content:center;
  }

  .photo-bottom .message {
    width:100%;
    color:#222;
    font-size: 1rem;
    line-height:1.5;
    text-align:left;
    max-height: calc(var(--bottom-extension) - 20px);
    overflow: auto;
    font-family: "Segoe UI", system-ui, -apple-system, "Helvetica Neue", Arial, sans-serif;
    font-weight: 500;  
  }

  .arrow {
    position: absolute;
    top: calc(var(--carousel-height) / 2);
    transform: translateY(-50%);
    font-size: 28px;
    background: none;
    color: white;
    padding: 10px;
    border-radius: 50%;
    cursor: pointer;
    user-select: none;
    z-index: 5;
  }
  .arrow.left { left: 12px; }
  .arrow.right { right: 12px; }

  @media (max-width: 420px) {
    :root { --carousel-width: 300px; --carousel-height: 400px; --bottom-extension: 320px; --photo-border:12px; }
    .arrow { font-size:22px; padding:8px; }
  }
</style>
</head>
<body>

  <div class="gift-container" onclick="openGift()">
    <div class="lid">
      <div class="ribbon-horizontal"></div>
      <div class="bow">🎀</div>
    </div>
    <div class="box">
      <div class="ribbon-vertical"></div>
    </div>
  </div>
  <div class="tap-text">Tap to Open 🎁</div>

  <div class="surprise" id="surprise">
    <div class="carousel" aria-label="photo carousel">
      <span class="arrow left" onclick="changePhoto(-1)" aria-hidden="true">&#10094;</span>
      <span class="arrow right" onclick="changePhoto(1)" aria-hidden="true">&#10095;</span>

      <div class="photo-card" id="photoCard">
        <div class="photo-frame">
          <img src="1.jpeg" alt="Photo 1" class="active">
          <img src="2.jpeg" alt="Photo 2">
          <img src="3.jpeg" alt="Photo 3">
          <img src="4.jpeg" alt="Photo 4">
          <img src="5.jpeg" alt="Photo 5">
          <img src="6.jpeg" alt="Photo 6">
        </div>
        <div class="photo-bottom">
          <div class="message" id="messageArea">
            Lorem ipsum dolor sit amet consectetur adipisicing elit. Recusandae rerum fugiat laborum mollitia harum deserunt facilis excepturi, fuga totam doloribus expedita eveniet itaque incidunt dolorum facere qui repellendus id voluptatibus at quia, ipsum deleniti amet. Distinctio, dolorum. Eius, deleniti! Architecto unde, atque adipisci placeat quas tenetur ut, necessitatibus aliquam a, repellendus odio inventore voluptates non exercitationem quo iure impedit ipsam. Error a ipsum, fugiat sequi accusamus, sapiente aperiam deleniti quisquam labore ex corrupti quae optio, libero maxime asperiores impedit assumenda illum aliquam! Dolor suscipit quam corporis ad placeat odio molestias repellat, totam officiis reiciendis dignissimos voluptatum ipsam, aliquid natus laboriosam?
          </div>
        </div>
      </div>
    </div>
  </div>

<script>
  let currentIndex = 0;
  const images = document.querySelectorAll('.photo-frame img');
  function showImage(index){
    images.forEach((img,i)=> img.classList.toggle('active', i === index));
  }

  function changePhoto(direction){
    currentIndex = (currentIndex + direction + images.length) % images.length;
    showImage(currentIndex);
  }

  function openGift(){
    const lid = document.querySelector('.lid');
    const giftBox = document.querySelector('.gift-container');
    lid.style.animation = 'bounceLid .8s ease';
    setTimeout(()=>{
      lid.style.animation = '';
      lid.style.transform = 'rotateX(-140deg)';
      document.querySelector('.tap-text').style.display = 'none';
      createConfetti();
      createFlowers();
      setTimeout(()=> {
        giftBox.style.opacity = '0';
        setTimeout(()=> giftBox.style.display = 'none', 500);
        document.getElementById('surprise').style.display = 'block';
      }, 1000);
    }, 800);
  }

  function createConfetti(){
    const container = document.querySelector('.gift-container');
    for(let i=0;i<60;i++){
      const confetti = document.createElement('div');
      confetti.className = 'confetti';
      confetti.style.left = Math.random()*220 + 'px';
      confetti.style.top = '0px';
      confetti.style.backgroundColor = `hsl(${Math.random()*360},70%,50%)`;
      confetti.style.animationDelay = (Math.random()*0.5) + 's';
      container.appendChild(confetti);
      setTimeout(()=> confetti.remove(), 3000);
    }
  }

  function createFlowers(){
    const flowerIcons = ["🌸","🌹","💐","🌷","🌺"];
    const container = document.querySelector('.gift-container');
    for(let i=0;i<15;i++){
      const flower = document.createElement('div');
      flower.className = 'flower';
      flower.textContent = flowerIcons[Math.floor(Math.random()*flowerIcons.length)];
      flower.style.left = Math.random()*200 + 'px';
      flower.style.bottom = '20px';
      flower.style.animationDelay = (Math.random()*0.5) + 's';
      container.appendChild(flower);
      setTimeout(()=> flower.remove(), 4000);
    }
  }

  showImage(currentIndex);
</script>
</body>
</html>
