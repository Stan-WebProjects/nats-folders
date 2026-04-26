# INVENTORY — verkenning vóór Stap 2

Snapshot van wat er in `index.html` zit en welke strings vertaald moeten worden.

## Bestanden in repo

- `index.html` — 3 759 regels, single-file (CSS + JS inline)
- `folder-icon.svg` / `folder-icon-empty.svg` — bron-SVG's (zelfde paths zijn ook inline gemount in `<div class="svg-sources">`)
- `README.md` — Chinees (45 regels). Briefing vraagt geen README-rewrite, maar `CHANGELOG-NATS.md` komt erbij.
- `.gitignore` — bevat 3 Chinese comments, geen functionele issues; niet vereist te wijzigen.

## Structuur van `index.html`

| Regio | Regels | Inhoud |
|---|---|---|
| `<head>` + `<style>` | 1 – 1140 | Inline CSS, "editorial paper" theme (sienna/cream, Instrument Serif + Hanken Grotesk + JetBrains Mono via Google Fonts) |
| Hidden `<div class="svg-sources">` | 1144 – 1209 | Twee 1024² folder-SVG's (`#svgEmpty`, `#svgFilled`) — niet aanraken (paths) |
| `<header class="masthead">` | 1211 – 1225 | Titel `iFolder` + GitHub/X header-link icons |
| `<div class="app">` | 1227 – 1369 | Preview + controls (color cards, text card, symbol card) |
| Loupe + Toast | 1371 – 1378 | Eyedropper-loupe, toast container |
| `<script>` | 1380 – 3757 | Alle JS inline: `SYMBOLS` (Tabler), `BRAND_SYMBOLS` + `BRAND_CATEGORIES` (Simple Icons), color picker init, overlay engine, export/copy, symbol picker UI |

## Externe dependencies

- **iro.js v5** via jsDelivr (line 7) — kleur-picker. *Behouden, niet vervangen.*
- **Google Fonts**: Instrument Serif + Hanken Grotesk + JetBrains Mono (lines 8–10). **Te verwijderen** in Stap 3 (briefing eist system-stack, geen Google Fonts).
- **Tabler Icons** — paths inlined als `SYMBOLS` const in JS (regel 1384 ev). Niet aanraken.
- **Simple Icons** — paths inlined als `BRAND_SYMBOLS` const + `BRAND_CATEGORIES`. Niet aanraken (alleen categorielabels vertalen).

## Chinese karakters: 58 regels

`grep -cP '[一-鿿]' index.html` → 58.

### A — Zichtbare HTML-strings (vertalen)

| Regel | String | Locatie |
|---|---|---|
| 1234 | `移除` | `title=` overlay-close |
| 1237/1240/1243/1246 | `圆角` | `title=` op 4 corner-radius handles |
| 1249 | `缩放` | `title=` overlay-resize |
| 1254 | `旋转` | `title=` overlay-rotate |
| 1265 | `导入图片` | card-title (upload zone) |
| 1266 | `点击上传图片，或 Cmd+V 粘贴` | upload zone label |
| 1272 | `导出` | card-title (export) |
| 1274 | `复制到剪贴板` | button text |
| 1275 | `下载 PNG` | button text |
| 1287 | `文件夹颜色` | card-title folder color |
| 1293, 1326 | `取色滴管` | `title=` + button text — eyedropper |
| 1297, 1330 | `取色滴管` | tool-btn label |
| 1299, 1332 | `恢复默认颜色` | `title=` reset color |
| 1301, 1334 | `恢复默认` | tool-btn label |
| 1311 | `文件颜色` | card-title file (paper) color |
| 1313 | `含文件` | toggle label |
| 1314 | `含文件样式 (切换空/满文件夹)` | `title=` op iOS-toggle |
| 1343 | `文字` | card-title text overlay |
| 1344 | `输入文字或数字 (例: A, 2026, Hi)` | `placeholder=` |
| 1350 | `图形` | card-title symbol/brand card |
| 1351 | `图形库` | `aria-label=` op lib-toggle |
| 1352 | `符号` | lib-toggle button (symbols mode) |
| 1353 | `品牌` | lib-toggle button (brands mode) |
| 1356 | `搜索 (例: heart, github, figma)` | `placeholder=` symbool-search |
| 1363 | `使用品牌色` | brand-color toggle label |

### B — JS string-literals (vertalen)

