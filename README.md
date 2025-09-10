<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hybrid Solar + Wind Dashboard</title>
  <style>
    body {
      font-family: "Segoe UI", Arial, sans-serif;
      background: #121212;
      margin: 0;
      padding: 0;
      color: #e0e0e0;
    }
    header {
      background: #1f1f1f;
      color: #4cafef;
      padding: 15px;
      text-align: center;
      border-bottom: 2px solid #333;
    }
    header h1 {
      margin: 0;
      font-size: 22px;
    }
    header p {
      margin: 5px 0 0;
      font-size: 14px;
      opacity: 0.7;
    }

    .container {
      max-width: 500px;
      margin: 20px auto;
      padding: 10px;
    }

    .card {
      background: #1e1e1e;
      border-radius: 12px;
      padding: 15px 20px;
      margin-bottom: 15px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.6);
      border: 1px solid #333;
    }

    .card h2 {
      margin: 0 0 12px;
      font-size: 16px;
      color: #4cafef;
    }

    .telemetry-row {
      display: flex;
      justify-content: space-between;
      margin: 6px 0;
      font-size: 15px;
    }
    .telemetry-label {
      font-weight: 500;
      color: #bdbdbd;
    }

    .status {
      display: inline-block;
      padding: 4px 12px;
      border-radius: 8px;
      font-size: 13px;
      font-weight: bold;
      color: white;
    }
    .on { background: #4caf50; }
    .off { background: #f44336; }
    .closed { background: #2196f3; }
    .open { background: #9e9e9e; }

    .buttons {
      display: flex;
      justify-content: space-around;
      margin-top: 15px;
    }
    .buttons button {
      flex: 1;
      margin: 0 5px;
      padding: 12px;
      border: none;
      border-radius: 8px;
      font-size: 15px;
      font-weight: bold;
      color: white;
      cursor: pointer;
      transition: 0.3s;
    }
    .on-btn {
      background: #4caf50;
    }
    .on-btn:hover {
      background: #388e3c;
    }
    .off-btn {
      background: #f44336;
    }
    .off-btn:hover {
      background: #c62828;
    }

    footer {
      text-align: center;
      padding: 10px;
      font-size: 13px;
      background: #1f1f1f;
      color: #888;
      border-top: 1px solid #333;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <header>
    <h1> Hybrid Solar + Wind Control</h1>
    <p>Real-time Monitoring & Remote ON/OFF</p>
  </header>

  <div class="container">

    <!-- Telemetry Card -->
    <div class="card">
      <h2>Real-time Telemetry</h2>
      <div class="telemetry-row"><span class="telemetry-label">Voltage:</span> <span id="voltage">-- V</span></div>
      <div class="telemetry-row"><span class="telemetry-label">Current:</span> <span id="current">-- A</span></div>
      <div class="telemetry-row"><span class="telemetry-label">Temperature:</span> <span id="temp">-- °C</span></div>
      <div class="telemetry-row"><span class="telemetry-label">Wind RPM:</span> <span id="rpm">--</span></div>
    </div>

    <!-- Status Card -->
    <div class="card">
      <h2>System Status</h2>
      <div class="telemetry-row"><span class="telemetry-label">Inverter:</span> <span id="inverter" class="status off">OFF</span></div>
      <div class="telemetry-row"><span class="telemetry-label">Main Contactor:</span> <span id="main" class="status open">Open</span></div>
      <div class="telemetry-row"><span class="telemetry-label">Connection:</span> <span class="status on">Online</span></div>
    </div>

    <!-- Control Card -->
    <div class="card">
      <h2>Generator Control</h2>
      <div class="buttons">
        <button class="on-btn" onclick="sendCmd('REMOTE_ON')">🔵 Remote ON</button>
        <button class="off-btn" onclick="sendCmd('REMOTE_OFF')">🔴 Remote OFF</button>
      </div>
    </div>

  </div>

  <footer>
    Hybrid Solar + Wind Project © 2025 | Remote Monitoring & Control
  </footer>

  <script>
    const BASE_URL = window.location.origin;

    function updateTelemetry() {
      fetch(BASE_URL + "/telemetry")
        .then(res => res.json())
        .then(data => {
          document.getElementById("voltage").textContent = data.voltage.toFixed(2) + " V";
          document.getElementById("current").textContent = data.current.toFixed(2) + " A";
          document.getElementById("temp").textContent = data.temp.toFixed(1) + " °C";
          document.getElementById("rpm").textContent = data.rpm;

          const inverterEl = document.getElementById("inverter");
          inverterEl.textContent = data.inverter ? "ON" : "OFF";
          inverterEl.className = "status " + (data.inverter ? "on" : "off");

          const mainEl = document.getElementById("main");
          mainEl.textContent = data.main ? "Closed" : "Open";
          mainEl.className = "status " + (data.main ? "closed" : "open");
        })
        .catch(err => console.error("Telemetry fetch failed:", err));
    }

    function sendCmd(cmd) {
      fetch(BASE_URL + "/cmd?c=" + cmd)
        .then(res => res.text())
        .then(msg => alert(msg));
    }

    setInterval(updateTelemetry, 2000);
    updateTelemetry();
  </script>
</body>
</html>
