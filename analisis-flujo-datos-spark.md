<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Análisis de Flujo de Datos con Spark</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #0f9b0f 0%, #00c853 100%);
            color: #333;
            line-height: 1.6;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        header {
            text-align: center;
            color: white;
            padding: 80px 20px;
            position: relative;
            overflow: hidden;
        }

        .particles {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            z-index: 0;
        }

        .particle {
            position: absolute;
            background: rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            animation: float 6s infinite ease-in-out;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0px) translateX(0px); }
            50% { transform: translateY(-30px) translateX(20px); }
        }

        header h1 {
            font-size: 3.5em;
            margin-bottom: 15px;
            position: relative;
            z-index: 1;
            text-shadow: 2px 2px 10px rgba(0,0,0,0.3);
        }

        header h2 {
            font-size: 1.5em;
            font-weight: 300;
            position: relative;
            z-index: 1;
            opacity: 0.95;
        }

        .content-section {
            background: white;
            margin: 30px 0;
            padding: 50px;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            animation: fadeInUp 0.8s ease;
        }

        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .section-title {
            font-size: 2.5em;
            color: #0f9b0f;
            margin-bottom: 30px;
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .icon-circle {
            width: 60px;
            height: 60px;
            background: linear-gradient(135deg, #0f9b0f, #00c853);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8em;
            box-shadow: 0 5px 15px rgba(15, 155, 15, 0.4);
        }

        .highlight-box {
            background: linear-gradient(135deg, #0f9b0f 0%, #00c853 100%);
            padding: 40px;
            border-radius: 20px;
            color: white;
            margin: 30px 0;
            box-shadow: 0 15px 40px rgba(15, 155, 15, 0.4);
            position: relative;
            overflow: hidden;
        }

        .highlight-box::before {
            content: '';
            position: absolute;
            top: -50%;
            right: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.15) 0%, transparent 70%);
            animation: pulse 4s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 0.5; }
            50% { transform: scale(1.2); opacity: 0.8; }
        }

        .highlight-box h3 {
            font-size: 2em;
            margin-bottom: 20px;
            position: relative;
        }

        .highlight-box ul {
            list-style: none;
            position: relative;
        }

        .highlight-box li {
            padding: 10px 0;
            font-size: 1.1em;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .highlight-box li::before {
            content: '✓';
            background: rgba(255,255,255,0.3);
            width: 30px;
            height: 30px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-shrink: 0;
        }

        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin: 30px 0;
            overflow: hidden;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        .data-table thead {
            background: linear-gradient(135deg, #0f9b0f, #00c853);
            color: white;
        }

        .data-table th, .data-table td {
            padding: 20px;
            text-align: left;
        }

        .data-table tbody tr:nth-child(even) { background: #f1f8f4; }
        .data-table tbody tr:hover {
            background: #c8e6c9;
            transform: scale(1.02);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .code-block {
            background: #1a1a1a;
            color: #d4d4d4;
            padding: 25px;
            border-radius: 15px;
            margin: 20px 0;
            overflow-x: auto;
            position: relative;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            border-left: 4px solid #00c853;
        }

        .code-block::before {
            content: '{ }';
            position: absolute;
            top: 10px;
            right: 20px;
            font-size: 1.5em;
            color: #00c853;
            opacity: 0.3;
        }

        .code-block code {
            font-family: 'Courier New', monospace;
            font-size: 1em;
        }

        .keyword { color: #4fc3f7; }
        .string { color: #a5d6a7; }
        .function { color: #81c784; }
        .comment { color: #66bb6a; }

        .info-cards {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 30px;
            margin: 40px 0;
        }

        .info-card {
            background: white;
            padding: 35px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            border-left: 5px solid;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .info-card.active { border-left-color: #00c853; background: linear-gradient(135deg, #f1f8f4 0%, white 100%); }
        .info-card.passive { border-left-color: #4fc3f7; background: linear-gradient(135deg, #e3f2fd 0%, white 100%); }

        .info-card h3 { font-size: 1.8em; margin-bottom: 15px; display: flex; align-items: center; gap: 10px; color: #0f9b0f; }

        .workflow {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
            margin: 50px 0;
            padding: 50px;
            background: linear-gradient(135deg, #0f9b0f 0%, #00c853 100%);
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(15, 155, 15, 0.4);
        }

        .workflow-step {
            background: rgba(255, 255, 255, 0.98);
            padding: 30px 50px;
            border-radius: 15px;
            width: 100%;
            max-width: 500px;
            text-align: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            transition: all 0.3s ease;
        }

        .workflow-step:hover { transform: scale(1.05); background: white; }

        .comparison-table {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            margin: 40px 0;
        }

        .comparison-card {
            padding: 35px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            transition: all 0.3s ease;
        }

        .comparison-card.batch {
            background: linear-gradient(135deg, #81c784 0%, #66bb6a 100%);
            color: white;
        }

        .comparison-card.streaming {
            background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
            color: white;
        }

        footer {
            text-align: center;
            color: white;
            padding: 50px 20px;
            margin-top: 50px;
        }

        .success-box {
            background: linear-gradient(135deg, #c8e6c9, #a5d6a7);
            padding: 30px;
            border-radius: 20px;
            border-left: 5px solid #2e7d32;
            margin: 30px 0;
            color: #1b5e20;
        }

        /* === NUEVO ESTILO PARA CONCLUSIÓN === */
        .conclusion-box {
            background: linear-gradient(135deg, #1b5e20, #2e7d32);
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4);
            border: none;
            color: #fff;
            border-radius: 20px;
            padding: 50px;
            position: relative;
            overflow: hidden;
        }

        .conclusion-box::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.15) 0%, transparent 70%);
            animation: pulse 4s infinite;
        }

        @media (max-width: 768px) {
            header h1 { font-size: 2em; }
            .content-section { padding: 30px 20px; }
            .comparison-table { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>
    <header>
        <div class="particles" id="particles"></div>
        <h1>📊 Análisis de Flujo de Datos</h1>
        <h2>Hecho por: Pablo Roncancio</h2>
        <div style="margin-top: 20px;">
            <span class="badge">Apache Spark</span>
            <span class="badge">Python</span>
            <span class="badge">Jekyll</span>
        </div>
    </header>

    <!-- CONTENIDO OMITIDO PARA BREVEDAD -->

        <div class="content-section">
            <div class="conclusion-box">
                <h3 style="text-align: center; font-size: 2.5em; color: #ffffff;">🎓 Conclusión</h3>
                <p style="font-size: 1.2em; text-align: center; margin-top: 20px; line-height: 1.8; color: #e8f5e9;">
                    Este ejercicio permitió aplicar <strong>analítica avanzada con Spark</strong>, demostrando cómo el procesamiento en streaming puede aportar <strong>valor inmediato</strong> a un negocio digital.
                </p>
                <p style="font-size: 1.1em; text-align: center; margin-top: 20px; line-height: 1.8; color: #e8f5e9;">
                    El uso de herramientas como <strong>Spark</strong>, <strong>Python</strong> y <strong>Jekyll</strong> facilita la integración de la analítica con la publicación de resultados en la web.
                </p>
            </div>
        </div>

    <footer>
        <p style="font-size: 1.3em;">© 2025 - Blog de <strong>Pablo Roncancio</strong></p>
        <p>Construido con ❤️ usando Spark, Python y Jekyll</p>
    </footer>

    <script>
        const particlesContainer = document.getElementById('particles');
        for (let i = 0; i < 20; i++) {
            const particle = document.createElement('div');
            particle.className = 'particle';
            particle.style.width = Math.random() * 10 + 5 + 'px';
            particle.style.height = particle.style.width;
            particle.style.left = Math.random() * 100 + '%';
            particle.style.top = Math.random() * 100 + '%';
            particle.style.animationDelay = Math.random() * 6 + 's';
            particle.style.animationDuration = Math.random() * 4 + 4 + 's';
            particlesContainer.appendChild(particle);
        }
    </script>
</body>
</html>