| Regel | String | Functie |
|---|---|---|
| 3528 | `'已下载 PNG'` | toast na PNG download |
| 3539 | `'已复制到剪贴板'` | toast na copy success |
| 3541 | `'复制失败，请重试'` | toast na copy fail |
| 3544 | `'浏览器不支持复制图片'` | toast clipboard unsupported |
| 3650 | `'没有匹配的品牌'` / `'没有匹配的符号'` | empty-state in symbol-grid |
| 3717-3719 | `'搜索品牌 (例: github, figma, spotify)'` / `'搜索 (例: heart, math, arrow)'` | dynamische placeholder |

### C — Categorielabels in JS (vertalen)

`SYMBOL_CATEGORIES` (regel 1974, 21 categorieën, gebruikt als zichtbare tab-labels):
`数学, 箭头, 形状, 天气, 自然, 动物, 食物, 交通, 科技, 媒体, 办公, 家居, 常用工具, 运动, 表情, 货币, 社交, 游戏, 医疗, 安全`

`BRAND_CATEGORIES` (regel 2331, 8 categorieën):
`开发, 设计, 社交, 媒体娱乐, 云与AI, 生产力, 购物生活, 浏览器系统`

### D — Comments / docs (laten staan of opruimen)

CSS- en JS-comments met Chinese woorden tussen Engels (regels 311, 312, 322, 383, 552, 745, 1234-context, 2559-2561, 2601, 2656, 2659, 2698, 3424, 3703). Deze zijn niet user-visible — bij vervangen van strings kunnen we ze gerust vernederlandsen of meenemen in de algemene rewrite. Geen prioriteit voor functionaliteit; wel meenemen om grep-final-check op `0` te krijgen.

## Engelse UI-strings die ook NL moeten worden

| Regel | Huidige tekst | Note |
|---|---|---|
| 2 | `<html lang="zh-CN">` | → `lang="nl"` |
| 6 | `<title>iFolder — A Mac Folder Icon Workshop</title>` | → `Folders · NATS` |
| - | (geen `<meta name="description">`) | toevoegen |
| 1212 | `<h1>iFolder</h1>` masthead | wordt vervangen door NATS brand-bar (Stap 4); masthead behouden of verwijderen — kies behouden, brand-bar komt erboven (fixed) |
| 1214, 1219 | upstream GitHub-link `wolfyxbt/iFolder` + X-link | aria/title blijft GitHub/X. URL kan blijven verwijzen naar upstream voor attribution. |

Geen andere Engelse UI-strings in de runtime (alle gebruikersfeedback komt uit Chinese constanten of komt niet voor — geen `alert()`/`confirm()` aanwezig).

## CSS-architectuur (Stap 3 implicaties)

Bestaande CSS gebruikt `--paper`, `--ink`, `--accent` (sienna `#8F2A18`), `--surface`, etc. + custom fonts. JS leest `--accent`/`--accent-wash` indirect via CSS, maar maakt geen `getComputedStyle` calls op deze tokens. Veilig om alle tokens te overschrijven.

Plan voor Stap 3:
1. Vervang `:root` block (regels 14–41) met Apple-HIG tokens uit briefing.
2. Voeg dark-mode block toe via `prefers-color-scheme: dark`.
3. Verwijder Google-Fonts links (regels 8–10) en font-family declaraties wijzigen naar `-apple-system, …` system stack.
4. Vervang sienna/paper styling door frosted-glass cards, accent-blauw buttons, subtiele lift/hover, Mac-style focus rings.
5. Behoud DOM-structuur en class-namen (geen JS-rebinding nodig).
6. Verwijder of dim de `body::before` paper-noise overlay (clasht met clean Mac-look).

## Stap-4 voorbereidingsnotitie

Brand-bar wordt `position: fixed; top: 0; height: 48px;` met `padding-top: 48px` op `<body>`. Bestaande masthead kan blijven staan (kleine titel) of vervangen door alleen brand-bar — keuze tijdens Stap 4. Voorkeur: brand-bar bovenaan, oude masthead vervangen door iets compacters of weglaten (briefing zegt brand-bar is de identificatie).

## Smoke-test checklist (zelfde als briefing)

PNG- en SVG-export, kleur-flow, eyedropper, tab-switching (Tekst/Vormen/Symbolen/Merken), kopiëren naar klembord, dark/light auto, mobile-viewport.

---

Klaar voor Stap 2.
