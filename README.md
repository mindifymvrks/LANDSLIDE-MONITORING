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
    </style>
</head>
<body>
    <h2>Real-Time Landslide Monitoring</h2>
    <p id="status">Loading data...</p>
    <table id="data-table"></table>

    <script>
        const url = "https://script.google.com/macros/s/AKfycby5m8GXi6m3gCnbZ9dyqUMRtsMzYsgzYAdrpCKcUUyknRUgMsuHIZyswQg2nES4I2L03A/exec";

        let lastNotification = ""; // Stores the last notification sent

        // Request permission for notifications
        document.addEventListener("DOMContentLoaded", () => {
            if ("Notification" in window) {
                Notification.requestPermission().then(permission => {
                    console.log("Notification permission:", permission);
                });
            }
        });

        function sendNotification(title, message) {
            if (Notification.permission === "granted") {
                new Notification(title, { 
                    body: message, 
                    icon: "https://cdn-icons-png.flaticon.com/512/189/189664.png" 
                });
            } else {
                console.log("Notification not granted.");
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

                    data.slice(1).reverse().forEach(row => {
                        let timestamp = new Date(row[0]).toLocaleString();
                        let detected = row[1];
                        let entryTime = new Date(row[0]).getTime();
                        
                        if (detected === "YES" && entryTime >= cutoffTime) {
                            let rainfall = 5;
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

                            latestStatus = slopeStatus; // Update latest status

                            let tr = document.createElement("tr");
                            tr.classList.add(rowClass);

                            [timestamp, rainfall, slopeStatus].forEach(cell => {
                                let td = document.createElement("td");
                                td.textContent = cell;
                                tr.appendChild(td);
                            });

                            table.appendChild(tr);
                        }
                    });

                    console.log("Latest Status:", latestStatus, "| Last Notification:", lastNotification);

                    // Send notification only if status changes
                    if (latestStatus !== lastNotification) {
                        if (latestStatus === "Alert") {
                            sendNotification("⚠️ Alert", "Rainfall reached 40mm. Stay cautious!");
                        } else if (latestStatus === "Warning") {
                            sendNotification("⚠️ Warning", "Rainfall reached 50mm. Risk of landslide increasing!");
                        } else if (latestStatus === "Failure") {
                            sendNotification("🚨 Failure", "Rainfall reached 70mm! Landslide possible!");
                        }
                        lastNotification = latestStatus; // Update last notification
                    }
                })
                .catch(error => {
                    document.getElementById("status").textContent = "Failed to load data: " + error.message;
                    console.error("Error fetching data:", error);
                });
        }

        fetchData(); // Initial fetch
        setInterval(fetchData, 5000); // Fetch data every 5 seconds
    </script>
</body>
</html>
