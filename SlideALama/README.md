# 🦙 Slide-a-Lama — Slots

Kopie herní mechaniky legendární ICQ hry **Slide-a-Lama**, varianta **Slots**.
Hra pro **dva hráče** — střídají se v tazích na sdílené desce, stejně jako v originále.
Hrát se dá u jedné obrazovky i online proti sobě.

## Tři způsoby hraní

**Lokálně** — otevři `index.html` v prohlížeči. Hrajete ve dvou u jedné obrazovky,
střídáte se v tazích. Žádný build ani server.

**Online** — tatáž stránka publikovaná jako Claude Artifact se schopností `db`.
Kdo otevře odkaz, sedne si na volné místo, třetí příchozí se jen dívá. Tahy se
propisují živě přes jeden sdílený dokument. Panely jsou popsané **Ty** a **Soupeř**
podle toho, na kterém místě sedíš. Kdo je na tahu, hlásí box s kameny: velkým textem
a rámečkem v barvě toho hráče.

Když se tlačítka v hlavičce přestanou vejít vedle názvu, sbalí se pod tlačítko **⋯**.
Nejsou to kopie — do rozbalovátka se přesunou ta samá tlačítka, takže se jejich stavy
nemůžou rozejít.

Vpravo nahoře jsou dvě tlačítka na správu míst:

- **Opustit hru** uvolní tvoje vlastní místo. Kdo místo nemá a některé je volné,
  vidí místo toho **Sednout si**.
- **Vyhodit soupeře** uvolní místo toho druhého — na to, když se už nevrátí.
  Divákovi se nabídne jako **Uvolnit obě místa**, aby se dala zaseknutá hra rozseknout.
  Vyhazování je nevratné, takže první klik jen varuje a druhý (do čtyř sekund) potvrzuje.
  Vyhozený se dozví, že ho někdo odhlásil.

Stejný soubor zvládne obojí: online vrstva se zapne, jen když je `db` k dispozici,
jinak stránka zůstane u hry pro dva na jednom zařízení.

> **Pozor:** artifact se schopností `db` je *organization-internal* a **nejde sdílet
> veřejně** — každý, kdo ho otevře, musí být přihlášený člen stejné organizace.
> Na hraní s někým zvenku slouží varianta s Firebase níž.

**Na vlastním webu (Netlify apod.)** — tatáž stránka, sdílení přes Firebase.
Odkaz může otevřít kdokoli. Nastavení viz níž; dokud konfigurace chybí, chová se
stránka jako hra u jedné obrazovky.

## Hraní po síti mimo Claude (Firebase)

Stránka sama o sobě žádný server nemá. Na vlastním hostingu si sdílený stav bere
z **Firebase Realtime Database** — bezplatné služby, kterou stačí založit a nakonfigurovat,
nic se nenasazuje.

1. Na <https://console.firebase.google.com> založ projekt (stačí výchozí volby,
   Google Analytics není potřeba).
2. V levém menu **Build → Realtime Database → Create Database**. Vyber region
   (např. `europe-west1`) a spusť v **testovacím režimu**.
3. V záložce **Rules** nastav, kam smí hra sahat:

   ```json
   {
     "rules": {
       "seats": { ".read": true, ".write": true },
       "state": { ".read": true, ".write": true }
     }
   }
   ```

4. Adresu databáze najdeš nad jejím obsahem, ve tvaru
   `https://…-default-rtdb.europe-west1.firebasedatabase.app`.
5. Vlož adresu databáze v `index.html` do konstanty `FIREBASE_CONFIG` (hned na začátku
   skriptu, označená komentářem). Nic dalšího z konfigurace potřeba není — `apiKey` a spol.
   se používají až pro přihlašování, které tu není, takže krok se zaregistrováním
   webové aplikace můžeš přeskočit.
6. Nahraj soubor na hosting a je hotovo.

**Ty údaje nejsou tajné.** U webových aplikací jsou vždycky veřejné — kdokoli si je
přečte ve zdroji stránky. Bezpečnost se řeší výhradně pravidly z kroku 3, a ta výše
jsou schválně jednoduchá: kdo zná adresu databáze, může do těch dvou uzlů zapisovat.
Pro hru mezi dvěma lidmi to stačí; kdyby ti to vadilo, jde to utáhnout přihlášením.

