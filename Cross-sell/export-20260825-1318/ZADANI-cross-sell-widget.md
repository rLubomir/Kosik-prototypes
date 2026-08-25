# Cross-sell widget v kategorii — zadání (export 2026-08-25 13:18)

> ⚠️ **Pro příjemce:** pole „Měření úspěchu" je označené `MISSING ⚠️` — metriky se teprve
> nastaví. Před zahájením implementace kontaktujte design department / zadavatele
> (Ľubomír Ruška, lubomir.ruska@kosik.cz) a metriky dohodněte.

## Key information

Prototyp: `category-l2-chleb.html` (v této složce; samostatný HTML soubor, stačí otevřít
v prohlížeči). Pilotní kategorie: **Chléb (c1034)** — slouží jen jako ukázka funkce,
řešení má být přenositelné na libovolnou kategorii.

## Problem statement

Zákazník v kategorii (pilot: Chléb) nakoupí hlavní produkt, ale související zboží
(máslo, pomazánky, šunky, salámy, plátkové sýry…) musí aktivně hledat po dalších
kategoriích — v místě nákupu mu nic souvisejícího nenabídneme. Přicházíme tak
o přirozenou příležitost rozšířit nákup a zákazník o pohodlí.

## Value

- **Pro zákazníka:** nabídka souvisejících produktů přímo v kontextu nákupu — lepší
  nákupní zážitek, nemusí procházet další kategorie.
- **Pro byznys:** více položek v nákupu a více add-to-cart z kategorie.

## Measurement of success

`MISSING ⚠️` — metriky zatím nejsou nastavené, zadavatel je doplní.

Kandidáti diskutovaní při tvorbě prototypu (nepotvrzeno):

- *Customer-wise (usability):* CTR widgetu a modalu; podíl zákazníků, kteří z widgetu
  přidají produkt.
- *Business-wise:* průměrný počet položek v objednávkách obsahujících produkt z pilotní
  kategorie; add-to-cart rate doplňkových produktů připsaných widgetu; ideálně
  vyhodnotit A/B testem proti současnému stavu.

**Jak se zákazník o funkci dozví:** kontextově — widget se zobrazí sám během nakupování
(po prvním přidání produktu z výpisu). Samostatná komunikační kampaň se pro pilot
nepředpokládá.

## Limitations

- Pilot běží jen v kategorii Chléb; jde o ukázku funkce, ne finální rozsah.
- V1 bez napojení na doporučovací engine — ruční kurátorský výběr produktů (viz
  Requirements).
- Kategorie Pomazánkové (c905) má aktuálně jen 13 produktů — modal s ní počítá
  (ostatních 5 kategorií dodává po 20).

## Requirements

1. **Spouštěč:** widget se vloží po **prvním** přidání produktu do košíku z hlavního
   výpisu kategorie (0 → 1+). Přidání z jiných míst (widgety, modaly, vyhledávání)
   spouštěčem není. Vložení proběhne **právě jednou** za návštěvu stránky a widget už
   zůstává na místě.
2. **Umístění:** celořádkový widget hned pod řádkem produktu, který vložení spustil.
   Pozice se musí dopočítat z aktuálního počtu sloupců gridu a držet na hranici řádku
   na všech šířkách viewportu i při resize (v prototypu řešeno vykreslením uvnitř CSS
   gridu s `grid-column: 1 / -1` a reaktivním `grid-row`).
3. **Komponenta widgetu:** design-systémový produktový widget v obrázkové variantě —
   bez titulku, banner s CTA „Zobrazit vše" jako první dlaždice karuselu na desktopu,
   široký banner nad karuselem na mobilu (breakpoint 768 px). **Klikací je celá plocha
   banneru**, CTA tlačítko i koncová dlaždice karuselu.
4. **Obsah widgetu (V1, ručně):** top 3 nejprodávanější produkty z každé napárované
   kategorie (pilot: 6 kategorií — c904, c905, c1068, c1059, c1053, c945), seřazené
   globálně podle prodaných kusů sestupně. Zdroj dat v prototypu: export prodejů za
   posledních 30 dní (viz Resources). Při úspěchu pilotu se výběr napojí na BE /
   doporučovací engine.
