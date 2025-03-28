<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spreadsheet Data Viewer</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f4;
            padding: 20px;
        }
        table {
            width: 80%;
            margin: 20px auto;
            border-collapse: collapse;
            background: white;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: center;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
    </style>
</head>
<body>
    <h2>Live Spreadsheet Data</h2>
    <table id="data-table">
        <thead>
            <tr id="table-header"></tr>
        </thead>
        <tbody id="table-body"></tbody>
    </table>

    <script>
        const sheetURL = "https://script.google.com/macros/s/AKfycbzldXn8izlLNKwFrzs6MXXRmfqVsWhxCvFyEmLIIktZgeHY2zX2nr5mitWRxjd7GmIp9w/exec";

        function fetchData() {
            fetch(sheetURL)
                .then(response => response.json())
                .then(data => {
                    if (data && data.length > 0) {
                        updateTable(data);
                    } else {
                        document.getElementById("table-body").innerHTML = "<tr><td colspan='100%'>No data available</td></tr>";
                    }
                })
                .catch(error => console.error("Error fetching data:", error));
        }

        function updateTable(data) {
            const tableHeader = document.getElementById("table-header");
            const tableBody = document.getElementById("table-body");
            tableHeader.innerHTML = "";
            tableBody.innerHTML = "";
            
            // Create table headers
            Object.keys(data[0]).forEach(key => {
                let th = document.createElement("th");
                th.textContent = key;
                tableHeader.appendChild(th);
            });
            
            // Populate table rows
            data.forEach(row => {
                let tr = document.createElement("tr");
                Object.values(row).forEach(value => {
                    let td = document.createElement("td");
                    td.textContent = value;
                    tr.appendChild(td);
                });
                tableBody.appendChild(tr);
            });
        }

        fetchData(); // Load data on page load
        setInterval(fetchData, 10000); // Refresh every 10 seconds
    </script>
</body>
</html>
