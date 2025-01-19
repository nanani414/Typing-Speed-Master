
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Typing Speed Master</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #1e293b, #64748b);
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            overflow: hidden;
        }

        h1 {
            font-size: 3rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.4);
        }

        .container {
            text-align: center;
            width: 90%;
            max-width: 600px;
            padding: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
        }

        .stats {
            display: flex;
            justify-content: space-between;
            margin: 20px 0;
        }

        .stats div {
            font-size: 1.2rem;
            background: rgba(255, 255, 255, 0.2);
            padding: 10px;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        .typing-box {
            font-size: 1.5rem;
            padding: 15px;
            margin: 20px auto;
            border: 2px solid #4caf50;
            border-radius: 10px;
            background: #2d3748;
            color: #fff;
            width: 80%;
            min-height: 50px;
        }

        .typed-highlight {
            color: #6ee7b7;
        }

        input[type="text"] {
            width: 100%;
            font-size: 1.5rem;
            padding: 10px;
            border: 2px solid #4caf50;
            border-radius: 10px;
            outline: none;
            background: #1e293b;
            color: #fff;
            margin-top: 10px;
        }

        input[type="text"]:focus {
            border-color: #6ee7b7;
            box-shadow: 0 0 8px #6ee7b7;
        }

        button {
            padding: 10px 20px;
            margin: 5px;
            font-size: 1rem;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            background: #4caf50;
            color: #fff;
            transition: all 0.3s;
        }

        button:hover {
            background: #81c784;
        }

        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 10;
            text-align: center;
        }

        .overlay h2 {
            font-size: 2.5rem;
            margin-bottom: 20px;
        }

        .overlay p {
            font-size: 1.2rem;
            margin: 5px 0;
        }

        .overlay .stat {
            font-size: 1.5rem;
            margin: 10px 0;
            font-weight: bold;
        }

        .overlay button {
            background: #f59e0b;
            font-size: 1.2rem;
            padding: 10px 20px;
        }
    </style>
