# Landslide-monitoring
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring System</title>
    <style>
        body {
            font-family: Ghetto, gismo;
            background: linear-gradient(to bottom, #d1a3ff, #5e0acc);
            text-align: center;
            color: black;
            margin: 0;
            padding: 0;
        }
        header {
            display: flex;
            justify-content: space-between;
            padding: 20px;
        }
        nav a {
            margin: 0 15px;
            color: black;
            text-decoration: none;
            font-weight: bold;
            cursor: pointer;
        }
        .dropdown-content {
            display: none;
            position: absolute;
            background-color: white;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
            padding: 10px;
        }
        .dropdown:hover .dropdown-content {
            display: block;
        }
        .main-heading {
            font-size: 32px;
            font-weight: bold;
            margin-top: 50px;
        }
        .button {
            background: purple;
            padding: 10px 20px;
            color: white;
            border-radius: 20px;
            display: inline-block;
            margin-top: 20px;
        }
        .container {
            display: flex;
            justify-content: space-around;
            margin-top: 50px;
        }
        .box {
            width: 30%;
            height: 200px;
            background: rgba(255, 255, 255, 0.3);
            border-radius: 10px;
            padding: 20px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <header>
        <div style="font-size: 24px; font-weight: bold; color: red;">MAVERICKS</div>
        <nav>
            <div class="dropdown">
                <a>Dashboard ▼</a>
                <div class="dropdown-content">
                    <a href="#rainfall">Rainfall Data</a><br>
                    <a href="#locations">Locations</a>
                </div>
            </div>
            <a href="#records">Records</a>
            <a href="#about">About us</a>
        </nav>
    </header>
    <div class="main-heading">Integrated Landslide Monitoring Early Warning System</div>
    <a href="#summary" class="button">Know more →</a>
    <div class="container">
        <div class="box">Work Progress</div>
        <div class="box">Collaborations</div>
        <div class="box">Locations</div>
    </div>
</body>
</html>

