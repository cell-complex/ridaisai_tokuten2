<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>N○M休 得点管理</title>
    <style>
        * {
            box-sizing: border-box;
        }

        html,
        body {
            width: 100%;
            height: 100%;
            margin: 0;
            overflow: hidden;
            font-family: "Yu Gothic", sans-serif;
            background: #15171b;
            color: #e5e7eb;
            text-align: center;
        }

        /* 上部の問題表示 */
        #problem-area {
            height: 23vh;
            min-height: 130px;
            padding: 12px 5vw 8px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            gap: 7px;
            background: #1d2026;
            border-bottom: 1px solid #393d46;
            position: relative;
        }

        #round-label {
            position: absolute;
            top: 9px;
            left: 16px;
            color: #d0a64e;
            font-size: clamp(11px, 1.1vw, 16px);
        }

        #problem-text {
            color: #81d2fb;
            font-size: clamp(17px, 2.15vw, 32px);
            line-height: 1.3;
            font-weight: bold;
            white-space: pre-wrap;
            overflow-wrap: anywhere;
            max-height: 2.6em;
            overflow: auto;
            text-align: left;
        }

        #problem-answer {
            color: #f0f1f3;
            font-size: clamp(15px, 1.8vw, 27px);
            line-height: 1.25;
            font-weight: bold;
            white-space: pre-wrap;
            overflow-wrap: anywhere;
            max-height: 2.5em;
            overflow: auto;
            text-align: center;
        }

        /* 得点管理コントロール */
        #controls {
            height: 11vh;
            min-height: 65px;
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            justify-content: center;
            gap: 0.65vw;
            padding: 5px 1vw;
        }

        #questionCount {
            font-weight: bold;
            font-size: clamp(18px, 2.1vw, 30px);
            margin: 0 1.1vw;
            color: #e0b65e;
            min-width: 3em;
        }

        button {
            outline: none;
            border: none;
            cursor: pointer;
            transition: 0.15s;
            font-family: inherit;
        }

        button:focus {
            outline: none;
        }

        button:active:not(:disabled) {
            filter: brightness(1.25);
            transform: scale(0.96);
        }

        button:disabled {
            cursor: default;
            opacity: 0.25;
        }

        .controlBtn {
            min-width: clamp(75px, 8vw, 115px);
            height: clamp(34px, 4vw, 52px);
            padding: 0 10px;
            border: 1px solid #444a55;
            border-radius: 3px;
            font-weight: bold;
            background: #292d35;
            color: #e5e7eb;
            font-size: clamp(11px, 1vw, 15px);
        }

        .GreenBtn {
            background: #285b50;
            border-color: #367365;
            color: white;
        }

        .GreenBtn:hover {
            background: #347363;
        }

        .PinkBtn {
            background: #63313b;
            border-color: #85414d;
            color: white;
        }

        .PinkBtn:hover {
            background: #82404c;
        }

        /* プレイヤーボード */
        #board {
            display: grid;
            grid-template-columns: repeat(6, minmax(0, 1fr));
            grid-template-rows: repeat(2, minmax(0, 1fr));
            gap: 0.7vw;
            justify-items: stretch;
            align-items: stretch;
            width: 96%;
            height: 64vh;
            margin: 0 auto;
            padding-bottom: 1vh;
        }

        .player {
            min-width: 0;
            min-height: 0;
            background: #23262d;
            border: 1px solid #393d46;
            border-radius: 3px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, .3);
            padding: 0.7vw;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            font-weight: bold;
            font-size: clamp(14px, 2.1vw, 30px);
        }

        .inactive {
            background: #1b1d22;
            color: #656b76;
            border-color: #292c33;
        }

        .winner {
            background: #681f29;
            color: #fff;
            border: 1px solid #a33b48;
        }

        .winner-text {
            font-size: clamp(17px, 2.4vw, 34px);
            color: #fff;
        }

        .score {
            font-size: clamp(24px, 4vw, 58px);
            font-weight: bold;
            color: #f0f1f3;
        }

        .status {
            font-size: clamp(11px, 1.4vw, 20px);
            font-weight: bold;
            min-height: 1.2em;
            color: #d0a64e;
        }

        .lock {
            font-size: clamp(20px, 3vw, 40px);
            color: #d0a64e;
        }

        .btns {
            display: flex;
            gap: 0.45vw;
            justify-content: center;
            width: 95%;
        }

        .actionBtn {
            flex: 1;
            min-width: 0;
            aspect-ratio: 1.25;
            border: 1px solid rgba(255, 255, 255, .08);
            border-radius: 3px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: clamp(17px, 2.3vw, 33px);
            font-weight: bold;
            color: white;
        }

        .action-plus {
            background: #a9323c;
        }

        .action-plus:hover:not(:disabled) {
            background: #c5414b;
        }

        .action-miss {
            background: #285b91;
        }

        .action-miss:hover:not(:disabled) {
            background: #3474b3;
        }

        .action-reset {
            background: #484d56;
        }

        .action-reset:hover {
            background: #606671;
        }

        .reset {
            flex: none;
            width: 85%;
            height: 34px;
            font-size: clamp(11px, 1.1vw, 16px);
            background: #484d56;
            color: white;
            border-radius: 3px;
        }

        .reset:hover {
            background: #606671;
        }

        /* ラウンド設定パネル */
        #settings-button {
            position: fixed;
            right: 10px;
            top: 25vh;
            z-index: 10;
            background: #292d35;
            color: #e5e7eb;
            border: 1px solid #444a55;
            border-radius: 3px;
            padding: 7px 14px;
            font-size: 13px;
        }

        #popup {
            position: fixed;
            inset: 0;
            background: rgba(0, 0, 0, .55);
            display: none;
            justify-content: flex-end;
            z-index: 1000;
        }

        #popup-content {
            width: 500px;
            max-width: 90vw;
            height: 100vh;
            background: #222;
            padding: 25px;
            overflow-y: auto;
            text-align: left;
            box-shadow: -10px 0 30px rgba(0, 0, 0, .5);
            animation: slideIn .2s ease;
        }

        @keyframes slideIn {
            from {
                transform: translateX(100%);
            }

            to {
                transform: translateX(0);
            }
        }

        .popup-round {
            margin-bottom: 28px;
        }

        .round-content {
            display: none;
            margin-top: 12px;
        }

        textarea {
            width: 100%;
            height: 120px;
            background: #333;
            color: white;
            border: 1px solid #454b56;
            padding: 10px;
            resize: vertical;
            font-size: 14px;
        }

        .round-buttons {
            display: flex;
            gap: 8px;
            margin-top: 9px;
        }

        .round-buttons button,
        #close-popup {
            background: #383d47;
            color: white;
            border-radius: 3px;
            padding: 9px 13px;
            font-size: 14px;
        }

        .round-buttons button:hover,
        #close-popup:hover {
            background: #505765;
        }

        @media (max-width: 700px) {
            #problem-area {
                height: 22vh;
                padding-left: 3vw;
                padding-right: 3vw;
            }

            #controls {
                height: 14vh;
                gap: 4px;
            }

            #board {
                height: 62vh;
                gap: 4px;
                width: 98%;
            }

            .player {
                padding: 3px;
            }

            .btns {
                width: 100%;
                gap: 2px;
            }
        }

        .score-settings {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin: 15px 0 25px;
        }

        .score-settings label {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 15px;
            font-size: 14px;
        }

        .score-settings input {
            width: 70px;
            padding: 6px;
            background: #333;
            color: white;
            border: 1px solid #555;
            border-radius: 3px;
            font-size: 15px;
        }
    </style>
