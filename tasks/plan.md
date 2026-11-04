# Náhled aktuálního měsíce v sidebaru

## Cíl

Pod seznam kategorií v levém sloupci (`.sidebar`) přidat malý kalendář aktuálního měsíce, ve kterém je zvýrazněný dnešní den. Klik na den nic nedělá (čistě informativní náhled).

## Potvrzená rozhodnutí

- Klik na den: nic (pouze náhled).
- Čísla týdnů: zobrazena v levém sloupci mřížky (ISO číslo týdne).
- Týden začíná vždy pondělím (stejně jako hlavní kalendář, `getMonday`).
- Dny mimo aktuální měsíc se nezobrazují (prázdné buňky).
- Náhled je statický: vykreslí se při `init()` a zobrazuje aktuální měsíc.

## Dotčené soubory

1. [index.html](/Users/mholec/Repository/skoleni-copilot/_vsc/src/index.html) – uvnitř `<aside class="sidebar">` za `#categoryList` přidat:
   - `<div class="mini-calendar">` obsahující
   - `<h2 class="sidebar-title" id="miniCalendarTitle">` (název měsíce, styl shodný s nadpisem „Kategorie“)
   - `<div class="mini-calendar-grid" id="miniCalendarGrid">`
   - Žádný inline CSS (AGENTS.md).

2. [app.js](/Users/mholec/Repository/skoleni-copilot/_vsc/src/app.js) –
   - `cacheDomReferences()`: `dom.miniCalendarTitle`, `dom.miniCalendarGrid`.
   - `init()`: volat `renderMiniCalendar()` hned za `renderCategories()`.
   - Nová funkce `renderMiniCalendar()` v sekci „Náhled měsíce“ (za „Kategorie“):
     - název: `toLocaleDateString('cs-CZ', { month: 'long', year: 'numeric' })` s velkým prvním písmenem;
     - hlavička dnů: `DAY_NAMES.map(n => n.slice(0, 2))` → Po, Út, St, Čt, Pá, So, Ne (bez nové konstanty);
     - začátek mřížky: pondělí na nebo před 1. dnem měsíce, `offset = (first.getDay() + 6) % 7`;
     - počet řádků: `Math.ceil((offset + daysInMonth) / 7)`;
     - každý řádek: nejdřív číslo týdne `getWeekNumber(rowStart)`, pak 7 dnů; dny mimo měsíc = prázdný `div`;
     - dnešek: `getDateString(date) === getDateString(new Date())` → třída `is-today`;
     - DOM se staví přes `createElement` / `textContent` (stejně jako `renderCategories`).
   - Komentáře česky, jen tam, kde je to nutné.

3. [styles.css](/Users/mholec/Repository/skoleni-copilot/_vsc/src/styles.css) – nová sekce „Sidebar – náhled měsíce“ za styly kategorií (před „Kalendář – hlavička“). Pravidla vždy na jednom řádku (`.github/instructions/css.instructions.md`):
   - `.mini-calendar` – `margin-top` od seznamu kategorií;
   - `.mini-calendar-grid` – `display: grid; grid-template-columns: repeat(7, 1fr); gap: 2px;`
   - `.mini-calendar-grid.has-week-numbers` – `grid-template-columns: 22px repeat(7, 1fr);` (šířka sidebaru 240 px − padding 2×20 px = 200 px)
   - `.mini-calendar-weekday`, `.mini-calendar-week` – malé šedé popisky (`--color-text-light`)
   - `.mini-calendar-day` – 24×24 px, `margin: 0 auto`, flex centrování, `border-radius: 50%`
   - `.mini-calendar-day.is-today` – `background-color: var(--color-primary); color: #ffffff; font-weight: 600;` (stejný vizuál jako `.calendar-header-cell.is-today .day-date`)
   - Použít pouze existující CSS proměnné (`--color-primary`, `--color-text`, `--color-text-light`), takže se náhled přizpůsobí i tmavému tématu.

4. [README.md](/Users/mholec/Repository/skoleni-copilot/_vsc/README.md) – v sekci „Funkce → Kalendář“ doplnit bod o náhledu aktuálního měsíce v levém panelu se zvýrazněným dnešním dnem a čísly týdnů.

## Mimo rozsah

- Navigace mezi měsíci v náhledu.
- Klik na den a přepnutí týdne.
- Zvýraznění týdne aktuálně zobrazeného v hlavním kalendáři.
- Značky dnů s událostmi.
- Automatické překreslení po půlnoci (stejně jako dnes u hlavního kalendáře, který „dnešek“ počítá jen při renderu).
- Úpravy `styles-dark.css` (není připojen v `index.html`).

## Ověření

- `node --check src/app.js` (syntaxe).
- Otevřít `src/index.html` přímo v prohlížeči (bez serveru); zkontrolovat:
  - název měsíce a správný počet řádků,
  - čísla týdnů odpovídají hlavnímu kalendáři,
  - zvýrazněný dnešek,
  - žádné chyby v konzoli,
  - vizuální kontrola screenshotem (šířka 200 px, zarovnání sloupců).
- Kontrola, že se náhled na mobilu (≤768 px) a při tisku skrývá spolu se sidebarem.
