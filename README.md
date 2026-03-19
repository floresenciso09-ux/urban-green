#URBAN GREEN
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sistema inteligente de hermosillo</title>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">

<style>
* {box-sizing:border-box}
body{margin:0;font-family:'Inter',Segoe UI,sans-serif;background:linear-gradient(135deg,#0b0b1f 0%,#1a1a3a 100%);color:#fff;height:100vh;overflow:hidden}

.header{height:80px;background:linear-gradient(90deg,#12122e 0%,#1a1a3a 100%);display:flex;justify-content:space-between;align-items:center;padding:0 30px;position:fixed;width:100%;z-index:1000;box-shadow:0 4px 20px rgba(0,0,0,0.3)}
.header h2{font-size:1.6rem;font-weight:700;background:linear-gradient(45deg,#00eaff,#2ed573);background-clip:text;-webkit-background-clip:text;-webkit-text-fill-color:transparent;margin:0}
#datetime{font-size:0.95rem;opacity:0.8}

.app{display:flex;margin-top:80px;height:calc(100vh - 80px)}
.sidebar{width:420px;background:linear-gradient(180deg,#1a1a3a 0%,#12122e 100%);padding:30px;overflow:auto;border-right:1px solid rgba(255,255,255,0.1);position:relative}
.sidebar::-webkit-scrollbar{width:6px}
.sidebar::-webkit-scrollbar-track{background:rgba(255,255,255,0.05)}
.sidebar::-webkit-scrollbar-thumb{background:#00eaff;border-radius:3px}

.section{margin-bottom:25px}
.section h3{font-size:1.2rem;font-weight:600;margin:0 0 20px 0;color:#00eaff;padding-bottom:10px;border-bottom:2px solid rgba(0,234,255,0.3);display:flex;align-items:center;gap:10px}

.metric{background:linear-gradient(145deg,#24245c,#2a2a6e);padding:20px;margin:12px 0;border-radius:16px;box-shadow:0 8px 25px rgba(0,0,0,0.3);transition:all 0.3s ease;border:1px solid rgba(0,234,255,0.1)}
.metric:hover{transform:translateY(-2px);box-shadow:0 12px 35px rgba(0,234,255,0.2)}
.metric-icon{font-size:1.8rem;margin-bottom:10px;opacity:0.8}
.metric-value{font-size:1.8rem;font-weight:700;color:#00eaff;margin-top:8px}
.metric-label{font-size:0.85rem;opacity:0.7;margin-top:4px}

.alert{background:linear-gradient(145deg,#ff4757,#ff6b7a);padding:15px;border-radius:12px;margin:10px 0;border-left:4px solid #fff;box-shadow:0 4px 15px rgba(255,71,87,0.3);animation:pulse 2s infinite}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:0.7}}

.parks{background:linear-gradient(145deg,#2ed573,#4ade80);padding:20px;border-radius:16px;margin:15px 0;color:#000;font-weight:600;box-shadow:0 8px 25px rgba(46,213,115,0.3)}
.parks-item{padding:12px 0;border-bottom:1px solid rgba(255,255,255,0.2);cursor:pointer;transition:all 0.3s ease}
.parks-item:last-child{border-bottom:none}
.parks-item:hover{background:rgba(255,255,255,0.1);padding-left:10px}

.btn{width:100%;padding:16px;border:none;border-radius:12px;margin:8px 0;font-weight:600;cursor:pointer;transition:all 0.3s ease;font-size:1rem;position:relative;overflow:hidden}
.btn-primary{background:linear-gradient(45deg,#00eaff,#0099cc);color:#fff;box-shadow:0 4px 15px rgba(0,234,255,0.3)}
.btn-primary:hover{transform:translateY(-2px);box-shadow:0 8px 25px rgba(0,234,255,0.4)}
.btn-success{background:linear-gradient(45deg,#2ed573,#1abc9c);color:#fff;box-shadow:0 4px 15px rgba(46,213,115,0.3)}
.btn-success:hover{transform:translateY(-2px);box-shadow:0 8px 25px rgba(46,213,115,0.4)}
.btn-warning{background:linear-gradient(45deg,#ffa502,#ff6348);color:#fff;box-shadow:0 4px 15px rgba(255,165,2,0.3)}

.modal{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.8);z-index:2000;justify-content:center;align-items:center}
.modal-content{background:linear-gradient(145deg,#1a1a3a,#12122e);padding:40px;border-radius:20px;max-width:500px;width:90%;max-height:80vh;overflow:auto;box-shadow:0 20px 60px rgba(0,0,0,0.5);border:1px solid rgba(0,234,255,0.3)}
.modal h3{color:#00eaff;margin-top:0}
.form-group{margin-bottom:20px}
.form-group label{display:block;margin-bottom:8px;font-weight:500;color:#e0e0e0}
.form-group input,.form-group select,.form-group textarea{width:100%;padding:12px;border:2px solid rgba(0,234,255,0.2);border-radius:10px;background:rgba(255,255,255,0.05);color:#fff;font-size:1rem;transition:all 0.3s ease}
.form-group input:focus,.form-group select:focus,.form-group textarea:focus{outline:none;border-color:#00eaff;box-shadow:0 0 0 3px rgba(0,234,255,0.1)}

#map{flex:1;transition:all 0.3s ease}
.leaflet-popup-content-wrapper{background:linear-gradient(145deg,#1a1a3a,#12122e);border-radius:12px;border:1px solid rgba(0,234,255,0.3)}
.leaflet-popup-content{font-size:0.95rem;color:#fff}
.legend{position:absolute;bottom:20px;right:20px;background:linear-gradient(145deg,#1a1a3a,#12122e);padding:20px;border-radius:16px;box-shadow:0 8px 30px rgba(0,0,0,0.5);border:1px solid rgba(0,234,255,0.2);z-index:1000}
.legend-item{display:flex;align-items:center;gap:10px;margin:8px 0;font-size:0.9rem}
.legend-color{width:20px;height:20px;border-radius:50%;border:2px solid #fff}

@media (max-width:768px){
.sidebar{width:100%;height:calc(100vh - 80px);position:fixed;z-index:999;transform:translateX(-100%);transition:transform 0.3s ease}
.sidebar.open{transform:translateX(0)}
.app{display:block}
.header{padding:0 20px}
#map{height:calc(100vh - 80px)}
}
</style>
</head>

<body>

<div class="header">
<h2><i class="fas fa-leaf"></i> AI HMO</h2>
<div id="datetime"></div>
<i class="fas fa-bars fa-2x menu-btn" onclick="toggleSidebar()" style="cursor:pointer;display:none"></i>
</div>

<div class="app">
<div class="sidebar" id="sidebar">
<div class="section">
<h3><i class="fas fa-cloud-sun"></i> Estado Ambiental</h3>
<div class="metric"><div class="metric-icon"><i class="fas fa-thermometer-half"></i></div>Temperatura<div class="metric-value" id="temp">--</div><div class="metric-label">°C</div></div>
<div class="metric"><div class="metric-icon"><i class="fas fa-tint"></i></div>Humedad<div class="metric-value" id="humidity">--</div><div class="metric-label">%</div></div>
<div class="metric"><div class="metric-icon"><i class="fas fa-wind"></i></div>AQI<div class="metric-value" id="aqi">--</div><div class="metric-label">Índice</div></div>
<div class="metric"><div class="metric-icon"><i class="fas fa-leaf"></i></div>Índice Ecológico<div class="metric-value" id="eco">--</div><div class="metric-label">Puntos</div></div>
</div>

<div class="section">
<h3><i class="fas fa-bell"></i> Notificaciones Eventos</h3>
<div id="notifications"></div>
</div>

<div class="section">
<h3><i class="fas fa-exclamation-triangle"></i> Alertas</h3>
<div id="alerts"></div>
</div>

<div class="section">
<h3><i class="fas fa-calendar-alt"></i> Eventos Próximos</h3>
<div id="events" class="parks"></div>
</div>

<div class="section">
<h3><i class="fas fa-tree"></i> Parques Cercanos</h3>
<div id="parks" class="parks">Detectando ubicación...</div>
</div>

<div class="section">
<h3><i class="fas fa-bullhorn"></i> Reportar a HMO GO</h3>
<button class="btn btn-primary" onclick="openReportModal()"><i class="fas fa-camera"></i> Reportar Problema</button>
</div>

<div class="section">
<h3><i class="fas fa-users"></i> Programas HMO</h3>
<button class="btn btn-success" onclick="openAdoptModal()"><i class="fas fa-seedling"></i> Adopta tu Parque</button>
<button class="btn btn-success" onclick="openReciclaModal()"><i class="fas fa-recycle"></i> CRECES Recicla</button>
</div>
</div>

<div id="map"></div>
</div>

<!-- Modal Reporte -->
<div id="reportModal" class="modal">
<div class="modal-content">
<h3><i class="fas fa-bullhorn"></i> Reporte Ciudadano HMO GO</h3>
<div class="form-group">
<label>Parque afectado</label>
<select id="reportPark">
<option value="">Seleccionar parque...</option>
</select>
</div>
<div class="form-group">
<label>Tipo de problema</label>
<select id="issueType">
<option value="basura">Basura acumulada</option>
<option value="arboles">Árboles dañados</option>
<option value="iluminacion">Falta iluminación</option>
<option value="otros">Otros</option>
</select>
</div>
<div class="form-group">
<label>Descripción</label>
<textarea id="issueDesc" rows="4" placeholder="Describe el problema..."></textarea>
</div>
<div class="form-group">
<label>Foto (simulada)</label>
<input type="file" id="issuePhoto" accept="image/*">
</div>
<button class="btn btn-primary" onclick="sendReport()" style="margin-top:20px">📤 Enviar a HMO GO</button>
<button class="btn btn-warning" onclick="closeReportModal()" style="margin-top:20px;margin-left:10px">Cancelar</button>
</div>
</div>

<!-- Modal Adopta tu Parque -->
<div id="adoptModal" class="modal">
<div class="modal-content">
<h3><i class="fas fa-seedling"></i> Adopta tu Parque</h3>
<p>¡Únete al programa oficial del Ayuntamiento de Hermosillo!</p>
<div class="form-group">
<label>Nombre completo</label>
<input type="text" id="adoptName" placeholder="Tu nombre">
</div>
<div class="form-group">
<label>Parque a adoptar</label>
<select id="adoptPark">
<option>🌳 Parque Madero (Vivero Municipal)</option>
<option>🎓 UNISON - Área Verde Estratégica</option>
<option>🍔 Gastro Park</option>
<option>🛒 Soriana Bachoco</option>
<option>🌳 Parque Zaragoza</option>
</select>
</div>
<div class="form-group">
<label>Correo electrónico</label>
<input type="email" id="adoptEmail" placeholder="tu@email.com">
</div>
<div class="form-group">
<label>Teléfono</label>
<input type="tel" id="adoptPhone" placeholder="662-XXX-XXXX">
</div>
<button class="btn btn-success" onclick="joinAdoptProgram()">✅ Inscribirme</button>
</div>
</div>

<!-- Modal CRECES Recicla -->
<div id="reciclaModal" class="modal">
<div class="modal-content">
<h3><i class="fas fa-recycle"></i> CRECES Recicla Centro</h3>
<p>Programa de reciclaje del Ayuntamiento de Hermosillo</p>
<div class="form-group">
<label>Nombre</label>
<input type="text" id="reciclaName" placeholder="Tu nombre">
</div>
<div class="form-group">
<label>Parque de recolección</label>
<select id="reciclaPark">
<option>🌳 Parque Madero</option>
<option>🎓 UNISON</option>
<option>🛒 Soriana Bachoco</option>
</select>
</div>
<button class="btn btn-success" onclick="joinReciclaProgram()">♻️ Unirme al programa</button>
<p style="font-size:0.85rem;margin-top:20px;opacity:0.7">Recibe brigadas de recolección quincenal</p>
</div>
</div>

<div class="legend">
<div class="legend-item"><div class="legend-color" style="background:#2ed573"></div><span>Parques (800)</span></div>
<div class="legend-item"><div class="legend-color" style="background:#00eaff"></div><span>Emblemáticos</span></div>
<div class="legend-item"><div class="legend-color" style="background:#ffa502"></div><span>Pulmón Ciudad</span></div>
<div class="legend-item"><div class="legend-color" style="background:#ff6b7a"></div><span>Tu ubicación</span></div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://unpkg.com/leaflet.heat@0.2.0/dist/leaflet-heat.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@turf/turf@6/turf.min.js"></script>

<script>
// ===== CONFIGURACIÓN AVANZADA =====
const CONFIG = {
  CENTER: [29.1028, -110.9773],
  ZOOM: 12,
  UPDATE_INTERVAL: 5000,
  NOTIFICATION_INTERVAL: 15000
};

// ===== MAPA AVANZADO =====
const map = L.map("map", {
  zoomControl: true,
  attributionControl: true
}).setView(CONFIG.CENTER, CONFIG.ZOOM);

L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
  attribution: '© OpenStreetMap | AI HMO Parques'
}).addTo(map);

// ===== ICONOS PERSONALIZADOS =====
const icons = {
  lung: L.icon({
    iconUrl: "https://cdn-icons-png.flaticon.com/512/616/616554.png",
    iconSize: [35, 35],
    iconAnchor: [17, 35],
    popupAnchor: [0, -35]
  }),
  university: L.icon({
    iconUrl: "https://cdn-icons-png.flaticon.com/512/2772/2772468.png",
    iconSize: [30, 30],
    iconAnchor: [15, 30]
  }),
  food: L.icon({
    iconUrl: "https://cdn-icons-png.flaticon.com/512/2421/2421740.png",
    iconSize: [30, 30],
    iconAnchor: [15, 30]
  }),
  nursery: L.icon({
    iconUrl: "https://cdn-icons-png.flaticon.com/512/3079/3079324.png",
    iconSize: [30, 30],
    iconAnchor: [15, 30]
  }),
  user: L.icon({
    iconUrl: "https://cdn-icons-png.flaticon.com/512/684/684908.png",
    iconSize: [30, 30],
    iconAnchor: [15, 30]
  })
};

// ===== PARQUES EMBLEMÁTICOS =====
const emblematicParks = [
  {
    coords: [29.0985, -110.9502],
    name: "🌳 Parque Madero - Pulmón de Hermosillo",
    icon: icons.lung,
    description: "Vivero Municipal • Mayor cobertura verde • 25 hectáreas",
    category: "pulmon"
  },
  {
    coords: [29.0839, -110.9591],
    name: "🎓 UNISON - Pulmón Universitario",
    icon: icons.university,
    description: "Área verde estratégica • Investigación ecológica",
    category: "emblematico"
  },
  {
    coords: [29.0901, -110.9456],
    name: "🍔 Gastro Park",
    icon: icons.food,
    description: "Zona recreativa gastronómica • Eventos culturales",
    category: "emblematico"
  },
  {
    coords: [29.0878, -110.9527],
    name: "🛒 Soriana Bachoco",
    icon: icons.nursery,
    description: "Área verde comercial • Mantenimiento constante",
    category: "emblematico"
  }
];

// ================== GENERAR 800 PARQUES ==================
const parkLayer = L.layerGroup().addTo(map);
const parkList = [];

for(let i=0;i<800;i++){
  const lat = CONFIG.CENTER[0] + (Math.random()-0.5)*0.45;
  const lng = CONFIG.CENTER[1] + (Math.random()-0.5)*0.45;

  const park = {
    name: "🌳 Parque HMO #" + (i+1),
    coords: [lat,lng]
  };

  parkList.push(park);

  L.circleMarker(park.coords,{
    radius:4,
    color:"#2ed573",
    fillOpacity:0.7
  }).addTo(parkLayer)
    .bindPopup(park.name);
}

// ================== PARQUES EMBLEMÁTICOS ==================
emblematicParks.forEach(p=>{
  L.marker(p.coords,{icon:p.icon})
    .addTo(map)
    .bindPopup(`
      <b>${p.name}</b><br>
      ${p.description}<br>
      <button onclick="flyToPark(${p.coords[0]},${p.coords[1]})">
      Ir al parque</button>
    `);
});

// ================== IR A PARQUE ==================
function flyToPark(lat,lng){
  map.flyTo([lat,lng],16);
}

// ================== EVENTOS DINÁMICOS ==================
const events = [
"🌱 Reforestación comunitaria — Parque Madero",
"♻️ Jornada de limpieza — UNISON",
"🎉 Festival cultural — Gastro Park",
"🚴 Carrera ecológica — Parque Zaragoza",
"🧒 Taller infantil ambiental"
];

function updateEvents(){
  document.getElementById("events").innerHTML =
    events.sort(()=>0.5-Math.random())
          .slice(0,3)
          .map(e=>`<div class="parks-item">${e}</div>`)
          .join("");
}
updateEvents();
setInterval(updateEvents,20000);

// ================== NOTIFICACIONES ==================
function notify(){
  const msg = events[Math.floor(Math.random()*events.length)];
  const box = document.getElementById("notifications");

  const div = document.createElement("div");
  div.className="alert";
  div.textContent="📢 "+msg;

  box.prepend(div);

  setTimeout(()=>div.remove(),10000);
}
setInterval(notify,CONFIG.NOTIFICATION_INTERVAL);

// ================== RELOJ ==================
setInterval(()=>{
  document.getElementById("datetime").textContent =
    new Date().toLocaleString("es-MX",
      {timeZone:"America/Hermosillo"});
},1000);

// ================== AMBIENTE ==================
function updateEnvironment(){
  const temp=(28+Math.random()*8).toFixed(1);
  const hum=(40+Math.random()*40).toFixed(0);
  const aqi=(50+Math.random()*120).toFixed(0);
  const eco=(60+Math.random()*40).toFixed(0);

  document.getElementById("temp").textContent=temp;
  document.getElementById("humidity").textContent=hum;
  document.getElementById("aqi").textContent=aqi;
  document.getElementById("eco").textContent=eco;

  const alertsBox=document.getElementById("alerts");
  alertsBox.innerHTML="";

  if(temp>35)
    alertsBox.innerHTML+='<div class="alert">🔥 Ola de calor extrema</div>';

  if(aqi>120)
    alertsBox.innerHTML+='<div class="alert">🌫️ Aire contaminado</div>';
}
setInterval(updateEnvironment,CONFIG.UPDATE_INTERVAL);
updateEnvironment();

// ================== GEOLOCALIZACIÓN ==================
navigator.geolocation?.getCurrentPosition(pos=>{
  const lat=pos.coords.latitude;
  const lng=pos.coords.longitude;

  L.marker([lat,lng],{icon:icons.user})
    .addTo(map)
    .bindPopup("📍 Tu ubicación");

  map.flyTo([lat,lng],14);

  const user = turf.point([lng,lat]);

  const nearest = parkList
    .map(p=>{
      const d = turf.distance(
        user,
        turf.point([p.coords[1],p.coords[0]])
      );
      return {name:p.name,dist:d};
    })
    .sort((a,b)=>a.dist-b.dist)
    .slice(0,5);

  document.getElementById("parks").innerHTML =
    nearest.map(p=>
      `<div class="parks-item">${p.name}<br>${p.dist.toFixed(2)} km</div>`
    ).join("");
});

// ================== MODALES ==================
function openReportModal(){
  document.getElementById("reportModal").style.display="flex";

  const select=document.getElementById("reportPark");
  select.innerHTML="<option>Seleccionar parque...</option>";

  parkList.slice(0,50).forEach(p=>{
    const op=document.createElement("option");
    op.textContent=p.name;
    select.appendChild(op);
  });
}

function closeReportModal(){
  document.getElementById("reportModal").style.display="none";
}

function openAdoptModal(){
  document.getElementById("adoptModal").style.display="flex";
}

function openReciclaModal(){
  document.getElementById("reciclaModal").style.display="flex";
}

// ================== ENVÍO DE FORMULARIOS ==================
function sendReport(){
  alert("✅ Reporte enviado a HMO GO");
  closeReportModal();
}

function joinAdoptProgram(){
  alert("🌱 Registro exitoso en Adopta tu Parque");
  document.getElementById("adoptModal").style.display="none";
}

function joinReciclaProgram(){
  alert("♻️ Inscripción completada");
  document.getElementById("reciclaModal").style.display="none";
}

// ================== SIDEBAR RESPONSIVE ==================
function toggleSidebar(){
  document.getElementById("sidebar")
    .classList.toggle("open");
}

</script>
