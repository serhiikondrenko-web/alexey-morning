# Morning Shell — Система утреннего режима

Планшетная оболочка для автономного выполнения утренней рутины ребёнком (13–14 лет) с атипичным нейроразвитием. Заменяет взрослое промптирование, постепенно формируя самостоятельность.

## Файлы проекта

| Файл | Назначение |
|---|---|
| `morning_v2.html` | Основная оболочка (запускается на планшете) |
| `yeelight_test.html` | Тестер управления лампой Yeelight W3 |
| `setup_shell_config.gs` | Google Apps Script для настройки Google Sheets |
| `docs/OVERVIEW.md` | Архитектура и принципы системы |
| `docs/AGENTS.md` | Агентные компоненты и логика поведения |
| `docs/SKILLS.md` | Навыки и интеграции |

## Быстрый старт

1. Открыть `morning_v2.html` на Android-планшете в Chrome
2. Убедиться что планшет и лампа Yeelight в одной Wi-Fi сети
3. В приложении Yeelight включить **LAN Control**
4. Для настройки расписания — открыть Google Sheets (ID ниже)

## Google Sheets

Spreadsheet ID: `1cqW87tHE6TQ0piTmaO6wNjJ-deze8bxA5ZbM3YOPKps`

Листы: `schedule` · `overrides` · `steps` · `voice`

## Лампа

Yeelight W3 · Модель YLDP006 · Wi-Fi 2.4GHz · LAN API порт 55443  
IP в домашней сети: `192.168.1.209` (зафиксировать в роутере через DHCP static)
