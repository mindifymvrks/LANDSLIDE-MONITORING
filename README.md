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
    </style>
</head>
<body>
    <h2>Spreadsheet Data</h2>
    <table id="data-table">
    </table>
    <script>
        const url = "https://script.google.com/macros/s/AKfycbzldXn8izlLNKwFrzs6MXXRmfqVsWhxCvFyEmLIIktZgeHY2zX2nr5mitWRxjd7GmIp9w/exec";
        
        fetch(url)
            .then(response => response.json())
            .then(data => {
                let table = document.getElementById("data-table");
                
                // Create table headers
                let headerRow = document.createElement("tr");
                data[0].forEach(header => {
                    let th = document.createElement("th");
                    th.textContent = header;
                    headerRow.appendChild(th);
                });
                table.appendChild(headerRow);
                
                // Create table rows
                data.slice(1).forEach(row => {
                    let tr = document.createElement("tr");
                    row.forEach(cell => {
                        let td = document.createElement("td");
                        td.textContent = cell;
                        tr.appendChild(td);
                    });
                    table.appendChild(tr);
                });
            })
            .catch(error => console.error("Error fetching data:", error));
    </script>
</body>
</html>