5. **Modal „Skvělé ke chlebu":** všechny tři klikací plochy otevírají modal velikosti
   md rozšířený na desktopu na ~1000 px (šířka detailu produktu; na mobilu bottom
   sheet, nikdy fullscreen). Obsah: grid product boxů se všemi produkty napárovaných
   kategorií (v prototypu prvních 20 z každé), **taby** nad gridem (DS Tabs, varianta
   white; na mobilu horizontálně scrollovatelné): Vše (výchozí) · Sýry · Šunky ·
   Máslo · Salámy · Pomazánkové máslo · Pomazánky — kategorie řazené podle
   prodávanosti (součet prodaných kusů top 3 produktů kategorie za 30 dní).
   Tab „Vše" zobrazuje produkty **míchané round-robinem** přes kategorie v pořadí
   prodávanosti (1. produkt z každé kategorie, pak 2. z každé, …; uvnitř kategorie
   platí produkční pořadí; vyčerpaná kategorie z rotace vypadává). Kategorie na svých
   tabech drží produkční pořadí. Přepnutí tabu filtruje grid a vrací scroll nahoru;
   při novém otevření modalu je aktivní „Vše".
6. **Interakce s košíkem:** přidání produktu z widgetu ani z modalu nesmí vyvolat další
   vložení widgetu. Automatické otevření náhledu košíku po prvním přidání produktu je
   **potlačeno** (kolidovalo by se zobrazením widgetu) — záměrné rozhodnutí prototypu,
   při implementaci potvrdit s Designem.
7. **Přenositelnost:** celé řešení (spouštěč, widget, modal, taby) musí být
   konfigurovatelné pro libovolnou kategorii a její sadu napárovaných kategorií.

## Resources

- Prototyp: `category-l2-chleb.html` (tato složka; jediný soubor, vše inline)
- Repo: https://github.com/rLubomir/Kosik-prototypes (privátní)
- Produkční referenční stránka: https://www.kosik.cz/c1034-chleb
- Bannery: `desk_widge_banner.png` (364×364, desktop dlaždice),
  `mob_widge_banner.png` (725×290, mobilní banner) — v kořeni složky prototypu
- Data prodejů: „Top 3 nejprodávanější produkty podle kategorie (posledních 30 dní).csv"
  (dodal zadavatel; SAP ID → web ID mapování je zapracované v prototypu)

## More information and notes

**Copy k posouzení copy týmem:**

- Label tabu **„Pomazánkové máslo"** — záměrná volba zadavatele pro srozumitelnost;
  produkční název kategorie je „Pomazánkové" a názvosloví „pomazánkové máslo" je
  legislativně citlivé. Finální znění potvrdí copy.
- Texty koncové dlaždice karuselu jsou globální DS překlady („Další produkty najdete
  v kategorii / Prozkoumat vše") — případná změna se dotkne všech widgetů.
- Titulek modalu „Skvělé ke chlebu" — pro jiné kategorie bude potřeba šablona titulku.

**Technické poznámky k prototypu (odchylky od produkce):**

- Výpis kategorie má načtenou jen první dávku 24 produktů, infinite scroll je vypnutý
  (hlavička ukazuje reálných 143 produktů).
- Ceny, akce a dostupnost jsou snapshot z 24. 8. 2026.
- Produkt „Fine Life Vysočina plátky" byl v době tvorby vyprodaný — v prototypu
  upraven na skladem, aby šel demonstrovat nákup.
- Obrázky produktů se načítají z produkční CDN (vyžadují připojení k internetu);
  bannery widgetu jsou vložené přímo v souboru.
- Editovatelné bloky prototypu jsou označené `PROTO:` markery (konfigurace widgetu:
  `PROTO:CONFIG cross-sell-widget`, modalu: `PROTO:CONFIG cross-sell-modal`).