</head>
<body>
    <h1>Typing Speed Master</h1>
    <div class="container">
        <div class="stats">
            <div>Time Left: <span id="timer">0</span>s</div>
            <div>Words Typed: <span id="words-typed">0</span></div>
            <div>Errors: <span id="errors">0</span></div>
        </div>
        <div class="typing-box" id="words-container">Press Start to Begin</div>
        <input type="text" id="user-input" placeholder="Type here..." disabled>
        <div class="button-group">
            <button id="start-button" onclick="startGame()">Start</button>
        </div>
    </div>
    <div class="overlay" id="results" style="display: none;">
        <h2>Game Over!</h2>
        <p class="stat">Words Typed: <span id="final-words"></span></p>
        <p class="stat">Typing Speed: <span id="typing-speed"></span> WPM</p>
        <p class="stat">Errors Made: <span id="final-errors"></span></p>
        <p class="stat">Skill Level: <span id="skill-level"></span></p>
        <div id="encouragement-message"></div>
        <button onclick="closeResults()">OK</button>
    </div>

    <script>
        let words = [
            "apel", "pisang", "cherry", "tanggal", "merah", "semangka", "anggur",
            "madu", "kiwi", "lemon", "mangga", "vitamin", "jeruk",
            "pepaya", "melon", "mie", "strawberry", "meja", "kluwek",
            "naga", "buah", "singa", "hewan", "sapi", "ayam", "sate", "meja", "rias","sehat", "stabil", "aktif",
            "bunga", "indah", "mekar", "maju", "hebat", "beruntung", "bisa", "pasti", "adil", "mawar", "baju", "pakaian",
            "baterai", "fisika", "kimia", "biologi", "magnet", "besi", "minyak", "bawang", "putih", "jingga", "hidup",
            "baik", "kipas", "nani", "tas", "kursi", "papan", "dia", "kamu", "gunung", "langit", "awan", "sesuatu",
            "samudra", "buaya", "akhir", "bahagia", "damai", "emosi", "mengerti", "bicara", "tatapan", "hanger",
            "pelembab", "kelas", "keluarga", "kisah", "awal", "abadi", "karya", "milik", "rasa", "puas", "positif",
            "belalang", "kunyit", "kencur", "juni", "agustus", "juli", "jaga", "orang", "damai", "tenang",
            "jalan", "selalu", "masih", "ada", "lain", "terimakasih", "katanya", "semua", "dirayakan", "tenang",
            "api", "luluh", "bisa", "berkembang", "pasti", "berhasil", "coba", "uang", "hangat", "kunci"
            
            ];

        let timer = 30;
        let wordsTyped = 0;
        let errors = 0;
        let gameInterval;
        let currentWords = [];
        let gameStarted = false;

        const timerDisplay = document.getElementById("timer");
        const wordsTypedDisplay = document.getElementById("words-typed");
        const errorsDisplay = document.getElementById("errors");
        const wordsContainer = document.getElementById("words-container");
        const userInput = document.getElementById("user-input");
        const resultsOverlay = document.getElementById("results");
        const finalWords = document.getElementById("final-words");
        const typingSpeed = document.getElementById("typing-speed");
        const finalErrors = document.getElementById("final-errors");
        const skillLevel = document.getElementById("skill-level");
        const encouragementMessage = document.getElementById("encouragement-message");

        function startGame() {
            resetGame();
            gameStarted = true;
            userInput.disabled = false;
            userInput.focus();
            generateWords();
            gameInterval = setInterval(updateTimer, 1000);
        }

        function resetGame() {
            timer = 60;
            wordsTyped = 0;
            errors = 0;
            gameStarted = false;
            currentWords = [];
            wordsContainer.innerHTML = "";
            resultsOverlay.style.display = "none";
            timerDisplay.textContent = timer;
            wordsTypedDisplay.textContent = wordsTyped;
            errorsDisplay.textContent = errors;
        }

        function updateTimer() {
            timer--;
            timerDisplay.textContent = timer;

            if (timer <= 0) {
                endGame();
            }
        }

        function generateWords() {
            for (let i = 0; i < 5; i++) {
                currentWords.push(words[Math.floor(Math.random() * words.length)]); 
            }
            updateWordsContainer();
        }

        function updateWordsContainer() {
            wordsContainer.innerHTML = currentWords
                .map((word, index) => {
                    if (index === 0) {
                        const typedPart = userInput.value.trim();
                        let remainingPart = word.slice(typedPart.length);
                        return `<span class="typed-highlight">${typedPart}</span><span>${remainingPart}</span>`;
                    } else {
                        return word;
                    }
                })
                .join(" ");
        }

        function checkInput() {
            if (!gameStarted) return;

            const inputText = userInput.value.trim();
            const firstWord = currentWords[0];

            if (firstWord.startsWith(inputText)) {
                updateWordsContainer();

                if (inputText === firstWord) {
                    wordsTyped++;
                    wordsTypedDisplay.textContent = wordsTyped;
                    currentWords.shift();
                    userInput.value = "";

                    if (currentWords.length === 0) {
                        generateWords();
                    } else {
                        updateWordsContainer();
                    }
                }
            } else {
                errors++;
                errorsDisplay.textContent = errors;
            }
        }

        function endGame() {
            clearInterval(gameInterval);
            userInput.disabled = true;
            calculateResults();
            resultsOverlay.style.display = "flex";
        }

        function calculateResults() {
            const wpm = Math.round((wordsTyped / 30) * 60);
            let level = "Beginner";
            let encouragement = "Tetap Berlatih! Kamu pasti bisa!!";

            if (wpm > 40) {
                level = "Intermediate";
                encouragement = "Kerja bagus!! Kamu mulai berkembang! Tetap berlatih dan capai tujuanmu!!";
            }
            if (wpm > 60) {
                level = "Advanced";
                encouragement = "Keren! Sekarang kamu sudah di level Advanced. Kamu berkembang dengan cepat!!";
            }
            if (wpm > 80) {
                level = "Expert";
                encouragement = "Luarbiasa!! Kamu sudah sampai di level master!! Pertahankan dan teruslah berlatih!!";
            }

            finalWords.textContent = wordsTyped;
            typingSpeed.textContent = wpm;
            finalErrors.textContent = errors;
            skillLevel.textContent = level;

            // Tambahkan kata-kata penyemangat
            encouragementMessage.textContent = encouragement;
            encouragementMessage.style.fontSize = '1.3rem';
            encouragementMessage.style.marginTop = '10px';
            encouragementMessage.style.fontWeight = 'bold';
            encouragementMessage.style.color = '#81c784';
        }

        function closeResults() {
            resultsOverlay.style.display = "none";
            startGame();
        }

        userInput.addEventListener("input", checkInput);
    </script>
</body>
</html>

