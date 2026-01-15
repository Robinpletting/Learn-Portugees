<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Taaltrainer Portugees</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { 
            font-family: ui-sans-serif, system-ui, -apple-system, sans-serif;
            background-color: #F1F5F9;
            color: #000;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }
        .card-contrast {
            background-color: #FFF;
            border: 3px solid #000;
            box-shadow: 4px 4px 0px #000;
            border-radius: 28px;
        }
        .locked {
            opacity: 0.4;
            filter: grayscale(1);
            cursor: not-allowed !important;
        }
        .option-btn {
            background-color: #FFF;
            border: 3px solid #000;
            transition: all 0.15s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            cursor: pointer;
            box-shadow: 4px 4px 0px #000;
            min-height: 60px;
            display: flex;
            align-items: center;
            padding: 0.75rem 1.5rem;
            font-weight: 800;
            text-align: left;
            border-radius: 9999px;
            width: 100%;
            font-size: 0.95rem;
        }
        .option-btn:active:not(.locked) {
            transform: translate(2px, 2px);
            box-shadow: 1px 1px 0px #000;
        }
        .correct { 
            background-color: #22C55E !important; 
            color: white !important; 
            border-color: #000 !important; 
            box-shadow: none !important; 
            transform: scale(1.02) translate(2px, 2px);
        }
        .wrong { 
            background-color: #EF4444 !important; 
            color: white !important; 
            border-color: #000 !important; 
            box-shadow: none !important; 
            transform: translate(2px, 2px); 
        }
        
        .stat-pill {
            padding: 6px 14px;
            border: 2px solid #000;
            border-radius: 9999px;
            font-weight: 900;
            font-size: 11px;
            display: flex;
            align-items: center;
            gap: 4px;
        }
        
        .slide-up { animation: slideUp 0.3s ease-out; }
        @keyframes slideUp { from { transform: translateY(100%); } to { transform: translateY(0); } }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="p-0 m-0">

    <div id="app">
        <!-- Dashboard -->
        <div id="dashboard" class="max-w-md mx-auto p-6 pb-24">
            <header class="flex justify-between items-center mb-8 pt-4">
                <div class="card-contrast p-3 px-6 bg-white border-blue-500 rounded-full flex flex-col">
                    <span class="text-[9px] font-black uppercase tracking-widest opacity-50">Voortgang</span>
                    <span id="rank-text" class="text-xs font-bold italic text-blue-600">Beginner</span>
                </div>
                <div class="card-contrast p-3 px-6 bg-white rounded-full flex flex-col items-center">
                    <span class="text-[9px] font-black uppercase tracking-widest opacity-50">Totaal XP</span>
                    <span id="display-xp" class="text-2xl font-black leading-none mt-1">0</span>
                </div>
            </header>

            <!-- Categorie Selector -->
            <div class="flex gap-2 overflow-x-auto mb-8 pb-2 no-scrollbar">
                <button onclick="setCategory('A1')" id="nav-A1" class="px-6 py-2 border-2 border-black rounded-full font-black whitespace-nowrap">A1</button>
                <button onclick="setCategory('A2')" id="nav-A2" class="px-6 py-2 border-2 border-black rounded-full font-black whitespace-nowrap">A2</button>
                <button onclick="setCategory('B1')" id="nav-B1" class="px-6 py-2 border-2 border-black rounded-full font-black whitespace-nowrap">B1</button>
                <button onclick="setCategory('B2')" id="nav-B2" class="px-6 py-2 border-2 border-black rounded-full font-black whitespace-nowrap">B2</button>
                <button onclick="setCategory('C1')" id="nav-C1" class="px-6 py-2 border-2 border-black rounded-full font-black whitespace-nowrap">C1</button>
                <button onclick="setCategory('C2')" id="nav-C2" class="px-6 py-2 border-2 border-black rounded-full font-black whitespace-nowrap">C2</button>
            </div>
            
            <div id="levels-list" class="space-y-4"></div>
        </div>

        <!-- Quiz Interface -->
        <div id="quiz-overlay" class="hidden fixed inset-0 bg-white z-50 flex flex-col">
            <div class="p-4 bg-white border-b-4 border-black">
                <div class="flex justify-between items-center mb-4">
                    <button onclick="confirmExit()" class="w-10 h-10 flex items-center justify-center bg-black text-white rounded-full font-black">✕</button>
                    <div class="flex gap-2">
                        <div class="stat-pill bg-green-100 text-green-700 border-green-700">✓ <span id="stat-correct">0</span></div>
                        <div class="stat-pill bg-red-100 text-red-700 border-red-700">✗ <span id="stat-wrong">0</span></div>
                        <div class="stat-pill bg-slate-100 border-slate-400 font-mono"><span id="stat-count">0</span>/100</div>
                    </div>
                </div>
                <div class="w-full bg-slate-100 border-2 border-black h-4 rounded-full overflow-hidden">
                    <div id="progress-bar" class="bg-blue-500 h-full transition-all duration-300" style="width: 0%"></div>
                </div>
            </div>
            
            <div class="flex-1 flex flex-col p-6 items-center justify-center relative overflow-hidden text-black">
                <div class="w-full max-w-lg">
                    <p id="level-indicator" class="text-center font-black text-[10px] opacity-40 mb-4 uppercase tracking-widest text-blue-600"></p>
                    <h2 id="q-text" class="text-xl md:text-2xl font-black mb-10 text-center leading-tight px-4"></h2>
                    <div id="options-container" class="space-y-3 px-2"></div>
                </div>

                <!-- Feedback Paneel (Fout Antwoord) -->
                <div id="feedback-panel" class="hidden absolute bottom-0 left-0 right-0 bg-white border-t-8 border-black p-8 rounded-t-[40px] shadow-[0_-20px_50px_rgba(0,0,0,0.1)] slide-up z-20">
                    <div class="max-w-md mx-auto">
                        <h3 class="text-red-500 font-black text-2xl mb-1 uppercase italic">Fout!</h3>
                        <p class="text-[10px] font-bold opacity-50 uppercase mb-2">Het juiste antwoord was:</p>
                        <p id="fb-correct" class="text-lg font-extrabold text-black mb-8 p-4 bg-slate-50 border-2 border-dashed border-black rounded-2xl text-center"></p>
                        <button onclick="closeFeedback()" class="w-full bg-black text-white p-5 rounded-full font-black uppercase shadow-[4px_4px_0px_#444] active:translate-y-1">Doorgaan</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Resultaat Scherm -->
        <div id="result-screen" class="hidden fixed inset-0 z-[60] flex flex-col items-center justify-center p-8 text-center bg-white border-[12px] border-black">
            <h2 id="result-title" class="text-4xl font-black mb-6 uppercase italic"></h2>
            <div class="bg-white border-4 border-black p-10 rounded-[48px] mb-10 shadow-[12px_12px_0px_#000]">
                <p id="result-score" class="text-7xl font-black leading-none">0/100</p>
                <p class="mt-4 font-black opacity-40 uppercase tracking-widest text-[10px]">Totaal Score</p>
            </div>
            <p id="result-msg" class="mb-10 font-bold text-lg max-w-xs"></p>
            <button onclick="returnToDashboard()" class="w-full max-w-xs py-5 rounded-full bg-black text-white font-black uppercase text-xl shadow-lg active:scale-95 transition-transform">Klaar</button>
        </div>
    </div>

    <script>
        const MASTER_DATA = {
            "A1": [
                {p:"Olá, como estás?", n:"Hallo, hoe gaat het?"}, {p:"Eu bebo água", n:"Ik drink water"}, {p:"Sim, por favor", n:"Ja, alstublieft"},
                {p:"Obrigado", n:"Bedankt"}, {p:"A maçã é vermelha", n:"De appel is rood"}, {p:"Bom dia, amigo", n:"Goedemorgen, vriend"},
                {p:"O gato dorme", n:"De kat slaapt"}, {p:"Um café preto", n:"Een zwarte koffie"}, {p:"A casa é grande", n:"Het huis is groot"},
                {p:"Onde estás?", n:"Waar ben je?"}, {p:"Eu gosto de pão", n:"Ik hou van brood"}, {p:"Até amanhã", n:"Tot morgen"},
                {p:"Eu sou estudante", n:"Ik ben student"}, {p:"Ela é bonita", n:"Zij is mooi"}, {p:"Nós falamos português", n:"Wij spreken Portugees"}
            ],
            "A2": [
                {p:"Ontem fui ao cinema", n:"Gisteren ben ik naar de bioscoop gegaan"}, {p:"Amanhã vou trabalhar", n:"Morgen ga ik werken"},
                {p:"Eu gosto de comer peixe", n:"Ik hou van vis eten"}, {p:"Estou muito cansado", n:"Ik ben erg moe"},
                {p:"Queres beber algo?", n:"Wil je iets drinken?"}, {p:"O tempo está bom", n:"Het weer is goed"},
                {p:"Onde fica a estação?", n:"Waar is het station?"}, {p:"Quanto custa isto?", n:"Hoeveel kost dit?"}
            ],
            "B1": [
                {p:"Eu acredito na tua palavra", n:"Ik geloof in jouw woord"}, {p:"Espero que tenhas sorte", n:"Ik hoop dat je geluk hebt"},
                {p:"O problema foi resolvido", n:"Het probleem is opgelost"}, {p:"Não me lembro de ti", n:"Ik herinner me jou niet"}
            ],
            "B2": [
                {p:"A sustentabilidade é vital", n:"Duurzaamheid is vitaal"}, {p:"As consequências são graves", n:"De gevolgen zijn ernstig"},
                {p:"É necessário investir mais", n:"Het is nodig om meer te investeren"}
            ],
            "C1": [
                {p:"A sua eloquência é notória", n:"Zijn welsprekendheid is overduidelijk"},
                {p:"Um pressuposto fundamental", n:"Een fundamentele veronderstelling"}
            ],
            "C2": [
                {p:"O tempo é inexorável", n:"De tijd is onverbiddelijk"}, {p:"Uma idiossincrasia bizarra", n:"Een bizarre eigenaardigheid"}
            ]
        };

        const CATS = ["A1", "A2", "B1", "B2", "C1", "C2"];
        let state = JSON.parse(localStorage.getItem('port_trainer_clean')) || {
            xp: 0,
            bestScores: {}, 
            unlockedCats: ["A1"]
        };

        let currentCat = "A1";
        let activeQuiz = null;

        function shuffle(arr) { return [...arr].sort(() => Math.random() - 0.5); }

        function renderDashboard() {
            document.getElementById('display-xp').innerText = state.xp;
            const list = document.getElementById('levels-list');
            list.innerHTML = '';
            
            CATS.forEach(cat => {
                const btn = document.getElementById(`nav-${cat}`);
                const isUnlocked = state.unlockedCats.includes(cat);
                btn.disabled = !isUnlocked;
                btn.className = `px-6 py-2 border-2 border-black rounded-full font-black transition-all ${!isUnlocked ? 'locked' : (cat === currentCat ? 'bg-yellow-400 shadow-[3px_3px_0px_#000] -translate-y-0.5' : 'bg-white hover:bg-slate-50')}`;
            });

            const rank = state.xp > 5000 ? "Portugees Expert" : (state.xp > 2000 ? "Gevorderd" : (state.xp > 500 ? "Leerling" : "Beginner"));
            document.getElementById('rank-text').innerText = rank;

            for(let i=1; i<=10; i++) {
                const levelId = `${currentCat}-${i}`;
                const score = state.bestScores[levelId] || 0;
                const isUnlocked = i === 1 || (state.bestScores[`${currentCat}-${i-1}`] >= 80);
                
                const card = document.createElement('div');
                card.className = `p-5 rounded-[32px] border-4 border-black flex items-center gap-4 bg-white shadow-[4px_4px_0px_#000] transition-transform ${!isUnlocked ? 'locked' : 'cursor-pointer active:scale-95'}`;
                
                if(isUnlocked) card.onclick = () => startQuiz(levelId);
                
                card.innerHTML = `
                    <div class="w-12 h-12 shrink-0 rounded-full flex items-center justify-center text-lg border-2 border-black font-black ${score >= 80 ? 'bg-green-400' : (isUnlocked ? 'bg-blue-400 text-white' : 'bg-slate-100')}">${i}</div>
                    <div class="flex-1">
                        <div class="flex justify-between font-black text-[10px] uppercase text-black mb-1.5">
                            <span>Level ${i} ${!isUnlocked ? '🔒' : ''}</span>
                            <span>${score}%</span>
                        </div>
                        <div class="w-full bg-slate-100 border-2 border-black h-3 rounded-full overflow-hidden">
                            <div class="bg-blue-600 h-full transition-all duration-700" style="width: ${score}%"></div>
                        </div>
                    </div>
                `;
                list.appendChild(card);
            }
        }

        function startQuiz(id) {
            const cat = id.split('-')[0];
            const basePool = MASTER_DATA[cat] || MASTER_DATA["A1"];
            
            const questions = [];
            for(let i=0; i<100; i++) {
                const correctItem = basePool[i % basePool.length];
                const distractors = shuffle(basePool.filter(x => x.n !== correctItem.n)).slice(0, 3).map(x => x.n);
                const options = shuffle([correctItem.n, ...distractors]);
                questions.push({
                    q: correctItem.p,
                    a: options,
                    c: options.indexOf(correctItem.n),
                    correctTxt: correctItem.n
                });
            }

            activeQuiz = { id, questions: shuffle(questions), index: 0, correct: 0, wrong: 0 };
            document.getElementById('quiz-overlay').classList.remove('hidden');
            document.getElementById('level-indicator').innerText = `Training ${cat} • Level ${id.split('-')[1]}`;
            renderQuestion();
            updateStats();
        }

        function renderQuestion() {
            const q = activeQuiz.questions[activeQuiz.index];
            document.getElementById('q-text').innerText = q.q;
            document.getElementById('feedback-panel').classList.add('hidden');
            const container = document.getElementById('options-container');
            container.innerHTML = '';

            q.a.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = "option-btn";
                btn.innerText = opt;
                btn.onclick = () => {
                    // Blokkeer alle knoppen om dubbelklikken te voorkomen
                    document.querySelectorAll('.option-btn').forEach(b => b.onclick = null);
                    
                    if(idx === q.c) {
                        btn.classList.add('correct');
                        activeQuiz.correct++;
                        // Langer wachten (800ms) zodat de gebruiker het groene succes ziet
                        setTimeout(next, 800);
                    } else {
                        btn.classList.add('wrong');
                        activeQuiz.wrong++;
                        showFeedback(q.correctTxt);
                    }
                };
                container.appendChild(btn);
            });
        }

        function showFeedback(txt) {
            document.getElementById('fb-correct').innerText = txt;
            document.getElementById('feedback-panel').classList.remove('hidden');
        }

        function closeFeedback() {
            document.getElementById('feedback-panel').classList.add('hidden');
            next();
        }

        function next() {
            activeQuiz.index++;
            updateStats();
            if(activeQuiz.index < 100) renderQuestion();
            else finishQuiz();
        }

        function updateStats() {
            document.getElementById('stat-correct').innerText = activeQuiz.correct;
            document.getElementById('stat-wrong').innerText = activeQuiz.wrong;
            document.getElementById('stat-count').innerText = activeQuiz.index;
            document.getElementById('progress-bar').style.width = `${activeQuiz.index}%`;
        }

        function finishQuiz() {
            const scorePercent = activeQuiz.correct; 
            const success = scorePercent >= 80;
            const cat = activeQuiz.id.split('-')[0];
            const levelNum = parseInt(activeQuiz.id.split('-')[1]);

            state.bestScores[activeQuiz.id] = Math.max(state.bestScores[activeQuiz.id] || 0, scorePercent);
            state.xp += activeQuiz.correct * 2;

            if(success && levelNum === 10) {
                const nextIdx = CATS.indexOf(cat) + 1;
                if(nextIdx < CATS.length && !state.unlockedCats.includes(CATS[nextIdx])) {
                    state.unlockedCats.push(CATS[nextIdx]);
                }
            }

            const screen = document.getElementById('result-screen');
            screen.classList.remove('hidden');
            screen.style.backgroundColor = success ? '#BBF7D0' : '#FECACA';
            document.getElementById('result-title').innerText = success ? "SUCCES!" : "PROBEER OPNIEUW";
            document.getElementById('result-score').innerText = `${activeQuiz.correct}/100`;
            document.getElementById('result-msg').innerText = success 
                ? "Level voltooid! Je hebt de drempel van 80% gehaald." 
                : "Helaas, je hebt minder dan 80% gescoord. Probeer het nog eens.";
            
            save();
        }

        function returnToDashboard() {
            document.getElementById('result-screen').classList.add('hidden');
            document.getElementById('quiz-overlay').classList.add('hidden');
            renderDashboard();
        }

        function confirmExit() {
            if(confirm("Wil je echt stoppen? Je voortgang in dit level (100 vragen) gaat verloren.")) {
                document.getElementById('quiz-overlay').classList.add('hidden');
            }
        }

        function save() { localStorage.setItem('port_trainer_clean', JSON.stringify(state)); }
        function setCategory(c) { currentCat = c; renderDashboard(); }

        renderDashboard();
    </script>
</body>
</html>

