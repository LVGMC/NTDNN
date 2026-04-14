<!DOCTYPE html>
<html lang="lv">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
  <title>Saules paneļi - Nedēļas plāns ar Google foto integrāciju</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: system-ui, 'Segoe UI', 'Inter', sans-serif;
    }

    body {
      background: linear-gradient(145deg, #e0f2fe 0%, #bae6fd 100%);
      min-height: 100vh;
      padding: 20px;
    }

    .dashboard {
      max-width: 1600px;
      margin: 0 auto;
      display: flex;
      gap: 24px;
      flex-wrap: wrap;
    }

    /* KREISĀ SIDEBAR - nedēļas plāns */
    .weekly-plan {
      width: 320px;
      background: rgba(255, 255, 255, 0.94);
      backdrop-filter: blur(2px);
      border-radius: 2rem;
      box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.2);
      padding: 1.5rem 1.2rem;
      border: 1px solid rgba(255,215,150,0.6);
      height: fit-content;
      order: 1;
    }

    .weekly-plan h2 {
      font-size: 1.8rem;
      font-weight: 700;
      background: linear-gradient(135deg, #f59e0b, #d97706);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
      border-left: 5px solid #f59e0b;
      padding-left: 15px;
      margin-bottom: 20px;
    }

    .plan-list {
      display: flex;
      flex-direction: column;
      gap: 14px;
    }

    .plan-day {
      background: #fef9e6;
      border-radius: 1.5rem;
      padding: 12px 16px;
      border: 1px solid #ffecb3;
    }

    .plan-day strong {
      font-size: 1.1rem;
      color: #b45309;
      display: block;
      border-bottom: 1px dashed #fed7aa;
      margin-bottom: 8px;
    }

    .plan-day p {
      color: #2c3e2f;
      font-size: 0.9rem;
      font-weight: 500;
      display: flex;
      align-items: center;
      gap: 6px;
      margin-top: 6px;
    }

    /* GALVENAIS SATURS */
    .main-content {
      flex: 1;
      min-width: 280px;
      display: flex;
      flex-direction: column;
      gap: 28px;
      order: 2;
    }

    /* AUGŠAS KARTE: laikapstākļi */
    .top-weather-card {
      background: white;
      border-radius: 2rem;
      padding: 1.5rem 2rem;
      box-shadow: 0 12px 25px -10px rgba(0,0,0,0.1);
      border: 1px solid rgba(255,235,170,0.8);
    }

    .date-row {
      display: flex;
      justify-content: space-between;
      align-items: baseline;
      flex-wrap: wrap;
      margin-bottom: 1.2rem;
      padding-bottom: 0.7rem;
      border-bottom: 2px solid #ffe0a3;
    }

    .today-date {
      font-size: 1.7rem;
      font-weight: 800;
      color: #1e3a5f;
    }

    .current-weather {
      display: flex;
      align-items: center;
      gap: 18px;
      flex-wrap: wrap;
      background: #fef7e0;
      padding: 15px 20px;
      border-radius: 1.8rem;
      margin-bottom: 20px;
    }

    .weather-big {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .weather-icon {
      font-size: 3rem;
    }

    .temp {
      font-size: 2.2rem;
      font-weight: 700;
    }

    .hour-cards, .days-forecast {
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
      margin-top: 10px;
    }

    .hour-item, .day-card {
      background: #f9f3e2;
      border-radius: 1.2rem;
      padding: 10px 16px;
      text-align: center;
      flex: 1;
      min-width: 80px;
    }

    /* GOOGLE FOTO GALERIJA */
    .google-photo-gallery {
      background: rgba(255, 248, 225, 0.9);
      border-radius: 2rem;
      padding: 1.5rem;
      backdrop-filter: blur(2px);
      box-shadow: 0 12px 22px rgba(0,0,0,0.05);
    }

    .google-photo-gallery h2 {
      font-size: 1.6rem;
      font-weight: 700;
      color: #b45f06;
      display: flex;
      align-items: center;
      gap: 10px;
      margin-bottom: 20px;
    }

    .gallery-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
      gap: 20px;
      margin-top: 15px;
    }

    .gallery-item {
      background: #fff7e8;
      border-radius: 1rem;
      padding: 10px;
      text-align: center;
      transition: transform 0.3s ease;
      cursor: pointer;
    }

    .gallery-item:hover {
      transform: scale(1.03);
    }

    .gallery-item img {
      width: 100%;
      height: 180px;
      object-fit: cover;
      border-radius: 0.8rem;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }

    .gallery-item span {
      display: block;
      margin-top: 8px;
      font-size: 0.85rem;
      color: #7c4d1e;
    }

    .loading {
      text-align: center;
      padding: 40px;
      font-size: 1.2rem;
      color: #b45309;
    }

    .error-message {
      background: #fee2e2;
      border-left: 4px solid #dc2626;
      padding: 15px;
      border-radius: 1rem;
      margin: 20px 0;
      color: #991b1b;
    }

    @media (max-width: 780px) {
      .dashboard {
        flex-direction: column;
      }
      .weekly-plan {
        width: 100%;
        order: 1;
      }
      .main-content {
        order: 2;
      }
    }
  </style>
</head>
<body>
<div class="dashboard">
  <!-- KREISĀ MALA: NEDĒĻAS PLĀNS -->
  <div class="weekly-plan">
    <h2>📋 Nedēļas plāns</h2>
    <div class="plan-list" id="weeklyPlanList"></div>
  </div>

  <!-- GALVENAIS SATURS -->
  <div class="main-content">
    <!-- Laikapstākļu karte -->
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
    </div>

    <!-- GOOGLE FOTO GALERIJA - MĒĢINĀSIM IELĀDĒT TIEŠOS ATTĒLUS -->
    <div class="google-photo-gallery">
      <h2>📸 Google foto albums (mainās ik pēc 5 minūtēm)</h2>
      <div id="googlePhotosContainer">
        <div class="loading">🖼️ Ielādē attēlus no Google foto albuma...</div>
      </div>
      <p style="text-align:center; margin-top: 12px; font-size:0.8rem; color:#7c4d1e;">
        ⚡ Attēli tiek atjaunoti automātiski ik pēc 5 minūtēm
      </p>
    </div>
  </div>
</div>

<script>
  // GOOGLE FOTO ALBUMA URL (JŪSU NORĀDĪTAIS)
  const GOOGLE_ALBUM_URL = 'https://photos.app.goo.gl/XTHecbbYbnKTNHzy6';
  
  // Saglabāsim atrastās attēlu saites
  let currentImageUrls = [];
  let rotationInterval = null;
  
  // ----------------------------- DATUMS -------------------------
  function updateDate() {
    const now = new Date();
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    const formatted = now.toLocaleDateString('lv-LV', options);
    document.getElementById('currentDate').innerHTML = formatted.charAt(0).toUpperCase() + formatted.slice(1);
  }
  
  // ----------------------------- LAIKAPSTĀKĻI (demo) -----------------
  function getCurrentWeather() {
    const hour = new Date().getHours();
    const weatherOptions = [
      { main: "Saulains", icon: "☀️", temp: 22, feel: 21, desc: "Lieliska diena saules paneļiem" },
      { main: "Daļēji mākoņains", icon: "⛅", temp: 19, feel: 18, desc: "Saule spīd cauri mākoņiem" },
      { main: "Skaidrs", icon: "🌞", temp: 24, feel: 23, desc: "Bez mākoņiem" }
    ];
    let index = Math.floor(hour / 8) % weatherOptions.length;
    return weatherOptions[index];
  }
  
  function displayCurrentWeather() {
    const weather = getCurrentWeather();
    const container = document.getElementById('currentWeatherBlock');
    container.innerHTML = `
      <div class="weather-big">
        <div class="weather-icon">${weather.icon}</div>
        <div class="temp">${weather.temp}°C</div>
        <div class="desc"><strong>${weather.main}</strong><br>${weather.desc}</div>
      </div>
      <div style="margin-left: auto; font-size: 0.9rem;">🌡️ Jūtas kā ${weather.feel}°C</div>
    `;
  }
  
  function get3HourForecast() {
    const baseTemp = getCurrentWeather().temp;
    const forecasts = [];
    for (let i = 1; i <= 3; i++) {
      forecasts.push({
        time: `${(new Date().getHours() + i) % 24}:00`,
        icon: i === 2 ? "⛅" : "☀️",
        temp: baseTemp + (i === 3 ? 2 : 1),
        desc: i === 1 ? "Saule stiprinās" : i === 2 ? "Mākoņu spraugas" : "Maksimālais starojums"
      });
    }
    return forecasts;
  }
  
  function render3Hourly() {
    const hourlyData = get3HourForecast();
    const container = document.getElementById('hourly3h');
    container.innerHTML = hourlyData.map(h => `
      <div class="hour-item">
        <div class="hour-time">${h.time}</div>
        <div style="font-size:1.8rem;">${h.icon}</div>
        <div>${h.temp}°C</div>
        <small>${h.desc}</small>
      </div>
    `).join('');
  }
  
  function renderThreeDays() {
    const days = ['Rīt', 'Parīt', 'Pēc 3 dienām'];
    const container = document.getElementById('threeDaysForecast');
    container.innerHTML = days.map((day, i) => `
      <div class="day-card">
        <strong>${day}</strong>
        <div style="font-size:2rem;">${i === 1 ? "🌤️" : "☀️"}</div>
        <div>${18 + i}° / ${10 + i}°</div>
        <small>${i === 0 ? "Saulains" : i === 1 ? "Mākoņains" : "Skaidrs"}</small>
      </div>
    `).join('');
  }
  
  // ----------------------------- GOOGLE FOTO INTEGRĀCIJA -----------------
  // Šī funkcija mēģina iegūt attēlu saites no Google foto albuma
  // Izmanto CORS starpniekserveri, lai apietu ierobežojumus (pagaidu risinājums)
  async function fetchGooglePhotos() {
    const container = document.getElementById('googlePhotosContainer');
    container.innerHTML = '<div class="loading">🔄 Mēģinu ielādēt Google foto albumu...</div>';
    
    try {
      // Izmantojam CORS proxy, lai varētu piekļūt Google lapas saturam
      // Šis ir publisks CORS proxy demonstrācijai - reālā projektā ieteicams savs serveris
      const proxyUrl = 'https://api.allorigins.win/get?url=' + encodeURIComponent(GOOGLE_ALBUM_URL);
      
      const response = await fetch(proxyUrl);
      const data = await response.json();
      
      if (data && data.contents) {
        // Meklējam attēlu saites HTML saturā
        const html = data.contents;
        
        // Google foto albumos attēli bieži ir šādā formātā:
        // "https://lh3.googleusercontent.com/..." vai "https://lh3.googleusercontent.com/..."
        const imgRegex = /https:\/\/lh3\.googleusercontent\.com\/[a-zA-Z0-9\-_]+/g;
        const matches = html.match(imgRegex);
        
        if (matches && matches.length > 0) {
          // Unikālas saites
          currentImageUrls = [...new Set(matches)];
          
          // Pievienojam parametru, lai iegūtu saprātīgu izmēru
          currentImageUrls = currentImageUrls.map(url => url + '=w400-h300');
          
          if (currentImageUrls.length > 0) {
            displayGooglePhotos();
          } else {
            throw new Error('Nav atrasts neviens attēls');
          }
        } else {
          throw new Error('Neizdevās atrast attēlu saites');
        }
      } else {
        throw new Error('Nevar nolasīt albuma saturu');
      }
    } catch (error) {
      console.error('Kļūda ielādējot Google foto:', error);
      container.innerHTML = `
        <div class="error-message">
          ⚠️ Neizdevās ielādēt attēlus no Google foto albuma.<br>
          <strong>Iespējamie iemesli:</strong><br>
          • Albums var būt privāts vai nav publiski pieejams<br>
          • Google bloķē pieprasījumus no citām vietnēm (CORS politika)<br>
          • <strong>Risinājums:</strong> Lūdzu, pārliecinieties, ka albums ir publiski pieejams (koplietošanas iestatījumi)<br><br>
          <small>🖼️ Alternatīvi - izmantoju demonstrācijas attēlus</small>
        </div>
        <div class="gallery-grid" id="fallbackGallery"></div>
      `;
      // Pievienojam alternatīvos attēlus, lai galerija nebūtu tukša
      showFallbackImages();
    }
  }
  
  function displayGooglePhotos() {
    const container = document.getElementById('googlePhotosContainer');
    if (currentImageUrls.length === 0) {
      container.innerHTML = '<div class="loading">❌ Nav atrasts neviens attēls albumā</div>';
      return;
    }
    
    // Parādām pirmos 12 attēlus (lai nepārslogotu)
    const imagesToShow = currentImageUrls.slice(0, 12);
    
    const galleryHtml = `
      <div class="gallery-grid">
        ${imagesToShow.map((url, index) => `
          <div class="gallery-item" onclick="window.open('${GOOGLE_ALBUM_URL}', '_blank')">
            <img src="${url}" alt="Google foto ${index + 1}" loading="lazy" onerror="this.src='https://picsum.photos/400/300?random=${index}'">
            <span>📸 Foto ${index + 1}</span>
          </div>
        `).join('')}
      </div>
    `;
    
    container.innerHTML = galleryHtml;
  }
  
  function showFallbackImages() {
    const fallbackContainer = document.getElementById('fallbackGallery');
    if (fallbackContainer) {
      // Alternatīvi attēli ar saules tēmu
      const fallbackImages = [
        'https://picsum.photos/id/29/400/300',  // saulains ainava
        'https://picsum.photos/id/30/400/300',  // saulriets
        'https://picsum.photos/id/116/400/300', // saule pār kalniem
        'https://picsum.photos/id/155/400/300', // saules stari
        'https://picsum.photos/id/158/400/300', // saulriets pilsētā
        'https://picsum.photos/id/169/400/300'  // saulains lauks
      ];
      
      fallbackContainer.innerHTML = fallbackImages.map((url, i) => `
        <div class="gallery-item">
          <img src="${url}" alt="Alternatīvs attēls ${i+1}">
          <span>🌞 Saules tēmas foto</span>
        </div>
      `).join('');
    }
  }
  
  // Atjauno Google foto attēlus
  function refreshGooglePhotos() {
    fetchGooglePhotos();
  }
  
  // ----------------------------- NEDĒĻAS PLĀNS -----------------
  function generateWeeklyPlan() {
    const weekDays = ['Pirmdiena', 'Otrdiena', 'Trešdiena', 'Ceturtdiena', 'Piektdiena', 'Sestdiena', 'Svētdiena'];
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
    let html = '';
    for (let i = 0; i < weekDays.length; i++) {
      const activity = activities[i % activities.length];
      html += `
        <div class="plan-day">
          <strong>${weekDays[i]}</strong>
          <p><span class="emoji">${activity.emoji}</span> ${activity.text}</p>
          <p style="font-size:0.75rem; color:#a56b2f;">🌞 Saules stundas: ${8 + (i % 4)}h</p>
        </div>
      `;
    }
    planContainer.innerHTML = html;
  }
  
  // ----------------------------- INICIALIZĀCIJA -----------------
  function init() {
    updateDate();
    displayCurrentWeather();
    render3Hourly();
    renderThreeDays();
    generateWeeklyPlan();
    
    // Ielādē Google foto attēlus
    fetchGooglePhotos();
    
    // Atjauno ik pēc 5 minūtēm
    setInterval(() => {
      refreshGooglePhotos();
    }, 300000); // 5 minūtes
    
    // Laikapstākļu atjaunošana
    setInterval(() => {
      displayCurrentWeather();
      render3Hourly();
      renderThreeDays();
      updateDate();
    }, 3600000);
  }
  
  init();
</script>
</body>
</html>
