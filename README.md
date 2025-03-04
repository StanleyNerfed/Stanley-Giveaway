<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Stanley Giveaway</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin: 20px; background-color: #f8f9fa; }
        .container { max-width: 400px; margin: auto; padding: 20px; border: 1px solid #007bff; border-radius: 10px; background-color: white; box-shadow: 0 0 10px rgba(0, 123, 255, 0.5); }
        button { margin-top: 10px; padding: 10px 15px; border: none; border-radius: 5px; background-color: #007bff; color: white; cursor: pointer; }
        button:hover { background-color: #0056b3; }
        .hidden { display: none; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #007bff; padding: 8px; text-align: center; }
        th { background-color: #007bff; color: white; }
    </style>
</head>
<body>
    <div class="container" id="registration">
        <h2 style="color: #007bff;">Register for Stanley Giveaway</h2>
        <input type="text" id="playerName" placeholder="Enter Name" required><br>
        <input type="text" id="playerGcash" placeholder="Enter GCash Number" required><br>
        <button onclick="registerPlayer()">Register</button>
        <button onclick="showAdminLogin()">Admin Login</button>
    </div>

    <div class="container hidden" id="adminLogin">
        <h2 style="color: #007bff;">Admin Login</h2>
        <input type="password" id="adminPass" placeholder="Enter Admin Password"><br>
        <button onclick="adminLogin()">Login</button>
        <button onclick="hideAdminLogin()">Back</button>
    </div>

    <div class="container hidden" id="adminPanel">
        <h2 style="color: #007bff;">Registered Players</h2>
        <table>
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Code</th>
                    <th>GCash Number</th>
                    <th>Date</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody id="playerTable"></tbody>
        </table>
        <button onclick="drawWinner()">Draw Winner</button>
        <button onclick="resetPlayers()">Reset All</button>
        <button onclick="logoutAdmin()">Logout</button>
    </div>

    <script>
        let players = JSON.parse(localStorage.getItem('players')) || [];
        const adminPassword = "stanleyadmin1218";

        function registerPlayer() {
            const name = document.getElementById('playerName').value;
            const gcash = document.getElementById('playerGcash').value;
            const code = Math.floor(1000 + Math.random() * 9000);
            const date = new Date().toLocaleDateString();

            if (players.some(player => player.name === name)) {
                alert('Name already registered. Wait for the next draw or reset.');
                return;
            }

            if (name && gcash) {
                const player = { name, code, gcash, date };
                players.push(player);
                localStorage.setItem('players', JSON.stringify(players));
                alert('Registered Successfully!');
                generateTicket(player);
                document.getElementById('playerName').value = '';
                document.getElementById('playerGcash').value = '';
            } else {
                alert('Please enter valid details');
            }
        }

        function generateTicket(player) {
            const canvas = document.createElement("canvas");
            canvas.width = 300;
            canvas.height = 200;
            const ctx = canvas.getContext("2d");
            ctx.fillStyle = "white";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "black";
            ctx.font = "16px Arial";
            ctx.fillText(`Name: ${player.name}`, 20, 50);
            ctx.fillText(`Code: ${player.code}`, 20, 80);
            ctx.fillText(`GCash: ${player.gcash}`, 20, 110);
            ctx.fillText(`Date: ${player.date}`, 20, 140);
            const link = document.createElement("a");
            link.href = canvas.toDataURL("image/png");
            link.download = `${player.name}_ticket.png`;
            link.click();
        }

        function showAdminLogin() {
            document.getElementById('registration').classList.add('hidden');
            document.getElementById('adminLogin').classList.remove('hidden');
        }

        function hideAdminLogin() {
            document.getElementById('adminLogin').classList.add('hidden');
            document.getElementById('registration').classList.remove('hidden');
        }

        function adminLogin() {
            const password = document.getElementById('adminPass').value;
            if (password === adminPassword) {
                document.getElementById('adminLogin').classList.add('hidden');
                document.getElementById('adminPanel').classList.remove('hidden');
                loadPlayers();
            } else {
                alert('Incorrect password!');
            }
        }

        function loadPlayers() {
            const table = document.getElementById('playerTable');
            table.innerHTML = '';
            players.forEach((player, index) => {
                let row = `<tr>
                    <td>${player.name}</td>
                    <td>${player.code}</td>
                    <td>${player.gcash}</td>
                    <td>${player.date}</td>
                    <td><button onclick="removePlayer(${index})">Remove</button></td>
                </tr>`;
                table.innerHTML += row;
            });
        }

        function removePlayer(index) {
            players.splice(index, 1);
            localStorage.setItem('players', JSON.stringify(players));
            loadPlayers();
        }

        function resetPlayers() {
            if (confirm("Are you sure you want to reset all registered players?")) {
                players = [];
                localStorage.setItem('players', JSON.stringify(players));
                loadPlayers();
            }
        }

        function drawWinner() {
            if (players.length === 0) {
                alert("No players registered for the draw.");
                return;
            }
            const winner = players[Math.floor(Math.random() * players.length)];
            alert(`Winner: ${winner.name} (Code: ${winner.code})`);
        }

        function logoutAdmin() {
            document.getElementById('adminPanel').classList.add('hidden');
            document.getElementById('adminLogin').classList.remove('hidden');
        }
    </script>
</body>
</html>
