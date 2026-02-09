# Teddy_day-
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>For Janki ❤️</title>
    <style>
        :root { --pink: #ff4d6d; --cyan: #00fffc; }
        * { box-sizing: border-box; touch-action: none; -webkit-tap-highlight-color: transparent; }
        body, html { margin: 0; padding: 0; background: #000; color: #fff; font-family: 'Courier New', monospace; overflow: hidden; height: 100%; width: 100%; }

        /* The Glitch Intro */
        .glitch { font-size: 2rem; font-weight: bold; text-align: center; text-transform: uppercase; color: #fff; text-shadow: 2px 2px var(--pink), -2px -2px var(--cyan); animation: glitch-anim 0.2s infinite; }
        @keyframes glitch-anim { 0% { transform: translate(0); } 20% { transform: translate(-2px, 2px); } 40% { transform: translate(-2px, -2px); } 60% { transform: translate(2px, 2px); } 80% { transform: translate(2px, -2px); } 100% { transform: translate(0); } }

        .screen { display: none; height: 100vh; width: 100vw; flex-direction: column; align-items: center; justify-content: center; position: absolute; padding: 20px; text-align: center; }
        .active { display: flex; }

        /* Game Board */
        #game-container { width: 90vw; height: 60vh; background: #111; border: 3px solid #333; position: relative; overflow: hidden; border-radius: 10px; }
        #basket { position: absolute; bottom: 10px; left: 50%; width: 70px; height: 50px; background: #4a2c2c; border-radius: 0 0 15px 15px; border: 2px solid #633; display: flex; justify-content: center; align-items: center; font-size: 30px; transform: translateX(-50%); z-index: 10; }
        .falling-item { position: absolute; font-size: 35px; z-index: 5; top: -50px; }

        /* Buttons */
        .controls { display: flex; gap: 20px; width: 90vw; margin-top: 20px; justify-content: center; }
        .btn { background: #222; border: 2px solid #444; color: white; padding: 25px 40px; font-size: 2rem; border-radius: 15px; flex: 1; text-align: center; }
        .btn:active { background: var(--pink); }
        .start-btn { background: var(--pink); border: none; padding: 20px 40px; font-size: 1.5rem; font-weight: bold; color: white; border-radius: 50px; margin-top: 20px; cursor: pointer; }

        #score-display { font-size: 1.5rem; color: var(--pink); margin-bottom: 10px; }
        #countdown-timer { font-size: 3rem; color: var(--pink); margin-top: 20px; }
    </style>
</head>
<body>

    <section id="screen-intro" class="screen active">
        <h1 id="intro-text" class="glitch">7th Feb: Rose Day</h1>
    </section>

    <section id="screen-challenge" class="screen">
        <h2 class="glitch">ARE YOU READY MY LADY? <br> IT'S A CHALLENGE</h2>
        <button class="start-btn" onclick="goToInstructions()">YES, START</button>
    </section>

    <section id="screen-instructions" class="screen">
        <h2 style="color:var(--pink)">CATCH THE TEDDIES</h2>
        <p>Use Arrows to move the basket.<br>Avoid 💣 (-500 pts)<br>Get <b>2500</b> to win!</p>
        <button class="start-btn" onclick="beginGame()">START MISSION</button>
    </section>

    <section id="screen-game" class="screen">
        <div id="score-display">SCORE: <span id="current-score">0</span> / 2500</div>
        <div id="game-container">
            <div id="basket">🧺</div>
        </div>
        <div class="controls">
            <div class="btn" id="btn-left">⬅️</div>
            <div class="btn" id="btn-right">➡️</div>
        </div>
    </section>

    <section id="screen-slideshow" class="screen">
        <div id="quote-display" class="glitch" style="font-size: 1.5rem; padding: 20px;"></div>
    </section>

    <section id="screen-final" class="screen">
        <h1 class="glitch">COMING SOON</h1>
        <div id="countdown-timer">00:00:00</div>
        <p>Until Promise Day Begins</p>
    </section>

    <script>
        let score = 0, basketPos = 50, moveDirection = 0, gameActive = false;
        const screens = ['screen-intro', 'screen-challenge', 'screen-instructions', 'screen-game', 'screen-slideshow', 'screen-final'];

        function show(id) {
            screens.forEach(s => document.getElementById(s).classList.remove('active'));
            document.getElementById(id).classList.add('active');
        }

        // 1. Intro Glitch
        const days = ["7th Feb: Rose Day", "8th Feb: Propose Day", "9th Feb: Chocolate Day", "10th Feb: TEDDY DAY"];
        let dayCount = 0;
        const introInterval = setInterval(() => {
            dayCount++;
            if(dayCount < 4) {
                document.getElementById('intro-text').innerText = days[dayCount];
            } else {
                clearInterval(introInterval);
                show('screen-challenge');
            }
        }, 3900);

        function goToInstructions() { show('screen-instructions'); }

        // 2. Game Logic
        const basket = document.getElementById('basket');
        const container = document.getElementById('game-container');
        const items = [
            {icon: '✨', pts: 250, msg: "Pure Gold!"},
            {icon: '🐶', pts: 200, msg: "Aditya's Pet!"},
            {icon: '🧸', pts: 100, msg: "Warm Hug!"},
            {icon: '👩‍❤️‍👨', pts: 300, msg: "Bubu Dudu!"},
            {icon: '💣', pts: -500, msg: "BOOM!"}
        ];

        function beginGame() {
            show('screen-game');
            gameActive = true;
            spawnTimer = setInterval(createItem, 1000);
            requestAnimationFrame(update);
        }

        // Controls
        document.getElementById('btn-left').onpointerdown = () => moveDirection = -1.5;
        document.getElementById('btn-right').onpointerdown = () => moveDirection = 1.5;
        document.onpointerup = () => moveDirection = 0;

        function createItem() {
            if(!gameActive) return;
            const item = document.createElement('div');
            const data = items[Math.floor(Math.random() * items.length)];
            item.className = 'falling-item';
            item.innerHTML = data.icon;
            item.dataset.pts = data.pts;
            item.dataset.msg = data.msg;
            item.style.left = Math.random() * 80 + 5 + '%';
            container.appendChild(item);
        }

        function update() {
            if(!gameActive) return;

            // Move Basket
            basketPos += moveDirection;
            if(basketPos < 5) basketPos = 5;
            if(basketPos > 95) basketPos = 95;
            basket.style.left = basketPos + '%';

            // Move Items
            const allItems = document.getElementsByClassName('falling-item');
            const bRect = basket.getBoundingClientRect();

            for(let i=0; i<allItems.length; i++) {
                let item = allItems[i];
                let top = parseFloat(item.style.top || -50);
                top += 3.5;
                item.style.top = top + 'px';

                // Check Collision
                let iRect = item.getBoundingClientRect();
                if(iRect.bottom > bRect.top && iRect.left < bRect.right && iRect.right > bRect.left && iRect.top < bRect.bottom) {
                    score += parseInt(item.dataset.pts);
                    if(score < 0) score = 0;
                    document.getElementById('current-score').innerText = score;
                    item.remove();
                    if(score >= 2500) winGame();
                } else if (top > 500) {
                    item.remove();
                }
            }
            requestAnimationFrame(update);
        }

        // 3. Victory & Slideshow
        function winGame() {
            gameActive = false;
            show('screen-slideshow');
            const quotes = [
                "You're the softest part of my world. 🧸",
                "Warm hugs for my favorite person.",
                "Aditya & Janki: A perfect match.",
                "To the cutest girl ever, Happy Teddy Day!",
                "May your day be as fluffy as a teddy.",
                "Sending you a million virtual hugs.",
                "You're my permanent cuddle partner.",
                "Keep smiling, it suits you best!",
                "My heart beats for you only.",
                "Wait for the next big surprise..."
            ];
            let qIdx = 0;
            const qDisp = document.getElementById('quote-display');
            const qInt = setInterval(() => {
                if(qIdx < quotes.length) {
                    qDisp.innerText = quotes[qIdx];
                    qIdx++;
                } else {
                    clearInterval(qInt);
                    show('screen-final');
                    startFinalTimer();
                }
            }, 3000);
        }

        // 4. Final Countdown
        function startFinalTimer() {
            const target = new Date("Feb 11, 2026 00:00:00").getTime();
            setInterval(() => {
                const now = new Date().getTime();
                const diff = target - now;
                const h = Math.floor(diff / (1000 * 60 * 60));
                const m = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
                const s = Math.floor((diff % (1000 * 60)) / 1000);
                document.getElementById('countdown-timer').innerText = `${h}h ${m}m ${s}s`;
            }, 1000);
        }
    </script>
</body>
</html>
