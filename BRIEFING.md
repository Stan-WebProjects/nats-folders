# BRIEFING — NATS Folders (iFolder NL fork)

> Voor Claude Code. Lees dit volledig vóór je begint. Stel geen vragen — beslis pragmatisch en voer uit. Stan committeert zelf, dus elke logische unit één commit met heldere message.

---

## Context

Dit is een fork van [`wolfyxbt/iFolder`](https://github.com/wolfyxbt/iFolder) — een single-file HTML tool om macOS folder-iconen te maken. Upstream is in het Chinees + deels Engels en heeft een functionele maar generieke uitstraling.

**Doel van deze fork:** een Nederlandstalige, NATS-gebrande versie die deployt op `folders.nats-ai.nl` (Cloudflare Pages). Stan gebruikt 'm vanuit een Mac Shortcut die de geëxporteerde PNG direct als folder-icoon toepast via `fileicon`.

**Waarom dit een job voor jou is en niet voor een runtime overlay:** strings zitten verspreid over `index.html` in DOM-text, attributen (`placeholder`, `aria-label`, `title`), JS template-literals, `alert()`/`confirm()` calls, console-output en mogelijk inline event-handlers. Een DOM-walker mist de helft. Jij kunt grep'en, lezen, en aan de bron vervangen.

---

## Wat je gaat opleveren

1. Volledig vertaalde `index.html` (Chinees → Nederlands, Engelse UI-strings ook NL)
2. Herziene styling: clean Mac-native look, glassmorphism, dark/light auto, NATS branding
3. Brand-bar bovenin met "Folders · macOS Icon Studio" + link naar `nats-ai.nl`
4. `<title>` en `<meta>` tags aangepast (Nederlands, NATS-context)
5. Cloudflare Pages-ready (`_headers` file, geen build step nodig)
6. Nette commits per logische unit
7. Korte `CHANGELOG-NATS.md` met wat er gewijzigd is t.o.v. upstream

---

## Werkvolgorde

### Stap 1 — Verkenning (read-only)

```bash
wc -l index.html                                 # ~3759 regels verwacht
grep -cP '[\u4e00-\u9fff]' index.html            # aantal regels met Chinese karakters
grep -nP '[\u4e00-\u9fff]' index.html | head -50 # eerste 50 voorkomens
```

Snap de structuur:
- Waar zit de CSS? (inline in `<style>`?)
- Waar zit de JS? (inline in `<script>`?)
- Hoeveel inline SVG (Tabler icons) is er? (overslaan bij vertaling)
- Welke bibliotheken? (iro.js voor color picker per upstream README)

**Maak `INVENTORY.md` met je findings** voor je begint te wijzigen.

### Stap 2 — Vertaalmap uitbreiden

Onderstaande map is een **startpunt**. Vul aan met alles wat je tegenkomt. Sorteer keys op lengte (langste eerst) bij vervangen, anders raakt "Download" eerder dan "Download PNG".

```
=== STARTMAP (CN → NL) ===
导入图片                      → Importeer afbeelding
点击上传图片，或 Cmd+V 粘贴   → Klik om te uploaden, of plak met Cmd+V
点击上传图片                   → Klik om te uploaden
导出                          → Exporteer
复制到剪贴板                   → Kopieer naar klembord
下载 PNG                      → Download PNG
下载 SVG                      → Download SVG
下载                          → Download
文件夹颜色                     → Folderkleur
文件颜色                       → Bestandskleur
含文件                         → Met bestanden
取色滴管                       → Kleurpipet
恢复默认                       → Standaard herstellen
默认                          → Standaard
颜色                          → Kleur
文字                          → Tekst
图形                          → Vormen
符号                          → Symbolen
品牌                          → Merken
使用品牌色                     → Gebruik merkkleur
图标                          → Icoon
搜索                          → Zoeken
搜索图标                       → Zoek icoon
搜索品牌                       → Zoek merk
清除                          → Wissen
取消                          → Annuleren
确定                          → OK
保存                          → Opslaan
应用                          → Toepassen
关闭                          → Sluiten
设置                          → Instellingen
大小                          → Grootte
位置                          → Positie
透明度                         → Transparantie
不透明度                       → Dekking
阴影                          → Schaduw
旋转                          → Rotatie
居中                          → Centreren
上传                          → Uploaden
已复制                         → Gekopieerd
已复制到剪贴板                  → Gekopieerd naar klembord
复制                          → Kopiëren
粘贴                          → Plakken
剪贴板                         → Klembord
提示                          → Tip
正在加载                       → Laden
加载中                         → Laden…
加载失败                       → Laden mislukt
请输入                         → Voer in
请选择                         → Selecteer
更多                          → Meer
全部                          → Alles
分类                          → Categorie
风格                          → Stijl
类型                          → Type
预览                          → Voorbeeld
编辑                          → Bewerken
删除                          → Verwijderen
添加                          → Toevoegen
新建                          → Nieuw
调色板                         → Palet
色卡                          → Kleurenkaart
自定义                         → Aangepast
清空                          → Leegmaken
```

**Vertaalregels:**
- Toon: zakelijk, kort, Apple-achtig (zoals macOS Systeeminstellingen)
- Knoppen: imperatief ("Exporteer", niet "Exporteren")
- Labels: substantief ("Folderkleur", niet "Kleur van folder")
- Geen overdreven Hollandismes ("Doei!" of "Krijg nou wat") — neutraal Nederlands
- Behoud Engelse termen waar de Mac/dev-community ze ook in NL gebruikt: "Download", "PNG", "SVG", "Cmd+V", "RGB", "HEX"
- Geen emoji's toevoegen die er niet stonden

**Edge cases waar je op moet letten:**
- Strings binnen template-literals: `` `...${var}...` `` — vertaal alleen de letterlijke delen
- Strings met variabelen middenin (`"Geüpload: " + filename`) — splits zorgvuldig
- `aria-label` mag iets uitgebreider zijn voor accessibility
- `<title>` → `Folders · NATS`
- `<meta name="description">` → `macOS folder-iconen maken in je browser. Eén klik, klaar.`
- `lang` attribuut op `<html>` → `nl`

### Stap 3 — Styling herzien

Doel: voelt aan als een native macOS Tahoe app, niet als generieke web tool.

**Design tokens** (gebruik CSS custom properties bovenin de `<style>`):

```css
:root {
  --font-text: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Inter", system-ui, sans-serif;
  --font-display: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Inter", system-ui, sans-serif;
  --radius: 12px;
  --radius-sm: 8px;
  --radius-lg: 18px;

  /* Light */
  --bg: #f5f5f7;
  --bg-elev: rgba(255, 255, 255, 0.72);
  --bg-elev-strong: rgba(255, 255, 255, 0.92);
  --border: rgba(0, 0, 0, 0.08);
  --border-strong: rgba(0, 0, 0, 0.14);
  --text: #1d1d1f;
  --text-secondary: #6e6e73;
  --accent: #0071e3;
  --accent-hover: #0077ed;
  --shadow: 0 1px 2px rgba(0,0,0,.04), 0 8px 24px rgba(0,0,0,.06);
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg: #1a1a1c;
    --bg-elev: rgba(36, 36, 38, 0.72);
    --bg-elev-strong: rgba(44, 44, 46, 0.92);
    --border: rgba(255, 255, 255, 0.10);
    --border-strong: rgba(255, 255, 255, 0.16);
    --text: #f5f5f7;
    --text-secondary: #a1a1a6;
    --accent: #0a84ff;
    --accent-hover: #409cff;
    --shadow: 0 1px 2px rgba(0,0,0,.20), 0 8px 24px rgba(0,0,0,.32);
  }
}
```

**Visuele richtlijnen:**
- **Typografie:** SF / system stack, `letter-spacing: -0.01em` op body, `-0.022em` op headings, font-weight 500 voor button-tekst
- **Panels/cards:** frosted glass — `backdrop-filter: saturate(180%) blur(20px)`, semi-transparant achtergrond, 1px subtiele border
- **Buttons:** Apple HIG stijl — 7px verticale padding, 14px horizontaal, font-size 13px, 8px radius, hover lift `translateY(-1px)`
- **Primary action** (Download, Exporteer): gevuld met `--accent`, witte tekst
- **Secondary actions:** `--bg-elev-strong` met `--border-strong`
- **Inputs:** focus ring 3px met 15% accent-opacity (zoals Apple)
- **Color swatches:** schalen `1.05x` op hover met subtiele shadow
- **Body achtergrond:** `var(--bg)` met optionele radial gradient blob (8% accent-opacity links boven, 6% paars rechts onder)
- **Scrollbars:** Mac-style — 10px breed, transparante track, semi-transparante thumb

**Mag je doen:**
- Bestaande CSS-variabelen die upstream gebruikt opnieuw bepalen (overschrijven met onze tokens)
- Hardcoded kleuren in de bestaande CSS vervangen door variabelen
- Layout/spacing aanpassen voor betere ritme (8px grid)

**Niet doen:**
- DOM-structuur ingrijpend wijzigen (anders breekt JS-event-binding)
- Bibliotheken toevoegen (single-file blijft single-file, behalve onze brand-bar in HTML)
- Fonts via Google Fonts laden (performance, en system-ui is sowieso beter op Mac)

### Stap 4 — Brand bar

Voeg toe direct na `<body>`:

```html
<div class="nats-brand-bar">
  <div class="nats-brand-left">
    <div class="nats-logo">N</div>
    <span class="nats-title">Folders</span>
    <span class="nats-subtitle">macOS Icon Studio</span>
  </div>
  <div class="nats-brand-right">
    <a href="https://nats-ai.nl" target="_blank" rel="noopener">NATS</a>
  </div>
</div>
```

Styling: fixed top, 48px hoog, frosted glass, kleine logo-square 24×24 met linear-gradient `#5AC8FA → #0071e3`, gewicht 600 op title, secondary tekst in `--text-secondary`. Zorg dat `body` `padding-top: 48px` krijgt, en op mobile (`max-width: 640px`) verberg de subtitle.

### Stap 5 — `_headers` voor Cloudflare Pages

Maak `_headers` in repo-root:

```
/*
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=()
```

### Stap 6 — Smoke test

Vóór commit:
1. `python3 -m http.server 8099` in repo-root, open `http://localhost:8099`
2. Doorloop UI: importeer een PNG, kies kleur, kies icoon uit elk tab (Tekst/Vormen/Symbolen/Merken), exporteer als PNG én SVG
3. Open DevTools → Console → check op JS errors
4. Toggle macOS Systeeminstellingen tussen Light/Dark, refresh — beide modi moeten goed staan
5. Hover over inputs/buttons — focus ring verschijnt? Hover lift werkt?
6. Mobiel: Chrome DevTools device emulation iPhone 14 Pro — brand-bar netjes? Touch targets groot genoeg?
7. Grep finalcheck: `grep -cP '[\u4e00-\u9fff]' index.html` moet `0` zijn

Als smoke test faalt op een punt: fix het, hertest, dan pas commit dat onderdeel.

---

## Acceptatiecriteria (definition of done)

- [ ] `grep -cP '[\u4e00-\u9fff]' index.html` returnt **0**
- [ ] Geen Engelse UI-strings meer zichtbaar tijdens normale workflow (export-flow, kleur-flow, icoon-flow)
- [ ] `<html lang="nl">`, `<title>` is `Folders · NATS`, `<meta description>` in NL
- [ ] Brand-bar zichtbaar bovenin op alle viewports (≥320px breed)
- [ ] Light + Dark mode beide netjes (auto-switch via `prefers-color-scheme`)
- [ ] Geen JS-errors in console tijdens elke kerntaak
- [ ] PNG-export werkt, SVG-export werkt, kopieer-naar-klembord werkt
- [ ] `_headers` aanwezig
- [ ] `CHANGELOG-NATS.md` aangemaakt
- [ ] Repo deployt zonder build-step (single-file HTML + assets)
- [ ] Alle commits hebben coherente messages, één logische unit per commit

---

## Out of scope (NIET doen)

- Geen refactor van de upstream JavaScript-architectuur — laat de logica intact, raak alleen strings + CSS aan
- Geen iro.js of Tabler Icons vervangen
- Geen Supabase / backend integratie — dit blijft een statische tool
- Geen build-tooling (Vite, webpack, npm scripts) toevoegen
- Geen tests / Playwright suite — smoke test handmatig is genoeg
- Geen license-wijziging (blijft MIT, voeg `Copyright © 2026 Stan Web-Projects` toe naast de upstream copyright)

---

## Wat al klaar is (niet aanraken)

Buiten deze repo bestaat al:

- **Mac Shortcut + `apply-icon.sh`** — gebruikt de geëxporteerde PNG en past 'm toe via `fileicon` in Finder. Geen wijziging nodig.
- **Cloudflare Pages deploy plan** — fork wordt op `folders.nats-ai.nl` gehost zodra deze briefing afgerond is.

---

## Commit-strategie

Voorbeeld goede commit-volgorde:

```
chore: add INVENTORY.md with codebase findings
feat(i18n): translate folder/file color section to Dutch
feat(i18n): translate icon picker tabs and search to Dutch
feat(i18n): translate export and clipboard actions to Dutch
feat(i18n): translate remaining UI strings and aria-labels
feat(meta): set lang="nl", update title and description
feat(brand): add NATS brand bar with logo and subtitle
style(tokens): introduce design token system with light/dark
style(panels): apply frosted glass treatment to cards
style(buttons): apply Apple HIG button styling
style(inputs): apply Mac-style focus rings and inputs
style(scroll): add Mac-style custom scrollbars
chore: add _headers for Cloudflare Pages
docs: add CHANGELOG-NATS.md
```

---

## Vragen die je *niet* hoeft te stellen

- "Welke kleur moet de accent zijn?" → `#0071e3` light / `#0a84ff` dark (Apple system blue)
- "Wat als ik een string niet kan vertalen?" → kies de meest natuurlijke NL-equivalent en noteer 'm in `CHANGELOG-NATS.md` onder "Vertaalbeslissingen"
- "Mag ik de bestaande styling helemaal vervangen?" → ja, zolang functionaliteit en DOM-structuur intact blijven
- "Moet ik tests schrijven?" → nee, smoke test handmatig is voldoende
- "Hoe deploy ik?" → niet jouw verantwoordelijkheid; Stan handelt deploy zelf af

---

## Start

```bash
# Verifieer dat je in de juiste repo zit
git remote -v
# Check upstream sync
git fetch origin && git status

# Begin
echo "▸ Stap 1: verkenning"
```

Succes. Geen ceremonie, gewoon afmaken.
