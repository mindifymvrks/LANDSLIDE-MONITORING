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
        .debug-info {
            font-family: monospace;
            font-size: 0.8em;
            background-color: #f8f9fa;
            padding: 10px;
            border-radius: 5px;
            margin-top: 20px;
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
        <div class="debug-info">
            <strong>Debug Information:</strong>
            <div id="debugOutput">Waiting for first data fetch...</div>
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
        const debugOutput = document.getElementById("debugOutput");

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

        // Update debug information
        function updateDebugInfo(message) {
            debugOutput.innerHTML = message.replace(/\n/g, "<br>");
        }

        // Try to parse JSON from various possible response formats
        function tryParseResponse(responseText) {
            // Try 1: Direct JSON parse
            try {
                return JSON.parse(responseText);
            } catch (e1) {
                // Try 2: JSON wrapped in success() function
                const jsonpMatch = responseText.match(/success\(({.*})\)/);
                if (jsonpMatch) {
                    try {
                        return JSON.parse(jsonpMatch[1]);
                    } catch (e2) {
                        updateDebugInfo(`JSONP parse failed:\n${e2.message}\n\nResponse text:\n${responseText}`);
                        throw new Error("JSONP parse failed");
                    }
                }
                
                // Try 3: Find first { and last } and try to parse that
                const firstBrace = responseText.indexOf('{');
                const lastBrace = responseText.lastIndexOf('}');
                if (firstBrace >= 0 && lastBrace > firstBrace) {
                    try {
                        return JSON.parse(responseText.substring(firstBrace, lastBrace + 1));
                    } catch (e3) {
                        updateDebugInfo(`Brace extraction parse failed:\n${e3.message}\n\nResponse text:\n${responseText}`);
                        throw new Error("Brace extraction failed");
                    }
                }
                
                // Try 4: Maybe it's already an object?
                if (typeof responseText === 'object') {
                    return responseText;
                }
                
                updateDebugInfo(`All parse attempts failed. Full response:\n${responseText}`);
                throw new Error("Could not parse response as JSON");
            }
        }

        // Fetch data from the API with robust error handling
        async function fetchData() {
            showLoading();
            hideError();
            updateDebugInfo("Starting data fetch...");
            
            try {
                console.log("Fetching data from:", API_URL);
                const response = await fetch(API_URL);
                
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                // Get the response as text first
                const responseText = await response.text();
                updateDebugInfo(`Raw response received (${responseText.length} chars):\n${responseText.substring(0, 200)}${responseText.length > 200 ? '...' : ''}`);
                
                // Try to parse the response
                const data = tryParseResponse(responseText);
                updateDebugInfo(`Successfully parsed data:\n${JSON.stringify(data, null, 2)}`);
                
                return data;
            } catch (error) {
                console.error("Error fetching data:", error);
                showError(`Error loading data: ${error.message}`);
                updateDebugInfo(`Error occurred:\n${error.stack || error.message}`);
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
                noDataMessage.innerHTML = '<td colspan="4" class="text-center">No data received from server</td>';
                return;
            }

            // Handle different possible response structures
            let dataArray;
            if (Array.isArray(data)) {
                dataArray = data;
            } else if (data.success && Array.isArray(data.success)) {
                dataArray = data.success;
            } else if (data.data && Array.isArray(data.data)) {
                dataArray = data.data;
            } else if (data.values && Array.isArray(data.values)) {
                dataArray = data.values;
            } else if (data.records && Array.isArray(data.records)) {
                dataArray = data.records;
            } else {
                console.error("Data is not in expected format:", data);
                showError("Data format is incorrect - expected array");
                noDataMessage.style.display = "table-row";
                noDataMessage.innerHTML = '<td colspan="4" class="text-center">Invalid data format</td>';
                updateDebugInfo(`Unrecognized data format:\n${JSON.stringify(data, null, 2)}`);
                return;
            }

            if (dataArray.length === 0) {
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
            dataArray.forEach((item, index) => {
                const row = document.createElement("tr");
                
                // Safely handle missing properties
                const id = item.id !== undefined ? item.id : index + 1;
                const name = item.name !== undefined ? item.name : "N/A";
                const value = item.value !== undefined ? item.value : "N/A";
                const timestamp = item.timestamp ? new Date(item.timestamp).toLocaleString() : 
                                item.date ? new Date(item.date).toLocaleString() : 
                                "N/A";
                
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
            updateDebugInfo(`Last update: ${new Date().toLocaleString()}\nDisplaying ${dataArray.length} records`);
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