**Místnosti.** Odkaz s `#room=nejaky-kod` hraje zvlášť od ostatních — hodí se, když
je stránka veřejná a nechceš, aby ti do partie vlezl někdo cizí. Bez místnosti se
všichni potkávají ve společné `main`. Není to zabezpečení, jen oddělení partií.

## Spuštění

Otevři `index.html` v prohlížeči. Žádný build, server ani závislosti — jeden soubor.

## Ovládání

Kámen vsuneš kliknutím na šipku u okraje desky. Případně klávesnicí:

| Klávesy | Směr |
|---------|------|
| `1`–`5` | shora do sloupce |
| `Q`–`T` | zleva do řádku |
| `A`–`G` | zprava do řádku |


## Pravidla

**Cíl.** Vyhrává ten, kdo získá nad soupeřem náskok **300 bodů**. Vychází to z původního
pravidla: každý hráč má 5 lam a za každých 60 bodů náskoku k tobě jedna soupeřova přeběhne,
takže 300 bodů je všech deset. Lamy se nekreslí — postup k výhře ukazuje pruh pod skóre
a text pod ním.

**Tah.** Hraje se na desce 5×5, která je na začátku už **částečně zaplněná náhodnými kameny**
(zhruba 60 % plochy) — nikdy ale tak, aby na startu ležela hotová trojice, takže první tah
nikomu nespadne do klína zadarmo. Hráči se střídají a losují kameny ze společné fronty
(vidíš i dva kameny dopředu). Kámen vsouváš ze tří stran: zleva a zprava do řádku,
shora do sloupce. Kameny se posouvají a padají dolů gravitací. Když vsuneš šestý kámen
do plné řady, kámen na protilehlém konci vypadne ze hry.

**Bodování.** Tři a více stejných symbolů vedle sebe v řádku nebo sloupci zmizí a boduje.
Tabulka je vidět přímo pod hrací deskou, není schovaná v pravidlech:

| Symbol | 3 stejné | 4 stejné (×2) | 5 stejných (×3) |
|--------|---------:|--------------:|----------------:|
| 🦙 Lama | 150 | 300 | 450 |
| ❤️ Srdce | 100 | 200 | 300 |
| 🍒 Třešně | 70 | 140 | 210 |
| 🍐 Hruška | 40 | 80 | 120 |
| 🍇 Švestka | 30 | 60 | 90 |
| 🍌 Banán | 20 | 40 | 60 |
| 🔔 Zvoneček | 10 | 20 | 30 |

**Komba.** Když po zmizení skupiny spadnou kameny tak, že vznikne další skupina,
sečte se i ta — a tak pořád dokola. Celý řetěz padá na účet hráče, který tah zahrál.

## Odkud pravidla pocházejí

Původní stránku s hrou se v tomto prostředí nepodařilo otevřít (blokuje ji síťová
politika). Přesná čísla a mechanika proto vycházejí ze **zdrojového kódu solveru
původní ICQ hry** ([zitmen/slide-a-lama](https://github.com/zitmen/slide-a-lama)),
který hru reverzně analyzoval — odtud jsou bodové hodnoty symbolů, vzorec
`hodnota × (počet − 2)`, chování vsouvání kamenů i podmínka výhry na rozdíl 300 bodů.

Kresba dvou nejcennějších políček je jiná než v originále — místo sedmičky je lama
a místo BARu srdce. Hodnoty, pořadí ani četnosti se tím nemění, jde čistě o vzhled.

Jediné, co ze zdrojů dohledat nešlo, je **četnost jednotlivých symbolů**. Ta je proto
navržená tak, aby cennější symboly padaly o něco vzácněji a partie měla rozumné tempo
(medián zhruba 85–115 tahů podle toho, jak přesně hráči hrají, tj. řádově 5–10 minut).

## Poznámky k implementaci

Celá herní logika je v `index.html` oddělená jako čisté funkce bez DOM
(`applyInsert`, `findClusters`, `bestCluster`, `resolveAll`), takže jde testovat
samostatně. Online režim toho využívá: kdo je na tahu, spočítá výsledek těmito
funkcemi, zapíše ho do sdíleného dokumentu a teprve pak si tah odanimuje. Druhá
strana přehraje stejný tah stejnou animací a nakonec porovná svou desku s deskou
z dokumentu — kdyby se rozešly, přebírá dokument, takže se stav sám narovná. Vykreslování stojí na CSS transformacích — každý kámen má vlastní element
s `transform: translate(...)`, takže posuny i pády se animují samy.
