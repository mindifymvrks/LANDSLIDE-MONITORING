<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Spreadsheet Viewer</title>
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
    </style>
</head>
<body>
    <h2>Real-Time Spreadsheet Data</h2>
    <p id="status">Loading data...</p>
    <table id="data-table"></table>
    
    <script>
        const url = "https://script.google.com/macros/s/AKfycby5m8GXi6m3gCnbZ9dyqUMRtsMzYsgzYAdrpCKcUUyknRUgMsuHIZyswQg2nES4I2L03A/exec";
        let lastNotification = ""; // Prevent repeated notifications
        let cumulativeRainfall = 0; // Track cumulative rainfall across refreshes

        // Request notification permission
        if ("Notification" in window) {
            Notification.requestPermission();
        }

        function sendNotification(title, message) {
            if (Notification.permission === "granted") {
                new Notification(title, { body: message, icon: "https://cdn-icons-png.flaticon.com/512/189/189664.png" });
            }
        }

        function fetchData() {
            fetch(url)
                .then(response => response.json())
                .then(data => {
                    let table = document.getElementById("data-table");
                    let status = document.getElementById("status");
                    status.style.display = "none";
                    table.innerHTML = ""; // Clear previous data
                    
                    if (!Array.isArray(data) || data.length === 0) {
                        table.innerHTML = "<tr><td>No data available</td></tr>";
                        return;
                    }
                    
                    let now = new Date();
                    let cutoffTime = now.getTime() - (24 * 60 * 60 * 1000); // 24-hour cutoff

                    let headerRow = document.createElement("tr");
                    ["Timestamp", "Cumulative Rainfall (mm)", "Slope Status"].forEach(header => {
                        let th = document.createElement("th");
                        th.textContent = header;
                        headerRow.appendChild(th);
                    });
                    table.appendChild(headerRow);
                    
                    // Reset cumulative rainfall for each fetch
                    let sessionRainfall = 0;
                    let latestStatus = "Safe";

                    // Process data in chronological order (oldest first)
                    data.slice(1).forEach(row => {
                        let timestamp = new Date(row[0]);
                        let entryTime = timestamp.getTime();
                        let detected = row[1];
                        
                        // Only process entries within last 24 hours
                        if (entryTime >= cutoffTime && detected === "YES") {
                            sessionRainfall += 0.2; // Each "YES" = 0.2mm rainfall

                            let slopeStatus = "Safe";
                            let rowClass = "";

                            if (sessionRainfall >= 5) {
                                slopeStatus = "Failure";
                                rowClass = "failure";
                            } else if (sessionRainfall >= 3) {
                                slopeStatus = "Warning";
                                rowClass = "warning";
                            } else if (sessionRainfall >= 2) {
                                slopeStatus = "Alert";
                                rowClass = "alert";
                            }

                            latestStatus = slopeStatus; // Update latest status

                            let tr = document.createElement("tr");
                            tr.classList.add(rowClass);

                            [timestamp.toLocaleString(), sessionRainfall.toFixed(1), slopeStatus].forEach(cell => {
                                let td = document.createElement("td");
                                td.textContent = cell;
                                tr.appendChild(td);
                            });

                            table.appendChild(tr);
                        }
                    });

                    // Update cumulative rainfall
                    cumulativeRainfall = sessionRainfall;

                    // Send notification only if status changes
                    if (latestStatus !== lastNotification) {
                        if (latestStatus === "Alert") {
                            sendNotification("⚠️ Alert", "Rainfall reached 2mm. Stay cautious!");
                        } else if (latestStatus === "Warning") {
                            sendNotification("⚠️ Warning", "Rainfall reached 3mm. Risk of landslide increasing!");
                        } else if (latestStatus === "Failure") {
                            sendNotification("🚨 Failure", "Rainfall reached 5mm! Landslide possible!");
                        }
                        lastNotification = latestStatus;
                    }
                })
                .catch(error => {
                    document.getElementById("status").textContent = "Failed to load data: " + error.message;
                    console.error("Error fetching data:", error);
                });
        }

        fetchData(); // Initial fetch
        setInterval(fetchData, 1000); // Fetch data every second
    </script>
</body>
</html>
