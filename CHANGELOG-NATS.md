# CHANGELOG — NATS Folders fork

Wat er gewijzigd is t.o.v. upstream [`wolfyxbt/iFolder`](https://github.com/wolfyxbt/iFolder).

## Doel

NATS-gebrande, Nederlandstalige versie voor `folders.nats-ai.nl` (Cloudflare Pages). Zelfde tool, andere taal en visuele identiteit.

## Wijzigingen

### Internationalisatie (Chinees → Nederlands)

- Alle zichtbare UI-strings vertaald: card-titels, knoplabels, placeholders, `title=` en `aria-label` attributen, toast-meldingen, empty-states, dynamische placeholders.
- 21 SYMBOL-categorielabels (Tabler) en 8 BRAND-categorielabels (Simple Icons) vertaald.
- CSS- en JS-comments met Chinese fragmenten vernederlandst zodat de grep-finalcheck `0` returnt.
- `<html lang>` van `zh-CN` naar `nl`.
- `<title>` en `<meta name="description">` vervangen door NL/NATS-context.

### Vertaalbeslissingen

| CN | NL | Reden |
|---|---|---|
| 取色滴管 | Kleurpipet | Kort en kraakhelder; "Pipet" alleen kan onduidelijk zijn |
| 含文件 | Met bestanden | Substantief — "含文件样式" → "Stijl met bestanden" als full uitleg |
| 图形 | Icoon | "Vormen" was te beperkt; "Icoon" dekt symbool + merk |
| 图形库 | Iconenbibliotheek | aria-label, mag iets uitgebreider |
| 符号 / 品牌 | Symbolen / Merken | Lib-toggle |
| 使用品牌色 | Gebruik merkkleur | Imperatief, conform briefing |
| 数学 / 箭头 / 形状 / … | Wiskunde / Pijlen / Vormen / … | Symbol-categorieën, gewone NL |
| 开发 / 设计 | Development / Design | Brand-categorieën — Engelse termen die in NL dev-community natuurlijk zijn |
| 云与AI | Cloud & AI | Idem |
| 已下载 PNG | PNG gedownload | Toast — kort, ww-vorm |
| 没有匹配的符号 | Geen symbolen gevonden | Empty-state — neutraal |

### Visuele identiteit (Apple HIG / macOS Tahoe-look)

- Volledig nieuwe design-token-laag in `:root`, plus `prefers-color-scheme: dark` mode (auto-switch).
- Kleurpalet vervangen: warm-papier sienna → Apple system blue (`#0071e3` light / `#0a84ff` dark).
- Typografie: Google Fonts (Instrument Serif, Hanken Grotesk, JetBrains Mono) verwijderd ten gunste van system-stack (`-apple-system`, `SF Pro Text/Display`, `ui-monospace`). Geen externe font-requests meer.
- Cards en preview frame gebruiken frosted glass (`backdrop-filter: saturate(180%) blur(20px)`).
- Buttons in Apple HIG-stijl: 8 px radius, hover lift `translateY(-1px)` met subtiele schaduw, focus-ring 3 px met 8% accent-opacity. Primary = gevuld accent + witte tekst. Secondary = elevated wit met border.
- Inputs en search met Mac-style focus-ring (3 px accent-wash op `:focus`).
- Symbol grid: hover scale `1.05x`, selected = accent-blauwe vulling + witte glyph.
- Library mode toggle = pill-segmented control in plaats van blokjes.
- iOS-toggle: thumb wit op accent-blauw track wanneer active (was oranje sienna).
- Toast: pill (radius 999) met dark-glass backdrop + witte tekst.
- Snap guides nu accent-blauw i.p.v. sienna.
- Mac-style scrollbars (10 px breed, transparente track, semi-transparente thumb).

### Branding

- NATS brand-bar fixed bovenin (`48px` hoog, frosted glass), met logo-square (gradient `#5AC8FA → #0071e3`), titel "Folders", subtitel "macOS Icon Studio" en rechts een NATS-link naar `nats-ai.nl`. Subtitel verdwijnt onder 640 px breed.
- Bestaande `iFolder` masthead behouden als kleinere subkop onder de brand-bar (compactere weergave, `22 px` Apple-typografie).

### Infrastructuur

- `_headers` toegevoegd voor Cloudflare Pages (`X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy: camera=(), microphone=(), geolocation=()`).
- Geen build-step vereist; blijft single-file HTML met inline CSS + JS.
- `index.html` blijft volledig zelfstandig functioneren bij dubbelklik (alleen iro.js komt nog van CDN — zelfde als upstream).

## Niet gewijzigd

- DOM-structuur, class-namen, IDs en JavaScript-architectuur — alleen strings + CSS aangepast.
- iro.js (color picker) en de inlined Tabler/Simple-Icons paths.
- License: blijft MIT. Copyright-regel uitgebreid met `Copyright © 2026 Stan Web-Projects` naast de upstream copyright (in commits/README, niet in een aparte LICENSE-update want die zit niet in deze repo).

## Smoke-test status

Lokaal getest met `python3 -m http.server 8099`:

- [x] HTML serveert (200 OK, ~701 kB, geen build-step)
- [x] Inline JS parseert zonder errors (`new Function(jsBlob)`)
- [x] `_headers` serveert
- [x] `grep -cP '[一-鿿]' index.html` returnt **0**
- [x] `<html lang="nl">`, `<title>Folders · NATS</title>`, `<meta name="description">` aanwezig
- [x] Brand-bar markup aanwezig en gestyled
- [x] Geen Google Fonts requests
- [ ] Browser-functietesten (PNG/SVG export, copy, eyedropper, dark/light auto, mobile) — door Stan in browser uit te voeren conform briefing
