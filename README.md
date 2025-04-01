<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spreadsheet Viewer</title>
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
        .warning {
            color: red;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <h2>Spreadsheet Data</h2>
    <p id="status">Loading data...</p>
    <table id="data-table"></table>
    
    <script>
        const url = "https://script.google.com/macros/s/AKfycby5m8GXi6m3gCnbZ9dyqUMRtsMzYsgzYAdrpCKcUUyknRUgMsuHIZyswQg2nES4I2L03A/exec";
        
        function fetchData() {
            fetch(url)
                .then(response => {
                    if (!response.ok) {
                        throw new Error(`HTTP error! Status: ${response.status}`);
                    }
                    return response.json();
                })
                .then(data => {
                    let table = document.getElementById("data-table");
                    let status = document.getElementById("status");
                    status.style.display = "none";
                    table.innerHTML = ""; // Clear previous data
                    
                    if (!Array.isArray(data) || data.length === 0) {
                        table.innerHTML = "<tr><td>No data available</td></tr>";
                        return;
                    }
                    
                    // Get current time and filter out entries older than 24 hours
                    let now = new Date();
                    let cutoffTime = now.getTime() - (24 * 60 * 60 * 1000); // 24 hours ago in milliseconds
                    
                    // Create table headers
                    let headerRow = document.createElement("tr");
                    ["Timestamp", "Rainfall (mm)", "Slope Status"].forEach(header => {
                        let th = document.createElement("th");
                        th.textContent = header;
                        headerRow.appendChild(th);
                    });
                    table.appendChild(headerRow);
                    
                    let totalRainfall = 0;
                    
                    // Create table rows
                    data.slice(1).forEach(row => {
                        let timestamp = new Date(row[0]).toLocaleString(); // Correct time formatting
                        let detected = row[1];
                        let entryTime = new Date(row[0]).getTime();
                        
                        if (detected === "YES" && entryTime >= cutoffTime) {
                            let rainfall = 10; // Each YES = 10mm
                            totalRainfall += rainfall;
                            let slopeStatus = totalRainfall > 200 ? "Landslide Warning" : "Safe";
                            
                            let tr = document.createElement("tr");
                            [timestamp, rainfall, slopeStatus].forEach((cell, index) => {
                                let td = document.createElement("td");
                                td.textContent = cell;
                                if (index === 2 && slopeStatus === "Landslide Warning") {
                                    td.classList.add("warning");
                                }
                                tr.appendChild(td);
                            });
                            table.appendChild(tr);
                        }
                    });
                })
                .catch(error => {
                    document.getElementById("status").textContent = "Failed to load data: " + error.message;
                    console.error("Error fetching data:", error);
                });
        }
        
        fetchData(); // Initial fetch
        setInterval(fetchData, 1000); // Refresh data every 1 second (1000 ms)
    </script>
</body>
</html>

