<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Landslide Monitoring</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }
        table {
            width: 80%;
            margin: 20px auto;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid black;
            padding: 10px;
            text-align: center;
        }
        th {
            background-color: #f2f2f2;
        }
        .alert {
            background-color: yellow;
            font-weight: bold;
        }
        .warning {
            background-color: orange;
            color: white;
            font-weight: bold;
        }
        .failure {
            background-color: red;
            color: white;
            font-weight: bold;
        }
        #debug {
            margin: 20px;
            padding: 10px;
            border: 1px solid #ccc;
            text-align: left;
            white-space: pre;
            font-family: monospace;
        }
    </style>
</head>
<body>
    <h2>Real-Time Landslide Monitoring</h2>
    <p id="status">Loading data...</p>
    <table id="data-table"></table>
    <div id="debug">Debug information will appear here</div>

    <script>
        const url = "https://script.google.com/macros/s/AKfycby5m8GXi6m3gCnbZ9dyqUMRtsMzYsgzYAdrpCKcUUyknRUgMsuHIZyswQg2nES4I2L03A/exec";
        let lastNotification = "";
        let lastData = null;

        // Debug logging
        function debugLog(message) {
            const debugDiv = document.getElementById('debug');
            debugDiv.textContent = message + '\n' + debugDiv.textContent;
            console.log(message);
        }

        // Request notification permission
        document.addEventListener("DOMContentLoaded", () => {
            if ("Notification" in window) {
                Notification.requestPermission().then(permission => {
                    debugLog("Notification permission: " + permission);
                });
            }
        });

        function sendNotification(title, message) {
            if (Notification.permission === "granted") {
                new Notification(title, { 
                    body: message, 
                    icon: "https://cdn-icons-png.flaticon.com/512/189/189664.png" 
                });
                debugLog("Notification sent: " + title + " - " + message);
            } else {
                debugLog("Notification not granted.");
            }
        }

        function processData(data) {
            debugLog("Raw data received: " + JSON.stringify(data));
            
            let table = document.getElementById("data-table");
            let status = document.getElementById("status");
            status.textContent = "Last updated: " + new Date().toLocaleTimeString();
            table.innerHTML = "";
            
            if (!Array.isArray(data) {
                debugLog("Data is not an array");
                table.innerHTML = "<tr><td colspan='3'>Invalid data format received</td></tr>";
                return { totalRainfall: 0, latestStatus: "No Data" };
            }
            
            if (data.length === 0) {
                debugLog("Empty data array");
                table.innerHTML = "<tr><td colspan='3'>No data available</td></tr>";
                return { totalRainfall: 0, latestStatus: "No Data" };
            }
            
            // Create table header
            let headerRow = document.createElement("tr");
            ["Timestamp", "Rainfall (mm)", "Slope Status"].forEach(header => {
                let th = document.createElement("th");
                th.textContent = header;
                headerRow.appendChild(th);
            });
            table.appendChild(headerRow);
            
            let totalRainfall = 0;
            let latestStatus = "Safe";
            let now = new Date();
            let cutoffTime = now.getTime() - (24 * 60 * 60 * 1000); // 24-hour cutoff

            // Process each row (skip header if present)
            const startRow = (data[0][0] === "Timestamp" || data[0][0] === "Date") ? 1 : 0;
            
            for (let i = startRow; i < data.length; i++) {
                const row = data[i];
                if (!row || row.length < 2) continue;
                
                try {
                    const timestamp = new Date(row[0]);
                    const entryTime = timestamp.getTime();
                    const detected = String(row[1]).toUpperCase();
                    
                    // Only process recent "YES" entries
                    if (detected === "YES" && entryTime >= cutoffTime) {
                        const rainfall = 5; // Assuming 5mm per detection
                        totalRainfall += rainfall;

                        let slopeStatus = "Safe";
                        let rowClass = "";

                        if (totalRainfall >= 70) {
                            slopeStatus = "Failure";
                            rowClass = "failure";
                        } else if (totalRainfall >= 50) {
                            slopeStatus = "Warning";
                            rowClass = "warning";
                        } else if (totalRainfall >= 40) {
                            slopeStatus = "Alert";
                            rowClass = "alert";
                        }

                        latestStatus = slopeStatus;

                        // Add row to table
                        const tr = document.createElement("tr");
                        if (rowClass) tr.classList.add(rowClass);
                        
                        [timestamp.toLocaleString(), rainfall, slopeStatus].forEach(cell => {
                            const td = document.createElement("td");
                            td.textContent = cell;
                            tr.appendChild(td);
                        });

                        table.appendChild(tr);
                    }
                } catch (e) {
                    debugLog("Error processing row " + i + ": " + e.message);
                }
            }
            
            debugLog("Processing complete. Total rainfall: " + totalRainfall + "mm, Status: " + latestStatus);
            return { totalRainfall, latestStatus };
        }

        function fetchData() {
            debugLog("Fetching data from: " + url);
            
            fetch(url)
                .then(response => {
                    if (!response.ok) {
                        throw new Error(`HTTP error! status: ${response.status}`);
                    }
                    return response.json();
                })
                .then(data => {
                    // Check if data is different from last fetch
                    if (JSON.stringify(data) !== JSON.stringify(lastData)) {
                        debugLog("New data detected");
                        lastData = data;
                        
                        const { latestStatus } = processData(data);
                        
                        // Send notification only if status changes
                        if (latestStatus !== lastNotification) {
                            if (latestStatus === "Alert") {
                                sendNotification("⚠️ Alert", "Rainfall reached 40mm. Stay cautious!");
                            } else if (latestStatus === "Warning") {
                                sendNotification("⚠️ Warning", "Rainfall reached 50mm. Risk of landslide increasing!");
                            } else if (latestStatus === "Failure") {
                                sendNotification("🚨 Failure", "Rainfall reached 70mm! Landslide possible!");
                            }
                            lastNotification = latestStatus;
                            debugLog("Status changed to: " + latestStatus);
                        }
                    } else {
                        debugLog("No new data - same as last fetch");
                        document.getElementById("status").textContent = "Last updated: " + new Date().toLocaleTimeString() + " (no new data)";
                    }
                })
                .catch(error => {
                    debugLog("Fetch error: " + error.message);
                    document.getElementById("status").textContent = "Error loading data: " + error.message;
                });
        }

        // Initial fetch
        fetchData();
        
        // Set up periodic fetching
        const interval = setInterval(fetchData, 5000);
        
        // For debugging: expose functions to console
        window.debugFunctions = {
            fetchData,
            processData,
            sendNotification
        };
    </script>
</body>
</html>
