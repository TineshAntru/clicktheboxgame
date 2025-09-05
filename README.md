<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Click the Box Game</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f9;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    h1 {
      margin: 20px;
      color: #333;
    }
    #container {
      display: flex;
      justify-content: center;
      align-items: flex-start;
      gap: 20px;
      width: 90%;
      max-width: 900px;
    }
    #usernameScreen, #game, #leaderboard {
      padding: 20px;
      border: 2px solid #333;
      border-radius: 10px;
      background: white;
    }
    #usernameScreen, #game {
      flex: 2;
    }
    #leaderboard {
      flex: 1;
      max-width: 250px;
    }
    button {
      padding: 10px 20px;
      margin: 10px;
      font-size: 16px;
      cursor: pointer;
    }
    #gameArea {
      position: relative;
      width: 300px;
      height: 300px;
      margin: 20px auto;
      border: 2px solid #333;
      background: #e6e6fa;
      overflow: hidden;
    }
    #box {
      position: absolute;
      width: 40px;
      height: 40px;
      background: red;
      border-radius: 5px;
      cursor: pointer;
      display: none;
    }
    ol {
      text-align: left;
      padding-left: 20px;
    }
    footer {
      margin-top: 30px;
      font-weight: bold;
    }
  </style>
</head>
<body>

  <h1>Click the Box Game 🎯</h1>

  <div id="container">
    <!-- Left side (Username + Game) -->
    <div>
      <div id="usernameScreen">
        <p>Enter your username:</p>
        <input type="text" id="usernameInput">
        <button onclick="startGame()">Start</button>
      </div>

      <div id="game" style="display:none;">
        <h2 id="welcome"></h2>
        <p>Score: <span id="score">0</span></p>
        <p>Rounds left: <span id="rounds">3</span></p>
        <p>Time left: <span id="time">10</span> sec</p>
        <div id="gameArea">
          <div id="box" onclick="hitBox()"></div>
        </div>
        <button onclick="beginRound()" id="startRoundBtn">Start Round</button>
      </div>
    </div>

    <!-- Right side (Leaderboard) -->
    <div id="leaderboard">
      <h2>Leaderboard</h2>
      <ol id="board"></ol>
    </div>
  </div>

  <footer>By Tinesh Reddy Geddam</footer>

  <script>
    let username = "";
    let score = 0;
    let rounds = 3;
    let timer;
    let timeLeft = 10;

    function startGame() {
      username = document.getElementById("usernameInput").value.trim();
      if (username === "") {
        alert("Enter a username!");
        return;
      }
      document.getElementById("usernameScreen").style.display = "none";
      document.getElementById("game").style.display = "block";
      document.getElementById("welcome").innerText = "Welcome, " + username;
      loadLeaderboard();
    }

    function beginRound() {
      if (rounds > 0) {
        timeLeft = 10;
        document.getElementById("time").innerText = timeLeft;
        document.getElementById("box").style.display = "block";
        document.getElementById("startRoundBtn").disabled = true;

        timer = setInterval(() => {
          timeLeft--;
          document.getElementById("time").innerText = timeLeft;
          moveBox();
          if (timeLeft <= 0) {
            endRound();
          }
        }, 1000);
      } else {
        alert("No rounds left!");
      }
    }

    function moveBox() {
      let box = document.getElementById("box");
      let area = document.getElementById("gameArea");
      let maxX = area.clientWidth - box.clientWidth;
      let maxY = area.clientHeight - box.clientHeight;
      let randomX = Math.floor(Math.random() * maxX);
      let randomY = Math.floor(Math.random() * maxY);
      box.style.left = randomX + "px";
      box.style.top = randomY + "px";
    }

    function hitBox() {
      score++;
      document.getElementById("score").innerText = score;
      moveBox();
    }

    function endRound() {
      clearInterval(timer);
      rounds--;
      document.getElementById("rounds").innerText = rounds;
      document.getElementById("box").style.display = "none";
      document.getElementById("startRoundBtn").disabled = false;

      if (rounds === 0) {
        saveScore();
        alert("Game Over! Your score: " + score);
      }
    }

    function saveScore() {
      let leaderboard = JSON.parse(localStorage.getItem("leaderboard")) || [];
      leaderboard.push({ name: username, score: score });
      leaderboard.sort((a, b) => b.score - a.score);
      leaderboard = leaderboard.slice(0, 10);
      localStorage.setItem("leaderboard", JSON.stringify(leaderboard));
      loadLeaderboard();
    }

    function loadLeaderboard() {
      let leaderboard = JSON.parse(localStorage.getItem("leaderboard")) || [];
      let board = document.getElementById("board");
      board.innerHTML = "";
      leaderboard.forEach(entry => {
        let li = document.createElement("li");
        li.innerText = entry.name + " - " + entry.score;
        board.appendChild(li);
      });
    }
  </script>

</body>
</html>
