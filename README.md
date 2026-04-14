<!DOCTYPE html>
<html lang="lv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes" />
  <title>Saules paneļi - Nedēļas plāns ar Google foto integrāciju</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: system-ui, 'Segoe UI', 'Inter', sans-serif; }
    body { background: linear-gradient(145deg, #e0f2fe 0%, #bae6fd 100%); min-height: 100vh; padding: 20px; }
    .dashboard { max-width: 1600px; margin: 0 auto; display: flex; gap: 24px; flex-wrap: wrap; }
    .weekly-plan { width: 320px; background: rgba(255, 255, 255, 0.94); backdrop-filter: blur(2px); border-radius: 2rem; box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.2); padding: 1.5rem 1.2rem; border: 1px solid rgba(255,215,150,0.6); height: fit-content; order: 1; }
    .weekly-plan h2 { font-size: 1.8rem; font-weight: 700; background: linear-gradient(135deg, #f59e0b, #d97706); -webkit-background-clip: text; background-clip: text; color: transparent; border-left: 5px solid #f59e0b; padding-left: 15px; margin-bottom: 20px; }
    .plan-list { display: flex; flex-direction: column; gap: 14px; }
    .plan-day { background: #fef9e6; border-radius: 1.5rem; padding: 12px 16px; border: 1px solid #ffecb3; }
    .plan-day strong { font-size: 1.1rem; color: #b45309; display: block; border-bottom: 1px dashed #fed7aa; margin-bottom: 8px; }
    .plan-day p { color: #2c3e2f; font-size: 0.9rem; font-weight: 500; display: flex; align-items: center; gap: 6px; margin-top: 6px; }

    .main-content { flex: 1; min-width: 280px; display: flex; flex-direction: column; gap: 28px; order: 2; }

    .top-weather-card { background: white; border-radius: 2rem; padding: 1.5rem 2rem; box-shadow: 0 12px 25px -10px rgba(0,0,0,0.1); border: 1px solid rgba(255,235,170,0.8); }
    .date-row { display: flex; justify-content: space-between; align-items: baseline; flex-wrap: wrap; margin-bottom: 1.2rem; padding-bottom: 0.7rem; border-bottom: 2px solid #ffe0a3; }
    .today-date { font-size: 1.7rem; font-weight: 800; color: #1e3a5f; }
    .current-weather { display: flex; align-items: center; gap: 18px; flex-wrap: wrap; background: #fef7e0; padding: 15px 20px; border-radius: 1.8rem; margin-bottom: 20px; }
    .weather-big { display: flex; align-items: center; gap: 12px; }
    .weather-icon { font-size: 3rem; }
    .temp { font-size: 2.2rem; font-weight: 700; }
    .hour-cards, .days-forecast { display: flex; flex-wrap: wrap; gap: 15px; margin-top: 10px; }
    .hour-item, .day-card { background: #f9f3e2; border-radius: 1.2rem; padding: 10px 16px; text-align: center; flex: 1; min-width: 80px; }

    .google-photo-gallery { background: rgba(255, 248, 225, 0.9); border-radius: 2rem; padding: 1.5rem; backdrop-filter: blur(2px); box-shadow: 0 12px 22px rgba(0,0,0,0.05); }
    .google-photo-gallery h2 { font-size: 1.6rem; font-weight: 700; color: #b45f06; display: flex; align-items: center; gap: 10px; margin-bottom: 20px; }
    .gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 20px; margin-top: 15px; }
    .gallery-item { background: #fff7e8; border-radius: 1rem; padding: 10px; text-align: center; transition: transform 0.3s ease; cursor: pointer; }
    .gallery-item:hover { transform: scale(1.03); }
    .gallery-item img { width: 100%; height: 180px; object-fit: cover; border-radius: 0.8rem; box-shadow: 0 4px 8px rgba(0,0,0,0.1); }
    .gallery-item span { display: block; margin-top: 8px; font-size: 0.85rem; color: #7c4d1e; }

    .loading { text-align: center; padding: 40px; font-size: 1.2rem; color: #b45309; }
    .error-message { background: #fee2e2; border-left: 4px solid #dc2626; padding: 15px; border-radius: 1rem; margin: 20px 0; color: #991b1b; }

    .debug {
      margin-top: 10px;
      font-size: 0.85rem;
      color: #1f2937;
      background: rgba(0,0,0,0.04);
      padding: 10px 12px;
      border-radius: 12px;
      overflow-wrap: anywhere;
    }

    @media (max-width: 780px) {
      .dashboard { flex-direction: column; }
      .weekly-plan { width: 100%; order: 1; }
      .main-content { order: 2; }
    }
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
          <div class="today-date" id="currentDate"></div>
          <div style="font-size: 0.9rem; background:#f0e5d2; padding: 5px 12px; border-radius: 40px;">🌍 Saules aktivitāte</div>
        </div>

        <div class="current-weather" id="currentWeatherBlock"></div>

        <div style="margin-top: 15px;">
          <h3>⏱️ Prognoze nākamajām 3 stundām</h3>
          <div class="hour-cards" id="hourly3h"></div>
        </div>

        <div style="margin-top: 20px;">
          <h3>📅 Nākamās 3 dienas</h3>
          <div class="days-forecast" id="threeDaysForecast"></div>
        </div>

        <div id="weatherDebug" class="debug" style="display:none;"></div>
      </div>

      <div class="google-photo-gallery">
        <h2>📸 Google foto albums (mainās ik pēc 5 minūtēm)</h2>
        <div id="googlePhotosContainer">
          <div class="loading">🖼️ Ielādē attēlus no Google foto albuma...</div>
        </div>
        <p style="text-align:center; margin-top: 12px; font-size:0.8rem; color:#7c4d1e;">
          ⚡ Attēli tiek atjaunoti automātiski ik pēc 5 minūtēm
        </p>
        <div id="photosDebug" class="debug" style="display:none;"></div>
      </div>
    </div>
  </div>

<script>
  // ==================== OPENWEATHERMAP KONFIGURĀCIJA ====================
  const API_KEY = 'ef8adfe42305ec8cf0e57ddfbad6cdbb';
  const LAT = 56.9496;
  const LON = 24.1052;

  // GOOGLE FOTO ALBUMA URL
  const GOOGLE_ALBUM_URL = 'https://photos.app.goo.gl/XTHecbbYbnKTNHzy6';

  let currentImageUrls = [];

  const weatherDebugEl = document.getElementById('weatherDebug');
  const photosDebugEl = document.getElementById('photosDebug');

  function setDebug(el, text, show = true) {
    if (!el) return;
    el.textContent = text;
    el.style.display = show ? 'block' : 'none';
  }

  function updateDate() {
    const now = new Date();
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    const formatted = now.toLocaleDateString('lv-LV', options);
    document.getElementById('currentDate').textContent =
      formatted.charAt(0).toUpperCase() + formatted.slice(1);
  }

  function getWeatherIconUrl(iconCode) {
    return `https://openweathermap.org/img/wn/${iconCode}@2x.png`;
  }

  async function safeFetchJson(url) {
    const res = await fetch(url, { cache: "no-store" });
    let bodyText = "";
    try { bodyText = await res.text(); } catch {}
    let json = null;
    try { json = JSON.parse(bodyText); } catch {}
    return { ok: res.ok, status: res.status, json, text: bodyText };
  }

  async function fetchCurrentWeather() {
    // ✅ SVARĪGI: te ir & nevis &amp;
    const url = `https://api.openweathermap.org/data/2.5/weather?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
    const result = await safeFetchJson(url);
    setDebug(weatherDebugEl, `OpenWeather /weather status: ${result.status}`, true);

    if (!result.ok) {
      console.error("OpenWeather /weather error:", result.status, result.text);
      return null;
    }
    return result.json;
  }

  async function fetchHourlyForecast() {
    const url = `https://api.openweathermap.org/data/2.5/forecast?lat=${LAT}&lon=${LON}&appid=${API_KEY}&units=metric&lang=lv`;
    const result = await safeFetchJson(url);
    setDebug(weatherDebugEl, `OpenWeather /weather status: ok | /forecast status: ${result.status}`, true);

    if (!result.ok) {
      console.error("OpenWeather /forecast error:", result.status, result.text);
      return null;
    }
    return result.json;
  }

  async function displayCurrentWeather() {
    const container = document.getElementById('currentWeatherBlock');
    container.innerHTML = '<div class="loading">🌤️ Ielādē laikapstākļus...</div>';

    const weather = await fetchCurrentWeather();
    if (!weather) {
      container.innerHTML = `
        <div class="error-message">
          ❌ Nevar ielādēt laikapstākļus.<br>
          <strong>Padoms:</strong> atver DevTools → Console/Network un paskaties vai nav 401 (API key) vai 429 (limiti).<br>
          <small>Status redzams zemāk debug blokā.</small>
        </div>`;
      return;
    }

    const temp = Math.round(weather.main.temp);
    const feelsLike = Math.round(weather.main.feels_like);
    const description = weather.weather[0].description;
    const iconCode = weather.weather[0].icon;
    const main = weather.weather[0].main;

    let solarTip = "";
    if (main === "Clear") solarTip = "☀️ Lieliska diena saules paneļiem!";
    else if (main === "Clouds") solarTip = "⛅ Daļēja saules enerģija - paneļi strādās ar vidēju efektivitāti";
    else if (main === "Rain") solarTip = "🌧️ Lietus diena - saules paneļu ražība būs zema";
    else if (main === "Snow") solarTip = "❄️ Sniegs - attīriet paneļus, lai uzlabotu efektivitāti";
    else solarTip = "🌍 Normāli apstākļi saules paneļiem";

    container.innerHTML = `
      <div class="weather-big">
        <div class="weather-icon">
          <imgtWeatherIconUrl(iconCode)}
        </div>
        <div class="temp">${temp}°C</div>
        <div class="desc">
          <strong>${description.charAt(0).toUpperCase() + description.slice(1)}</strong><br>${solarTip}
        </div>
      </div>
      <div style="margin-left: auto; font-size: 0.9rem;">
        🌡️ Jūtas kā ${feelsLike}°C<br>
        💧 Mitrums: ${weather.main.humidity}%<br>
        💨 Vējš: ${Math.round(weather.wind.speed)} m/s
      </div>
    `;
  }

  async function render3Hourly() {
    const container = document.getElementById('hourly3h');
    container.innerHTML = '<div class="loading">⏳ Ielādē prognozi...</div>';

    const forecast = await fetchHourlyForecast();
    if (!forecast || !forecast.list) {
      container.innerHTML = '<div class="error-message">❌ Nevar ielādēt stundu prognozi</div>';
      return;
    }

    const nextForecasts = forecast.list.slice(0, 3);
    container.innerHTML = nextForecasts.map(item => {
      const time = new Date(item.dt * 1000);
      const hours = String(time.getHours()).padStart(2, "0");
      const temp = Math.round(item.main.temp);
      const iconCode = item.weather[0].icon;
      const desc = item.weather[0].description;

      return `
        <div class="hour-item">
          <div class="hour-time">${hours}:00</div>
          <div style="font-size:2rem;">
            <img src="${getWeatherIconUrl(iconCode)}"</div>
          <div>${temp}°C</div>
          <small>${desc.substring(0, 18)}</small>
        </div>
      `;
    }).join('');
  }

  async function renderThreeDays() {
    const container = document.getElementById('threeDaysForecast');
    container.innerHTML = '<div class="loading">📅 Ielādē dienu prognozi...</div>';

    const forecast = await fetchHourlyForecast();
    if (!forecast || !forecast.list) {
      container.innerHTML = '<div class="error-message">❌ Nevar ielādēt dienu prognozi</div>';
      return;
    }

    const dailyData = {};
    forecast.list.forEach(item => {
      const date = new Date(item.dt * 1000);
      const dayKey = date.toLocaleDateString('lv-LV');
      if (!dailyData[dayKey]) dailyData[dayKey] = { temps: [], icons: [] };
      dailyData[dayKey].temps.push(item.main.temp);
      dailyData[dayKey].icons.push(item.weather[0].icon);
    });

    const days = Object.keys(dailyData).slice(1, 4);
    const dayNames = ['Rīt', 'Parīt', 'Pēc 3 dienām'];

    container.innerHTML = days.map((day, index) => {
      const data = dailyData[day];
      const avgTemp = Math.round(data.temps.reduce((a,b)=>a+b,0) / data.temps.length);
      const minTemp = Math.round(Math.min(...data.temps));
      const maxTemp = Math.round(Math.max(...data.temps));
      const icon = data.icons[Math.floor(data.icons.length / 2)];

      return `
        <div class="day-card">
          <strong>${dayNames[index]}</strong>
          <div style="font-size:2rem;">
            <img srctherIconUrl(icon)}
          </div>
          <div>${maxTemp}° / ${minTemp}°</div>
          <small>Vid: ${avgTemp}°C</small>
        </div>
      `;
    }).join('');
  }

  // ----------------------------- GOOGLE FOTO (BEST EFFORT + FALLBACK) -----------------
  async function fetchGooglePhotos() {
    const container = document.getElementById('googlePhotosContainer');
    container.innerHTML = '<div class="loading">🔄 Mēģinu ielādēt Google foto albumu...</div>';
    setDebug(photosDebugEl, '', false);

    try {
      // allorigins var mainīties / limitēt — bet atstājam "best effort"
      const proxyUrl = 'https://api.allorigins.win/get?url=' + encodeURIComponent(GOOGLE_ALBUM_URL);
      const res = await fetch(proxyUrl, { cache: "no-store" });
      const payload = await res.json();

      if (!payload || !payload.contents) throw new Error('Proxy neatgrieza contents (iespējams rate-limit).');

      const html = payload.contents;

      // Uzlabots patterns: bieži ir lh3.googleusercontent.com/p/... vai /...
      const imgRegex = /https:\/\/lh3\.googleusercontent\.com\/[^\s"'<>\\]+/g;
      const matches = html.match(imgRegex);

      if (!matches || matches.length === 0) {
        setDebug(photosDebugEl,
          "Google Photos: HTML ielādējās, bet attēlu URL neatradu. Tas ir normāli, ja Google iedod JS shell/consent lapu. Iesaku API vai Drive publisku mapi.",
          true
        );
        throw new Error('Neizdevās atrast attēlu saites albumā.');
      }

      currentImageUrls = [...new Set(matches)]
        .map(u => u.split('=')[0] + '=w400-h300'); // normalizējam

      displayGooglePhotos();
      setDebug(photosDebugEl, `Google Photos: atrasti ${currentImageUrls.length} URL (rādu pirmos 12).`, true);

    } catch (error) {
      console.error('Kļūda ielādējot Google foto:', error);

      container.innerHTML = `
        <div class="error-message">
          ⚠️ Neizdevās ielādēt attēlus no Google foto albuma.<br>
          <strong>Kāpēc tā var būt:</strong><br>
          • Google albumu HTML bieži vairs nesatur tiešus attēlu URL<br>
          • Proxy (allorigins) var bloķēt pieprasījumu (rate limit)<br>
          • Albums var būt privāts / vajag “public link” bez ierobežojumiem<br><br>
          <small>🖼️ Zemāk rādās fallback demonstrācijas attēli</small>
        </div>
        <div class="gallery-grid" id="fallbackGallery"></div>
      `;
      setDebug(photosDebugEl, `Kļūda: ${String(error.message || error)}`, true);
      showFallbackImages();
    }
  }

  function displayGooglePhotos() {
    const container = document.getElementById('googlePhotosContainer');
    if (!currentImageUrls.length) {
      container.innerHTML = '<div class="loading">❌ Nav atrasts neviens attēls albumā</div>';
      return;
    }

    const imagesToShow = currentImageUrls.slice(0, 12);
    container.innerHTML = `
      <div class="gallery-grid">
        ${imagesToShow.map((url, index) => `
          <div class="gallery-item" onclick="window.open('${GOOGLE_ALBUM_URL}', '_blank')">
            <img src="${url}" alt="Google foto ${index + 1}" loading="lazy"
                 onerror="this.src='https://picsum.photos/400/300?random=${index}') {
    const fallbackContainer = document.getElementById('fallbackGallery');
    if (!fallbackContainer) return;

    const fallbackImages = [
      'https://picsum.photos/id/29/400/300',
      'https://picsum.photos/id/30/400/300',
      'https://picsum.photos/id/116/400/300',
      'https://picsum.photos/id/155/400/300',
      'https://picsum.photos/id/158/400/300',
      'https://picsum.photos/id/169/400/300'
    ];

    fallbackContainer.innerHTML = fallbackImages.map((url, i) => `
      <div class="gallery-item">
        ${url}
        <span>🌞 Saules tēmas foto</span>
      </div>
    `).join('');
  }

  function generateWeeklyPlan() {
    const weekDays = ['Pirmdiena','Otrdiena','Trešdiena','Ceturtdiena','Piektdiena','Sestdiena','Svētdiena'];
    const activities = [
      { emoji: "🧹", text: "Saules paneļu tīrīšana un pārbaude" },
      { emoji: "🔋", text: "Bateriju uzlādes optimizācija" },
      { emoji: "📊", text: "Enerģijas patēriņa analīze" },
      { emoji: "☀️", text: "Maksimāla saules izmantošana" },
      { emoji: "🛠️", text: "Invertora pārbaude un apkope" },
      { emoji: "🌿", text: "Dārza laistīšana ar saules sūkni" },
      { emoji: "📈", text: "Nedēļas atskaite par enerģiju" }
    ];

    const planContainer = document.getElementById('weeklyPlanList');
    planContainer.innerHTML = weekDays.map((d, i) => {
      const a = activities[i % activities.length];
      return `
        <div class="plan-day">
          <strong>${d}</strong>
          <p><span class="emoji">${a.emoji}</span> ${a.text}</p>
          <p style="font-size:0.75rem; color:#a56b2f;">🌞 Saules stundas: ${8 + (i % 4)}h</p>
        </div>
      `;
    }).join('');
  }

  async function init() {
    updateDate();
    await displayCurrentWeather();
    await render3Hourly();
    await renderThreeDays();
    generateWeeklyPlan();

    fetchGooglePhotos();

    setInterval(fetchGooglePhotos, 300000);
    setInterval(async () => {
      updateDate();
      await displayCurrentWeather();
      await render3Hourly();
      await renderThreeDays();
    }, 1800000);
  }

  document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
