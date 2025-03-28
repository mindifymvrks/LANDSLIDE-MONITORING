<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rainfall Warning System</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
        }
        #warning {
            color: red;
            font-size: 24px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <h1>Rainfall Monitoring System</h1>
    <p id="status">Fetching data...</p>
    <p id="warning"></p>
    
    <script>
        async function fetchRainfallData() {
            const url = "https://script.google.com/macros/s/AKfycbzldXn8izlLNKwFrzs6MXXRmfqVsWhxCvFyEmLIIktZgeHY2zX2nr5mitWRxjd7GmIp9w/exec";
            try {
                let response = await fetch(url);
                let data = await response.json();
                let totalRainfall = data.filter(entry => entry === "YES").length * 10;
                
                document.getElementById("status").textContent = `Accumulated Rainfall: ${totalRainfall} mm`;
                
                if (totalRainfall > 120) {
                    document.getElementById("warning").textContent = "Landslide Warning!";
                }
            } catch (error) {
                document.getElementById("status").textContent = "Error fetching data.";
                console.error("Error:", error);
            }
        }

        fetchRainfallData();
    </script>
</body>
</html>
