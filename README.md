<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring System</title>
    <style>
        :root {
            --safe-color: #28a745;
            --warning-color: #ffc107;
            --danger-color: #dc3545;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f8f9fa;
            color: #343a40;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        header {
            background-color: #2c3e50;
            color: white;
            padding: 20px 0;
            text-align: center;
            margin-bottom: 30px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        h1 {
            margin: 0;
            font-size: 2.2rem;
        }
        .status-card {
            background: white;
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 30px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
        }
        .metric {
            text-align: center;
            padding: 15px;
            flex: 1;
            min-width: 200px;
        }
        .metric-value {
            font-size: 2.5rem;
            font-weight: bold;
            margin: 10px 0;
        }
        .metric.safe .metric-value {
            color: var(--safe-color);
        }
        .metric.warning .metric-value {
            color: var(--warning-color);
        }
        .metric.danger .metric-value {
            color: var(--danger-color);
        }
        table {
            width: 100%;
            border-collapse: collapse;
            background: white;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
        }
        th, td {
            padding: 15px;
            text-align: left;
            border-bottom: 1px solid #dee2e6;
        }
        th {
            background-color: #2c3e50;
            color: white;
        }
        tr.warning {
            background-color: rgba(255, 193, 7, 0.1);
        }
        tr.danger {
            background-color: rgba(220, 53, 69, 0.1);
            animation: pulse 1.5s infinite;
        }
        .alert {
            padding: 15px;
            margin-bottom: 20px;
            border-radius: 8px;
            color: white;
            font-weight: bold;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .alert-danger {
            background-color: var(--danger-color);
        }
        .alert-warning {
            background-color: var(--warning-color);
            color: #343a40;
        }
        .close-alert {
            background: none;
            border: none;
            color: inherit;
            font-size: 1.5rem;
            cursor: pointer;
        }
        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.7; }
            100% { opacity: 1; }
        }
        .last-updated {
            text-align: right;
            font-style: italic;
            color: #6c757d;
            margin-top: 10px;
        }
        @media (max-width: 768px) {
            .status-card {
                flex-direction: column;
            }
            .metric {
                margin-bottom: 15px;
            }
            th, td {
                padding: 10px 5px;
                font-size: 0.9rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Landslide Monitoring System</h1>
            <p>Real-time rainfall and slope stability analysis</p>
        </header>

        <div id="alertContainer"></div>

        <div class="status-card">
            <div class="metric" id="rainfallMetric">
                <h3>Current Rainfall</h3>
                <div class="metric-value">0 mm</div>
                <p>Last hour accumulation</p>
            </div>
            <div class="metric" id="safetyMetric">
                <h3>Safety Factor</h3>
                <div class="metric-value">2.50</div>
                <p>Stability index</p>
            </div>
            <div class="metric" id="statusMetric">
                <h3>Slope Status</h3>
                <div class="metric-value">Stable</div>
                <p>Current condition</p>
            </div>
        </div>

        <h2>Recent Measurements</h2>
        <table>
            <thead>
                <tr>
                    <th>Timestamp</th>
                    <th>Rainfall (mm)</th>
                    <th>Safety Factor</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <!-- Data will be inserted here -->
            </tbody>
        </table>
        <div class="last-updated" id="lastUpdated">Last updated: Never</div>
    </div>

    <script>
        // Configuration
        const API_URL = 'api/data.json';
        const SAFETY_THRESHOLD = 1.2;
        const WARNING_THRESHOLD = 1.5;
        const UPDATE_INTERVAL = 30000; // 30 seconds
        const MAX_ROWS = 20;

        // DOM Elements
        const tableBody = document.getElementById('tableBody');
        const alertContainer = document.getElementById('alertContainer');
        const rainfallMetric = document.getElementById('rainfallMetric');
        const safetyMetric = document.getElementById('safetyMetric');
        const statusMetric = document.getElementById('statusMetric');
        const lastUpdatedEl = document.getElementById('lastUpdated');

        // Current state
        let currentStatus = 'stable';
        let alertActive = false;

        // Format timestamp
        function formatTimestamp(date) {
            return date.toLocaleString();
        }

        // Update metrics display
        function updateMetrics(data) {
            // Update rainfall
            const rainfallEl = rainfallMetric.querySelector('.metric-value');
            rainfallEl.textContent = `${data.rainfall_mm.toFixed(1)} mm`;
            
            // Update safety factor
            const safetyEl = safetyMetric.querySelector('.metric-value');
            safetyEl.textContent = data.safety_factor.toFixed(2);
            
            // Update status
            const statusEl = statusMetric.querySelector('.metric-value');
            let statusText, statusClass;
            
            if (data.safety_factor < SAFETY_THRESHOLD) {
                statusText = 'Critical';
                statusClass = 'danger';
                currentStatus = 'danger';
            } else if (data.safety_factor < WARNING_THRESHOLD) {
                statusText = 'Warning';
                statusClass = 'warning';
                currentStatus = 'warning';
            } else {
                statusText = 'Stable';
                statusClass = 'safe';
                currentStatus = 'stable';
            }
            
            statusEl.textContent = statusText;
            
            // Update metric classes
            rainfallMetric.className = safetyMetric.className = statusMetric.className = 'metric';
            statusMetric.classList.add(statusClass);
            
            // Highlight safety metric if not safe
            if (statusClass !== 'safe') {
                safetyMetric.classList.add(statusClass);
            }
        }

        // Update table with new data
        function updateTable(data) {
            const now = new Date();
            const row = document.createElement('tr');
            
            // Set row class based on safety factor
            if (data.safety_factor < SAFETY_THRESHOLD) {
                row.classList.add('danger');
                if (!alertActive) showAlert('LANDSLIDE WARNING: Safety factor critical!', 'danger');
            } else if (data.safety_factor < WARNING_THRESHOLD) {
                row.classList.add('warning');
                if (!alertActive) showAlert('Warning: Safety factor decreasing', 'warning');
            }
            
            row.innerHTML = `
                <td>${formatTimestamp(now)}</td>
                <td>${data.rainfall_mm.toFixed(1)}</td>
                <td>${data.safety_factor.toFixed(2)}</td>
                <td>${currentStatus.toUpperCase()}</td>
            `;
            
            tableBody.insertBefore(row, tableBody.firstChild);
            
            // Keep table to reasonable size
            if (tableBody.children.length > MAX_ROWS) {
                tableBody.removeChild(tableBody.lastChild);
            }
            
            // Update last updated time
            lastUpdatedEl.textContent = `Last updated: ${formatTimestamp(now)}`;
        }

        // Show alert banner
        function showAlert(message, type) {
            alertActive = true;
            alertContainer.innerHTML = `
                <div class="alert alert-${type}">
                    <span>${message}</span>
                    <button class="close-alert" onclick="dismissAlert()">×</button>
                </div>
            `;
            
            // Auto-dismiss after 10 seconds if danger
            if (type === 'warning') {
                setTimeout(dismissAlert, 10000);
            }
        }

        // Dismiss alert
        window.dismissAlert = function() {
            alertContainer.innerHTML = '';
            alertActive = false;
        }

        // Fetch data from API
        async function fetchData() {
            try {
                const response = await fetch(`${API_URL}?t=${new Date().getTime()}`);
                if (!response.ok) throw new Error('Network response was not ok');
                return await response.json();
            } catch (error) {
                console.error('Error fetching data:', error);
                return {
                    rainfall_mm: 0,
                    safety_factor: 2.5,
                    is_unsafe: false
                };
            }
        }

        // Process and display data
        async function updateData() {
            const data = await fetchData();
            updateMetrics(data);
            updateTable(data);
        }

        // Initialize and set update interval
        updateData();
        setInterval(updateData, UPDATE_INTERVAL);

        // Service worker for offline capability
        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register('/sw.js').catch(registrationError => {
                    console.log('SW registration failed: ', registrationError);
                });
            });
        }
    </script>
</body>
</html>
