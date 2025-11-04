<!DOCTYPE html>
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
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
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
            background: rgba(255, 255, 255, 0.2);
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
            from {
                opacity: 0;
                transform: translateY(30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .section-title {
            font-size: 2.5em;
            color: #667eea;
            margin-bottom: 30px;
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .icon-circle {
            width: 60px;
            height: 60px;
            background: linear-gradient(135deg, #667eea, #764ba2);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8em;
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        .highlight-box {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 40px;
            border-radius: 20px;
            color: white;
            margin: 30px 0;
            box-shadow: 0 15px 40px rgba(102, 126, 234, 0.4);
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
            background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);
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
            background: linear-gradient(135deg, #667eea, #764ba2);
            color: white;
        }

        .data-table th, .data-table td {
            padding: 20px;
            text-align: left;
        }

        .data-table tbody tr {
            transition: all 0.3s ease;
        }

        .data-table tbody tr:nth-child(even) {
            background: #f8f9fa;
        }

        .data-table tbody tr:hover {
            background: #e3e7ff;
            transform: scale(1.02);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .code-block {
            background: #1e1e1e;
            color: #d4d4d4;
            padding: 25px;
            border-radius: 15px;
            margin: 20px 0;
            overflow-x: auto;
            position: relative;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }

        .code-block::before {
            content: '{ }';
            position: absolute;
            top: 10px;
            right: 20px;
            font-size: 1.5em;
            color: #667eea;
            opacity: 0.3;
        }

        .code-block code {
            font-family: 'Courier New', monospace;
            font-size: 1em;
        }

        .keyword { color: #569cd6; }
        .string { color: #ce9178; }
        .function { color: #dcdcaa; }
        .comment { color: #6a9955; }

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

        .info-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, transparent 0%, rgba(102, 126, 234, 0.05) 100%);
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .info-card:hover::before {
            opacity: 1;
        }

        .info-card:hover {
            transform: translateY(-10px);
            box-shadow: 0 20px 50px rgba(0,0,0,0.2);
        }

        .info-card.active { border-left-color: #ffc107; }
        .info-card.passive { border-left-color: #17a2b8; }

        .info-card h3 {
            font-size: 1.8em;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .workflow {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
            margin: 50px 0;
            padding: 50px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(102, 126, 234, 0.4);
        }

        .workflow-step {
            background: rgba(255, 255, 255, 0.95);
            padding: 30px 50px;
            border-radius: 15px;
            width: 100%;
            max-width: 500px;
            text-align: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            transition: all 0.3s ease;
            position: relative;
        }

        .workflow-step:hover {
            transform: scale(1.05);
            background: white;
        }

        .workflow-step h3 {
            color: #667eea;
            font-size: 1.8em;
            margin-bottom: 10px;
        }

        .workflow-step p {
            color: #666;
            font-size: 1.1em;
        }

        .workflow-arrow {
            font-size: 3em;
            color: white;
            text-shadow: 0 2px 10px rgba(0,0,0,0.3);
            animation: bounce 2s infinite;
        }

        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

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

        .comparison-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 50px rgba(0,0,0,0.2);
        }

        .comparison-card.batch {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
        }

        .comparison-card.streaming {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
        }

        .comparison-card h3 {
            font-size: 2em;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .comparison-card ul {
            list-style: none;
        }

        .comparison-card li {
            padding: 12px 0;
            font-size: 1.1em;
            border-bottom: 1px solid rgba(255,255,255,0.2);
        }

        .comparison-card li:last-child {
            border-bottom: none;
        }

        footer {
            text-align: center;
            color: white;
            padding: 50px 20px;
            margin-top: 50px;
        }

        footer p {
            margin: 10px 0;
            font-size: 1.1em;
        }

        .badge {
            display: inline-block;
            background: rgba(255,255,255,0.2);
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 0.9em;
            margin: 5px;
        }

        @media (max-width: 768px) {
            header h1 { font-size: 2em; }
            .content-section { padding: 30px 20px; }
            .comparison-table { grid-template-columns: 1fr; }
            .section-title { font-size: 1.8em; }
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

    <div class="container">
        <div class="content-section">
            <h2 class="section-title">
                <div class="icon-circle">🎯</div>
                Objetivo
            </h2>
            <p style="font-size: 1.2em; line-height: 1.8;">
                Aplicar <strong>analítica avanzada</strong> para procesar un flujo de datos simulado en un contexto empresarial usando <strong>Python</strong> y <strong>Spark</strong>, demostrando el poder del procesamiento distribuido y el análisis en tiempo real.
            </p>
        </div>

        <div class="content-section">
            <h2 class="section-title">
                <div class="icon-circle">🛒</div>
                Escenario: Tienda Online
            </h2>
            
            <div class="highlight-box">
                <h3>Imaginemos una tienda online que desea analizar en tiempo real los clics de los usuarios</h3>
                <ul>
                    <li>Detectar patrones de navegación</li>
                    <li>Medir el nivel de interés de los clientes</li>
                    <li>Optimizar campañas publicitarias</li>
                    <li>Mejorar recomendaciones de productos</li>
                </ul>
            </div>

            <h3 style="font-size: 1.8em; margin: 40px 0 20px 0; color: #667eea;">📋 Estructura del Dataset</h3>
            
            <table class="data-table">
                <thead>
                    <tr>
                        <th>Campo</th>
                        <th>Descripción</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td><strong>Timestamp</strong></td>
                        <td>Fecha y hora del clic</td>
                    </tr>
                    <tr>
                        <td><strong>User_ID</strong></td>
                        <td>Identificador único del usuario</td>
                    </tr>
                    <tr>
                        <td><strong>Clicks</strong></td>
                        <td>Número de clics en esa franja temporal</td>
                    </tr>
                </tbody>
            </table>

            <p style="text-align: center; font-size: 1.1em; color: #667eea; font-weight: bold; margin-top: 20px;">
                📊 Total de registros: 1000 clics simulados
            </p>
        </div>

        <div class="content-section">
            <h2 class="section-title">
                <div class="icon-circle">⚙️</div>
                Implementación con Spark
            </h2>

            <h3 style="font-size: 1.8em; margin: 30px 0 20px 0; color: #667eea;">1️⃣ Configuración del Entorno</h3>
            <p style="font-size: 1.1em; margin-bottom: 20px;">
                Se instaló <strong>Apache Spark 3.5.0</strong> en Google Colab y se configuró el entorno de ejecución.
            </p>

            <div class="code-block">
                <code>
<span class="keyword">from</span> pyspark.sql <span class="keyword">import</span> SparkSession

spark = SparkSession.builder.<span class="function">appName</span>(<span class="string">"ClickstreamAnalysis"</span>).<span class="function">getOrCreate</span>()
<span class="function">print</span>(<span class="string">"Versión de Spark:"</span>, spark.version)
                </code>
            </div>

            <h3 style="font-size: 1.8em; margin: 40px 0 20px 0; color: #667eea;">2️⃣ Carga y Exploración de Datos</h3>

            <div class="code-block">
                <code>
df = spark.read.<span class="function">csv</span>(<span class="string">"clickstream_data.csv"</span>, header=<span class="keyword">True</span>, inferSchema=<span class="keyword">True</span>)
df.<span class="function">show</span>(<span class="string">5</span>)
                </code>
            </div>

            <div style="background: linear-gradient(135deg, #e8f5e9, #c8e6c9); padding: 25px; border-radius: 15px; margin: 25px 0; border-left: 5px solid #4caf50;">
                <strong style="font-size: 1.2em;">✅ Ventaja:</strong> Spark permite procesar datasets que no cabrían en memoria usando un solo equipo.
            </div>

            <h3 style="font-size: 1.8em; margin: 40px 0 20px 0; color: #667eea;">3️⃣ Procesamiento del Flujo</h3>

            <div class="code-block">
                <code>
<span class="keyword">from</span> pyspark.sql.functions <span class="keyword">import</span> window, col, sum

clicks_per_user = df.<span class="function">groupBy</span>(<span class="string">"User_ID"</span>).<span class="function">agg</span>(<span class="function">sum</span>(<span class="string">"Clicks"</span>).<span class="function">alias</span>(<span class="string">"Total_Clicks"</span>))
clicks_per_user.<span class="function">show</span>()
                </code>
            </div>
        </div>

        <div class="content-section">
            <h2 class="section-title">
                <div class="icon-circle">💡</div>
                Interpretación Analítica
            </h2>

            <div class="info-cards">
                <div class="info-card active">
                    <h3>👥 Usuarios Activos</h3>
                    <p>Algunos usuarios presentan <strong>clics significativamente mayores</strong>, indicando:</p>
                    <ul style="margin-top: 15px; line-height: 1.8;">
                        <li>• Mayor interacción con productos</li>
                        <li>• Usuarios recurrentes o fieles</li>
                        <li>• Oportunidad para marketing personalizado</li>
                    </ul>
                </div>

                <div class="info-card passive">
                    <h3>🎯 Usuarios Pasivos</h3>
                    <p>Los usuarios con <strong>poca actividad</strong> representan:</p>
                    <ul style="margin-top: 15px; line-height: 1.8;">
                        <li>• Visitantes ocasionales</li>
                        <li>• Clientes potenciales</li>
                        <li>• Necesidad de incentivos para conversión</li>
                    </ul>
                </div>
            </div>
        </div>

        <div class="content-section">
            <h2 class="section-title">
                <div class="icon-circle">🏗️</div>
                Arquitectura del Proyecto
            </h2>

            <div class="workflow">
                <div class="workflow-step">
                    <h3>Google Colab + PySpark</h3>
                    <p>Procesamiento y Visualización</p>
                </div>
                <div class="workflow-arrow">↓</div>
                <div class="workflow-step">
                    <h3>Jekyll</h3>
                    <p>Generación del Blog Estático</p>
                </div>
                <div class="workflow-arrow">↓</div>
                <div class="workflow-step">
                    <h3>GitHub Pages</h3>
                    <p>Alojamiento en la Nube (Gratuito)</p>
                </div>
            </div>
        </div>

        <div class="content-section">
            <h2 class="section-title">
                <div class="icon-circle">⚡</div>
                Streaming vs Procesamiento por Lotes
            </h2>

            <div class="comparison-table">
                <div class="comparison-card batch">
                    <h3>📦 Procesamiento por Lotes</h3>
                    <ul>
                        <li>Analiza datos acumulados y almacenados</li>
                        <li>Mayor latencia</li>
                        <li>Procesamiento periódico</li>
                        <li><strong>Ejemplo:</strong> Análisis de ventas diarias</li>
                    </ul>
                </div>

                <div class="comparison-card streaming">
                    <h3>⚡ Procesamiento en Streaming</h3>
                    <ul>
                        <li>Analiza datos en tiempo real</li>
                        <li>Baja latencia y decisiones inmediatas</li>
                        <li>Procesamiento continuo</li>
                        <li><strong>Ejemplo:</strong> Monitoreo de clics por segundo</li>
                    </ul>
                </div>
            </div>

            <div style="background: linear-gradient(135deg, #d4edda, #b8dabd); padding: 30px; border-radius: 20px; border-left: 5px solid #28a745; margin: 30px 0; box-shadow: 0 10px 30px rgba(0,0,0,0.1);">
                <strong style="font-size: 1.3em;">✨ Insight 2025:</strong>
                <p style="font-size: 1.1em; margin-top: 10px; line-height: 1.8;">
                    El streaming permite detectar comportamientos instantáneos, esencial para empresas que buscan reaccionar de forma ágil ante sus clientes.
                </p>
            </div>
        </div>

        <div class="content-section">
            <div class="highlight-box">
                <h3 style="text-align: center; font-size: 2.5em;">🎓 Conclusión</h3>
                <p style="font-size: 1.2em; text-align: center; margin-top: 20px; line-height: 1.8;">
                    Este ejercicio permitió aplicar <strong>analítica avanzada con Spark</strong>, demostrando cómo el procesamiento en streaming puede aportar <strong>valor inmediato</strong> a un negocio digital.
                </p>
                <p style="font-size: 1.1em; text-align: center; margin-top: 20px; line-height: 1.8;">
                    El uso de herramientas como <strong>Spark</strong>, <strong>Python</strong> y <strong>Jekyll</strong> facilita la integración de la analítica con la publicación de resultados en la web.
                </p>
            </div>
        </div>
    </div>

    <footer>
        <p style="font-size: 1.3em; margin-bottom: 15px;">© 2025 - Blog de <strong>Pablo Roncancio</strong></p>
        <p>Construido con ❤️ usando Spark, Python y Jekyll</p>
    </footer>

    <script>
        // Create animated particles
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
