# Ассеты — источники и лицензии

Все сторонние ассеты используются в `versions/miner-clicker-v3.html`.

## Спрайты — `assets/sprites/`

**Источник:** Kenney (www.kenney.nl), паки **"Roguelike Caves & Dungeons"** и
**"Roguelike/RPG pack"**.
**Лицензия:** CC0 1.0 Universal (public domain) — https://creativecommons.org/publicdomain/zero/1.0/
**Автор:** Kenney Vleugels — атрибуция не обязательна, но указывается из вежливости.

Из спрайт-листов вырезаны отдельные тайлы 16×16 (масштабируются в игре как
пиксель-арт, `image-rendering: pixelated`, дополнительно тонируются CSS-фильтрами
под палитру локации/редкости):

| Файл | Источник (паке / тайл) | Использование в игре |
|---|---|---|
| `pickaxe_a.png` | Roguelike Caves & Dungeons | иконка апгрейда кирки на карте |
| `ore_purple.png`, `ore_gold.png`, `ore_silver.png`, `ore_orange.png`, `ore_green.png` | Roguelike Caves & Dungeons | прожилки руды на самой руде — по одной на локацию (`LOCATIONS[i].ore`) |
| `rock_chunk_a.png`, `rock_chunk_b.png`, `rock_chunk_c.png` | Roguelike Caves & Dungeons | осколки в пылевом взрыве при ударе киркой |
| `torch_lit_a.png`, `torch_lit_b.png` | Roguelike Caves & Dungeons | факелы в шапке интерфейса |
| `rockpile_b.png` | Roguelike/RPG pack | декоративный завал у подножия руды |
| `figure_dark.png`, `figure_hood.png` | Roguelike/RPG pack | силуэты шахтёров в рулетке гачи |

Скачаны, но не задействованы в текущей версии (лежат про запас на будущее —
`lantern_off.png`, `pickaxe_b.png`, `rockpile_a.png`, `rockpile_c.png`,
`ore_coal.png`, `ore_cream.png`, `ore_rust.png`).

Источники паков:
- https://kenney.nl/assets/roguelike-caves-dungeons
- https://kenney.nl/assets/roguelike-rpg-pack

## Звуки — `assets/sounds/`

**Источник:** Kenney (www.kenney.nl), паки **"Impact Sounds"** и **"Interface Sounds"**.
**Лицензия:** CC0 1.0 Universal — https://creativecommons.org/publicdomain/zero/1.0/

| Файл | Использование |
|---|---|
| `impactMining_000.ogg` … `impactMining_004.ogg` | удар киркой по руде (случайный выбор из 5) |
| `impactSoft_heavy_000.ogg` | глухой стук — появление сундука |
| `open_001.ogg`, `open_002.ogg` | открытие сундука |
| `close_002.ogg` | скрип/щелчок крышки |
| `tick_001.ogg`, `tick_002.ogg`, `tick_004.ogg` | тики рулетки гачи (замедляющиеся) |
| `confirmation_002.ogg` | подтверждение при получении шахтёра |
| `impactBell_heavy_000.ogg`, `impactBell_heavy_001.ogg` | колокол при выпадении легендарного шахтёра |

Источники паков:
- https://kenney.nl/assets/impact-sounds
- https://kenney.nl/assets/interface-sounds

## Шрифты

**Источник:** Google Fonts (fonts.googleapis.com), подключены как веб-шрифты
(не хранятся локально в `/assets/fonts`).
**Лицензия:** SIL Open Font License 1.1.

| Шрифт | Использование |
|---|---|
| Pirata One | заголовки, гравюрный/готический стиль |
| IM Fell English / IM Fell English SC | основной текст карт, состаренная типографика |

## Ассеты, добавленные пользователем (не найдены в этой сессии)

Пользователь упомянул 4 локальных файла, которые не попали в репозиторий:
`Pick Hitting Rock.wav`, `pixeland.PNG`, `roguelikeDungeon_magenta.png`,
`roguelikeDungeon_transparent.png`. Последние два — это в точности спрайт-лист
пака Kenney "Roguelike Caves & Dungeons" (см. выше, уже используется через
вырезанные тайлы). `Pick Hitting Rock.wav` и `pixeland.PNG` не идентифицированы;
при добавлении их в репозиторий эту таблицу нужно дополнить.

## Всё остальное — процедурная генерация

Текстуры локаций и руды, трещины, пыль/частицы (кроме осколков `rock_chunk_*`),
эффект зерна/виньетки, сургучные печати редкости, гравюрные контуры кирки/руды —
сгенерированы кодом (canvas 2D + value noise) или нарисованы как SVG/CSS
непосредственно в `miner-clicker-v3.html`, сторонних ассетов не используют.
