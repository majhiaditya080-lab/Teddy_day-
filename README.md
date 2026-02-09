# Teddy_day-
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>For Janki</title>
    <style>
        body, html { margin: 0; padding: 0; background: #000; color: #fff; font-family: sans-serif; overflow: hidden; height: 100%; width: 100%; position: fixed; }
        .screen { display: none; height: 100%; width: 100%; flex-direction: column; align-items: center; justify-content: center; text-align: center; padding: 20px; box-sizing: border-box; }
        .active { display: flex; }
        
        /* Version Tag to check if GitHub updated */
        .debug-tag { position: absolute; top: 5px; left: 5px; font-size: 10px; color: #333; }

        .glitch { font-size: 2rem; color: #ff4d6d; text-transform: uppercase; text-shadow: 2px 2px #00fffc; font-weight: bold; }
        
        #game-box { width: 100%; max-width: 350px; height: 50vh; background: #111; border: 2px solid #444; position: relative; overflow: hidden; }
        #basket { position: absolute; bottom: 5px; width: 70px; height: 50px; background: #4a2c2c; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 30px; transition: left 0.1s; }
        .falling { position: absolute; font-size: 30px; top: -50px; }

        .controls { display: flex; width: 100%; max-width: 350px; gap: 10px; margin-top: 20px; }
        .btn { flex: 1; padding: 25px; background: #222; border: 1px solid #ff4d6d; color: white; font-size: 2rem; border-radius: 10px; -webkit-user-select: none; }
        .start-btn { padding: 20px 40px; background: #ff4d6d; border: none; color: white; font-weight: bold; font-size: 1.2rem; border-radius: 50px; }
    </style>
</head>
<body>
    <div class="debug-tag">VERSION 5.0 - REFRESH IF YOU DONT SEE THIS</div>

    <section id="s1" class="screen active"><h1 id="intro-txt" class="glitch">7th Feb: Rose Day</h1></section>
    
    <section id="s2" class="screen">
        <h2 class="glitch">READY MY LADY?</h2>
        <button class="start-btn" onclick="next(3)">YES, START</button>
    </section>

    <section id="s3" class="screen">
        <p>CATCH THE TEDDIES!<br>Target: 2500 Points<br>Bomb = -500</p>
        <button class="start-btn" onclick="startGame()">GO!</button>
    </section>

    <section id="s4" class="screen">
        <div id="score-val" style="color:#ff4d6d; font-size: 1.5rem; margin-bottom: 10px;">SCORE: 0</div>
        <div id="game-box"><div id="basket">🧺</div></div>
        <div class="controls">
            <button class="btn" onpointerdown="move(-25)">⬅️</button>
            <button class="btn" onpointerdown="move(25)">➡️</button>
        </div>
    </section>

    <section id="s5" class="screen"><div id="quote-area" class="glitch" style="font-size: 1.2rem;"></div></section>

    <section id="s6" class="screen">
        <h1 class="glitch">COMING SOON</h1>
        <div id="timer" style="font-size: 3rem; color: #ff4d6d;">00:00:00</div>
    </section>

    <script>
        let score = 0, bX = 140, gameOn = false;
        const days = ["7th Feb: Rose Day", "8th Feb: Propose Day", "9th Feb: Chocolate Day", "10th Feb: TEDDY DAY"];

        // Step 1: Intro
        let d = 0;
        const i = setInterval(() => {
            d++;
            if(d < 4) document.getElementById('intro-txt').innerText = days[d];
            else { clearInterval(i); next(2); }
        }, 3900);

        function next(n) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById('s'+n).classList.add('active');
        }

        // Step 2: Game
        function move(amt) {
            bX += amt;
            if(bX < 0) bX = 0;
            if(bX > 280) bX = 280;
            document.getElementById('basket').style.left = bX + 'px';
        }

        function startGame() {
            next(4); gameOn = true;
            setInterval(spawn, 1000);
            update();
        }

        function spawn() {
            if(!gameOn) return;
            const items = [{i:'✨',p:250},{i:'🐶',p:200},{i:'🧸',p:100},{i:'👩‍❤️‍👨',p:300},{i:'💣',p:-500}];
            const data = items[Math.floor(Math.random()*items.length)];
            const div = document.createElement('div');
            div.className = 'falling';
            div.innerHTML = data.i;
            div.dataset.p = data.p;
            div.style.left = Math.random() * 300 + 'px';
            document.getElementById('game-box').appendChild(div);
        }

        function update() {
            if(!gameOn) return;
            const fallers = document.querySelectorAll('.falling');
            const b = document.getElementById('basket').getBoundingClientRect();

            fallers.forEach(f => {
                let y = (parseFloat(f.style.top) || -50) + 4;
                f.style.top = y + 'px';
                let r = f.getBoundingClientRect();

                if(r.bottom > b.top && r.left < b.right && r.right > b.left && r.top < b.bottom) {
                    score += parseInt(f.dataset.p);
                    document.getElementById('score-val').innerText = "SCORE: " + (score < 0 ? 0 : score);
                    f.remove();
                    if(score >= 2500) win();
                } else if(y > 500) f.remove();
            });
            requestAnimationFrame(update);
        }

        // Step 3: Win
        function win() {
            gameOn = false; next(5);
            const q = ["You're the best!","Hugs forever!","Janki & Aditya ❤️","Stay Cute!","My Teddy!","I Love You!","Always Together.","Warm Smiles.","Pure Magic.","Coming Soon..."];
            let idx = 0;
            const qi = setInterval(() => {
                if(idx < 10) { document.getElementById('quote-area').innerText = q[idx]; idx++; }
                else { clearInterval(qi); next(6); startFinal(); }
            }, 3000);
        }

        function startFinal() {
            const target = new Date("Feb 11, 2026 00:00:00").getTime();
            setInterval(() => {
                const diff = target - Date.now();
                const h = Math.floor(diff/36e5), m = Math.floor((diff%36e5)/6e4), s = Math.floor((diff%6e4)/1000);
                document.getElementById('timer').innerText = h+"h "+m+"m "+s+"s";
            }, 1000);
        }
    </script>
</body>
</html>


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