</head>

<body>
    <section id="problem-area">
        <div id="round-label">未選択</div>
        <div id="problem-text">問題未表示</div>
        <div id="problem-answer">設定からラウンドを選択してください</div>
    </section>

    <div id="controls">
        <div id="winCountDisplay">
            3回正解で抜け！ 誤答で1回休み
        </div>
        <span id="questionCount">Q1</span>
        <button id="nextBtn" class="controlBtn GreenBtn">スルー</button>
        <button id="backBtn" class="controlBtn GreenBtn">1つ戻る</button>
        <button id="AllResetBtn" class="controlBtn PinkBtn">全枠リセット</button>
        <button id="resetQuestionCount" class="controlBtn PinkBtn">問題リセット</button>
    </div>

    <div id="board"></div>

    <button id="settings-button" onclick="openPopup()">設定</button>
    <div id="popup">
        <div id="popup-content">
            <h3>得点設定</h3>
            <div class="score-settings">
                <label>
                    抜けるまでの正解数
                    <input type="number" id="winCount" value="3" min="1">
                </label>

                <label>
                    誤答時の休み数
                    <input type="number" id="lockCount" value="1" min="1">
                </label>
            </div>

            <hr>

            <h3>問題文設定</h3>
            <div id="round-container"></div>
            <button id="close-popup" onclick="closePopup()">閉じる</button>
        </div>
    </div>

    <script>
        /* ===== 得点管理 ===== */
        const board = document.getElementById("board");
        const N = 12;
        let winNeeded = parseInt(document.getElementById("winCount").value, 10);
        let lockTurns = parseInt(document.getElementById("lockCount").value, 10);

        let players = Array.from({ length: N }, (_, i) => ({
            id: i + 1, score: 0, lock: 0, active: true, winner: false
        }));
        let history = [];
        let questionCount = 1;

        function saveScoreState() {
            history.push({
                players: JSON.parse(JSON.stringify(players)),
                questionCount
            });
            if (history.length > 50) history.shift();
            persistScoreState();
        }

        function persistScoreState() {
            localStorage.setItem("players", JSON.stringify(players));
            localStorage.setItem("questionCount", String(questionCount));
        }

        function updateQuestionCount() {
            document.getElementById("questionCount").textContent = "Q" + questionCount;
        }

        // 得点側の問題数を唯一の進行基準とし、問題表示も同じ番号に合わせる
        function advanceQuestion() {
            questionCount++;
            showCurrentProblem();
            updateQuestionCount();
            persistScoreState();
        }

        function render() {
            board.innerHTML = "";
            winNeeded = Math.max(1, parseInt(document.getElementById("winCount").value, 10) || 1);
            lockTurns = Math.max(1, parseInt(document.getElementById("lockCount").value, 10) || 1);

            players.forEach(p => {
                const div = document.createElement("div");
                div.className = "player";
                if (!p.active && !p.winner) div.classList.add("inactive");
                if (p.winner) div.classList.add("winner");

                const header = document.createElement("div");
                header.textContent = `No.${p.id}`;

                const score = document.createElement("div");
                score.className = "score";
                score.innerHTML = p.winner
                    ? '<span class="winner-text">抜け！</span>'
                    : String(p.score);

                const status = document.createElement("div");
                status.className = p.lock > 0 && !p.winner ? "status lock" : "status";
                status.textContent = p.lock > 0 && !p.winner ? `Lock${p.lock}` : "";

                const btns = document.createElement("div");
                btns.className = "btns";

                if (!p.winner && p.lock <= 0) {
                    const plusBtn = document.createElement("button");
                    plusBtn.textContent = "○";
                    plusBtn.className = "actionBtn action-plus";
                    plusBtn.disabled = !p.active;
                    plusBtn.onclick = () => {
                        saveScoreState();
                        p.score++;
                        if (p.score >= winNeeded) {
                            p.winner = true;
                            p.active = false;
                        }
                        reduceOtherLocks(p.id);
                        advanceQuestion();
                        render();
                    };

                    const missBtn = document.createElement("button");
                    missBtn.textContent = "×";
                    missBtn.className = "actionBtn action-miss";
                    missBtn.disabled = !p.active;
                    missBtn.onclick = () => {
                        saveScoreState();
                        p.lock = lockTurns;
                        p.active = false;
                        reduceOtherLocks(p.id);
                        advanceQuestion();
                        render();
                    };

                    const scoreReset = document.createElement("button");
                    scoreReset.textContent = "0";
                    scoreReset.className = "actionBtn action-reset";
                    scoreReset.onclick = () => {
                        saveScoreState();
                        p.score = 0;
                        p.lock = 0;
                        p.active = true;
                        render();
                        persistScoreState();
                    };
                    btns.append(plusBtn, missBtn, scoreReset);
                } else if (p.winner) {
                    const resetBtn = document.createElement("button");
                    resetBtn.textContent = "リセット";
                    resetBtn.className = "reset";
                    resetBtn.onclick = () => {
                        saveScoreState();
                        Object.assign(p, { score: 0, lock: 0, active: true, winner: false });
                        render();
                        persistScoreState();
                    };
                    btns.append(resetBtn);
                }

                div.append(header, score, status, btns);
                board.appendChild(div);
            });
            updateQuestionCount();
        }

        function reduceOtherLocks(exceptId) {
            players.forEach(other => {
                if (other.id !== exceptId && other.lock > 0) {
                    other.lock--;
                    if (other.lock === 0 && !other.winner) other.active = true;
                }
            });
        }

        document.getElementById("nextBtn").onclick = () => {
            saveScoreState();
            players.forEach(p => {
                if (p.lock > 0) {
                    p.lock--;
                    if (p.lock === 0 && !p.winner) p.active = true;
                }
            });
            advanceQuestion();
            render();
        };

        document.getElementById("AllResetBtn").onclick = () => {
            if (confirm("全枠をリセットしますか？")) {
                saveScoreState();
                players = players.map(p => ({
                    id: p.id, score: 0, lock: 0, active: true, winner: false
                }));
                render();
                persistScoreState();
            }
        };

        document.getElementById("backBtn").onclick = () => {
            if (history.length > 0) {
                const last = history.pop();
                players = last.players;
                questionCount = last.questionCount;
                render();
                showCurrentProblem();
                persistScoreState();
            }
        };

        document.getElementById("resetQuestionCount").onclick = () => {
            if (confirm("問題数をリセットしますか？")) {
                questionCount = 1;
                history = [];
                updateQuestionCount();
                showCurrentProblem();
                persistScoreState();
            }
        };

        function updateWinCountDisplay() {
            const win = Math.max(1, parseInt(document.getElementById("winCount").value, 10) || 1);
            const lock = Math.max(1, parseInt(document.getElementById("lockCount").value, 10) || 1);

            winNeeded = win;
            lockTurns = lock;

            document.getElementById("winCountDisplay").textContent =
                `${win}回正解で抜け！ 誤答で${lock}回休み`;
        }

        document.getElementById("winCount").addEventListener("input", updateWinCountDisplay);
        document.getElementById("lockCount").addEventListener("input", updateWinCountDisplay);

        updateWinCountDisplay();

        /* ===== ラウンド・問題データ ===== */

        const setList = [];
        for (let i = 1; i <= 5; i++) {
            setList.push(`set${i}`);
        }

        let rounds = JSON.parse(localStorage.getItem("rounds")) || {};
        let currentRound = localStorage.getItem("currentRound") || "";
        let currentProblems = [];

        function saveProblemState() {
            localStorage.setItem("rounds", JSON.stringify(rounds));
            localStorage.setItem("currentRound", currentRound);
        }

        function buildRoundUI() {
            const container = document.getElementById("round-container");
            container.innerHTML = "";
            setList.forEach((roundName, index) => {
                const contentId = `round-content-${index}`;
                const inputId = `input-${index}`;
                const section = document.createElement("div");
                section.className = "popup-round";
                section.innerHTML = `
                <div class="round-buttons">
                    <button type="button" data-toggle="${contentId}">${roundName}</button>
                    <button type="button" data-switch="${index}">表示</button>
                </div>
                <div class="round-content" id="${contentId}">
                    <textarea id="${inputId}" placeholder="問題文1[TAB]答え1&#10;問題文2[TAB]答え2&#10;..."></textarea>
                    <div class="round-buttons">
                        <button type="button" data-load="${index}">読み込み</button>
                        <button type="button" data-reset="${index}">リセット</button>
                    </div>
                </div>`;
                container.appendChild(section);

                section.querySelector("[data-toggle]").onclick = () => {
                    const el = document.getElementById(contentId);
                    el.style.display = el.style.display === "block" ? "none" : "block";
                };
                section.querySelector("[data-switch]").onclick = () => switchRound(roundName);
                section.querySelector("[data-load]").onclick = () => loadRound(roundName, inputId);
                section.querySelector("[data-reset]").onclick = () => resetRound(roundName, inputId);
            });
        }

        function openPopup() {
            document.getElementById("popup").style.display = "flex";
        }
        function closePopup() {
            document.getElementById("popup").style.display = "none";
        }
        document.getElementById("popup").addEventListener("click", e => {
            if (e.target.id === "popup") closePopup();
        });

        function loadRound(roundName, textareaId) {
            const text = document.getElementById(textareaId).value.trim();
            if (!text) {
                alert("問題文を入力してください");
                return;
            }
            const lines = text.split(/\r?\n/);
            const problems = [{ problem: "", answer: "" }];
            lines.forEach(line => {
                const cols = line.split("\t");
                problems.push({ problem: cols[0] || "", answer: cols.slice(1).join("\t") || "" });
            });
            rounds[roundName] = problems;
            saveProblemState();
            alert(roundName + " を読み込みました");
        }

        function resetRound(roundName, textareaId) {
            if (!confirm(`${roundName} をリセットしますか？`)) return;
            delete rounds[roundName];
            document.getElementById(textareaId).value = "";
            if (currentRound === roundName) {
                currentRound = "";
                currentProblems = [];
                document.getElementById("round-label").textContent = "未選択";
                document.getElementById("problem-text").textContent = "問題未表示";
                document.getElementById("problem-answer").textContent = "設定からセットを選択してください";
            }
            saveProblemState();
        }

        function switchRound(roundName) {
            currentRound = roundName;
            currentProblems = rounds[roundName] || [];
            questionCount = 1;
            history = [];
            document.getElementById("round-label").textContent = roundName;
            showCurrentProblem();
            updateQuestionCount();
            persistScoreState();
            saveProblemState();
            closePopup();
        }

        // questionCount=1 は読み込んだ問題配列の1番目（Q1）を指す
        function showCurrentProblem() {
            const problemText = document.getElementById("problem-text");
            const answerText = document.getElementById("problem-answer");

            if (!currentRound) {
                problemText.textContent = "問題未表示";
                answerText.textContent = "設定からセットを選択してください";
                return;
            }

            document.getElementById("round-label").textContent = currentRound;
            const total = Math.max(0, currentProblems.length - 1);
            if (questionCount <= total && currentProblems[questionCount]) {
                const p = currentProblems[questionCount];
                problemText.textContent = p.problem;
                answerText.textContent = p.answer;
            } else {
                problemText.textContent = total === 0 ? "問題データがありません" : "全問題終了";
                answerText.textContent = "";
            }
        }

        /* ===== 初期化・復元 ===== */
        function initialize() {
            buildRoundUI();

            const savedPlayers = localStorage.getItem("players");
            const savedQ = localStorage.getItem("questionCount");
            if (savedPlayers) {
                try { players = JSON.parse(savedPlayers); } catch (_) { }
            }
            if (savedQ !== null && Number.isFinite(Number(savedQ))) {
                questionCount = Math.max(1, Number(savedQ));
            }

            if (currentRound && rounds[currentRound]) {
                currentProblems = rounds[currentRound];
                document.getElementById("round-label").textContent = currentRound;
            } else if (currentRound) {
                currentRound = "";
                saveProblemState();
            }

            render();
            showCurrentProblem();
        }

        document.addEventListener("keydown", e => {
            if (e.key === "Escape") closePopup();
        });

        initialize();
    </script>
</body>

</html>
