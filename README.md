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
            cursor: pointer;
        }

        .nav a:hover {
            color: #ffcc00;
        }

        /* Main Title */
        .main-title {
            font-size: 32px;
            font-weight: bold;
            margin-top: 100px;
        }

        /* View Live Data Button */
        .live-data-btn {
            display: inline-block;
            margin-top: 20px;
            padding: 15px 30px;
            font-size: 20px;
            font-weight: bold;
            color: #0d0d0d;
            background: #ffcc00;
            border: none;
            border-radius: 30px;
            cursor: pointer;
            transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
            box-shadow: 0 4px 15px rgba(255, 204, 0, 0.6);
        }

        .live-data-btn:hover {
            transform: scale(1.1);
            box-shadow: 0 6px 20px rgba(255, 204, 0, 0.9);
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

        /* Glassmorphism Effect for Sections */
        .content-box {
            background: rgba(255, 255, 255, 0.1);
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 4px 10px rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
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

    <!-- Page Title (Only for Home Page) -->
    <div class="main-title">Integrated Landslide Monitoring and Early Warning System</div>
    <button class="live-data-btn" onclick="showSection('datas')">VIEW LIVE DATA</button>

    <!-- Sections -->
    <div id="home" class="section visible">
        <div class="content-box">
            <h2>Welcome to the Landslide Monitoring System</h2>
            <p>This system provides real-time landslide monitoring and early warnings to ensure safety.</p>
        </div>
    </div>

    <div id="about" class="section">
        <div class="content-box">
            <h2>About the Integrated Landslide Monitoring System</h2>
            <p>Our project provides real-time monitoring of landslide-prone areas.</p>
        </div>
    </div>

    <div id="datas" class="section">
        <div class="content-box">
            <h2>Live Data</h2>
            <p>Below is the real-time rainfall monitoring data:</p>
        </div>
    </div>

    <div id="gallery" class="section">
        <div class="content-box">
            <h2>Gallery</h2>
            <p>Images and diagrams related to landslide monitoring.</p>
        </div>
    </div>

    <div id="contact" class="section">
        <div class="content-box">
            <h2>Contact Details</h2>
            <p>Email: contact@example.com</p>
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

            // Smooth scroll to the section
            document.getElementById(sectionId).scrollIntoView({ behavior: 'smooth' });
        }
    </script>
</body>
</html>
