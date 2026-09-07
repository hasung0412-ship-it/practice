<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>진수 변환 퀴즈 게임</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Pretendard:wght@400;600;700;800&display=swap');
        body {
            font-family: 'Pretendard', sans-serif;
        }
        .bit-card {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .bit-card:active {
            transform: scale(0.92);
        }
        @keyframes success-bounce {
            0%, 100% { transform: translateY(0) scale(1); }
            50% { transform: translateY(-10px) scale(1.05); }
        }
        .animate-success {
            animation: success-bounce 0.5s ease-in-out;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col items-center justify-center p-4">

    <!-- App Container -->
    <div class="w-full max-w-4xl bg-slate-800 border border-slate-700 rounded-2xl shadow-2xl overflow-hidden my-auto">
        
        <!-- Header & Mode Selection -->
        <header class="bg-slate-900/90 p-6 border-b border-slate-700 text-center relative">
            <!-- Continuous Correct Answer Streak Badge -->
            <div class="absolute top-4 right-4 bg-slate-800 border border-amber-500/50 px-3 py-1 rounded-full text-xs font-bold text-amber-400 flex items-center gap-1 shadow-md">
                🔥 연속 정답: <span id="streak-count" class="text-sm font-extrabold text-amber-300">0</span>
            </div>

            <h1 class="text-2xl md:text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-sky-400 to-indigo-400 mb-4">
                진수 변환 퀴즈 게임
            </h1>
            
            <!-- Mode Switcher Buttons -->
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-2 bg-slate-950 p-1.5 rounded-xl border border-slate-800 max-w-2xl mx-auto">
                <button id="btn-mode1" onclick="switchMode('bin-dec')" 
                    class="mode-btn py-2.5 px-4 rounded-lg font-semibold text-sm transition-all bg-indigo-600 text-white shadow-lg">
                    2진수 ↔ 10진수
                </button>
                <button id="btn-mode2" onclick="switchMode('hex-dec')" 
                    class="mode-btn py-2.5 px-4 rounded-lg font-semibold text-sm transition-all text-slate-400 hover:text-white hover:bg-slate-800">
                    16진수 ↔ 10진수
                </button>
                <button id="btn-mode3" onclick="switchMode('bin-hex')" 
                    class="mode-btn py-2.5 px-4 rounded-lg font-semibold text-sm transition-all text-slate-400 hover:text-white hover:bg-slate-800">
                    2진수 ↔ 16진수
                </button>
            </div>
        </header>

        <!-- Main Content Area -->
        <main class="p-6 md:p-8 space-y-6">
            
            <!-- Target Challenge Card -->
            <div id="target-box" class="bg-slate-950 border-2 border-indigo-500/40 rounded-2xl p-6 text-center shadow-inner relative overflow-hidden">
                <div class="text-xs md:text-sm font-bold text-indigo-400 uppercase tracking-wider mb-2">목표 도전 과제</div>
                <div id="target-question-text" class="text-2xl md:text-4xl font-black text-white tracking-wide">
                    10진수 <span class="text-indigo-400">?</span>를 2진수로 만드세요!
                </div>
                
                <!-- Action Controls: Next / Hint -->
                <div class="mt-4 flex items-center justify-center gap-3">
                    <button onclick="generateNewProblem()" class="bg-slate-800 hover:bg-slate-700 text-xs md:text-sm font-semibold px-4 py-2 rounded-lg border border-slate-600 transition-all text-slate-300">
                        🔄 다른 문제
                    </button>
                    <button onclick="toggleHint()" class="bg-slate-800 hover:bg-slate-700 text-xs md:text-sm font-semibold px-4 py-2 rounded-lg border border-slate-600 transition-all text-amber-300">
                        💡 힌트 보기
                    </button>
                </div>

                <!-- Hint Banner (Hidden by default) -->
                <div id="hint-banner" class="hidden mt-4 pt-3 border-t border-slate-800 text-xs md:text-sm text-slate-400 font-mono">
                    <span id="hint-text"></span>
                </div>
            </div>

            <!-- SUCCESS BANNER (Initially Hidden) -->
            <div id="success-message" class="hidden bg-emerald-950/90 border-2 border-emerald-400 rounded-2xl p-6 text-center animate-success shadow-2xl shadow-emerald-900/50">
                <div class="text-3xl md:text-4xl font-black text-emerald-300 mb-2">🎉 정답입니다!</div>
                <p class="text-emerald-200 text-sm md:text-base mb-4">완벽하게 변환에 성공하셨습니다!</p>
                <button onclick="generateNewProblem()" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-extrabold text-base px-6 py-2.5 rounded-xl transition-all shadow-lg hover:scale-105">
                    다음 문제 풀기 ➔
                </button>
            </div>

            <!-- MODE 1: 2진수 ↔ 10진수 -->
            <div id="section-bin-dec" class="space-y-6">
                <!-- 8비트 카드 조작 영역 -->
                <div>
                    <div class="flex justify-between items-center mb-3 px-1">
                        <span class="text-slate-400 text-xs md:text-sm font-semibold tracking-wider uppercase">비트 카드를 클릭해 0과 1을 맞추세요</span>
                        <button onclick="resetCurrentBits()" class="text-xs text-slate-400 hover:text-indigo-400 underline">모두 0으로</button>
                    </div>
                    <div id="bit-container-1" class="grid grid-cols-4 sm:grid-cols-8 gap-2 md:gap-3">
                        <!-- JS에서 8개 비트 카드 생성 -->
                    </div>
                </div>

                <!-- 현재 입력 상태 계산 가이드 -->
                <div class="bg-slate-950/60 rounded-xl p-4 border border-slate-800 text-xs md:text-sm text-slate-300 font-mono">
                    <div class="flex justify-between items-center mb-1">
                        <span class="text-slate-500">현재 입력된 값 계산:</span>
                        <span id="current-val-display-1" class="text-indigo-400 font-bold text-base">0</span>
                    </div>
                    <div id="math-expression-1" class="text-slate-400 font-semibold truncate">0 = 0</div>
                </div>
            </div>

            <!-- MODE 2: 16진수 ↔ 10진수 -->
            <div id="section-hex-dec" class="hidden space-y-6">
                <div class="flex flex-col items-center justify-center bg-slate-900/50 p-6 rounded-2xl border border-slate-700/50">
                    <span class="text-slate-400 text-xs md:text-sm font-semibold tracking-wider uppercase mb-4">16진수 각 자릿수 설정</span>
                    <div class="flex items-center gap-6">
                        <span class="text-3xl font-extrabold text-slate-500">0x</span>
                        <!-- Hex Digit 1 (High) -->
                        <div class="flex flex-col items-center">
                            <button onclick="cycleHex(1, 1)" class="text-slate-300 hover:text-emerald-400 p-2 text-xl font-bold">▲</button>
                            <div id="hex-card-1" class="w-20 h-24 bg-emerald-950/80 border-2 border-emerald-500/50 rounded-2xl flex items-center justify-center text-4xl font-black text-emerald-400 shadow-lg">0</div>
                            <button onclick="cycleHex(1, -1)" class="text-slate-300 hover:text-emerald-400 p-2 text-xl font-bold">▼</button>
                            <span class="text-xs text-slate-400 mt-1">16의 자리</span>
                        </div>
                        <!-- Hex Digit 0 (Low) -->
                        <div class="flex flex-col items-center">
                            <button onclick="cycleHex(0, 1)" class="text-slate-300 hover:text-emerald-400 p-2 text-xl font-bold">▲</button>
                            <div id="hex-card-0" class="w-20 h-24 bg-emerald-950/80 border-2 border-emerald-500/50 rounded-2xl flex items-center justify-center text-4xl font-black text-emerald-400 shadow-lg">0</div>
                            <button onclick="cycleHex(0, -1)" class="text-slate-300 hover:text-emerald-400 p-2 text-xl font-bold">▼</button>
                            <span class="text-xs text-slate-400 mt-1">1의 자리</span>
                        </div>
                    </div>
                </div>

                <!-- 현재 입력 상태 계산 가이드 -->
                <div class="bg-slate-950/60 rounded-xl p-4 border border-slate-800 text-xs md:text-sm text-slate-300 font-mono">
                    <div class="flex justify-between items-center mb-1">
                        <span class="text-slate-500">현재 입력된 값 계산:</span>
                        <span id="current-val-display-2" class="text-emerald-400 font-bold text-base">0</span>
                    </div>
                    <div id="math-expression-2" class="text-slate-400 font-semibold">0x00 = (0 × 16) + (0 × 1) = 0</div>
                </div>
            </div>

            <!-- MODE 3: 2진수 ↔ 16진수 -->
            <div id="section-bin-hex" class="hidden space-y-6">
                <!-- 8비트 카드 조작 (4비트씩 그룹) -->
                <div>
                    <div class="flex justify-between items-center mb-3 px-1">
                        <span class="text-slate-400 text-xs md:text-sm font-semibold tracking-wider uppercase">2진수 비트 카드를 맞춰 16진수 결과를 완성하세요</span>
                        <button onclick="resetCurrentBits()" class="text-xs text-slate-400 hover:text-purple-400 underline">모두 0으로</button>
                    </div>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <!-- High Nibble (상위 4비트) -->
                        <div class="bg-purple-950/20 border border-purple-500/20 p-4 rounded-xl">
                            <div class="text-xs text-purple-400 font-semibold mb-2 text-center">첫 번째 16진수 자리 (상위 4비트)</div>
                            <div id="bit-container-3-high" class="grid grid-cols-4 gap-2"></div>
                        </div>
                        <!-- Low Nibble (하위 4비트) -->
                        <div class="bg-purple-950/20 border border-purple-500/20 p-4 rounded-xl">
                            <div class="text-xs text-purple-400 font-semibold mb-2 text-center">두 번째 16진수 자리 (하위 4비트)</div>
                            <div id="bit-container-3-low" class="grid grid-cols-4 gap-2"></div>
                        </div>
                    </div>
                </div>

                <!-- 현재 입력 상태 계산 가이드 -->
                <div class="bg-slate-950/60 rounded-xl p-4 border border-slate-800 text-xs md:text-sm text-slate-300 font-mono">
                    <div class="flex justify-between items-center mb-1">
                        <span class="text-slate-500">현재 입력된 비트의 16진수 값:</span>
                        <span id="current-val-display-3" class="text-purple-400 font-bold text-base">0x00</span>
                    </div>
                    <div id="math-expression-3" class="text-slate-400 font-semibold">
                        [0000 = 0x0] + [0000 = 0x0] ➔ 0x00
                    </div>
                </div>
            </div>

        </main>
        
        <!-- Footer Info -->
        <footer class="bg-slate-950 p-4 border-t border-slate-800 text-center text-xs text-slate-500">
            카드 조작을 통해 목표 숫자를 맞히면 자동으로 정답 체크가 진행됩니다!
        </footer>
    </div>

    <!-- Application JavaScript Logic -->
    <script>
        // 전역 상태 관리
        let targetValue = 0;       // 정답 목표 값 (0 ~ 255)
        let userValue = 0;         // 현재 사용자가 맞춘 값 (0 ~ 255)
        let currentMode = 'bin-dec'; // 'bin-dec', 'hex-dec', 'bin-hex'
        let streak = 0;            // 연속 정답 횟수
        let isSolved = false;      // 현재 문제 정답 여부

        // 비트 별 가중치 배열 (MSB -> LSB)
        const bitWeights = [128, 64, 32, 16, 8, 4, 2, 1];

        // 초기 시작
        window.onload = function() {
            initBitCardsMode1();
            initBitCardsMode3();
            generateNewProblem();
        };

        // 새로운 문제 생성
        function generateNewProblem() {
            // 이전 문제의 정답 값을 피해서 새 값 뽑기
            let newTarget;
            do {
                newTarget = Math.floor(Math.random() * 256); // 0 ~ 255
            } while (newTarget === targetValue);

            targetValue = newTarget;
            userValue = 0; // 사용자 입력 초기화
            isSolved = false;

            // UI 갱신
            document.getElementById('success-message').classList.add('hidden');
            document.getElementById('hint-banner').classList.add('hidden');
            
            updateTargetText();
            updateUI();
        }

        // 목표 텍스트 표시 설정
        function updateTargetText() {
            const questionEl = document.getElementById('target-question-text');
            const targetHex = targetValue.toString(16).padStart(2, '0').toUpperCase();
            const targetBin = targetValue.toString(2).padStart(8, '0');

            if (currentMode === 'bin-dec') {
                questionEl.innerHTML = `10진수 <span class="text-indigo-400 font-extrabold">${targetValue}</span>에 맞게 2진수 비트를 구성하세요!`;
            } else if (currentMode === 'hex-dec') {
                questionEl.innerHTML = `10진수 <span class="text-emerald-400 font-extrabold">${targetValue}</span>에 대응하는 16진수 카드를 맞추세요!`;
            } else if (currentMode === 'bin-hex') {
                questionEl.innerHTML = `16진수 <span class="text-purple-400 font-extrabold">0x${targetHex}</span>에 맞는 2진수 비트를 구성하세요!`;
            }
        }

        // 힌트 보기 토글
        function toggleHint() {
            const hintBanner = document.getElementById('hint-banner');
            const hintText = document.getElementById('hint-text');
            
            const hexVal = targetValue.toString(16).padStart(2, '0').toUpperCase();
            const binVal = targetValue.toString(2).padStart(8, '0');

            if (currentMode === 'bin-dec') {
                hintText.textContent = `💡 힌트: 10진수 ${targetValue} = 16진수(0x${hexVal}) / 2진수(${binVal.slice(0, 4)} ${binVal.slice(4)})`;
            } else if (currentMode === 'hex-dec') {
                hintText.textContent = `💡 힌트: 상위 자리는 ${Math.floor(targetValue / 16)} (${Math.floor(targetValue / 16).toString(16).toUpperCase()}), 하위 자리는 ${targetValue % 16} (${(targetValue % 16).toString(16).toUpperCase()})입니다.`;
            } else if (currentMode === 'bin-hex') {
                hintText.textContent = `💡 힌트: 상위 4비트는 10진수 ${Math.floor(targetValue / 16)}, 하위 4비트는 10진수 ${targetValue % 16}의 합입니다.`;
            }

            hintBanner.classList.toggle('hidden');
        }

        // 모드 전환
        function switchMode(mode) {
            currentMode = mode;
            
            // 모드 버튼 스타일 변경
            const btn1 = document.getElementById('btn-mode1');
            const btn2 = document.getElementById('btn-mode2');
            const btn3 = document.getElementById('btn-mode3');

            const activeClasses = 'bg-indigo-600 text-white shadow-lg';
            const inactiveClasses = 'text-slate-400 hover:text-white hover:bg-slate-800';

            btn1.className = `mode-btn py-2.5 px-4 rounded-lg font-semibold text-sm transition-all ${mode === 'bin-dec' ? activeClasses : inactiveClasses}`;
            btn2.className = `mode-btn py-2.5 px-4 rounded-lg font-semibold text-sm transition-all ${mode === 'hex-dec' ? activeClasses : inactiveClasses}`;
            btn3.className = `mode-btn py-2.5 px-4 rounded-lg font-semibold text-sm transition-all ${mode === 'bin-hex' ? activeClasses : inactiveClasses}`;

            // 섹션 표시 토글
            document.getElementById('section-bin-dec').classList.toggle('hidden', mode !== 'bin-dec');
            document.getElementById('section-hex-dec').classList.toggle('hidden', mode !== 'hex-dec');
            document.getElementById('section-bin-hex').classList.toggle('hidden', mode !== 'bin-hex');

            // 모드 변경 시 새로운 문제 출제
            generateNewProblem();
        }

        // Mode 1: 비트 카드 초기화
        function initBitCardsMode1() {
            const container = document.getElementById('bit-container-1');
            container.innerHTML = '';

            bitWeights.forEach((weight, index) => {
                const card = document.createElement('div');
                card.id = `bit-card-1-${index}`;
                card.onclick = () => toggleBit(index);
                card.className = 'bit-card cursor-pointer bg-slate-950 border-2 border-slate-700 hover:border-indigo-500 rounded-xl p-2 md:p-3 flex flex-col items-center justify-between transition-all select-none';
                
                card.innerHTML = `
                    <span class="text-[10px] md:text-xs font-bold text-slate-500 mb-1">${weight}</span>
                    <span id="bit-val-1-${index}" class="text-2xl md:text-3xl font-black text-slate-600 my-1">0</span>
                    <span class="text-[9px] text-slate-600">2^${7 - index}</span>
                `;
                container.appendChild(card);
            });
        }

        // Mode 3: 비트 카드 초기화
        function initBitCardsMode3() {
            const containerHigh = document.getElementById('bit-container-3-high');
            const containerLow = document.getElementById('bit-container-3-low');
            containerHigh.innerHTML = '';
            containerLow.innerHTML = '';

            bitWeights.forEach((weight, index) => {
                const card = document.createElement('div');
                card.id = `bit-card-3-${index}`;
                card.onclick = () => toggleBit(index);
                card.className = 'bit-card cursor-pointer bg-slate-950 border-2 border-slate-700 hover:border-purple-500 rounded-xl p-2 flex flex-col items-center justify-between transition-all select-none';
                
                const nibbleWeight = bitWeights[index % 4];

                card.innerHTML = `
                    <span class="text-[10px] font-bold text-slate-500 mb-1">${nibbleWeight}</span>
                    <span id="bit-val-3-${index}" class="text-2xl font-black text-slate-600 my-1">0</span>
                `;

                if (index < 4) {
                    containerHigh.appendChild(card);
                } else {
                    containerLow.appendChild(card);
                }
            });
        }

        // 비트 클릭 토글
        function toggleBit(bitIndex) {
            if (isSolved) return; // 정답 맞힌 후 조작 방지
            const mask = 1 << (7 - bitIndex);
            userValue = userValue ^ mask;
            updateUI();
            checkAnswer();
        }

        // 사용자 비트 초기화
        function resetCurrentBits() {
            if (isSolved) return;
            userValue = 0;
            updateUI();
        }

        // Mode 2: Hex 클릭 상/하 변경
        function cycleHex(digitIndex, direction) {
            if (isSolved) return;
            let high = Math.floor(userValue / 16);
            let low = userValue % 16;

            if (digitIndex === 1) {
                high = (high + direction + 16) % 16;
            } else {
                low = (low + direction + 16) % 16;
            }

            userValue = (high * 16) + low;
            updateUI();
            checkAnswer();
        }

        // 정답 검사
        function checkAnswer() {
            if (userValue === targetValue && !isSolved) {
                isSolved = true;
                streak++;
                document.getElementById('streak-count').textContent = streak;
                document.getElementById('success-message').classList.remove('hidden');
                document.getElementById('hint-banner').classList.add('hidden');
            }
        }

        // UI 업데이트
        function updateUI() {
            userValue = Math.max(0, Math.min(255, userValue));

            const binaryString = userValue.toString(2).padStart(8, '0');
            const bits = binaryString.split('').map(Number);

            // 1. Mode 1 UI 업데이트
            bits.forEach((bit, idx) => {
                const card = document.getElementById(`bit-card-1-${idx}`);
                const valText = document.getElementById(`bit-val-1-${idx}`);
                if (!card || !valText) return;

                valText.textContent = bit;
                if (bit === 1) {
                    card.className = 'bit-card cursor-pointer bg-indigo-950/90 border-2 border-indigo-400 rounded-xl p-2 md:p-3 flex flex-col items-center justify-between transition-all select-none shadow-lg shadow-indigo-500/20 scale-105';
                    valText.className = 'text-2xl md:text-3xl font-black text-indigo-300 my-1';
                } else {
                    card.className = 'bit-card cursor-pointer bg-slate-950 border-2 border-slate-800 hover:border-slate-700 rounded-xl p-2 md:p-3 flex flex-col items-center justify-between transition-all select-none';
                    valText.className = 'text-2xl md:text-3xl font-black text-slate-600 my-1';
                }
            });

            document.getElementById('current-val-display-1').textContent = userValue;
            const activeTerms1 = bitWeights.filter((w, idx) => bits[idx] === 1);
            document.getElementById('math-expression-1').textContent = 
                activeTerms1.length > 0 ? activeTerms1.join(' + ') + ` = ${userValue}` : '0 = 0';

            // 2. Mode 2 UI 업데이트
            const hexHigh = Math.floor(userValue / 16).toString(16).toUpperCase();
            const hexLow = (userValue % 16).toString(16).toUpperCase();

            document.getElementById('hex-card-1').textContent = hexHigh;
            document.getElementById('hex-card-0').textContent = hexLow;
            document.getElementById('current-val-display-2').textContent = userValue;

            const highDec = Math.floor(userValue / 16);
            const lowDec = userValue % 16;
            document.getElementById('math-expression-2').textContent = 
                `0x${hexHigh}${hexLow} = (${hexHigh}[${highDec}] × 16) + (${hexLow}[${lowDec}] × 1) = ${userValue}`;

            // 3. Mode 3 UI 업데이트
            bits.forEach((bit, idx) => {
                const card = document.getElementById(`bit-card-3-${idx}`);
                const valText = document.getElementById(`bit-val-3-${idx}`);
                if (!card || !valText) return;

                valText.textContent = bit;
                if (bit === 1) {
                    card.className = 'bit-card cursor-pointer bg-purple-950/90 border-2 border-purple-400 rounded-xl p-2 flex flex-col items-center justify-between transition-all select-none shadow-lg shadow-purple-500/20';
                    valText.className = 'text-2xl font-black text-purple-300 my-1';
                } else {
                    card.className = 'bit-card cursor-pointer bg-slate-950 border-2 border-slate-800 hover:border-slate-700 rounded-xl p-2 flex flex-col items-center justify-between transition-all select-none';
                    valText.className = 'text-2xl font-black text-slate-600 my-1';
                }
            });

            const hexFull = userValue.toString(16).padStart(2, '0').toUpperCase();
            document.getElementById('current-val-display-3').textContent = `0x${hexFull}`;

            const highBits = binaryString.slice(0, 4);
            const lowBits = binaryString.slice(4, 8);
            document.getElementById('math-expression-3').textContent = 
                `[${highBits} = 0x${hexHigh}] + [${lowBits} = 0x${hexLow}] ➔ 0x${hexFull}`;
        }
    </script>
</body>
</html>
