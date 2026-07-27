<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Трекер: Виктор Корнеплод и Анастасия Землеройка</title>
  
  <!-- Подключаем стили Leaflet -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #121212; color: #fff; display: flex; flex-direction: column; height: 100vh; }
    
    #header { padding: 12px 20px; background: #1e1e24; display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #333; }
    #header h1 { font-size: 1.1rem; font-weight: 600; color: #4dabf7; }
    .status-badge { background: #2b8a3e; padding: 4px 10px; border-radius: 12px; font-size: 0.8rem; font-weight: bold; animation: pulse 2s infinite; }
    
    @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.4; } 100% { opacity: 1; } }
    
    #map { flex: 1; width: 100%; }
    
    #panel { padding: 15px; background: #1e1e24; display: grid; grid-template-columns: 1fr 1fr; gap: 15px; border-top: 2px solid #333; }
    .card { background: #2a2a32; padding: 12px; border-radius: 8px; border-left: 4px solid #ccc; }
    .card.viktor { border-color: #ff922b; }
    .card.anastasia { border-color: #cc5de8; }
    .card h3 { font-size: 0.95rem; margin-bottom: 6px; }
    .card p { font-size: 0.85rem; color: #aaa; margin: 3px 0; }
    .coords { font-family: monospace; color: #ffd43b; }

    /* Кастомные значки меток */
    .custom-icon {
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
      background: rgba(30, 30, 36, 0.9);
      border: 2px solid #fff;
      border-radius: 50%;
      box-shadow: 0 4px 10px rgba(0,0,0,0.5);
    }
  </style>
</head>
<body>

  <div id="header">
    <h1>🛰️ Спутниковый трекер «Корнеплод-1»</h1>
    <span class="status-badge">● LIVE</span>
  </div>

  <div id="map"></div>

  <div id="panel">
    <div class="card viktor">
      <h3>🥕 Виктор Корнеплод</h3>
      <p>Статус: <span id="viktor-status">Погружение в грунт</span></p>
      <p>Координаты: <span id="viktor-coords" class="coords">--.--, --.--</span></p>
      <p>Скорость: <span id="viktor-speed">0</span> км/ч</p>
    </div>
    <div class="card anastasia">
      <h3>🐾 Анастасия Землеройка</h3>
      <p>Статус: <span id="anastasia-status">Прокладывание тоннеля</span></p>
      <p>Координаты: <span id="anastasia-coords" class="coords">--.--, --.--</span></p>
      <p>Скорость: <span id="anastasia-speed">0</span> км/ч</p>
    </div>
  </div>

  <!-- Подключаем скрипт Leaflet -->
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script>
    // Начальные координаты
    let viktorPos = [50.4501, 30.5234];
    let anastasiaPos = [50.4540, 30.5310];

    // Инициализация карты
    const map = L.map('map').setView(viktorPos, 14);

    // Слой карты OpenStreetMap
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19,
      attribution: '© OpenStreetMap'
    }).addTo(map);

    // Создаем кастомные маркеры с эмодзи
    const viktorIcon = L.divIcon({ className: 'custom-icon', html: '🥕', iconSize: [36, 36] });
    const anastasiaIcon = L.divIcon({ className: 'custom-icon', html: '🐾', iconSize: [36, 36] });

    const viktorMarker = L.marker(viktorPos, { icon: viktorIcon }).addTo(map)
      .bindPopup('<b>Виктор Корнеплод</b><br>Зафиксирован всплеск мудрости.');

    const anastasiaMarker = L.marker(anastasiaPos, { icon: anastasiaIcon }).addTo(map)
      .bindPopup('<b>Анастасия Землеройка</b><br>Активно копает.');

    // Линии следа движения
    const viktorTrail = L.polyline([viktorPos], { color: '#ff922b', weight: 3, opacity: 0.7 }).addTo(map);
    const anastasiaTrail = L.polyline([anastasiaPos], { color: '#cc5de8', weight: 3, opacity: 0.7 }).addTo(map);

    // Список случайных статусов
    const viktorActions = [
      "Впитывает минералы из почвы", "Совершает неожиданное действие", 
      "Слушает звон колоколов", "Медитирует под дубом", "Делится мудростью"
    ];

    const anastasiaActions = [
      "Рычит на кротов", "Скоростное копание", "Обходит подземный кабель", 
      "Ищет корешки", "Затаилась в норе"
    ];

    // Функция обновления данных
    function updateLocations() {
      const move = (pos) => [
        pos[0] + (Math.random() - 0.5) * 0.0012,
        pos[1] + (Math.random() - 0.5) * 0.0012
      ];

      viktorPos = move(viktorPos);
      anastasiaPos = move(anastasiaPos);

      // Обновляем положение меток и линий
      viktorMarker.setLatLng(viktorPos);
      viktorTrail.addLatLng(viktorPos);

      anastasiaMarker.setLatLng(anastasiaPos);
      anastasiaTrail.addLatLng(anastasiaPos);

      // Обновление карточек
      document.getElementById('viktor-coords').textContent = `${viktorPos[0].toFixed(4)}, ${viktorPos[1].toFixed(4)}`;
      document.getElementById('anastasia-coords').textContent = `${anastasiaPos[0].toFixed(4)}, ${anastasiaPos[1].toFixed(4)}`;

      document.getElementById('viktor-speed').textContent = (Math.random() * 4 + 1).toFixed(1);
      document.getElementById('anastasia-speed').textContent = (Math.random() * 10 + 2).toFixed(1);

      if (Math.random() > 0.5) {
        document.getElementById('viktor-status').textContent = viktorActions[Math.floor(Math.random() * viktorActions.length)];
      }
      if (Math.random() > 0.5) {
        document.getElementById('anastasia-status').textContent = anastasiaActions[Math.floor(Math.random() * anastasiaActions.length)];
      }
    }

    // Автообновление каждые 2.5 секунды
    setInterval(updateLocations, 2500);
    updateLocations();
  </script>
</body>
</html>

