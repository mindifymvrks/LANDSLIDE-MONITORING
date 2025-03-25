<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .alert { 
            padding: 15px; 
            margin: 10px 0; 
            border-radius: 5px;
            background-color: #ffdddd;
            color: #d8000c;
            display: none;
        }
        table {
            width: 100%;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }
        tr.danger { background-color: #ffcccc; }
        tr.warning { background-color: #fff3cd; }
    </style>
</head>
<body>
    <h1>Landslide Monitoring System</h1>
    
    <div id="alertBox" class="alert">
        Warning: Landslide risk detected! Safety factor < 1.2
    </div>

    <table id="dataTable">
        <thead>
            <tr>
                <th>Timestamp</th>
                <th>Rainfall (mm)</th>
                <th>Safety Factor</th>
                <th>Status</th>
            </tr>
        </thead>
        <tbody id="tableBody"></tbody>
    </table>

    <script>
        // API Configuration
        const API_URL = 'api/data.txt'; // Plain text file
        const UPDATE_INTERVAL = 30000; // 30 seconds

        // DOM Elements
        const alertBox = document.getElementById('alertBox');
        const tableBody = document.getElementById('tableBody');

        // Parse plain text data (format: rainfall_mm,safety_factor,is_unsafe)
        function parseData(text) {
            const parts = text.split(',');
            return {
                rainfall: parseFloat(parts[0]),
                safety: parseFloat(parts[1]),
                isUnsafe: parts[2] === "1",
                timestamp: new Date()
            };
        }

        // Update the table
        function updateTable(data) {
            const row = document.createElement('tr');
            if (data.isUnsafe) {
                row.classList.add('danger');
                alertBox.style.display = 'block';
            } else if (data.safety < 1.5) {
                row.classList.add('warning');
            }

            row.innerHTML = `
                <td>${data.timestamp.toLocaleString()}</td>
                <td>${data.rainfall.toFixed(1)} mm</td>
                <td>${data.safety.toFixed(2)}</td>
                <td>${data.isUnsafe ? 'DANGER' : 'Safe'}</td>
            `;

            // Add to top of table
            tableBody.insertBefore(row, tableBody.firstChild);

            // Keep only last 20 entries
            if (tableBody.children.length > 20) {
                tableBody.removeChild(tableBody.lastChild);
            }
        }

        // Fetch data from GitHub
        async function fetchData() {
            try {
                const response = await fetch(`${API_URL}?t=${new Date().getTime()}`);
                const text = await response.text();
                return parseData(text);
            } catch (error) {
                console.error("Error fetching data:", error);
                return {
                    rainfall: 0,
                    safety: 2.5,
                    isUnsafe: false,
                    timestamp: new Date()
                };
            }
        }

        // Main update function
        async function update() {
            const data = await fetchData();
            updateTable(data);
        }

        // Initial load and periodic updates
        update();
        setInterval(update, UPDATE_INTERVAL);
    </script>
</body>
</html>
