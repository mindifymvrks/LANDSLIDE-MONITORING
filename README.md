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
            padding: 20px;
        }
        .card {
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
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
        #lastUpdated {
            font-weight: bold;
            color: #0d6efd;
        }
        #loadingSpinner {
            display: none;
        }
        .error-message {
            color: #dc3545;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="container mt-3">
        <h1 class="text-center mb-4">Real-Time Spreadsheet Data</h1>
        
        <div class="card">
            <div class="card-header">
                <div class="row">
                    <div class="col-md-6">
                        <h5>Current Data</h5>
                    </div>
                    <div class="col-md-6 text-end">
                        <div id="loadingSpinner" class="spinner-border spinner-border-sm" role="status">
                            <span class="visually-hidden">Loading...</span>
                        </div>
                        <small>Last updated: <span id="lastUpdated">Never</span></small>
                    </div>
                </div>
            </div>
            <div class="card-body">
                <div id="errorMessage" class="error-message mb-3" style="display: none;"></div>
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
                            <tr id="noDataMessage">
                                <td colspan="4" class="text-center">Loading data...</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
        <div class="text-center text-muted">
            <small>Auto-refreshing every 0.5 seconds</small>
        </div>
    </div>

    <script>
        // Configuration
        const API_URL = "https://script.google.com/macros/s/AKfycbzldXn8izlLNKwFrzs6MXXRmfqVsWhxCvFyEmLIIktZgeHY2zX2nr5mitWRxjd7GmIp9w/exec";
        const REFRESH_INTERVAL = 500; // 0.5 seconds
        
        // DOM Elements
        const dataTableBody = document.querySelector("#dataTable tbody");
        const lastUpdatedSpan = document.getElementById("lastUpdated");
        const loadingSpinner = document.getElementById("loadingSpinner");
        const errorMessage = document.getElementById("errorMessage");
        const noDataMessage = document.getElementById("noDataMessage");

        // Show loading state
        function showLoading() {
            loadingSpinner.style.display = "inline-block";
        }

        // Hide loading state
        function hideLoading() {
            loadingSpinner.style.display = "none";
        }

        // Show error message
        function showError(message) {
            errorMessage.textContent = message;
            errorMessage.style.display = "block";
        }

        // Hide error message
        function hideError() {
            errorMessage.style.display = "none";
        }

        // Fetch data from the API with improved JSON parsing
        async function fetchData() {
            showLoading();
            hideError();
            
            try {
                console.log("Fetching data from:", API_URL);
                const response = await fetch(API_URL);
                
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                // First get the response as text
                const responseText = await response.text();
                console.log("Raw response:", responseText);
                
                // Try to parse as JSON
                let data;
                try {
                    data = JSON.parse(responseText);
                } catch (jsonError) {
                    // If parsing fails, check if it's a JSONP-like response
                    if (responseText.includes('success(')) {
                        // Extract JSON from JSONP-like response
                        const jsonStart = responseText.indexOf('{');
                        const jsonEnd = responseText.lastIndexOf('}') + 1;
                        const jsonString = responseText.slice(jsonStart, jsonEnd);
                        data = JSON.parse(jsonString);
                    } else {
                        throw new Error("Response is not valid JSON: " + responseText);
                    }
                }
                
                console.log("Parsed data:", data);
                return data;
            } catch (error) {
                console.error("Error fetching data:", error);
                showError(`Error loading data: ${error.message}`);
                return null;
            } finally {
                hideLoading();
            }
        }

        // Update the table with new data
        function updateTable(data) {
            if (!data) {
                console.log("No data received");
                noDataMessage.style.display = "table-row";
                return;
            }

            // Handle case where data might be wrapped in a success object
            if (data.success && Array.isArray(data.success)) {
                data = data.success;
            } else if (data.data && Array.isArray(data.data)) {
                data = data.data;
            }

            if (!Array.isArray(data)) {
                console.error("Data is not an array:", data);
                showError("Data format is incorrect - expected array");
                noDataMessage.style.display = "table-row";
                noDataMessage.innerHTML = '<td colspan="4" class="text-center">Invalid data format</td>';
                return;
            }

            if (data.length === 0) {
                console.log("Empty data array received");
                noDataMessage.style.display = "table-row";
                noDataMessage.innerHTML = '<td colspan="4" class="text-center">No data available</td>';
                return;
            }

            // Hide no data message
            noDataMessage.style.display = "none";

            // Clear existing rows (except the no data message)
            while (dataTableBody.firstChild) {
                if (dataTableBody.firstChild !== noDataMessage) {
                    dataTableBody.removeChild(dataTableBody.firstChild);
                } else {
                    break;
                }
            }

            // Add new rows
            data.forEach(item => {
                const row = document.createElement("tr");
                
                // Safely handle missing properties
                const id = item.id !== undefined ? item.id : "N/A";
                const name = item.name !== undefined ? item.name : "N/A";
                const value = item.value !== undefined ? item.value : "N/A";
                const timestamp = item.timestamp ? new Date(item.timestamp).toLocaleString() : "N/A";
                
                row.innerHTML = `
                    <td>${id}</td>
                    <td>${name}</td>
                    <td>${value}</td>
                    <td>${timestamp}</td>
                `;
                
                dataTableBody.appendChild(row);
            });

            // Update last updated time
            lastUpdatedSpan.textContent = new Date().toLocaleString();
        }

        // Main function to fetch and update data
        async function updateData() {
            const data = await fetchData();
            updateTable(data);
        }

        // Initial load
        updateData();

        // Set up periodic refresh
        setInterval(updateData, REFRESH_INTERVAL);
    </script>
</body>
</html>
