<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Honor and Good vs Bad Path Meter</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f4f4f4;
            text-align: center;
        }
        .header-text {
            font-size: 20px;
            margin-bottom: 10px;
            color: #333;
            padding: 0 20px;
        }
        .meter-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 500px;
            margin-bottom: 20px;
        }
        .meter {
            position: relative;
            width: 100%;
            height: 30px;
            background: black;
            border-radius: 15px;
            border: 8px solid black;
            overflow: hidden;
            margin-bottom: 20px;
        }
        .meter-background {
            position: absolute;
            width: 100%;
            height: 100%;
            background: linear-gradient(to right, darkred 10%, black 50%, white 90%);
            border-radius: 15px;
        }
        .meter-pin {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 20px;
            height: 20px;
            background: white;
            border-radius: 50%;
            border: 2px solid black;
            transition: left 0.3s ease;
        }
        .progress-bar {
            width: 100%;
            background-color: #e0e0e0;
            border-radius: 25px;
            overflow: hidden;
            margin-bottom: 15px;
        }
        .progress {
            height: 30px;
            line-height: 30px;
            text-align: center;
            color: #fff;
            border-radius: 25px;
        }
        .progress-good {
            background-color: #4caf50;
            width: 0%;
        }
        .progress-evil {
            background-color: #f44336;
            width: 0%;
        }
        .input-container {
            display: flex;
            align-items: center;
            width: 100%;
            margin-bottom: 20px;
        }
        input[type="number"] {
            width: 35%;
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 5px;
            margin-right: 10px; /* Space between input and buttons */
        }
        .button-container {
            display: flex;
            align-items: center;
            width: 100%;
            justify-content: center; /* Center buttons */
            margin-bottom: 20px;
        }
        .main-buttons {
            display: flex;
            align-items: center;
            justify-content: space-between;
            width: 100%;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            border: none;
            border-radius: 5px;
            color: white;
            background-color: black;
        }
        #subtract-points-btn {
            background-color: red;
            margin-right: 10px;
        }
        #add-points-btn {
            background-color: green;
            margin-left: 10px;
        }
        #options-btn, #settings-btn, #life-plan-btn, #edit-acts-btn {
            background-color: black;
            margin-left: 5px;
            border: 1px solid #333; /* Slightly darker border for better visibility */
            padding: 10px; /* Ensure padding is consistent */
        }
        #edit-acts-btn {
            background-color: #333; /* Darker color for edit button */
        }
        #good-acts-btn, #bad-acts-btn {
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            border: none;
            border-radius: 5px;
            color: white;
        }
        #good-acts-btn {
            background-color: green;
        }
        #bad-acts-btn {
            background-color: darkred;
        }
        #clock {
            font-size: 24px;
            margin-top: 10px;
            color: #333;
        }
    </style>
