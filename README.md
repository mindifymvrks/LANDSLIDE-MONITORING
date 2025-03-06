<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Integrated Landslide Monitoring And Early Warning System</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;700&family=Merriweather:wght@300;400;700&family=Playfair+Display:wght@400;700&display=swap');
        
        body {
            font-family: 'Poppins', sans-serif;
            background: #FFC72C;
            text-align: center;
            color: black;
            margin: 0;
            padding: 0;
            overflow-x: hidden;
        }
        header {
            display: flex;
            justify-content: space-between;
            padding: 20px;
        }
        nav {
            display: flex;
            gap: 20px;
        }
        nav a {
            color: black;
            text-decoration: none;
            font-weight: bold;
            cursor: pointer;
        }
        .main-content {
            transition: opacity 0.5s ease-out;
        }
        .hidden {
            display: none;
        }
        .section {
            padding: 50px;
            opacity: 0;
            transform: translateY(50px);
            transition: opacity 1s ease-out, transform 1s ease-out;
        }
        .visible {
            opacity: 1;
            transform: translateY(0);
        }
        .team-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 20px;
            margin-top: 30px;
        }
        .team-member {
            text-align: center;
            width: 250px;
            background: rgba(255, 255, 255, 0.2);
            padding: 15px;
            border-radius: 10px;
        }
        .team-member img {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            object-fit: cover;
        }
        .button {
            background: purple;
            padding: 10px 20px;
            color: white;
            border-radius: 20px;
            display: inline-block;
            margin-top: 20px;
            cursor: pointer;
            font-family: 'Merriweather', serif;
        }
    </style>
</head>
<body>
    <header>
        <div style="font-size: 24px; font-weight: bold; color: white; font-family: 'Playfair Display', serif;">MAVERICKS</div>
        <nav>
            <a href="#dashboard">Dashboard</a>
            <a href="#records">Records</a>
            <a href="#contact">Contact Details</a>
        </nav>
    </header>
    <div id="main-content" class="main-content">
        <div class="main-heading" style="font-size: 36px; font-weight: bold; font-family: 'Playfair Display', serif; margin-top: 50px; color: black;">Landslide Monitoring</div>
        <div class="button" onclick="showDetails()">Know more →</div>
    </div>
    
    <div id="details" class="section hidden">
        <h2 style="font-family: 'Playfair Display', serif;">Project Overview</h2>
        <p style="font-family: 'Merriweather', serif;">India is experiencing an unprecedented increase in landslide occurrences, posing significant risks to infrastructure and communities... (Full description here)</p>
        
        <h2 style="font-family: 'Playfair Display', serif;">Meet the Team</h2>
        <div class="team-container">
            <div class="team-member">
                <h3>Guide</h3>
                <img src="guide.jpg" alt="Guide">
                <p>Er. Nirmal John Joy</p>
                <p>Assistant Professor, Department of Civil Engineering</p>
                <p>Saintgits College of Engineering</p>
            </div>
            <div class="team-member">
                <h3>Co-Guide</h3>
                <img src="co-guide.jpg" alt="Co-Guide">
                <p>Er. Joe G Philip</p>
                <p>Assistant Professor, Department of Civil Engineering</p>
                <p>Saintgits College of Engineering</p>
            </div>
        </div>
        <div class="team-container">
            <div class="team-member">
                <h3>Core Member</h3>
                <img src="jithin.jpg" alt="Jithin Joseph Loveson">
                <p>Jithin Joseph Loveson</p>
                <p>Saintgits College of Engineering</p>
            </div>
            <div class="team-member">
                <h3>Core Member</h3>
                <img src="mervin.jpg" alt="Mervin Mathew Shibu">
                <p>Mervin Mathew Shibu</p>
                <p>Saintgits College of Engineering</p>
            </div>
            <div class="team-member">
                <h3>Core Member</h3>
                <img src="noble.jpg" alt="Noble Sajan">
                <p>Noble Sajan</p>
                <p>Saintgits College of Engineering</p>
            </div>
        </div>
        <div class="team-container">
            <div class="team-member">
                <h3>Joint Co-Partner</h3>
                <img src="manu.jpg" alt="Manu M Kumar">
                <p>Manu M Kumar</p>
                <p>Saintgits College of Engineering</p>
            </div>
        </div>
        <div class="button" onclick="goBack()">Click Back</div>
    </div>
    
    <script>
        function showDetails() {
            document.getElementById('main-content').style.opacity = '0';
            setTimeout(() => {
                document.getElementById('main-content').classList.add('hidden');
                document.getElementById('details').classList.remove('hidden');
                document.getElementById('details').classList.add('visible');
            }, 500);
        }
        function goBack() {
            document.getElementById('details').classList.remove('visible');
            setTimeout(() => {
                document.getElementById('details').classList.add('hidden');
                document.getElementById('main-content').classList.remove('hidden');
                document.getElementById('main-content').style.opacity = '1';
            }, 500);
        }
    </script>
</body>
</html>
