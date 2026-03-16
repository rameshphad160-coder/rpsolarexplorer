<!DOCTYPE html>
<html lang="mr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Solar System Interactive Lab</title>
    <style>
        :root {
            --bg-color: #05070a;
            --glass-bg: rgba(255, 255, 255, 0.1);
            --text-color: #ffffff;
        }

        body {
            margin: 0;
            padding: 0;
            background: var(--bg-color);
            color: var(--text-color);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        /* Header & Search Bar Section */
        header {
            width: 100%;
            text-align: center;
            padding: 20px 0;
            z-index: 100;
        }

        .search-container {
            margin-top: 15px;
        }

        input {
            padding: 12px 25px;
            width: 300px;
            max-width: 80%;
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.3);
            background: var(--glass-bg);
            color: white;
            backdrop-filter: blur(10px);
            outline: none;
            transition: 0.3s;
        }

        input:focus {
            border-color: #00d2ff;
            box-shadow: 0 0 15px rgba(0, 210, 255, 0.5);
        }

        /* Solar System Map */
        #solar-system {
            position: relative;
            width: 100vw;
            height: 80vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .sun {
            width: 80px;
            height: 80px;
            background: radial-gradient(circle, #ffcc33 20%, #ff8800 100%);
            border-radius: 50%;
            box-shadow: 0 0 50px #ff8800;
            cursor: pointer;
            z-index: 10;
        }

        .planet {
            position: absolute;
            border-radius: 50%;
            cursor: pointer;
            transition: transform 0.3s ease;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .planet:hover {
            transform: scale(1.2);
            box-shadow: 0 0 20px white;
        }

        /* Planets Position & Size (Professional Adjustments) */
        .mercury { width: 15px; height: 15px; background: #a5a5a5; left: calc(50% + 70px); }
        .venus { width: 25px; height: 25px; background: #e3bb76; left: calc(50% + 110px); }
        .earth { width: 28px; height: 28px; background: #2271b3; left: calc(50% + 160px); }
        .mars { width: 20px; height: 20px; background: #e27b58; left: calc(50% + 210px); }
        .jupiter { width: 55px; height: 55px; background: #d39c7e; left: calc(50% + 270px); }

        .label {
            position: absolute;
            top: 100%;
            font-size: 12px;
            margin-top: 5px;
            white-space: nowrap;
        }

        /* Info Box */
        #info-box {
            position: fixed;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            width: 350px;
            background: var(--glass-bg);
            backdrop-filter: blur(20px);
            padding: 20px;
            border-radius: 15px;
            border: 1px solid rgba(255,255,255,0.2);
            display: none; /* सुरुवातीला लपवलेले */
            text-align: center;
            animation: slideUp 0.4s ease;
        }

        @keyframes slideUp {
            from { transform: translate(-50%, 50px); opacity: 0; }
            to { transform: translate(-50%, 0); opacity: 1; }
        }

        /* Mobile Responsive */
        @media (max-width: 600px) {
            .sun { width: 60px; height: 60px; }
            #info-box { width: 85%; }
            /* मोबाईलवर ग्रह धुसर दिसू नयेत म्हणून Crisp Rendering */
            .planet { image-rendering: -webkit-optimize-contrast; }
        }
    </style>
</head>
<body>

    <header>
        <h1>Solar System Interactive Lab</h1>
        <div class="search-container">
            <input type="text" placeholder="ग्रह शोधा / Search planet..." id="searchInput">
        </div>
    </header>

    <div id="solar-system">
        <div class="sun" onclick="showInfo('सूर्य', 'सूर्य हा आपल्या सौरमालेचा केंद्रबिंदू आहे. हा एक तारा आहे जो ऊर्जा आणि प्रकाश देतो.')"></div>

        <div class="planet mercury" onclick="showInfo('बुध', 'सौरमालेतील हा सर्वात लहान आणि सूर्याच्या सर्वात जवळचा ग्रह आहे.')">
            <span class="label">बुध</span>
        </div>
        <div class="planet venus" onclick="showInfo('शुक्र', 'हा सौरमालेतील सर्वात उष्ण ग्रह आहे.')">
            <span class="label">शुक्र</span>
        </div>
        <div class="planet earth" onclick="showInfo('पृथ्वी', 'आपले घर! हा एकमेव ग्रह आहे जिथे जीवसृष्टी अस्तित्वात आहे.')">
            <span class="label">पृथ्वी</span>
        </div>
        <div class="planet mars" onclick="showInfo('मंगळ', 'याला लाल ग्रह म्हटले जाते कारण याच्या पृष्ठभागावर लोह ऑक्साईड आहे.')">
            <span class="label">मंगळ</span>
        </div>
        <div class="planet jupiter" onclick="showInfo('गुरु', 'हा सौरमालेतील सर्वात मोठा ग्रह आहे.')">
            <span class="label">गुरु</span>
        </div>
    </div>

    <div id="info-box">
        <h3 id="planet-name" style="color: #00d2ff;"></h3>
        <p id="planet-desc"></p>
        <button onclick="document.getElementById('info-box').style.display='none'" style="background:none; border:1px solid white; color:white; border-radius:5px; cursor:pointer;">बंद करा</button>
    </div>

    <script>
        function showInfo(name, desc) {
            const box = document.getElementById('info-box');
            document.getElementById('planet-name').innerText = name;
            document.getElementById('planet-desc').innerText = desc;
            box.style.display = 'block';
        }

        // सोपी सर्च सिस्टिम
        document.getElementById('searchInput').addEventListener('keyup', function(e) {
            let value = e.target.value.toLowerCase();
            if(value === "earth" || value === "पृथ्वी") {
                showInfo('पृथ्वी', 'आपले घर! हा एकमेव ग्रह आहे जिथे जीवसृष्टी अस्तित्वात आहे.');
            }
            // अश्या प्रकारे इतर ग्रहांचे सर्च लॉजिक वाढवता येईल
        });
    </script>
</body>
</html>
