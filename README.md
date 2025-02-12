<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kraftwerk Memory-Quiz</title>
    <style>
        body {
            text-align: center;
            font-family: Arial, sans-serif;
        }
        #game-board {
            display: grid;
            grid-template-columns: repeat(4, 150px);
            grid-gap: 10px;
            justify-content: center;
            margin-top: 20px;
        }
        .card {
            width: 150px;
            height: 100px;
            background-color: #3498db;
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            border-radius: 10px;
            transition: background 0.3s;
        }
        .card.flipped {
            background-color: #2ecc71;
        }
        .info {
            margin-top: 20px;
            font-size: 18px;
        }
        .reset-button {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background-color: red;
            color: white;
            border: none;
            border-radius: 5px;
        }
    </style>
</head>
<body>

    <h2>⚡ Kraftwerk Memory-Quiz ⚡</h2>
    <p>Finde die passenden Paare aus **Kraftwerkskomponenten** und ihren **Funktionen**.</p>

    <div class="info">
        🔄 Aktueller Spieler: <span id="current-player">Spieler 1</span>  
        <br> 🎯 **Punkte** - Spieler 1: <span id="score1">0</span> | Spieler 2: <span id="score2">0</span>
    </div>

    <div id="game-board"></div>
    <p id="status"></p>

    <button class="reset-button" onclick="resetGame()">🔄 Neues Spiel</button>

    <script>
        const components = [
            { name: "Kessel", function: "Erhitzt Wasser zu Dampf" },
            { name: "Generator", function: "Wandelt mechanische Energie in Strom um" },
            { name: "Turbine", function: "Wird durch Dampf angetrieben und dreht sich" },
            { name: "Kühlturm", function: "Gibt Wärmeenergie an die Umgebung ab" },
            { name: "Kondensator", function: "Kühlt den Dampf zu Wasser" },
            { name: "Speisewasserpumpe", function: "Pumpt Wasser in den Kessel" },
            { name: "Erregung", function: "Erzeugt das Magnetfeld für den Generator" },
            { name: "Ventile", function: "Regulieren den Dampfstrom" }
        ];

        let cards = [];
        let flippedCards = [];
        let matchedPairs = 0;
        let currentPlayer = 1;
        let scores = { 1: 0, 2: 0 };

        function shuffle(array) {
            return array.sort(() => Math.random() - 0.5);
        }

        function createGameBoard() {
            const board = document.getElementById("game-board");
            board.innerHTML = "";
            let shuffledComponents = shuffle([...components, ...components.map(c => ({ name: c.function, function: c.name }))]);

            shuffledComponents.forEach((item, index) => {
                let card = document.createElement("div");
                card.classList.add("card");
                card.dataset.name = item.name;
                card.dataset.function = item.function;
                card.innerText = "?";
                card.addEventListener("click", () => flipCard(card));
                board.appendChild(card);
                cards.push(card);
            });

            updateUI();
        }

        function flipCard(card) {
            if (flippedCards.length < 2 && !card.classList.contains("flipped")) {
                card.classList.add("flipped");
                card.innerText = card.dataset.name;
                flippedCards.push(card);

                if (flippedCards.length === 2) {
                    setTimeout(checkMatch, 800);
                }
            }
        }

        function checkMatch() {
            let [card1, card2] = flippedCards;
            if (card1.dataset.function === card2.dataset.name || card2.dataset.function === card1.dataset.name) {
                matchedPairs++;
                scores[currentPlayer]++;  // Punkte für den aktuellen Spieler
                if (matchedPairs === components.length) {
                    document.getElementById("status").innerText = `🎉 Alle Paare gefunden! Gewinner: ${scores[1] > scores[2] ? "Spieler 1" : scores[1] === scores[2] ? "Unentschieden!" : "Spieler 2"}!`;
                }
            } else {
                card1.classList.remove("flipped");
                card2.classList.remove("flipped");
                card1.innerText = "?";
                card2.innerText = "?";
                switchPlayer();
            }
            flippedCards = [];
            updateUI();
        }

        function switchPlayer() {
            currentPlayer = currentPlayer === 1 ? 2 : 1;
        }

        function updateUI() {
            document.getElementById("current-player").innerText = `Spieler ${currentPlayer}`;
            document.getElementById("score1").innerText = scores[1];
            document.getElementById("score2").innerText = scores[2];
        }

        function resetGame() {
            cards = [];
            flippedCards = [];
            matchedPairs = 0;
            currentPlayer = 1;
            scores = { 1: 0, 2: 0 };
            document.getElementById("status").innerText = "";
            createGameBoard();
        }

        createGameBoard();
    </script>

</body>
</html>
