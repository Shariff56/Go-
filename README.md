<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Step Tracker</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: #1e1e2e;
            color: white;
            text-align: center;
            margin: 50px;
        }
        .container {
            background: #29293d;
            padding: 20px;
            border-radius: 10px;
            display: inline-block;
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.2);
        }
        h1 {
            color: #ffcc00;
        }
        input {
            padding: 10px;
            font-size: 18px;
            width: 120px;
            text-align: center;
            border-radius: 5px;
            border: none;
        }
        button {
            padding: 10px 20px;
            font-size: 18px;
            cursor: pointer;
            margin-top: 10px;
            border-radius: 5px;
            border: none;
            background: #ffcc00;
            color: black;
            font-weight: bold;
            transition: 0.3s;
        }
        button:hover {
            background: #e6b800;
        }
        #stepsLeft {
            font-size: 32px;
            font-weight: bold;
            margin: 20px;
            color: #00ff99;
            transition: 0.3s;
        }
    </style>
</head>
<body>

    <h1>Step Tracker</h1>
    <div class="container">
        <label for="stepInput">Enter Total Steps:</label>
        <input type="number" id="stepInput" min="1000" step="1" placeholder="1000">
        <br><br>
        <button onclick="startCounting()">Start Tracking</button>
        <h2 id="stepsLeft">Steps Left: 0</h2>
    </div>

    <script>
        let totalSteps = 0;
        let lastWalkTime = Date.now();
        let lastY = null;
        let stepThreshold = 1.2; // Sensitivity for step detection

        function startCounting() {
            totalSteps = parseInt(document.getElementById("stepInput").value);
            if (isNaN(totalSteps) || totalSteps < 1000) {
                alert("Enter a valid step count (minimum 1000)");
                return;
            }
            updateSteps();
            startStepTracking();
            setInterval(checkInactivity, 5000); // Check inactivity every 5 sec
        }

        function updateSteps() {
            let stepsDisplay = document.getElementById("stepsLeft");
            stepsDisplay.innerText = `Steps Left: ${totalSteps}`;
            stepsDisplay.style.transform = "scale(1.2)";
            setTimeout(() => stepsDisplay.style.transform = "scale(1)", 200);
        }

        function checkInactivity() {
            if (Date.now() - lastWalkTime > 10000 && totalSteps > 0) { // 10 sec inactivity
                speak("Keep going! Don't stop now!");
            }
        }

        function speak(message) {
            let speech = new SpeechSynthesisUtterance(message);
            speech.lang = "en-US";
            window.speechSynthesis.speak(speech);
        }

        function startStepTracking() {
            if (window.DeviceMotionEvent) {
                window.addEventListener("devicemotion", function (event) {
                    let yAcceleration = event.accelerationIncludingGravity.y;

                    if (lastY !== null) {
                        let deltaY = Math.abs(yAcceleration - lastY);

                        if (deltaY > stepThreshold) {
                            stepDetected();
                        }
                    }
                    lastY = yAcceleration;
                }, true);
            } else {
                alert("Your device does not support motion tracking.");
            }
        }

        function stepDetected() {
            if (totalSteps > 0) {
                totalSteps -= 1; // Decrease one step at a time
                lastWalkTime = Date.now();
                updateSteps();

                if (totalSteps % 1000 === 0 && totalSteps !== 0) {
                    speak(`Great job! Only ${totalSteps} steps left!`);
                }

                if (totalSteps === 0) {
                    speak("Congratulations! You've reached your goal!");
                }
            }
        }
    </script>

</body>
</html>
