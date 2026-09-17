# 🦙 Slide-a-Lama — Slots

Kopie herní mechaniky legendární ICQ hry **Slide-a-Lama**, varianta **Slots**.
Hra pro **dva hráče u jedné obrazovky** — hráči se střídají v tazích na sdílené desce,
stejně jako v originále.

## Dva režimy

**Lokálně** — otevři `index.html` v prohlížeči. Hrajete ve dvou u jedné obrazovky,
střídáte se v tazích. Žádný build ani server.

**Online** — tatáž stránka publikovaná jako Claude Artifact se schopností `db`.
Kdo otevře odkaz, sedne si na volné místo, třetí příchozí se jen dívá. Tahy se
propisují živě přes jeden sdílený dokument. Panely jsou popsané **Ty** a **Soupeř**
podle toho, na kterém místě sedíš; velký text vedle kamene říká, kdo je na tahu
(nebo že se čeká na soupeře, případně že se jen koukáš).

Vpravo nahoře jsou dvě tlačítka na správu míst:

- **Opustit hru** uvolní tvoje vlastní místo. Kdo místo nemá a některé je volné,
  vidí místo toho **Sednout si**.
- **Vyhodit soupeře** uvolní místo toho druhého — na to, když se už nevrátí.
  Divákovi se nabídne jako **Uvolnit obě místa**, aby se dala zaseknutá hra rozseknout.
  Vyhazování je nevratné, takže první klik jen varuje a druhý (do čtyř sekund) potvrzuje.
  Vyhozený se dozví, že ho někdo odhlásil.

Stejný soubor zvládne obojí: online vrstva se zapne, jen když je `db` k dispozici,
jinak stránka zůstane u hry pro dva na jednom zařízení.

> **Pozor:** artifact se schopností `db` je *organization-internal* — každý, kdo ho
> otevře, musí být přihlášený člen stejné organizace. Na hraní s někým zvenku by
> bylo potřeba jiné řešení (např. Firebase).

## Spuštění

Otevři `index.html` v prohlížeči. Žádný build, server ani závislosti — jeden soubor.

## Ovládání

Kámen vsuneš kliknutím na šipku u okraje desky. Případně klávesnicí:

| Klávesy | Směr |
|---------|------|
| `1`–`5` | shora do sloupce |
| `Q`–`T` | zleva do řádku |
| `A`–`G` | zprava do řádku |

Jména hráčů jdou přepsat kliknutím na ně.

## Pravidla

**Cíl.** Na stole je 10 lam, každý hráč má na začátku 5. Za **každých 60 bodů náskoku**
před soupeřem k tobě přeběhne jedna jeho lama. Kdo získá všech 10 lam — tedy náskok
**300 bodů** — vyhrává. Dokud se náskok mění, lamy se můžou vracet zpátky.

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
| 7 Sedmička | 150 | 300 | 450 |
| BAR | 100 | 200 | 300 |
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
