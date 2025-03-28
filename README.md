<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring</title>
    <style>
        /* General Styles */
        body {
            background-color: #0d0d0d;
            color: white;
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            text-align: center;
        }

        /* Header Styles */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 30px;
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
            border-bottom: 1px solid #555;
            position: fixed;
            width: 100%;
            top: 0;
            left: 0;
            z-index: 1000;
        }

        .logo {
            font-size: 24px;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: #ffcc00;
        }

        /* Navigation Bar */
        .nav a {
            color: white;
            text-decoration: none;
            margin: 0 15px;
            font-size: 18px;
            transition: color 0.3s ease-in-out;
            cursor: pointer;
        }

        .nav a:hover {
            color: #ffcc00;
        }

        /* Main Title */
        .main-title {
            font-size: 32px;
            font-weight: bold;
            margin-top: 100px;
        }

        /* Section Styling */
        .section {
            display: none;
            padding: 60px 20px;
            max-width: 800px;
            margin: 0 auto;
            text-align: left;
            opacity: 0;
            transition: opacity 0.5s ease-out, transform 0.5s ease-out;
        }

        .visible {
            display: block;
            opacity: 1;
            transform: translateY(0);
        }

        /* Glassmorphism Effect */
        .content-box {
            background: rgba(255, 255, 255, 0.1);
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 4px 10px rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        /* Table Styles */
        .data-table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }

        .data-table th, .data-table td {
            border: 1px solid white;
            padding: 10px;
            text-align: center;
        }

        .data-table th {
            background-color: #ffcc00;
            color: black;
        }

        /* Warning Styles */
        .warning-row {
            background-color: rgba(255, 204, 0, 0.1);
            border-left: 4px solid #ffcc00;
            animation: pulseWarning 2s infinite;
        }
        
        .warning-text {
            color: #ffcc00;
            font-weight: bold;
        }
        
        @keyframes pulseWarning {
            0% { opacity: 1; }
            50% { opacity: 0.7; }
            100% { opacity: 1; }
        }
    </style>
</head>
<body>

    <!-- Header -->
    <header class="header">
        <div class="logo">MAVERICKS</div>
        <nav class="nav">
            <a onclick="showSection('home')">Home</a>
            <a onclick="showSection('about')">About</a>
            <a onclick="showSection('datas')">Datas</a>
            <a onclick="showSection('gallery')">Gallery</a>
            <a onclick="showSection('contact')">Contact</a>
        </nav>
    </header>

    <!-- Page Title -->
    <div class="main-title">Integrated Landslide Monitoring and Early Warning System</div>

    <!-- Sections -->
    <div id="home" class="section visible">
        <div class="content-box">
            <h2>Welcome to the Landslide Monitoring System</h2>
            <p>This system provides real-time landslide monitoring and early warnings to ensure safety.</p>
        </div>
    </div>

    <div id="datas" class="section">
        <div class="content-box">
            <h2>Live Data</h2>
            <p>Real-time rainfall monitoring (2mm per deflection):</p>
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
                    <tr>
                        <td colspan="4">Loading data...</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>

    <script>
        const ESP32_IP = "192.168.1.100"; // Update with your ESP32's actual IP
        const UPDATE_INTERVAL = 4000; // Refresh every 4 seconds
        const MM_PER_DEFLECTION = 2; // Each deflection equals 2mm of rain
        const WARNING_THRESHOLD = 12; // Landslide warning at 12mm

        let rainfallHistory = [];

        function fetchSensorData() {
            fetch(`http://${ESP32_IP}/data?t=${Date.now()}`)
                .then(response => response.json())
                .then(data => {
                    const now = new Date();
                    const currentRain = data.rainfall * MM_PER_DEFLECTION;

                    rainfallHistory.push({
                        time: now.getTime(),
                        value: currentRain
                    });

                    // Remove old data (older than 4 minutes)
                    rainfallHistory = rainfallHistory.filter(r => r.time > (now.getTime() - 4 * 60 * 1000));

                    // Calculate 4-min total
                    const totalRain = rainfallHistory.reduce((sum, r) => sum + r.value, 0);

                    // Update table
                    updateDataTable(now, currentRain, totalRain);
                })
                .catch(error => {
                    console.error("Error fetching sensor data:", error);
                    document.getElementById('sensorData').innerHTML = `
                        <tr>
                            <td colspan="4">Error fetching data</td>
                        </tr>
                    `;
                });
        }

        function updateDataTable(timestamp, currentRain, totalRain) {
            const tableBody = document.getElementById('sensorData');
            const newRow = document.createElement('tr');

            if (totalRain >= WARNING_THRESHOLD) {
                newRow.classList.add('warning-row');
            }

            newRow.innerHTML = `
                <td>${timestamp.toLocaleTimeString()}</td>
                <td>${currentRain.toFixed(1)}</td>
                <td>${totalRain.toFixed(1)}</td>
                <td>${totalRain >= WARNING_THRESHOLD ? 
                     '<span class="warning-text">WARNING: Possible landslide</span>' : 
                     'Normal'}</td>
            `;

            tableBody.insertBefore(newRow, tableBody.firstChild);

            if (tableBody.children.length > 15) {
                tableBody.removeChild(tableBody.lastChild);
            }
        }

        setInterval(fetchSensorData, UPDATE_INTERVAL);
        fetchSensorData(); // Initial fetch

        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => {
                section.classList.remove('visible');
            });
            setTimeout(() => {
                document.getElementById(sectionId).classList.add('visible');
            }, 100);
        }
    </script>
</body>
</html>
