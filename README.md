<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Teddy Catch</title>

<style>
html, body {
    margin: 0;
    padding: 0;
    background: #000;
    color: #fff;
    font-family: sans-serif;
    overflow: hidden;
    height: 100%;
}

.screen {
    display: none;
    height: 100%;
    width: 100%;
    position: absolute;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    padding: 20px;
    box-sizing: border-box;
}

.active { display: flex; }

.glitch {
    font-size: 1.8rem;
    color: #ff4d6d;
    text-transform: uppercase;
    text-shadow: 2px 2px #00fffc;
    font-weight: bold;
}

#game-box {
    width: 100%;
    max-width: 360px;
    height: 55vh;
    max-height: 500px;
    background: #111;
    border: 3px solid #333;
    position: relative;
    overflow: hidden;
    border-radius: 10px;
}

#basket {
    position: absolute;
    bottom: 20px;
    width: 90px;
    height: 60px;
    background: #4a2c2c;
    border-radius: 10px;
    font-size: 35px;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 2px solid #ff4d6d;
    transition: background 0.1s;
}

.falling {
    position: absolute;
    font-size: 35px;
    width: 40px;
    height: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.controls {
    width: 100%;
    max-width: 360px;
    display: flex;
    gap: 15px;
    margin-top: 20px;
}

.btn {
    flex: 1;
    padding: 18px;
    background: #222;
    border: 2px solid #ff4d6d;
    color: #fff;
    font-size: 2rem;
    border-radius: 15px;
    touch-action: none;
}

.start-btn {
    padding: 18px 40px;
    background: #ff4d6d;
    border: none;
    color: #fff;
    font-size: 1.2rem;
    border-radius: 50px;
}
</style>
</head>

<body>

<!-- 🔊 SOUNDS -->
<audio id="snd-click" src="https://assets.mixkit.co/active_storage/sfx/2568/2568-preview.mp3"></audio>
<audio id="snd-catch" src="https://assets.mixkit.co/active_storage/sfx/2003/2003-preview.mp3"></audio>
<audio id="snd-bomb" src="https://assets.mixkit.co/active_storage/sfx/2747/2747-preview.mp3"></audio>
<audio id="snd-win" src="https://assets.mixkit.co/active_storage/sfx/1435/1435-preview.mp3"></audio>

<section id="s1" class="screen active">
    <h1 id="intro-txt" class="glitch"></h1>
</section>

<section id="s2" class="screen">
    <h2 class="glitch">READY MY LADY?</h2>
    <button class="start-btn" onclick="play('snd-click'); show(3)">I AM READY</button>
</section>

<section id="s3" class="screen">
    <h3 style="color:#ff4d6d">MISSION BRIEF</h3>
    <p>Catch Items: 100–300 pts<br>Avoid Bombs: -500 pts<br>Goal: 2500 Points</p>
    <button class="start-btn" onclick="play('snd-click'); startGame()">START GAME</button>
</section>

<section id="s4" class="screen">
    <div id="score-val" style="color:#ff4d6d;font-size:1.6rem;margin-bottom:10px;">SCORE: 0</div>
    <div id="game-box">
        <div id="basket">🧺</div>
    </div>
    <div class="controls">
        <button class="btn" id="lBtn">⬅️</button>
        <button class="btn" id="rBtn">➡️</button>
    </div>
</section>

<section id="s5" class="screen">
    <div id="quote-area" class="glitch" style="font-size:1.3rem;"></div>
</section>

<section id="s6" class="screen">
    <h1 class="glitch">COMING SOON</h1>
    <div id="timer" style="font-size:3rem;color:#ff4d6d;font-family:monospace;"></div>
    <p>Until Promise Day</p>
</section>

<script>
let score = 0, bX = 0, gameOn = false, spawnInt, moveInt;

const days = [
    "7th Feb: Rose Day",
    "8th Feb: Propose Day",
    "9th Feb: Chocolate Day",
    "10th Feb: TEDDY DAY"
];

// 🔊 PLAY SOUND
function play(id){
    const a = document.getElementById(id);
    if(!a) return;
    a.currentTime = 0;
    a.play().catch(()=>{});
}

// INTRO
let d = 0;
intro();
function intro(){
    document.getElementById("intro-txt").innerText = days[d];
    d++;
    if(d < days.length) setTimeout(intro, 3900);
    else setTimeout(()=>show(2), 3900);
}

function show(n){
    document.querySelectorAll(".screen").forEach(s=>s.classList.remove("active"));
    document.getElementById("s"+n).classList.add("active");
}

// CONTROLS
const basket = document.getElementById("basket");
const box = document.getElementById("game-box");

function move(dir){
    clearInterval(moveInt);
    moveInt = setInterval(()=>{
        const maxX = box.clientWidth - basket.offsetWidth;
        bX += dir * 8;
        bX = Math.max(0, Math.min(bX, maxX));
        basket.style.left = bX + "px";
    }, 20);
}

lBtn.onpointerdown = ()=>move(-1);
rBtn.onpointerdown = ()=>move(1);
window.onpointerup = ()=>clearInterval(moveInt);

// GAME
function startGame(){
    show(4);
    score = 0;
    gameOn = true;
    document.getElementById("score-val").innerText = "SCORE: 0";
    bX = (box.clientWidth - basket.offsetWidth)/2;
    basket.style.left = bX + "px";
    spawnInt = setInterval(spawn, 900);
    requestAnimationFrame(update);
}

function spawn(){
    if(!gameOn) return;
    const items = [
        {i:"✨",p:250},{i:"🐶",p:200},{i:"🧸",p:100},
        {i:"👩‍❤️‍👨",p:300},{i:"💣",p:-500}
    ];
    const d = items[Math.floor(Math.random()*items.length)];
    const f = document.createElement("div");
    f.className = "falling";
    f.innerHTML = d.i;
    f.dataset.p = d.p;
    f.style.left = Math.random()*(box.clientWidth-40)+"px";
    f.style.top = "-40px";
    box.appendChild(f);
}

function update(){
    if(!gameOn) return;
    document.querySelectorAll(".falling").forEach(f=>{
        let y = (parseFloat(f.style.top)||-40)+5;
        f.style.top = y+"px";
        const fLeft = parseFloat(f.style.left);
        const fBottom = y+40;
        const basketTop = box.clientHeight-80;

        if(fBottom>=basketTop && fLeft+20>=bX && fLeft<=bX+basket.offsetWidth){
            const pts = parseInt(f.dataset.p);
            score += pts;
            if(score<0) score=0;
            play(pts>0?"snd-catch":"snd-bomb");
            document.getElementById("score-val").innerText = "SCORE: "+score;
            basket.style.background="#ff4d6d";
            setTimeout(()=>basket.style.background="#4a2c2c",100);
            f.remove();
            if(score>=2500) win();
        }
        if(y>box.clientHeight) f.remove();
    });
    requestAnimationFrame(update);
}

function win(){
    gameOn=false;
    clearInterval(spawnInt);
    play("snd-win");
    show(5);
    const q=[
        "Pure Magic ❤️","Warmest Hugs!","Aditya Loves Janki",
        "Always Yours","Teddy Day Joy","Love You Infinity",
        "My Cuddle Queen","Softest Heart","Forever Together","Wait for it..."
    ];
    let i=0;
    const qi=setInterval(()=>{
        if(i<q.length) quote-area.innerText=q[i++];
        else{clearInterval(qi);show(6);startFinal();}
    },3000);
}

function startFinal(){
    const target=new Date("Feb 11, 2026 00:00:00").getTime();
    setInterval(()=>{
        let diff=target-Date.now();
        if(diff<=0){timer.innerText="00:00:00";return;}
        timer.innerText=
            Math.floor(diff/36e5)+"h "+
            Math.floor(diff%36e5/6e4)+"m "+
            Math.floor(diff%6e4/1000)+"s";
    },1000);
}
</script>
</body>
</html>
