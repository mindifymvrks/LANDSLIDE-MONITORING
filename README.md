<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring</title>
    <style>
        /* General Styles */
        body {
            background-color: #0d0d0d;
            color: white;
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            text-align: center;
        }

        /* Header Styles */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 30px;
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
            border-bottom: 1px solid #555;
            position: fixed;
            width: 100%;
            top: 0;
            left: 0;
            z-index: 1000;
        }

        .logo {
            font-size: 24px;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: #ffcc00;
        }

        /* Navigation Bar */
        .nav a {
            color: white;
            text-decoration: none;
            margin: 0 15px;
            font-size: 18px;
            position: relative;
            transition: color 0.3s ease-in-out;
        }

        .nav a:hover {
            color: #ffcc00;
        }

        .nav a::after {
            content: "";
            display: block;
            height: 2px;
            width: 0;
            background: #ffcc00;
            transition: width 0.3s ease-in-out;
            position: absolute;
            bottom: -5px;
            left: 50%;
            transform: translateX(-50%);
        }

        .nav a:hover::after {
            width: 100%;
        }

        /* Main Title */
        .main-title {
            font-size: 32px;
            font-weight: bold;
            margin-top: 100px;
        }

        /* Button Styles */
        .live-data-btn {
            display: inline-block;
            margin-top: 20px;
            padding: 15px 30px;
            font-size: 20px;
            font-weight: bold;
            color: white;
            background: #ffcc00;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: transform 0.3s ease-in-out, background 0.3s ease-in-out;
        }

        .live-data-btn:hover {
            transform: scale(1.1);
            background: #e6b800;
        }

        /* Section Styling */
        .section {
            display: none;
            padding: 60px 20px;
            max-width: 800px;
            margin: 0 auto;
            opacity: 0;
            transform: translateY(20px);
            transition: opacity 0.5s ease-out, transform 0.5s ease-out;
            text-align: left;
        }

        .visible {
            display: block;
            opacity: 1;
            transform: translateY(0);
        }

        /* Table Styles */
        .data-table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }

        .data-table th, .data-table td {
            border: 1px solid white;
            padding: 10px;
            text-align: center;
        }

        .data-table th {
            background-color: #ffcc00;
            color: black;
        }

    </style>
</head>
<body>

    <!-- Header -->
    <header class="header">
        <div class="logo">MAVERICKS</div>
        <nav class="nav">
            <a onclick="showSection('home')">Home</a>
            <a onclick="showSection('about')">About</a>
            <a onclick="showSection('datas')">Datas</a>
            <a onclick="showSection('gallery')">Gallery</a>
            <a onclick="showSection('contact')">Contact</a>
        </nav>
    </header>

    <!-- Homepage -->
    <div id="home" class="section visible">
        <div class="content-box">
            <h2>Integrated Landslide Monitoring and Early Warning System</h2>
            <p>Welcome to the Landslide Monitoring System. This system provides real-time landslide monitoring and early warnings to ensure safety.</p>
            <button class="live-data-btn" onclick="showSection('datas')">VIEW LIVE DATA</button>
        </div>
    </div>

    <div id="datas" class="section">
        <div class="content-box">
            <h2>Live Data</h2>
            <p>Below is the real-time rainfall monitoring data:</p>

            <table class="data-table">
                <thead>
                    <tr>
                        <th>Rainfall (mm)</th>
                        <th>Slope Status</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>50</td>
                        <td>Stable</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>

    <script>
        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => {
                section.classList.remove('visible');
            });

            setTimeout(() => {
                document.getElementById(sectionId).classList.add('visible');
            }, 100);
        }
    </script>

</body>
</html>
