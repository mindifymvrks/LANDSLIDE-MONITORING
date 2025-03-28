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
            position: relative;
            transition: color 0.3s ease-in-out;
            cursor: pointer;
        }

        .nav a:hover {
            color: #ffcc00;
        }

        .nav a::after {
            content: "";
            display: block;
            height: 2px;
            width: 0;
            background: #ffcc00;
            transition: width 0.3s ease-in-out;
            position: absolute;
            bottom: -5px;
            left: 50%;
            transform: translateX(-50%);
        }

        .nav a:hover::after {
            width: 100%;
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
            opacity: 0;
            transform: translateY(20px);
            transition: opacity 0.5s ease-out, transform 0.5s ease-out;
            text-align: left;
        }

        .visible {
            display: block;
            opacity: 1;
            transform: translateY(0);
        }

        /* Glassmorphism Effect for Sections */
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

        /* Responsive Design */
        @media (max-width: 768px) {
            .header {
                flex-direction: column;
                padding: 10px;
            }

            .nav {
                margin-top: 10px;
            }

            .nav a {
                font-size: 16px;
                margin: 5px;
            }

            .main-title {
                font-size: 24px;
                margin-top: 80px;
            }

            .section {
                padding: 40px 10px;
            }
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

    <div id="about" class="section">
        <div class="content-box">
            <h2>About the Integrated Landslide Monitoring System</h2>
            <p>Landslides are a growing concern in India, particularly in hilly regions where intense rainfall and unstable slopes pose a severe risk to infrastructure and human lives...</p>
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
                    <!-- Data will be inserted here automatically -->
                    <tr>
                        <td colspan="4">Loading data...</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>

    <div id="gallery" class="section">
        <div class="content-box">
            <h2>Gallery</h2>
            <p>Images and diagrams related to landslide monitoring.</p>
        </div>
    </div>

    <div id="contact" class="section">
        <div class="content-box">
            <h2>Contact Details</h2>
            
            <p><strong>Email:</strong></p>
            <p>nirmal.john@saintgits.org</p>
            <p>jithin.cea2125@saintgits.org</p>
            <p>manu.ceb2125@saintgits.org</p>
            <p>mervin.ceb2125@saintgits.org</p>
            <p>noble.ceb2125@saintgits.org</p>

            <p><strong>Phone Numbers:</strong></p>
            <p>+91 95266 08654</p>
            <p>+91 89211 23469</p>
            <p>+91 85908 32820</p>
            <p>+91 75580 69423</p>
        </div>
    </div>

    <script>
        // ESP32 Data Integration
        const ESP32_IP = "192.168.1.100"; // Replace with your ESP32's IP
        const UPDATE_INTERVAL = 4000; // 4 seconds for testing
        // const UPDATE_INTERVAL = 240000; // 4 minutes for production
        const MM_PER_DEFLECTION = 2; // 2mm per pulse
        const WARNING_THRESHOLD = 12; // 12mm in 4 minutes triggers warning
        
        let rainfallHistory = [];
        
        function fetchSensorData() {
            fetch(`http://${ESP32_IP}/data?t=${Date.now()}`)
                .then(response => response.json())
                .then(data => {
                    const now = new Date();
                    const currentRain = data.rainfall * MM_PER_DEFLECTION;
                    
                    // Add new reading to history
                    rainfallHistory.push({
                        time: now.getTime(),
                        value: currentRain
                    });
                    
                    // Remove readings older than 4 minutes
                    const cutoffTime = now.getTime() - (4 * 60 * 1000);
                    rainfallHistory = rainfallHistory.filter(r => r.time > cutoffTime);
                    
                    // Calculate 4-minute total
                    const totalRain = rainfallHistory.reduce((sum, r) => sum + r.value, 0);
                    
                    // Update the table
                    updateDataTable(now, currentRain, totalRain);
                })
                .catch(error => {
                    console.error("Error fetching sensor data:", error);
                    document.getElementById('sensorData').innerHTML = `
                        <tr>
                            <td colspan="4">Error connecting to sensor</td>
                        </tr>
                    `;
                });
        }
        
        function updateDataTable(timestamp, currentRain, totalRain) {
            const tableBody = document.getElementById('sensorData');
            const newRow = document.createElement('tr');
            
            // Add warning class if threshold exceeded
            if (totalRain > WARNING_THRESHOLD) {
                newRow.classList.add('warning-row');
            }
            
            newRow.innerHTML = `
                <td>${timestamp.toLocaleTimeString()}</td>
                <td>${currentRain.toFixed(1)}</td>
                <td>${totalRain.toFixed(1)}</td>
                <td>${totalRain > WARNING_THRESHOLD ? 
                     '<span class="warning-text">WARNING: Possible landslide</span>' : 
                     'Normal'}</td>
            `;
            
            // Add to top of table
            tableBody.insertBefore(newRow, tableBody.firstChild);
            
            // Keep only the last 15 readings
            if (tableBody.children.length > 15) {
                tableBody.removeChild(tableBody.lastChild);
            }
        }
        
        // Start periodic updates
        setInterval(fetchSensorData, UPDATE_INTERVAL);
        fetchSensorData(); // Initial call

        // Your existing navigation function
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
