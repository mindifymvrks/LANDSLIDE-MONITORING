<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring</title>
    <style>
        /* General Styles */
        body { background-color: #0d0d0d; color: white; font-family: 'Poppins', sans-serif; text-align: center; }
        
        /* Header Styles */
        .header { display: flex; justify-content: space-between; align-items: center; padding: 15px 30px; background: rgba(0, 0, 0, 0.8); border-bottom: 1px solid #555; position: fixed; width: 100%; top: 0; left: 0; z-index: 1000; }
        .logo { font-size: 24px; font-weight: bold; color: #ffcc00; }
        
        /* Navigation Bar */
        .nav a { color: white; text-decoration: none; margin: 0 15px; font-size: 18px; transition: color 0.3s ease-in-out; cursor: pointer; }
        .nav a:hover { color: #ffcc00; }
        
        /* Sections */
        .section { display: none; padding: 60px 20px; max-width: 800px; margin: auto; text-align: left; }
        .visible { display: block; }
        
        /* Table Styles */
        .data-table { width: 100%; margin-top: 20px; border-collapse: collapse; }
        .data-table th, .data-table td { border: 1px solid white; padding: 10px; text-align: center; }
        .data-table th { background-color: #ffcc00; color: black; }
        .warning-row { background-color: rgba(255, 204, 0, 0.1); border-left: 4px solid #ffcc00; animation: pulseWarning 2s infinite; }
        .warning-text { color: #ffcc00; font-weight: bold; }
        
        @keyframes pulseWarning { 0% { opacity: 1; } 50% { opacity: 0.7; } 100% { opacity: 1; } }
    </style>
</head>
<body>
    <header class="header">
        <div class="logo">MAVERICKS</div>
        <nav class="nav">
            <a onclick="showSection('home')">Home</a>
            <a onclick="showSection('datas')">Datas</a>
        </nav>
    </header>

    <div class="main-title">Integrated Landslide Monitoring and Early Warning System</div>

    <div id="datas" class="section visible">
        <div class="content-box">
            <h2>Live Data</h2>
            <table class="data-table">
                <thead>
                    <tr>
                        <th>Time</th>
                        <th>Rainfall (mm)</th>
                        <th>4-min Total</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody id="sensorData">
                    <tr><td colspan="4">Waiting for data...</td></tr>
                </tbody>
            </table>
        </div>
    </div>

    <script>
        const ESP32_IP = "192.168.1.100"; // Replace with your ESP32's IP
        const WARNING_THRESHOLD = 12; // 12mm in 4 minutes triggers warning
        let rainfallHistory = [];

        // WebSocket Connection
        let socket = new WebSocket(`ws://${ESP32_IP}:81/`);
        
        socket.onopen = () => console.log("✅ WebSocket Connected");
        socket.onmessage = (event) => {
            console.log("📩 Data Received: ", event.data);
            let data = JSON.parse(event.data);
            updateSensorData(data.sensor);
        };
        socket.onerror = (error) => console.log("❌ WebSocket Error: ", error);
        socket.onclose = () => console.log("🔌 WebSocket Disconnected");

        function updateSensorData(currentRain) {
            const now = new Date();
            rainfallHistory.push({ time: now.getTime(), value: currentRain });
            const cutoffTime = now.getTime() - (4 * 60 * 1000);
            rainfallHistory = rainfallHistory.filter(r => r.time > cutoffTime);
            const totalRain = rainfallHistory.reduce((sum, r) => sum + r.value, 0);
            
            const tableBody = document.getElementById('sensorData');
            const newRow = document.createElement('tr');
            if (totalRain > WARNING_THRESHOLD) newRow.classList.add('warning-row');
            newRow.innerHTML = `
                <td>${now.toLocaleTimeString()}</td>
                <td>${currentRain.toFixed(1)}</td>
                <td>${totalRain.toFixed(1)}</td>
                <td>${totalRain > WARNING_THRESHOLD ? '<span class="warning-text">WARNING</span>' : 'Normal'}</td>
            `;
            tableBody.insertBefore(newRow, tableBody.firstChild);
            if (tableBody.children.length > 15) tableBody.removeChild(tableBody.lastChild);
        }

        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => section.classList.remove('visible'));
            setTimeout(() => document.getElementById(sectionId).classList.add('visible'), 100);
        }
    </script>
</body>
</html>
