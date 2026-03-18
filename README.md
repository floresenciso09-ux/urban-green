# urban-green
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hermosillo Verde - Dashboard Ambiental Inteligente</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        :root {
            --primary: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            --success: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            --warning: linear-gradient(135deg, #fa709a 0%, #fee140 100%);
            --danger: linear-gradient(135deg, #ff6b6b 0%, #ee5a24 100%);
            --glass: rgba(255,255,255,0.1);
            --glass-border: rgba(255,255,255,0.2);
            --dark-bg: #0f0f23;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: 'Inter', sans-serif;
            background: var(--dark-bg);
            color: white;
            height: 100vh;
            overflow: hidden;
        }

        .app {
            display: flex;
            height: 100vh;
        }

        /* HEADER */
        .header {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            background: rgba(15,15,35,0.95);
            backdrop-filter: blur(25px);
            z-index: 1000;
            padding: 15px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid var(--glass-border);
        }

        .logo {
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 24px;
            font-weight: 700;
            background: var(--primary);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .header-right {
            display: flex;
            gap: 20px;
            align-items: center;
        }

        .toggle-btn {
            background: var(--glass);
            border: 1px solid var(--glass-border);
            padding: 10px 18px;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 500;
        }

        .toggle-btn.active { background: var(--success); }

        #datetime { font-size: 14px; opacity: 0.8; }

        /* SIDEBAR */
        .sidebar {
            width: 380px;
            background: rgba(20,20,40,0.95);
            backdrop-filter: blur(25px);
            border-right: 1px solid var(--glass-border);
            padding: 80px 25px 25px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 25px;
        }

        .section {
            background: var(--glass);
            padding: 20px;
            border-radius: 20px;
            border: 1px solid var(--glass-border);
        }

        .section-title {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .metrics-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 20px;
        }

        .metric {
            text-align: center;
            padding: 15px;
            background: rgba(255,255,255,0.05);
            border-radius: 12px;
            transition: all 0.3s;
        }

        .metric:hover { transform: translateY(-3px); }

        .metric-value {
            font-size: 22px;
            font-weight: 700;
            margin-bottom: 5px;
        }

        .metric-label { font-size: 12px; opacity: 0.7; text-transform: uppercase; letter-spacing: 0.5px; }

        /* PARKS LIST */
        .park-item {
            background: rgba(255,255,255,0.05);
            padding: 18px;
            border-radius: 16px;
            margin-bottom: 12px;
            cursor: pointer;
            transition: all 0.3s;
            border: 1px solid var(--glass-border);
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .park-item:hover, .park-item.active {
            background: rgba(102,126,234,0.15);
            border-color: #667eea;
            transform: translateX(5px);
        }

        .park-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .park-name { font-weight: 600; font-size: 16px; }
        .park-zone { font-size: 12px; opacity: 0.7; }

        .park-status { 
            padding: 6px 12px; 
            border-radius: 20px; 
            font-size: 11px; 
            font-weight: 500;
        }

        .status-good { background: rgba(74,222,128,0.2); color: #4ade80; }
        .status-warning { background: rgba(250,204,21,0.2); color: #facc15; }
        .status-danger { background: rgba(239,68,68,0.2); color: #ef4444; }

        .park-data { display: flex; justify-content: space-between; font-size: 13px; }

        /* CHARTS */
        .chart-container {
            height: 120px;
            position: relative;
        }

        /* MAP */
        .map-container {
            flex: 1;
            margin-top: 70px;
            position: relative;
        }

        #map { height: 100%; width: 100%; }

        .map-controls {
            position: absolute;
            top: 20px;
            right: 20px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 1000;
        }

        .map-btn {
            width: 48px;
            height: 48px;
            border-radius: 12px;
            border: 1px solid var(--glass-border);
            background: var(--glass);
            color: white;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .map-btn:hover, .map-btn.active {
            background: var(--success);
            transform: scale(1.05);
        }

        /* MODAL */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.8);
            z-index: 2000;
            display: flex;
            align-items: center;
            justify-content: center;
            opacity: 0;
            visibility: hidden;
            transition: all 0.4s;
        }

        .modal.active {
            opacity: 1;
            visibility: visible;
        }

        .modal-content {
            background: rgba(25,25,45,0.98);
            backdrop-filter: blur(30px);
            border-radius: 24px;
            border: 1px solid var(--glass-border);
            width: 90%;
            max-width: 600px;
            max-height: 90vh;
            overflow-y: auto;
            transform: scale(0.9);
            transition: all 0.4s;
        }

        .modal.active .modal-content {
            transform: scale(1);
        }

        .modal-header {
            padding: 30px;
            border-bottom: 1px solid var(--glass-border);
            position: relative;
        }

        .modal-title { font-size: 28px; font-weight: 700; margin-bottom: 10px; }
        .modal-close {
            position: absolute;
            top: 30px;
            right: 30px;
            background: none;
            border: none;
            color: #aaa;
            font-size: 24px;
            cursor: pointer;
            width: 44px;
            height: 44px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .modal-body { padding: 30px; }

        .detail-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .detail-metric {
            text-align: center;
            padding: 25px;
            background: var(--glass);
            border-radius: 16px;
        }

        .ai-prediction {
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            padding: 20px;
            border-radius: 16px;
            margin-top: 20px;
        }

        /* RESPONSIVE */
        @media (max-width: 768px) {
            .sidebar { width: 100%; height: 50%; padding: 80px 20px 20px; }
            .metrics-grid { grid-template-columns: 1fr; }
            .app { flex-direction: column; }
        }

        .pulse {
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
    </style>
</head>
<body>
    <div class="app">
        <!-- HEADER -->
        <div class="header">
            <div class="logo">
                <i class="fas fa-leaf"></i>
                Hermosillo Verde AI
            </div>
            <div class="header-right">
                <div class="toggle-btn active" id="trafficToggle">
                    <i class="fas fa-car"></i> Tráfico
                </div>
                <div id="datetime"></div>
            </div>
        </div>

        <!-- SIDEBAR -->
        <div class="sidebar">
            <div class="section">
                <h3 class="section-title">
                    <i class="fas fa-chart-line"></i>
                    Dashboard en Tiempo Real
                </h3>
                <div class="metrics-grid">
                    <div class="metric">
                        <div class="metric-value" id="totalParks">18</div>
                        <div class="metric-label">Parques</div>
                    </div>
                    <div class="metric">
                        <div class="metric-value" id="aqi">42</div>
                        <div class="metric-label">AQI</div>
                    </div>
                    <div class="metric">
                        <div class="metric-value" id="avgTemp">--°C</div>
                        <div class="metric-label">Temperatura</div>
                    </div>
                    <div class="metric">
                        <div class="metric-value" id="avgHumidity">--%</div>
                        <div class="metric-label">Humedad</div>
                    </div>
                </div>
                <canvas id="tempChart" class="chart-container"></canvas>
            </div>

            <div class="section">
                <h3 class="section-title">
                    <i class="fas fa-tree"></i>
                    Parques Activos
                </h3>
                <div id="parksList"></div>
            </div>
        </div>

        <!-- MAP -->
        <div class="map-container">
            <div id="map"></div>
            <div class="map-controls">
                <button class="map-btn active" title="Parques" data-layer="parks">
                    <i class="fas fa-tree"></i>
                </button>
                <button class="map-btn" title="Tráfico" data-layer="traffic">
                    <i class="fas fa-car"></i>
                </button>
                <button class="map-btn" title="Calidad Aire" data-layer="aqi">
                    <i class="fas fa-wind"></i>
                </button>
                <button class="map-btn" title="Todo" data-layer="all">
                    <i class="fas fa-layer-group"></i>
                </button>
            </div>
        </div>
    </div>

    <!-- MODAL DETAIL -->
    <div class="modal" id="parkModal">
        <div class="modal-content">
            <div class="modal-header">
                <button class="modal-close" onclick="closeModal()">
                    <i class="fas fa-times"></i>
                </button>
                <h1 class="modal-title" id="modalTitle">Parque Madero</h1>
            </div>
            <div class="modal-body">
                <div class="detail-grid" id="detailGrid"></div>
                <div class="ai-prediction">
                    <h4><i class="fas fa-brain"></i> Predicción IA (Próximas 3h)</h4>
                    <p id="aiPrediction">Temperatura subirá 2°C. Riesgo moderado de sequía.</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // MAP & DATA
        let map, parksLayer, trafficLayer, aqiLayer;
        let parks = [];
        let tempChart;

        const hermosilloParks = [
            {name:"Parque Madero",lat:29.0985,lng:-110.9502,zone:"Centro"},
            {name:"Parque Zaragoza",lat:29.1021,lng:-110.9623,zone:"Centro"},
            {name:"Parque Juárez",lat:29.1058,lng:-110.9701,zone:"Centro"},
            {name:"Parque Las Sillas",lat:29.1152,lng:-110.9558,zone:"Norte"},
            {name:"Parque La Ruina",lat:29.0901,lng:-110.9456,zone:"Sur"},
            {name:"Parque San Antonio",lat:29.0823,lng:-110.9789,zone:"Oeste"},
            {name:"Parque Olivares",lat:29.1124,lng:-110.9852,zone:"Norte"},
            {name:"Parque Los Naranjos",lat:29.0887,lng:-110.9678,zone:"Sur"},
            {name:"Parque El Quelite",lat:29.1205,lng:-110.9603,zone:"Norte"},
            {name:"Parque Villa de Seris",lat:29.0956,lng:-110.9921,zone:"Oeste"},
            {name:"Parque Colosio",lat:29.0789,lng:-110.9534,zone:"Sur"},
            {name:"Parque Pitic",lat:29.1087,lng:-110.9489,zone:"Centro"},
            {name:"Parque México",lat:29.1034,lng:-110.9756,zone:"Centro"},
            {name:"Parque La Corregidora",lat:29.1102,lng:-110.9401,zone:"Norte"},
            {name:"Parque Solidaridad",lat:29.0856,lng:-110.9892,zone:"Sur"},
            {name:"Parque Las Americas",lat:29.1189,lng:-110.9723,zone:"Norte"},
            {name:"Parque Constitución",lat:29.0923,lng:-110.9618,zone:"Centro"},
            {name:"Parque 5 de Mayo",lat:29.1008,lng:-110.9687,zone:"Centro"}
        ];

        // INIT
        function init() {
            initMap();
            initParks();
            initCharts();
            updateClock();
            loadData();
            setInterval(loadData, 15000); // 15s update
        }

        function initMap() {
            map = L.map('map').setView([29.1028, -110.9773], 12);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenStreetMap | Hermosillo Verde AI'
            }).addTo(map);

            parksLayer = L.layerGroup().addTo(map);
            trafficLayer = L.layerGroup().addTo(map);
            aqiLayer = L.layerGroup().addTo(map);

            // Map controls
            document.querySelectorAll('.map-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.map-btn').forEach(b => b.classList.remove('active'));
                    this.classList.add('active');
                    toggleLayers(this.dataset.layer);
                });
            });
        }

        function initParks() {
            const container = document.getElementById('parksList');
            hermosilloParks.forEach((park, index) => {
                const parkData = {
                    ...park,
                    temp: 25 + Math.random() * 10,
                    humidity: 40 + Math.random() * 40,
                    aqi: 20 + Math.random() * 60,
                    traffic: ['normal', 'moderado', 'intenso'][Math.floor(Math.random()*3)],
                    status: 'good'
                };
                parks.push(parkData);

                const div = document.createElement('div');
                div.className = 'park-item';
                div.innerHTML = `
                    <div class="park-header">
                        <div>
                            <div class="park-name">${park.name}</div>
                            <div class="park-zone">${park.zone}</div>
                        </div>
                        <div class="park-status status-${parkData
