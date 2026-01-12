<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Magiczne Pary: Tabliczka Mnożenia</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Quicksand:wght@400;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.5.1/dist/confetti.browser.min.js"></script>
    <style>
        body {
            font-family: 'Quicksand', sans-serif;
            background: linear-gradient(135deg, #fef9e7 0%, #f0f4ff 100%);
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* Karty */
        .card {
            perspective: 1000px;
            cursor: pointer;
            height: 110px;
            width: 90px;
        }

        .card-inner {
            position: relative;
            width: 100%;
            height: 100%;
            text-align: center;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
        }

        .card.flipped .card-inner {
            transform: rotateY(180deg);
        }

        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            border: 4px solid white;
        }

        .card-front {
            background: linear-gradient(45deg, #4f46e5, #3b82f6);
            color: white;
            font-size: 1.5rem;
        }

        .card-back {
            background-color: white;
            transform: rotateY(180deg);
            font-weight: 700;
            font-size: 1.25rem;
            flex-direction: column;
        }

        .hint-number {
            position: absolute;
            top: 5px;
            right: 8px;
            font-size: 0.7rem;
            color: #94a3b8;
            font-weight: 400;
            pointer-events: none;
        }

        .card.matched .card-back {
            background-color: #d1fae5;
            border-color: #34d399;
            color: #065f46;
        }

        .question-card .card-back { color: #1e40af; }
        .answer-card .card-back { color: #be185d; }

        /* Pasek Zapamiętywania */
        #memory-ribbon {
            position: fixed;
            top: -120px;
            left: 50%;
            transform: translateX(-50%);
            background: white;
            padding: 1rem 2rem;
            border-radius: 0 0 25px 25px;
            box-shadow: 0 10px 25px rgba(59, 130, 246, 0.2);
            border: 3px solid #3b82f6;
            border-top: none;
            z-index: 100;
            transition: top 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            display: flex;
            align-items: center;
            gap: 1rem;
        }

        #memory-ribbon.active { top: 0; }

        /* Potwór Stylizacja */
        #monster-container {
            width: 140px;
            height: 140px;
            margin: 0 auto;
            position: relative;
        }
        .monster-body { fill: #3b82f6; transition: fill 0.3s; }
        .monster-horn { fill: #e5e7eb; stroke: #9ca3af; stroke-width: 1; }
        .monster-fur { stroke: #2563eb; stroke-width: 1.5; fill: none; }
        .monster-eye { fill: white; }
        .monster-pupil { fill: black; }
        .monster-mouth { stroke: #1e3a8a; stroke-width: 3; fill: none; stroke-linecap: round; }
        
        .happy .monster-body { fill: #60a5fa; }
        .sad .monster-body { fill: #1e3a8a; }

        /* Menu */
        .menu-btn {
            transition: all 0.2s;
            border-bottom-width: 4px;
        }
        .menu-btn:active {
            transform: translateY(2px);
            border-bottom-width: 2px;
        }
    </style>
</head>
<body class="p-4">
    <!-- Subtelny pasek z wynikiem -->
    <div id="memory-ribbon">
        <div class="text-left">
            <span class="text-[10px] uppercase font-bold text-blue-400 block tracking-wider">Zapamiętaj:</span>
            <div id="ribbon-equation" class="text-3xl font-black text-blue-800"></div>
        </div>
        <div class="text-3xl">🌟</div>
    </div>

    <!-- Overlay Startowy -->
    <div id="menu-overlay" class="fixed inset-0 bg-white/95 backdrop-blur-md z-[60] flex items-center justify-center p-4">
        <div class="max-w-md w-full text-center">
            <div class="mb-4">
                <svg viewBox="0 0 100 100" class="w-32 h-32 mx-auto">
                    <path class="monster-horn" d="M30,25 L20,10 Q25,10 35,20 Z" />
                    <path class="monster-horn" d="M70,25 L80,10 Q75,10 65,20 Z" />
                    <path class="monster-body" d="M20,85 Q50,15 80,85 Z" fill="#3b82f6"/>
                    <circle cx="40" cy="50" r="5" fill="white"/>
                    <circle cx="60" cy="50" r="5" fill="white"/>
                    <path d="M40,70 Q50,80 60,70" stroke="white" stroke-width="2" fill="none"/>
                </svg>
            </div>
            <h2 class="text-3xl font-black text-blue-700 mb-2">Magiczne Pary</h2>
            <p class="text-gray-600 mb-8">Wybierz, co chcesz dziś ćwiczyć!</p>
            
            <div class="grid grid-cols-1 gap-4">
                <button onclick="startGame('mix')" class="menu-btn bg-indigo-600 border-indigo-800 text-white font-bold py-4 rounded-2xl text-xl hover:bg-indigo-700">
                    Mieszana Tabliczka 🎲
                </button>
                
                <div class="bg-blue-50 p-4 rounded-2xl border-2 border-blue-100">
                    <p class="font-bold text-blue-800 mb-3">Ćwicz wybraną cyfrę:</p>
                    <div class="flex flex-wrap justify-center gap-2">
                        <script>
                            for(let i=2; i<=10; i++) {
                                document.write(`<button onclick="startGame(${i})" class="w-10 h-10 bg-white border-2 border-blue-200 rounded-lg font-bold text-blue-600 hover:bg-blue-600 hover:text-white transition-colors">${i}</button>`);
                            }
                        </script>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div id="game-container" class="max-w-4xl mx-auto text-center hidden">
        <div class="flex justify-end mb-2">
            <button id="fullscreen-btn" class="text-blue-500 hover:text-blue-700 flex items-center gap-2 font-semibold transition-all text-xs bg-white/50 px-3 py-1 rounded-lg">
                <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" fill="currentColor" viewBox="0 0 16 16">
                    <path d="M1.5 1a.5.5 0 0 0-.5.5v4a.5.5 0 0 1-1 0v-4A1.5 1.5 0 0 1 1.5 0h4a.5.5 0 0 1 0 1h-4zM10 .5a.5.5 0 0 1 .5-.5h4A1.5 1.5 0 0 1 16 1.5v4a.5.5 0 0 1-1 0v-4a.5.5 0 0 0-.5-.5h-4a.5.5 0 0 1-.5-.5zM.5 10a.5.5 0 0 1 .5.5v4a.5.5 0 0 0 .5.5h4a.5.5 0 0 1 0 1h-4A1.5 1.5 0 0 1 0 14.5v-4a.5.5 0 0 1 .5-.5zm15 0a.5.5 0 0 1 .5.5v4a1.5 1.5 0 0 1-1.5 1.5h-4a.5.5 0 0 1 0-1h4a.5.5 0 0 0 .5-.5v-4a.5.5 0 0 1 .5-.5z"/>
                </svg>
                Pełny Ekran
            </button>
        </div>

        <div id="monster-container">
            <svg viewBox="0 0 100 100">
                <path class="monster-horn" d="M30,25 L20,10 Q25,10 35,20 Z" />
                <path class="monster-horn" d="M70,25 L80,10 Q75,10 65,20 Z" />
                <path id="monster-body" class="monster-body" d="M20,85 L18,80 L22,75 L18,70 L22,65 L18,60 L22,55 L18,50 L22,45 L25,35 Q50,15 75,35 L78,45 L82,50 L78,55 L82,60 L78,65 L82,70 L78,75 L82,80 L80,85 Z" />
                <circle cx="38" cy="48" r="6" fill="white" />
                <circle cx="38" cy="48" r="2.5" fill="black" />
                <circle cx="62" cy="48" r="6" fill="white" />
                <circle cx="62" cy="48" r="2.5" fill="black" />
                <path id="monster-mouth" class="monster-mouth" d="M40,68 Q50,78 60,68" />
                <path class="monster-fur" d="M45,35 L40,40 M55,35 L60,40 M50,30 L50,38" />
            </svg>
        </div>

        <div class="flex justify-center items-center gap-8 my-4">
            <div class="text-left">
                <span class="text-[10px] font-bold text-gray-400 uppercase">Tryb</span>
                <div id="current-mode" class="text-lg font-bold text-blue-600">Mix</div>
            </div>
            <div class="text-left">
                <span class="text-[10px] font-bold text-gray-400 uppercase">Czas</span>
                <div id="timer" class="text-lg font-bold text-indigo-500">00:00</div>
            </div>
            <button onclick="resetToMenu()" class="bg-white border-2 border-gray-100 hover:border-blue-200 p-2 rounded-xl px-4 text-xs font-bold transition-all shadow-sm">Zmień tryb ⚙️</button>
        </div>

        <div id="game-grid" class="grid grid-cols-4 gap-3 justify-items-center max-w-md mx-auto mb-8">
            <!-- 16 Kart -->
        </div>

        <!-- Ekran końcowy -->
        <div id="success-msg" class="hidden fixed inset-0 bg-black/60 flex items-center justify-center z-[70] p-4 backdrop-blur-sm">
            <div class="bg-white p-8 rounded-3xl shadow-2xl border-4 border-blue-400 max-w-sm w-full text-center">
                <h2 class="text-3xl font-bold text-blue-700 mb-2">Brawo! 🏆</h2>
                <p id="final-time" class="text-gray-600 mb-6 font-bold"></p>
                <button onclick="resetToMenu()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-4 rounded-2xl font-bold shadow-lg transition-transform active:scale-95">Graj jeszcze raz! 🌟</button>
            </div>
        </div>
    </div>

    <script>
        const grid = document.getElementById('game-grid');
        const timerEl = document.getElementById('timer');
        const modeEl = document.getElementById('current-mode');
        const menuOverlay = document.getElementById('menu-overlay');
        const gameContainer = document.getElementById('game-container');
        const ribbon = document.getElementById('memory-ribbon');
        const ribbonEq = document.getElementById('ribbon-equation');
        const successMsg = document.getElementById('success-msg');
        const fullscreenBtn = document.getElementById('fullscreen-btn');
        const monsterMouth = document.getElementById('monster-mouth');
        const monster = document.getElementById('monster-container');

        let flippedCards = [];
        let matchedPairs = 0;
        let seconds = 0;
        let timerInterval;

        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        function playSound(freq, duration = 0.2) {
            if (audioCtx.state === 'suspended') audioCtx.resume();
            const osc = audioCtx.createOscillator();
            const g = audioCtx.createGain();
            osc.connect(g); g.connect(audioCtx.destination);
            osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
            g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + duration);
            osc.start(); osc.stop(audioCtx.currentTime + duration);
        }

        fullscreenBtn.addEventListener('click', () => {
            if (!document.fullscreenElement) {
                document.documentElement.requestFullscreen();
            } else {
                document.exitFullscreen();
            }
        });

        function setMonsterState(state) {
            monster.className = state;
            if (state === 'happy') {
                monsterMouth.setAttribute('d', 'M35,65 Q50,88 65,65');
            } else if (state === 'sad') {
                monsterMouth.setAttribute('d', 'M38,72 Q50,65 62,72');
            } else {
                monsterMouth.setAttribute('d', 'M40,68 Q50,78 60,68');
            }
            if(state !== '') setTimeout(() => setMonsterState(''), 1500);
        }

        function resetToMenu() {
            clearInterval(timerInterval);
            successMsg.classList.add('hidden');
            gameContainer.classList.add('hidden');
            menuOverlay.style.display = 'flex';
            ribbon.classList.remove('active');
            flippedCards = [];
            grid.innerHTML = '';
            timerEl.textContent = '00:00';
            seconds = 0;
        }

        function startGame(mode) {
            menuOverlay.style.display = 'none';
            gameContainer.classList.remove('hidden');
            modeEl.textContent = mode === 'mix' ? "Wszystko" : `× ${mode}`;
            initGame(mode);
        }

        function initGame(mode) {
            grid.innerHTML = '';
            matchedPairs = 0;
            seconds = 0;
            startTimer();

            let pool = [];
            if (mode === 'mix') {
                const pairs = [];
                while(pairs.length < 8) {
                    const a = Math.floor(Math.random() * 9) + 2;
                    const b = Math.floor(Math.random() * 9) + 2;
                    const q = `${a} × ${b}`;
                    if(!pairs.some(p => p.q === q)) pairs.push({ q, a: (a*b).toString() });
                }
                pool = pairs;
            } else {
                const multipliers = [1,2,3,4,5,6,7,8,9,10].sort(() => Math.random() - 0.5).slice(0, 8);
                pool = multipliers.map(m => ({ q: `${mode} × ${m}`, a: (mode * m).toString() }));
            }

            let cardsData = [];
            pool.forEach((pair, idx) => {
                cardsData.push({ val: pair.q, id: idx, type: 'q', res: pair.a });
                cardsData.push({ val: pair.a, id: idx, type: 'a' });
            });

            cardsData.sort(() => Math.random() - 0.5);

            cardsData.forEach(c => {
                const card = document.createElement('div');
                card.className = `card ${c.type === 'q' ? 'question-card' : 'answer-card'}`;
                card.dataset.id = c.id;
                card.dataset.type = c.type;
                card.dataset.cleanValue = c.val; 
                
                const hint = c.type === 'q' ? `<span class="hint-number">${c.res}</span>` : '';
                
                card.innerHTML = `
                    <div class="card-inner">
                        <div class="card-front">✨</div>
                        <div class="card-back">${hint}<span class="main-val">${c.val}</span></div>
                    </div>`;
                card.onclick = () => handleFlip(card);
                grid.appendChild(card);
            });
        }

        function handleFlip(card) {
            if (flippedCards.length === 2 || card.classList.contains('flipped') || card.classList.contains('matched')) return;
            
            playSound(350, 0.1);
            card.classList.add('flipped');
            flippedCards.push(card);

            if (flippedCards.length === 2) {
                const [c1, c2] = flippedCards;
                if (c1.dataset.id === c2.dataset.id && c1.dataset.type !== c2.dataset.type) {
                    matchedPairs++;
                    c1.classList.add('matched');
                    c2.classList.add('matched');
                    playSound(440, 0.3);
                    setMonsterState('happy');
                    confetti({ particleCount: 20, spread: 50, origin: { y: 0.85 } });
                    
                    const q = c1.dataset.type === 'q' ? c1.dataset.cleanValue : c2.dataset.cleanValue;
                    const a = c1.dataset.type === 'a' ? c1.dataset.cleanValue : c2.dataset.cleanValue;
                    showRibbon(q, a);

                    flippedCards = [];
                    if (matchedPairs === 8) setTimeout(endGame, 1000);
                } else {
                    setMonsterState('sad');
                    setTimeout(() => {
                        c1.classList.remove('flipped');
                        c2.classList.remove('flipped');
                        flippedCards = [];
                    }, 1000);
                }
            }
        }

        function showRibbon(q, a) {
            ribbonEq.textContent = `${q} = ${a}`;
            ribbon.classList.add('active');
            setTimeout(() => {
                ribbon.classList.remove('active');
            }, 2500);
        }

        function startTimer() {
            clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                seconds++;
                const m = Math.floor(seconds/60).toString().padStart(2,'0');
                const s = (seconds%60).toString().padStart(2,'0');
                timerEl.textContent = `${m}:${s}`;
            }, 1000);
        }

        function endGame() {
            clearInterval(timerInterval);
            document.getElementById('final-time').textContent = `Twój czas: ${timerEl.textContent}`;
            successMsg.classList.remove('hidden');
            confetti({ particleCount: 150, spread: 70, origin: { y: 0.6 } });
        }
    </script>
</body>
</html>
