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
            margin: 20px;
            color: black;
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
            font-weight: bold;
        }
        .failure {
            background-color: red;
            font-weight: bold;
        }
        #last-update {
            font-style: italic;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h2>Real-Time Rainfall Monitoring</h2>
    <p id="status">Loading data...</p>
    <div id="last-update"></div>
    <table id="data-table"></table>
    
    <script>
        const url = "https://script.google.com/macros/s/AKfycby5m8GXi6m3gCnbZ9dyqUMRtsMzYsgzYAdrpCKcUUyknRUgMsuHIZyswQg2nES4I2L03A/exec";
        let lastNotification = "";
        let lastData = [];

        // Request notification permission
        if ("Notification" in window && Notification.permission !== "granted") {
            Notification.requestPermission().then(permission => {
                console.log("Notification permission:", permission);
            });
        }

        function sendNotification(title, message) {
            if (Notification.permission === "granted") {
                new Notification(title, { 
                    body: message, 
                    icon: "https://cdn-icons-png.flaticon.com/512/189/189664.png" 
                });
            }
        }

        function processData(data) {
            const now = new Date();
            // Set cutoff time to today at 12:27
            const today1227 = new Date();
            today1227.setHours(12, 27, 0, 0);  // Changed from 11:40 to 12:27
            const cutoffTime = today1227.getTime();
            
            let cumulativeRainfall = 0;
            let latestStatus = "Safe";
            let tableHTML = `
                <tr>
                    <th>Timestamp</th>
                    <th>Cumulative Rainfall (mm)</th>
                    <th>Slope Status</th>
                </tr>
            `;

            // Filter and process only new data
            const newData = data.slice(1).filter(row => {
                const rowTime = new Date(row[0]).getTime();
                return rowTime > (lastData.length > 0 ? 
                    new Date(lastData[lastData.length - 1][0]).getTime() : 0);
            });

            if (newData.length === 0 && lastData.length > 0) {
                // No new data, use last data to maintain display
                data = lastData;
            } else {
                lastData = data.slice(1);
            }

            data.slice(1)
                .filter(row => new Date(row[0]).getTime() >= cutoffTime && row[1] === "YES")
                .forEach(row => {
                    cumulativeRainfall += 0.2;
                    
                    let status = "Safe";
                    let rowClass = "";
                    
                    if (cumulativeRainfall >= 5) {
                        status = "Failure";
                        rowClass = "failure";
                    } else if (cumulativeRainfall >= 3) {
                        status = "Warning";
                        rowClass = "warning";
                    } else if (cumulativeRainfall >= 2) {
                        status = "Alert";
                        rowClass = "alert";
                    }
                    
                    latestStatus = status;
                    
                    tableHTML += `
                        <tr class="${rowClass}">
                            <td>${new Date(row[0]).toLocaleString()}</td>
                            <td>${cumulativeRainfall.toFixed(1)}</td>
                            <td>${status}</td>
                        </tr>
                    `;
                });

            // Update the display
            document.getElementById("data-table").innerHTML = tableHTML;
            document.getElementById("last-update").textContent = `Last updated: ${new Date().toLocaleTimeString()}`;
            
            // Handle notifications
            if (latestStatus !== lastNotification) {
                if (latestStatus === "Alert") {
                    sendNotification("⚠️ Alert", "Rainfall reached 2mm. Stay cautious!");
                } else if (latestStatus === "Warning") {
                    sendNotification("⚠️ Warning", "Rainfall reached 3mm. Risk increasing!");
                } else if (latestStatus === "Failure") {
                    sendNotification("🚨 Failure", "Rainfall reached 5mm! Landslide possible!");
                }
                lastNotification = latestStatus;
            }
        }

        function fetchData() {
            fetch(url)
                .then(response => {
                    if (!response.ok) throw new Error("Network response was not ok");
                    return response.json();
                })
                .then(data => {
                    document.getElementById("status").style.display = "none";
                    processData(data);
                })
                .catch(error => {
                    console.error("Fetch error:", error);
                    document.getElementById("status").textContent = "Error loading data. Retrying...";
                    setTimeout(fetchData, 5000);
                });
        }

        // Initial load
        fetchData();
        
        // Refresh every 1 second
        const refreshInterval = setInterval(fetchData, 1000);
        
        // Add error handling for the interval
        window.addEventListener('error', (e) => {
            console.error("Script error:", e);
            clearInterval(refreshInterval);
            document.getElementById("status").textContent = "Script error occurred. Please reload the page.";
        });
    </script>
</body>
</html>
