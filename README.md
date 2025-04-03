function processData(data) {
    const now = new Date();
    // Set cutoff time to today at 10:35 AM
    const today1035 = new Date();
    today1035.setHours(10, 35, 0, 0);
    const cutoffTime = today1035.getTime();

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
