#URBAN GREEN
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Hermosillo Verde AI</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<style>
body{
    margin:0;
    font-family:'Inter',sans-serif;
    background:#0f0f23;
    color:white;
}

.header{
    position:fixed;
    top:0; left:0; right:0;
    height:70px;
    background:#14142b;
    display:flex;
    justify-content:space-between;
    align-items:center;
    padding:0 25px;
    z-index:1000;
}

.sidebar{
    width:350px;
    background:#1a1a35;
    height:100vh;
    padding:90px 20px 20px;
    overflow:auto;
}

.app{ display:flex; }

.map-container{
    flex:1;
    margin-top:70px;
}

#map{ height:calc(100vh - 70px); }

.metric{
    background:#26264d;
    padding:12px;
    border-radius:10px;
    text-align:center;
    margin-bottom:10px;
}

.park-item{
    background:#26264d;
    padding:12px;
    border-radius:10px;
    margin-bottom:8px;
    cursor:pointer;
}

.park-item:hover{ background:#33336b; }

.map-controls{
    position:absolute;
    top:90px;
    right:15px;
    display:flex;
    flex-direction:column;
    gap:10px;
    z-index:1000;
}

.map-btn{
    width:45px;
    height:45px;
    border:none;
    border-radius:10px;
    background:#26264d;
    color:white;
    cursor:pointer;
}

.modal{
    position:fixed;
    inset:0;
    background:rgba(0,0,0,0.8);
    display:none;
    align-items:center;
    justify-content:center;
}

.modal-content{
    background:#1f1f3d;
    padding:25px;
    border-radius:15px;
    width:400px;
}
</style>
</head>

<body>

<div class="header">
    <h2><i class="fas fa-leaf"></i> Hermosillo Verde AI</h2>
    <div id="datetime"></div>
</div>

<div class="app">

<!-- SIDEBAR -->
<div class="sidebar">

<h3>Dashboard</h3>
<h3>Historial Ambiental</h3>

<select id="historySelect">
  <option value="today">Hoy</option>
  <option value="yesterday">Ayer</option>
  <option value="before">Antier</option>
  <option value="week">Últimos 7 días</option>
</select>

<canvas id="historyChart"></canvas>

<div class="metric">Parques: <b id="totalParks">18</b></div>
<div class="metric">Temperatura: <b id="avgTemp">--°C</b></div>
<div class="metric">Humedad: <b id="avgHumidity">--%</b></div>

<canvas id="tempChart"></canvas>

<h3>Parques</h3>
<div id="parksList"></div>

</div>

<!-- MAP -->
<div class="map-container">
<div id="map"></div>

<div class="map-controls">
<button class="map-btn" onclick="showLayer('parks')"><i class="fas fa-tree"></i></button>
<button class="map-btn" onclick="showLayer('traffic')"><i class="fas fa-car"></i></button>
<button class="map-btn" onclick="showLayer('aqi')"><i class="fas fa-wind"></i></button>
<button class="map-btn" onclick="showLayer('all')"><i class="fas fa-layer-group"></i></button>
</div>

</div>
</div>

<!-- MODAL -->
<div class="modal" id="modal">
<div class="modal-content">
<h2 id="modalTitle"></h2>
<div id="modalData"></div>
<br>
<button onclick="closeModal()">Cerrar</button>
</div>
</div>

<script>
let map, parksLayer, trafficLayer, aqiLayer;

const hermosilloParks = [
{name:"Parque Madero",lat:29.0985,lng:-110.9502,zone:"Centro"},
{name:"Parque Zaragoza",lat:29.1021,lng:-110.9623,zone:"Centro"},
{name:"Parque Juárez",lat:29.1058,lng:-110.9701,zone:"Centro"},
{name:"Parque La Ruina",lat:29.0901,lng:-110.9456,zone:"Sur"},
{name:"Parque Colosio",lat:29.0789,lng:-110.9534,zone:"Sur"}
];

let parksData=[];

function init(){

map=L.map('map').setView([29.1028,-110.9773],12);

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png')
.addTo(map);

parksLayer=L.layerGroup().addTo(map);
trafficLayer=L.layerGroup().addTo(map);
aqiLayer=L.layerGroup().addTo(map);

initParks();
initTraffic();
initAQI();
initChart();
updateClock();
loadData();

setInterval(loadData,15000);
}

function initParks(){

const container=document.getElementById('parksList');

hermosilloParks.forEach(p=>{

const data={
...p,
temp:25+Math.random()*10,
humidity:40+Math.random()*40,
aqi:20+Math.random()*60
};

parksData.push(data);

L.marker([p.lat,p.lng])
.addTo(parksLayer)
.bindPopup(p.name)
.on('click',()=>openModal(data));

const div=document.createElement('div');
div.className="park-item";
div.textContent=p.name;
div.onclick=()=>openModal(data);

container.appendChild(div);

});
}

function initTraffic(){
for(let i=0;i<5;i++){
L.circle(
[29.05+Math.random()*0.1,-110.99+Math.random()*0.1],
{radius:400,color:'red',fillOpacity:0.3}
).addTo(trafficLayer);
}
}

function initAQI(){
for(let i=0;i<5;i++){
L.circle(
[29.05+Math.random()*0.1,-110.99+Math.random()*0.1],
{radius:500,color:'yellow',fillOpacity:0.25}
).addTo(aqiLayer);
}
}

function showLayer(type){
parksLayer.clearLayers();
trafficLayer.clearLayers();
aqiLayer.clearLayers();

if(type==='parks'||type==='all') initParks();
if(type==='traffic'||type==='all') initTraffic();
if(type==='aqi'||type==='all') initAQI();
}

function openModal(p){
document.getElementById('modal').style.display='flex';
document.getElementById('modalTitle').textContent=p.name;
document.getElementById('modalData').innerHTML=
`Temp: ${p.temp.toFixed(1)}°C<br>
Humedad: ${p.humidity.toFixed(0)}%<br>
AQI: ${p.aqi.toFixed(0)}`;
}

function closeModal(){
document.getElementById('modal').style.display='none';
}

function initChart(){

new Chart(document.getElementById('tempChart'),{
type:'line',
data:{
labels:['-5','-4','-3','-2','-1','Ahora'],
datasets:[{
data:[28,29,30,29,31,30],
borderColor:'#4facfe',
tension:0.4
}]
},
options:{
plugins:{legend:{display:false}},
scales:{x:{display:false},y:{display:false}}
}
});
}

function updateClock(){
setInterval(()=>{
document.getElementById('datetime')
.textContent=new Date().toLocaleString();
},1000);
}

function loadData(){
document.getElementById('avgTemp').textContent=(28+Math.random()*4).toFixed(1)+"°C";
document.getElementById('avgHumidity').textContent=(50+Math.random()*20).toFixed(0)+"%";
}

document.addEventListener("DOMContentLoaded",init);
let historyChart;

// Datos simulados del ecosistema
const historyData = {
  today: [30,31,32,31,30,29],
  yesterday: [28,29,30,29,28,27],
  before: [27,28,29,28,27,26],
  week: [26,27,29,30,28,31,30]
};

function initHistoryChart(){

  const ctx = document.getElementById('historyChart');

  historyChart = new Chart(ctx,{
    type:'line',
    data:{
      labels:['6am','9am','12pm','3pm','6pm','9pm'],
      datasets:[{
        label:'Temperatura °C',
        data: historyData.today,
        borderColor:'#00f2fe',
        tension:0.4
      }]
    },
    options:{
      responsive:true,
      plugins:{legend:{display:true}}
    }
  });

}

// Cambiar periodo histórico
document.getElementById('historySelect')
.addEventListener('change',function(){

  const selected = this.value;

  if(selected === 'week'){
    historyChart.data.labels =
      ['Lun','Mar','Mié','Jue','Vie','Sáb','Dom'];
  }else{
    historyChart.data.labels =
      ['6am','9am','12pm','3pm','6pm','9pm'];
  }

  historyChart.data.datasets[0].data =
    historyData[selected];

  historyChart.update();

});
</script>

</body>
</html>
