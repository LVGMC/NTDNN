<html lang="lv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Saules paneļi - Nedēļas plāns</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: system-ui, 'Segoe UI', sans-serif; }
    body { background: linear-gradient(145deg, #e0f2fe 0%, #bae6fd 100%); min-height: 100vh; padding: 20px; }
    .dashboard { max-width: 1600px; margin: 0 auto; display: flex; gap: 24px; flex-wrap: wrap; align-items: flex-start; }
    .weekly-plan { width: 320px; background: rgba(255,255,255,0.94); border-radius: 2rem; box-shadow: 0 20px 35px -12px rgba(0,0,0,0.2); padding: 1.5rem 1.2rem; border: 1px solid rgba(255,215,150,0.6); height: fit-content; }
    .weekly-plan h2 { font-size: 1.8rem; font-weight: 700; background: linear-gradient(135deg, #f59e0b, #d97706); -webkit-background-clip: text; background-clip: text; color: transparent; border-left: 5px solid #f59e0b; padding-left: 15px; margin-bottom: 20px; }
    .plan-list { display: flex; flex-direction: column; gap: 14px; }
    .plan-day { background: #fef9e6; border-radius: 1.5rem; padding: 12px 16px; border: 1px solid #ffecb3; }
    .plan-day strong { font-size: 1.1rem; color: #b45309; display: block; border-bottom: 1px dashed #fed7aa; margin-bottom: 8px; }
    .plan-day p { color: #2c3e2f; font-size: 0.9rem; font-weight: 500; margin-top: 6px; }
    .main-content { flex: 1; min-width: 280px; display: flex; flex-direction: column; gap: 28px; }
    .top-weather-card { background: white; border-radius: 2rem; padding: 1.5rem 2rem; box-shadow: 0 12px 25px -10px rgba(0,0,0,0.1); border: 1px solid rgba(255,235,170,0.8); }
    .date-row { display: flex; justify-content: space-between; align-items: baseline; flex-wrap: wrap; margin-bottom: 1.2rem; padding-bottom: 0.7rem; border-bottom: 2px solid #ffe0a3; }
    .today-date { font-size: 1.7rem; font-weight: 800; color: #1e3a5f; }
    .current-weather { display: flex; align-items: center; gap: 18px; flex-wrap: wrap; background: #fef7e0; padding: 15px 20px; border-radius: 1.8rem; margin-bottom: 20px; }
    .weather-big { display: flex; align-items: center; gap: 12px; }
    .temp { font-size: 2.2rem; font-weight: 700; }
    .hour-cards, .days-forecast { display: flex; flex-wrap: wrap; gap: 15px; margin-top: 10px; }
    .hour-item, .day-card { background: #f9f3e2; border-radius: 1.2rem; padding: 10px 16px; text-align: center; flex: 1; min-width: 80px; }
    .google-photo-gallery { background: rgba(255,248,225,0.9); border-radius: 2rem; padding: 1.5rem; box-shadow: 0 12px 22px rgba(0,0,0,0.05); }
    .google-photo-gallery h2 { font-size: 1.6rem; font-weight: 700; color: #b45f06; margin-bottom: 20px; }
    .gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 20px; margin-top: 15px; }
    .gallery-item { background: #fff7e8; border-radius: 1rem; padding: 10px; text-align: center; transition: transform 0.3s ease; cursor: pointer; }
    .gallery-item:hover { transform: scale(1.03); }
    .gallery-item img { width: 100%; height: 180px; object-fit: cover; border-radius: 0.8rem; box-shadow: 0 4px 8px rgba(0,0,0,0.1); }
    .gallery-item span { display: block; margin-top: 8px; font-size: 0.85rem; color: #7c4d1e; }
    .loading { text-align: center; padding: 40px; font-size: 1.1rem; color: #b45309; }
    .error-message { background: #fee2e2; border-left: 4px solid #dc2626; padding: 15px; border-radius: 1rem; margin: 20px 0; color: #991b1b; }
    @media (max-width: 780px) { .dashboard { flex-direction: column; } .weekly-plan { width: 100%; } }
  </style>
</head>
<body>
  <div class="dashboard">
    <div class="weekly-plan">
      <h2>📋 Nedēļas plāns</h2>
      <div class="plan-list" id="weeklyPlanList"></div>
    </div>
    <div class="main-content">
      <div class="top-weather-card">
        <div class="date-row">
          <div class="today-date" id="currentDate">Ielādē datumu…</div>
          <div style="font-size:0.9rem; background:#f0e5d2; padding:5px 12px; border-radius:40px;">🌍 Saules aktivitāte</div>
        </div>
        <div class="current-weather" id="currentWeatherBlock"><div class="loading">🌤️ Ielādē laikapstākļus…</div></div>
        <div style="margin-top:15px;">
          <h3>⏱️ Prognoze nākamajām 3 stundām</h3>
          <div class="hour-cards" id="hourly3h"></div>
        </div>
        <div style="margin-top:20px;">
          <h3>📅 Nākamās 3 dienas</h3>
          <div class="days-forecast" id="threeDaysForecast"></div>
        </div>
      </div>
      <div class="google-photo-gallery">
        <h2>📸 Attēlu galerija</h2>
        <div id="googlePhotosContainer"><div class="loading">🖼️ Ielādē attēlus...</div></div>
      </div>
    </div>
  </div>

<script>
const API_KEY = 'c43d82886b01f58c9754b116e45751b7';
const LAT = 56.9496;
const LON = 24.1052;

function $(id) { return document.getElementById(id); }

function updateDate() {
  const now = new Date();
  const formatted = now.toLocaleDateString('lv-LV', { weekday:'long', year:'numeric', month:'long', day:'numeric' });
  $('currentDate').textContent = formatted.charAt(0).toUpperCase() + formatted.slice(1);
}

function getIconUrl(icon) { return `https://openweathermap.org/img/wn/${icon}@2x.png`; }

async function fetchWeather() {
  const url = `https://api.openweathermap.org/data/2.5/weather?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
  const res = await fetch(url);
  if (!res.ok) throw new Error('HTTP ' + res.status);
  return await res.json();
}

async function fetchForecast() {
  const url = `https://api.openweathermap.org/data/2.5/forecast?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
  const res = await fetch(url);
  if (!res.ok) throw new Error('HTTP ' + res.status);
  return await res.json();
}

async function showWeather() {
  try {
    const w = await fetchWeather();
    const temp = Math.round(w.main.temp);
    const feels = Math.round(w.main.feels_like);
    const desc = w.weather[0].description;
    const icon = w.weather[0].icon;
    const main = w.weather[0].main;
    
    let tip = "🌍 Normāli apstākļi saules paneļiem";
    if (main === "Clear") tip = "☀️ Lieliska diena saules paneļiem!";
    else if (main === "Clouds") tip = "⛅ Vidēja efektivitāte (mākoņi)";
    else if (main === "Rain") tip = "🌧️ Zema ražība (lietus)";
    else if (main === "Snow") tip = "❄️ Notīri paneļus (sniegs)";
    
    $('currentWeatherBlock').innerHTML = `
      <div class="weather-big">
        <img src="${getIconUrl(icon)}" style="width:60px;height:60px;">
        <div class="temp">${temp}°C</div>
        <div>
          <strong>${desc.charAt(0).toUpperCase() + desc.slice(1)}</strong><br>
          <small>${tip}</small>
        </div>
      </div>
      <div style="margin-left:auto;font-size:0.9rem;">
        🌡️ Jūtas kā ${feels}°C<br>
        💧 Mitrums: ${w.main.humidity}%<br>
        💨 Vējš: ${Math.round(w.wind.speed)} m/s
      </div>`;
  } catch (err) {
    $('currentWeatherBlock').innerHTML = `<div class="error-message">❌ Kļūda: ${err.message}</div>`;
  }
}

async function showHourly() {
  try {
    const f = await fetchForecast();
    const items = f.list.slice(0, 3);
    $('hourly3h').innerHTML = items.map(item => {
      const t = new Date(item.dt * 1000);
      const hh = String(t.getHours()).padStart(2, '0');
      return `
        <div class="hour-item">
          <div>${hh}:00</div>
          <img src="${getIconUrl(item.weather[0].icon)}" style="width:40px;height:40px;">
          <div>${Math.round(item.main.temp)}°C</div>
          <small>${item.weather[0].description}</small>
        </div>`;
    }).join('');
  } catch (err) {
    $('hourly3h').innerHTML = `<div class="error-message">❌ ${err.message}</div>`;
  }
}

async function showDays() {
  try {
    const f = await fetchForecast();
    const daily = {};
    f.list.forEach(item => {
      const d = new Date(item.dt * 1000).toLocaleDateString('lv-LV');
      if (!daily[d]) daily[d] = { temps: [], icons: [] };
      daily[d].temps.push(item.main.temp);
      daily[d].icons.push(item.weather[0].icon);
    });
    
    const days = Object.keys(daily).slice(1, 4);
    const labels = ['Rīt', 'Parīt', 'Pēc 3 dienām'];
    
    $('threeDaysForecast').innerHTML = days.map((d, i) => {
      const data = daily[d];
      const min = Math.round(Math.min(...data.temps));
      const max = Math.round(Math.max(...data.temps));
      const icon = data.icons[Math.floor(data.icons.length / 2)];
      return `
        <div class="day-card">
          <strong>${labels[i]}</strong>
          <img src="${getIconUrl(icon)}" style="width:50px;height:50px;">
          <div>${max}° / ${min}°</div>
        </div>`;
    }).join('');
  } catch (err) {
    $('threeDaysForecast').innerHTML = `<div class="error-message">❌ ${err.message}</div>`;
  }
}

function generatePlan() {
  const days = ['Pirmdiena', 'Otrdiena', 'Trešdiena', 'Ceturtdiena', 'Piektdiena', 'Sestdiena', 'Svētdiena'];
  const acts = [
    { e: '🧹', t: 'Saules paneļu tīrīšana un pārbaude' },
    { e: '🔋', t: 'Bateriju uzlādes optimizācija' },
    { e: '📊', t: 'Enerģijas patēriņa analīze' },
    { e: '☀️', t: 'Maksimāla saules izmantošana' },
    { e: '🛠️', t: 'Invertora pārbaude un apkope' },
    { e: '🌿', t: 'Dārza laistīšana ar saules sūkni' },
    { e: '📈', t: 'Nedēļas atskaite par enerģiju' }
  ];
  
  $('weeklyPlanList').innerHTML = days.map((day, i) => `
    <div class="plan-day">
      <strong>${day}</strong>
      <p>${acts[i % 7].e} ${acts[i % 7].t}</p>
      <p style="font-size:0.75rem;color:#a56b2f;">🌞 Saules stundas: ${8 + (i % 4)}h</p>
    </div>`).join('');
}

function loadGallery() {
  const ids = [10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21];
  $('googlePhotosContainer').innerHTML = `
    <div class="gallery-grid">
      ${ids.map((id, i) => `
        <div class="gallery-item">
          <img src="https://picsum.photos/id/${id}/400/300" alt="Foto ${i+1}">
          <span>Foto ${i+1}</span>
        </div>`).join('')}
    </div>`;
}

async function init() {
  updateDate();
  generatePlan();
  await showWeather();
  await showHourly();
  await showDays();
  loadGallery();
  
  setInterval(() => {
    updateDate();
    showWeather();
    showHourly();
    showDays();
  }, 1800000);
}

document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