</head>
<body>
    <div class="header-text">
        Honor is not people pleasing! It's about honesty, fairness, or integrity in one's beliefs and actions: a man of honor.
    </div>
    <div id="clock"></div>

    <!-- Honor Meter -->
    <div class="meter-container">
        <div class="meter">
            <div class="meter-background"></div>
            <div id="meterPin" class="meter-pin"></div>
        </div>
        <div class="input-container">
            <input type="number" id="pointsInput" placeholder="Enter points (+/-)">
            <button id="options-btn" onclick="showOptions()">Options</button>
            <button id="settings-btn" onclick="showSettings()">Settings</button>
            <button id="life-plan-btn" onclick="showLifePlan()">Lifeplan</button>
        </div>
        <div class="button-container">
            <button id="subtract-points-btn" onclick="changeHonor(-parseInt(document.getElementById('pointsInput').value || 0))">Subtract Points to Low Honor</button>
            <button id="add-points-btn" onclick="changeHonor(parseInt(document.getElementById('pointsInput').value || 0))">Add Points to High Honor</button>
        </div>
    </div>

    <!-- Good vs Bad Path Progress Bar -->
    <div class="progress-container">
        <div class="progress-bar" style="width: 600px;">
            <div class="progress progress-good" id="progress-good">Good Path: 0/2500</div>
        </div>
        <div class="progress-bar" style="width: 600px;">
            <div class="progress progress-evil" id="progress-evil">Bad Path: 0/2500</div>
        </div>
    </div>

    <!-- Buttons for Good Acts, Bad Acts, and Edit -->
    <div class="button-container">
        <button id="good-acts-btn" onclick="showGoodActs()">Show Good Acts</button>
        <button id="bad-acts-btn" onclick="showBadActs()">Show Bad Acts</button>
        <button id="edit-acts-btn" onclick="editActs()">Edit Acts</button> <!-- Styled Edit Button -->
    </div>

    <audio id="high-honor-sound" src="/mnt/data/High-honor-sound.mp3"></audio>
    <audio id="low-honor-sound" src="/mnt/data/Low honour.mp3"></audio>

    <script>
        let honorValue = 0;  
        let goodPoints = 0;
        let evilPoints = 0;

        let goodActs = [
            'Honesty (+1)', 'Kindness (+1)', 'Generosity (+1)', 'Compassion (+1)', 'Courage (+1)', 'Forgiveness (+1)', 'Respect (+1)', 'Loyalty (+1)', 'Gratitude (+1)', 'Responsibility (+1)'
            // Additional good acts
        ];

        let badActs = [
            'Lying (-20)', 'Stealing (-25)', 'Pride (-25)', 'Envy (-25)', 'Greed (-25)', 'Anger (Wrath) (-50)', 'Gossiping (-50)', 'Dishonoring Parents (-100)', 'Sexual Immorality (-75)', 'Substance Abuse (-100)'
            // Additional bad acts
        ];

        function changeHonor(points) {
            honorValue += points;
            updateMeter();

            if (points > 0) {
                goodPoints += points;
                updateProgress('good', goodPoints);
            } else {
                evilPoints += Math.abs(points);
                updateProgress('evil', evilPoints);
            }
        }

        function updateMeter() {
            const meterPin = document.getElementById('meterPin');
            const meterWidth = document.querySelector('.meter').clientWidth;
            const newLeft = ((honorValue + 250) / 500) * meterWidth;
            meterPin.style.left = Math.min(Math.max(newLeft, 0), meterWidth - 20) + 'px';
        }

        function updateProgress(type, value) {
            const progressElement = document.getElementById(`progress-${type}`);
            const maxPoints = 2500;
            const progressWidth = (value / maxPoints) * 100;
            progressElement.style.width = Math.min(progressWidth, 100) + '%';
            progressElement.textContent = `${type === 'good' ? 'Good Path' : 'Bad Path'}: ${value}/${maxPoints}`;
        }

        function showGoodActs() {
            alert('List of Good Acts:\n- ' + goodActs.join('\n- '));
        }

        function showBadActs() {
            alert('List of Bad Acts:\n- ' + badActs.join('\n- '));
        }

        function editActs() {
            const newGoodActs = prompt("Edit Good Acts (comma-separated):", goodActs.join(", "));
            const newBadActs = prompt("Edit Bad Acts (comma-separated):", badActs.join(", "));
            
            if (newGoodActs !== null) {
                goodActs = newGoodActs.split(",").map(act => act.trim());
            }

            if (newBadActs !== null) {
                badActs = newBadActs.split(",").map(act => act.trim());
            }
        }

        function updateClock() {
            const now = new Date();
            const formattedTime = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit' });
            const formattedDate = now.toLocaleDateString();
            document.getElementById('clock').textContent = `Date: ${formattedDate} | Time: ${formattedTime}`;
        }

        setInterval(updateClock, 1000);
        updateClock();
        updateMeter();

        function showOptions() {
            alert('Options button clicked! Implement your options here.');
        }

        function showSettings() {
            alert('Settings button clicked! Implement your settings here.');
        }

        function showLifePlan() {
            alert('Life Plan button clicked! Implement your life plan here.');
        }
    </script>
</body>
</html>
