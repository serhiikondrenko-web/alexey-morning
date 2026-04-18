# Навыки и интеграции

---

## Голосовые промпты

### Записи мамы (MP3)
Приоритетный вариант. Мама записывает короткие фразы для каждого шага.

**Скрипт записи:**
```
anchor.mp3    — [двойной мягкий колокол, без слов]
umylsya.mp3   — "Иди умойся"
odelsa.mp3    — "Одевайся"
ryukzak.mp3   — "Собери рюкзак, не забудь еду"
zavtrak.mp3   — "Завтракай"
obulsya.mp3   — "Обувайся"
done.mp3      — "Молодец, всё готово!"
```

**Рекомендации записи:**
- Тихое помещение, телефон близко
- Спокойный нейтральный тон (не строгий, не сюсюкающий)
- Формат MP3, 128kbps достаточно
- Разместить в папке `voice/` репозитория

### Web Speech API (fallback)
Используется если MP3 не загружен.
```javascript
const utter = new SpeechSynthesisUtterance(text);
utter.lang = 'ru-RU';
utter.rate = 0.9;
speechSynthesis.speak(utter);
```

---

## Google Sheets — удалённое управление

### Зачем
Мама может изменить расписание или шаги без доступа к коду — только через таблицу.

### Apps Script
Файл: `setup_shell_config.gs`  
Функция: автозаполнение четырёх листов начальными данными.

### Структура листов

**schedule:**
```
weekday | phase      | start | end
Mon-Fri | morning    | 07:00 | 08:00
Mon-Fri | school     | 08:00 | 17:00
Mon-Fri | home       | 17:00 | 21:00
Sat-Sun | morning    | 08:00 | 10:00
```

**overrides:**
```
date       | mode     | note
2025-01-01 | holiday  | Новый год
2025-03-08 | holiday  | 
```

**steps:**
```
order | id        | label_ru        | duration_min
1     | umylsya   | Умылся          | 5
2     | odelsa    | Оделся          | 5
3     | ryukzak   | Рюкзак с едой   | 3
4     | zavtrak   | Завтрак         | 15
5     | obulsya   | Обулся          | 2
```

**voice:**
```
step_id   | text_ru
umylsya   | Иди умойся
odelsa    | Одевайся
ryukzak   | Собери рюкзак, не забудь еду
zavtrak   | Завтракай
obulsya   | Обувайся
```

### Polling в HTML
```javascript
const SHEET_ID = '1cqW87tHE6TQ0piTmaO6wNjJ-deze8bxA5ZbM3YOPKps';
const API_KEY  = 'YOUR_API_KEY';

async function fetchSchedule() {
  const url = `https://sheets.googleapis.com/v4/spreadsheets/${SHEET_ID}/values/schedule?key=${API_KEY}`;
  const res = await fetch(url);
  const data = await res.json();
  return data.values;
}

setInterval(fetchSchedule, 2 * 60 * 1000); // каждые 2 минуты
```

---

## Yeelight W3 — умная лампа

### Характеристики
- Модель: YLDP006
- Цоколь: E26 (подходит к E27)
- Диапазон: 2700K–6500K (белая, без RGB)
- Протокол: Wi-Fi 2.4GHz + LAN TCP API

### Настройка
1. Подключить через приложение Yeelight
2. Включить **LAN Control** в настройках устройства
3. Зафиксировать IP в роутере (DHCP static lease по MAC)

### Пример вызова из браузера
```javascript
async function yeelightCmd(ip, method, params) {
  const body = JSON.stringify({ id: Date.now(), method, params }) + '\r\n';
  await fetch(`http://${ip}:55443`, {
    method: 'POST',
    mode: 'no-cors',
    headers: { 'Content-Type': 'application/json' },
    body
  });
}

// Примеры
yeelightCmd('192.168.1.209', 'set_power',  ['on', 'smooth', 500]);
yeelightCmd('192.168.1.209', 'set_ct_abx', [2700, 'smooth', 1000]);
yeelightCmd('192.168.1.209', 'set_bright', [30, 'smooth', 1000]);
```

### Ограничения
- Лампа белая — нет RGB, нет красного цвета
- Просрочку сигнализируем яркостью 100% + мигание вместо красного
- no-cors: браузер не читает ответ лампы, только отправляет

---

## GitHub Pages — хостинг

### Зачем
Планшет открывает оболочку по URL без локального сервера.

### Структура репозитория
```
/
├── morning_v2.html          ← основная оболочка
├── yeelight_test.html       ← тестер лампы
├── setup_shell_config.gs    ← Google Apps Script
├── voice/                   ← MP3 файлы голоса мамы
│   ├── anchor.mp3
│   ├── umylsya.mp3
│   └── ...
├── docs/
│   ├── OVERVIEW.md
│   ├── AGENTS.md
│   └── SKILLS.md
└── README.md
```

### Деплой
```bash
git add .
git commit -m "update"
git push
```
Изменения доступны на планшете через ~1 минуту.

---

## Android — киоск-режим (ручной)

**Цель:** планшет показывает только оболочку, без возможности выйти.

**Способ (без root):**
1. Настройки → Специальные возможности → Закрепление экрана
2. Открыть Chrome с `morning_v2.html`
3. Закрепить экран через меню последних приложений

**Ограничение:** ребёнок не может выйти в браузер или другие приложения до выполнения рутины.

**Будущее (Phase 6):** APK который автоматически переключает между оболочкой и школьным приложением по расписанию.
