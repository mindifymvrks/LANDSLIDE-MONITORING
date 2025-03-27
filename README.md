<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring</title>
    <style>
        /* Your existing CSS styles */
        body {
            background-color: #0d0d0d;
            color: white;
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            text-align: center;
        }
        /* ... (keep all your existing styles) ... */
        
        /* Add these new styles */
        .warning-row {
            animation: pulseWarning 1s infinite;
            background-color: rgba(255, 204, 0, 0.2);
        }
        @keyframes pulseWarning {
            0% { background-color: rgba(255, 204, 0, 0.2); }
            50% { background-color: rgba(255, 204, 0, 0.4); }
            100% { background-color: rgba(255, 204, 0, 0.2); }
        }
        .warning-message {
            color: #ffcc00;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <!-- Your existing header and navigation -->
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

    <div class="main-title">Integrated Landslide Monitoring and Early Warning System</div>

    <!-- Modified Data Section -->
    <div id="datas" class="section">
        <div class="content-box">
            <h2>Live Data</h2>
            <p>Real-time rainfall monitoring (2mm per deflection):</p>

            <table class="data-table">
                <thead>
                    <tr>
                        <th>Time</th>
                        <th>Rainfall (mm)</th>
                        <th>4-min Accumulation</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody id="dataBody">
                    <!-- Data will be inserted here -->
                </tbody>
            </table>
        </div>
    </div>

    <!-- Your other sections remain unchanged -->
    <script>
        // Configuration
        const ESP32_API_URL = 'http://[ESP32_IP]/data'; // Replace with your ESP32 IP
        const UPDATE_INTERVAL = 4000; // 4 minutes in ms (240000) - using 4s for testing
        const RAIN_THRESHOLD = 12; // 12mm threshold
        const MM_PER_DEFLECTION = 2; // Each pulse = 2mm

        let rainfallHistory = [];
        let total4MinRain = 0;

        // Fetch data from ESP32
        async function fetchData() {
            try {
                const response = await fetch(`${ESP32_API_URL}?t=${Date.now()}`);
                if (!response.ok) throw new Error('Network error');
                return await response.json();
            } catch (error) {
                console.error('Fetch error:', error);
                return null;
            }
        }

        // Update the data table
        function updateTable() {
            fetchData().then(data => {
                if (!data) return;

                const now = new Date();
                const timeString = now.toLocaleTimeString();
                
                // Calculate 4-minute accumulation
                rainfallHistory.push({
                    time: now.getTime(),
                    value: data.rainfall * MM_PER_DEFLECTION
                });
                
                // Remove entries older than 4 minutes
                const cutoff = now.getTime() - (4 * 60 * 1000);
                rainfallHistory = rainfallHistory.filter(entry => entry.time > cutoff);
                
                // Calculate total
                total4MinRain = rainfallHistory.reduce((sum, entry) => sum + entry.value, 0);
                
                // Determine status
                let status = 'Normal';
                let rowClass = '';
                if (total4MinRain > RAIN_THRESHOLD) {
                    status = '<span class="warning-message">WARNING: Landslide possible</span>';
                    rowClass = 'warning-row';
                }

                // Create new table row
                const row = document.createElement('tr');
                if (rowClass) row.className = rowClass;
                row.innerHTML = `
                    <td>${timeString}</td>
                    <td>${(data.rainfall * MM_PER_DEFLECTION).toFixed(1)}</td>
                    <td>${total4MinRain.toFixed(1)}</td>
                    <td>${status}</td>
                `;

                // Add to table
                const tableBody = document.getElementById('dataBody');
                tableBody.insertBefore(row, tableBody.firstChild);
                
                // Keep table manageable size
                if (tableBody.children.length > 15) {
                    tableBody.removeChild(tableBody.lastChild);
                }
            });
        }

        // Initialize
        setInterval(updateTable, UPDATE_INTERVAL);
        updateTable(); // First update

        // Your existing showSection function
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
