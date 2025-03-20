# Simple Chess Game 4U
Great Little Chess Game For You To Play Anytime Anywhere
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Regal Online Chess</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chessboardjs/1.0.0/js/chessboard-1.0.0.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.3/chess.min.js"></script>
    <style>
        body {
            background-color: #ADD8E6;
            text-align: center;
            font-family: 'Georgia', serif;
        }
        h1 {
            color: #2C3E50;
        }
        #board {
            width: 400px;
            margin: auto;
            border: 10px solid #8B4513;
            box-shadow: 0px 0px 15px rgba(0,0,0,0.5);
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            margin-top: 10px;
            cursor: pointer;
            border: none;
            background-color: #2C3E50;
            color: white;
            border-radius: 5px;
        }
        button:hover {
            background-color: #1A252F;
        }
        #music-player {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>Regal Online Chess</h1>
    <div id="board"></div>
    <br>
    <label for="difficulty">Select Level:</label>
    <select id="difficulty" onchange="setDifficulty()">
        <option value="2">Beginner</option>
        <option value="5">Intermediate</option>
        <option value="10">Master</option>
    </select>
    <button onclick="restartGame()">Restart Game</button>
    <p id="status">Your Turn!</p>

    <div id="music-player">
        <p>Relaxing Chess Music</p>
        <audio controls loop>
            <source src="your-music-file.mp3" type="audio/mp3">
            Your browser does not support the audio element.
        </audio>
    </div>

    <script>
        let board = Chessboard('board', {
            draggable: true,
            dropOffBoard: 'snapback',
            onDrop: onDrop
        });
        let game = new Chess();
        let difficulty = 2;

        function setDifficulty() {
            difficulty = document.getElementById("difficulty").value;
            localStorage.setItem("difficulty", difficulty);
        }

        function onDrop(source, target) {
            let move = game.move({ from: source, to: target, promotion: 'q' });
            if (!move) return 'snapback';

            localStorage.setItem("chessBoard", game.fen());
            document.getElementById("status").innerText = "Computer's Turn...";
            setTimeout(makeComputerMove, 1000);
        }

        function makeComputerMove() {
            fetch('https://api.chess.com/pub/player/stockfish/move', {
                method: 'POST',
                body: JSON.stringify({ fen: game.fen(), level: difficulty }),
                headers: { 'Content-Type': 'application/json' }
            })
            .then(res => res.json())
            .then(data => {
                game.move(data.move);
                board.position(game.fen());
                localStorage.setItem("chessBoard", game.fen());
                document.getElementById("status").innerText = "Your Turn!";
            });
        }

        function restartGame() {
            game.reset();
            board.position(game.fen());
            localStorage.removeItem("chessBoard");
            document.getElementById("status").innerText = "Your Turn!";
        }

        window.onload = function() {
            let savedGame = localStorage.getItem("chessBoard");
            let savedDifficulty = localStorage.getItem("difficulty");
            if (savedGame) {
                game.load(savedGame);
                board.position(savedGame);
            }
            if (savedDifficulty) {
                document.getElementById("difficulty").value = savedDifficulty;
                difficulty = savedDifficulty;
            }
        }
    </script>
</body>
</html>
