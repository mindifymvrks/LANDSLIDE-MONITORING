<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Landslide Monitoring System</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;700&family=Merriweather:wght@300;400;700&family=Playfair+Display:wght@400;700&display=swap');
        
        body {
            font-family: 'Poppins', sans-serif;
            background: black;
            color: white;
            margin: 0;
            padding: 0;
            overflow-x: hidden;
        }
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px;
        }
        nav {
            display: flex;
            gap: 20px;
        }
        nav a {
            color: white;
            text-decoration: none;
            font-weight: bold;
            cursor: pointer;
            position: relative;
        }
        .main-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 50px;
            margin-top: 50px;
        }
        .left-text, .right-text {
            width: 45%;
            text-align: justify;
        }
        .section {
            padding: 50px;
            display: none;
            opacity: 0;
            transform: translateY(50px);
            transition: opacity 1s ease-out, transform 1s ease-out;
        }
        .visible {
            display: block;
            opacity: 1;
            transform: translateY(0);
        }
    </style>
</head>
<body>
    <header>
        <div style="font-size: 24px; font-weight: bold; color: white; font-family: 'Playfair Display', serif;">MAVERICKS</div>
        <nav>
            <a href="#" onclick="showHome()">Home</a>
            <a href="#" onclick="showSection('about')">About</a>
            <a href="#" onclick="showSection('datas')">Datas</a>
            <a href="#" onclick="showSection('gallery')">Gallery</a>
            <a href="#" onclick="showSection('contact')">Contact Details</a>
        </nav>
    </header>
    <div id="home" class="main-content visible">
        <div class="left-text">
            <h1 style="font-family: 'Playfair Display', serif; color: white;">Integrated Landslide Monitoring</h1>
        </div>
        <div class="right-text">
            <h1 style="font-family: 'Playfair Display', serif; color: white;">and Early Warning System</h1>
        </div>
    </div>
    
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
