<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring</title>
    <style>
        body {
            background-color: black;
            color: white;
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px;
            background-color: black;
            border-bottom: 1px solid white;
        }
        .logo {
            font-size: 24px;
            font-weight: bold;
            text-transform: uppercase;
            margin-left: 20px;
        }
        .nav {
            margin-right: 20px;
        }
        .nav a {
            color: white;
            text-decoration: none;
            margin: 0 10px;
            font-size: 18px;
        }
        .main-title {
            text-align: center;
            font-size: 28px;
            font-weight: bold;
            margin-top: 50px;
        }
    </style>
</head>
<body>
    <header class="header">
        <div class="logo">MAVERICKS</div>
        <nav class="nav">
            <a href="#home">Home</a>
            <a href="#about">About</a>
            <a href="#datas">Datas</a>
            <a href="#gallery">Gallery</a>
            <a href="#contact">Contact Details</a>
        </nav>
    </header>
    <div class="main-title">Integrated Landslide Monitoring and Early Warning System</div>
    
    <script>
        function showSection(sectionId) {
            document.querySelectorAll('.section, .main-content').forEach(section => {
                section.classList.remove('visible');
            });
            document.getElementById(sectionId).classList.add('visible');
        }
        
        function showHome() {
            document.querySelectorAll('.section, .main-content').forEach(section => {
                section.classList.remove('visible');
            });
            document.getElementById('home').classList.add('visible');
        }
    </script>
</body>
</html>
