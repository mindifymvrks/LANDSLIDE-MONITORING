<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Spreadsheet Data</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .card {
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .card-header {
            background-color: #f1f8ff;
            border-bottom: 1px solid #dee2e6;
        }
        .table {
            margin-bottom: 0;
        }
        .table th {
            background-color: #f1f8ff;
            border-top: none;
        }
        .table-responsive {
            overflow-x: auto;
        }
        #lastUpdated {
            font-weight: bold;
            color: #0d6efd;
        }
    </style>
</head>
<body>
    <div class="container mt-5">
        <h1 class="text-center mb-4">Real-Time Spreadsheet Data</h1>
        <div class="card">
            <div class="card-header">
                <div class="row">
                    <div class="col-md-6">
                        <h5>Current Data</h5>
                    </div>
                    <div class="col-md-6 text-end">
                        <small>Last updated: <span id="lastUpdated">Never</span></small>
                    </div>
                </div>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-striped table-hover" id="dataTable">
                        <thead>
                            <tr>
                                <th>ID</th>
                                <th>Name</th>
                                <th>Value</th>
                                <th>Timestamp</th>
                            </tr>
                        </thead>
                        <tbody>
                            <!-- Data will be inserted here by JavaScript -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
        <div class="mt-3 text-center text-muted">
            <small>Auto-refreshing every 10 seconds</small>
        </div>
    </div>

    <script>
        // Configuration
        const API_URL = "https://script.google.com/macros/s/AKfycbzldXn8izlLNKwFrzs6MXXRmfqVsWhxCvFyEmLIIktZgeHY2zX2nr5mitWRxjd7GmIp9w/exec";
        const REFRESH_INTERVAL = 10000; // 10 seconds

        // DOM Elements
        const dataTableBody = document.querySelector("#dataTable tbody");
        const lastUpdatedSpan = document.getElementById("lastUpdated");

        // Fetch data from the API
        async function fetchData() {
            try {
                const response = await fetch(API_URL);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                const data = await response.json();
                return data;
            } catch (error) {
                console.error("Error fetching data:", error);
                return null;
            }
        }

        // Update the table with new data
        function updateTable(data) {
            if (!data || !Array.isArray(data)) {
                console.error("Invalid data format");
                return;
            }

            // Clear existing rows
            dataTableBody.innerHTML = "";

            // Add new rows
            data.forEach(item => {
                const row = document.createElement("tr");
                
                const idCell = document.createElement("td");
                idCell.textContent = item.id || "N/A";
                row.appendChild(idCell);
                
                const nameCell = document.createElement("td");
                nameCell.textContent = item.name || "N/A";
                row.appendChild(nameCell);
                
                const valueCell = document.createElement("td");
                valueCell.textContent = item.value || "N/A";
                row.appendChild(valueCell);
                
                const timestampCell = document.createElement("td");
                timestampCell.textContent = item.timestamp ? new Date(item.timestamp).toLocaleString() : "N/A";
                row.appendChild(timestampCell);
                
                dataTableBody.appendChild(row);
            });

            // Update last updated time
            lastUpdatedSpan.textContent = new Date().toLocaleString();
        }

        // Main function to fetch and update data
        async function updateData() {
            const data = await fetchData();
            if (data) {
                updateTable(data);
            }
        }

        // Initial load
        updateData();

        // Set up periodic refresh
        setInterval(updateData, REFRESH_INTERVAL);
    </script>
</body>
</html>
