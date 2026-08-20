# Ассеты — источники и лицензии

Все сторонние ассеты используются в `versions/miner-clicker-v3.html` — с редизайна
под single-file (см. CLAUDE.md, "Updating v3's embedded assets") они зашиты прямо
в HTML как base64 (`SPRITES`/`SOUNDS` в начале `<script>`), а файлы под `assets/`
ниже остаются исходниками для справки/пересборки, а не тем, что грузится в браузере.

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
| `rock_chunk_a.png`, `rock_chunk_b.png`, `rock_chunk_c.png` | Roguelike Caves & Dungeons | осколки в пылевом взрыве при ударе киркой |
| `torch_lit_a.png`, `torch_lit_b.png` | Roguelike Caves & Dungeons | факелы в шапке интерфейса |
| `rockpile_b.png` | Roguelike/RPG pack | декоративный завал у подножия руды |

Скачаны, но не задействованы в текущей версии (лежат про запас на будущее —
`lantern_off.png`, `pickaxe_b.png`, `rockpile_a.png`, `rockpile_c.png`,
`ore_coal.png`, `ore_cream.png`, `ore_rust.png`). Сюда же теперь попали
`pickaxe_a.png`, `ore_purple/gold/silver/orange/green.png`, `figure_dark.png`,
`figure_hood.png` — раньше использовались как иконка кирки в карточке апгрейда,
прожилки-акценты на руде и силуэты в рулетке гачи, но были заменены на
нарисованные SVG (см. ниже) как более узнаваемые, чем 16×16 пиксель-арт. Base64
этих файлов остался зашит в `SPRITES` (см. CLAUDE.md), но код его больше не
читает.

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

## Фоны локаций и иконки — нарисованный SVG, не подобранные ассеты

При запросе на "настоящие узнаваемые фоны/иконки" был доступ в интернет
(проверено — kenney.nl и opengameart.org отвечали), но вместо подбора готовых
паков под каждую из 5 локаций фоны и часть иконок нарисованы вручную как SVG
прямо в коде (`generateLocationArt()` и хелперы `svgCrystal`/`svgVein`/
`svgIcicle`/`svgCrack`/`svgStarField`/`svgAsteroid`, плюс `PICKAXE_ICON_URL`/
`gemIconUrl`/`minerIconUrl` для иконок). Причина — гарантированная стилевая
целостность: пять фонов, собранных из разных сторонних паков, почти наверняка
разъехались бы по манере рисовки (или их пришлось бы долго перекрашивать/
дотягивать под палитру карточек), тогда как единая ручная система гарантирует
одинаковый "гравюрный" язык на всех локациях и сразу отвечает описанию
(грани кристаллов у пещеры, ветвящиеся золотые жилы с самородками, свисающие/
растущие сосульки у льда, сеть светящихся трещин у лавы, астероид с кратерами
и звёздное поле в космосе). При необходимости эти фоны можно заменить на
подобранные внешние ассеты позже — интерфейс подключения (`document.body`
background-image через `svgToDataUrl(generateLocationArt(loc))`) для этого
менять не придётся, достаточно поменять то, что возвращает функция.

## Всё остальное — процедурная генерация

Текстура самой руды (canvas 2D value noise, отдельно от фона — см. выше),
трещины на руде, пыль/частицы (кроме осколков `rock_chunk_*`), сургучные
печати редкости, гравюрные контуры кирки-курсора — сгенерированы кодом или
нарисованы как SVG/CSS непосредственно в `miner-clicker-v3.html`, сторонних
ассетов не используют. Эффект зерна плёнки и виньетки, который был здесь
раньше, убран полностью по просьбе — экран больше не затемняется по краям.
