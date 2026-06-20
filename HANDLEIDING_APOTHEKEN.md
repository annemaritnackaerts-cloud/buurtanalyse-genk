# Handleiding — Apotheken-data beheren in het dashboard

Deze handleiding is voor wie na mij de Google Sheet van het dashboard beheert.
Ze legt uit hoe je FarmaFlux-registraties kan tonen op het niveau dat je wil:
**heel Genk** (huidige situatie), **per groep van apotheken**, en/of **per individuele
apotheek**. Je moet hiervoor **niets aanpassen in de code** van het dashboard
(`index.html`) — alles werkt via de Google Sheet.

---

## 1. De drie tabbladen

| Tabblad | Voor wat | Kolommen |
|---|---|---|
| `farmaflux_groep` | Officiële, door FarmaFlux geleverde cijfers — Genk-totaal en/of per groep | `periode · cnk_code · product · registraties · groep` |
| `farmaflux_individueel` | Cijfers die een apotheek **zelf rechtstreeks** aanlevert | `periode · cnk_code · product · registraties · apotheek` |
| `apotheek_groepen` | Bepaalt welke apotheek bij welke groep hoort | `apotheek · groep` |

**Belangrijk principe:** `farmaflux_groep` en `farmaflux_individueel` zijn twee
**volledig losse** databronnen. Ze worden **nooit samengeteld**. Dat is bewust zo:
FarmaFlux levert haar cijfers aggregaat (per groep of als één totaal), en als een
apotheek daarnaast ook zelf cijfers aanlevert, zou je die niet zomaar bij het
FarmaFlux-totaal mogen optellen — dat risico op dubbeltellingen willen we vermijden. Daarom blijven
beide datasets in het dashboard apart zichtbaar, in twee apart getoonde tabellen.

---

## 2. Tabblad `farmaflux_groep`

Dit is het tabblad dat nu al bestaat (vroeger heette het gewoon "farmaflux"),
met één nieuwe kolom: `groep`.

- **Rij zonder `groep`-waarde** → telt gewoon mee in het **Genk-totaal**
  (de bestaande KPI-kaartjes, de balkgrafiek "Totaal per type registratie" en de
  trendgrafiek "Evolutie per periode" — dit blijft exact werken zoals vandaag).
- **Rij mét `groep`-waarde** → telt ook mee in het Genk-totaal, maar verschijnt
  **bovendien** in een extra tabel "Uitsplitsing per groep".

**Voorbeeld** — als FarmaFlux op een gegeven moment per groep rapporteert:

| periode | cnk_code | product | registraties | groep |
|---|---|---|---|---|
| sep-okt 2025 | 5522289 | Sensibilisering HPV | 8 | |
| nov-dec 2025 | 5522289 | Sensibilisering HPV | 5 | Centrum-groep |
| nov-dec 2025 | 5522289 | Sensibilisering HPV | 7 | Noord-groep |
| nov-dec 2025 | 3402799 | Gardasil 9 (vaccin) | 4 | Centrum-groep |

In dit voorbeeld telt de eerste rij (zonder groep) mee in het Genk-totaal, maar
verschijnt niet in de groep-tabel. De andere drie rijen tellen ook mee in het
Genk-totaal, én verschijnen in de tabel "Uitsplitsing per groep" onder
"Centrum-groep" en "Noord-groep".

> Zolang er geen `groep`-kolom ingevuld wordt, blijft alles exact zoals nu: één
> Genk-totaal, geen extra tabel.

---

## 3. Tabblad `farmaflux_individueel`

Dit tabblad maak je pas aan zodra een apotheek effectief eigen cijfers aanlevert
(los van FarmaFlux). Kolommen: `periode · cnk_code · product · registraties · apotheek`.

**Voorbeeld:**

| periode | cnk_code | product | registraties | apotheek |
|---|---|---|---|---|
| sep-okt 2025 | 5522289 | Sensibilisering HPV | 3 | Apotheek Vanhaesebrouck Bvba |
| nov-dec 2025 | 3402799 | Gardasil 9 (vaccin) | 4 | Apotheek Boxberg |
| nov-dec 2025 | 5510185 | Doorverwijzing arts | 6 | Apoteek Mertens |

Zodra dit tabblad minstens één rij heeft, verschijnt automatisch een tabel
"Uitsplitsing per apotheek" — **los van** het Genk-totaal uit `farmaflux_groep`.
Het maakt niet uit of een apotheek hier wel cijfers aanlevert en een andere niet:
elke apotheek staat gewoon op haar eigen rij, er moet niets herberekend worden.

---

## 4. Tabblad `apotheek_groepen`

Dit tabblad bepaalt welke apotheek bij welke groep hoort — puur als naslagtabel,
niet om cijfers in op te tellen. Kolommen: `apotheek · groep`.

| apotheek | groep |
|---|---|
| Apotheek Vanhaesebrouck Bvba | Centrum-groep |
| Apotheek Solidaris Genk | Centrum-groep |
| Apoteek Mertens | Noord-groep |
| Apotheek Boxberg | Noord-groep |

Dit wordt gebruikt om:
- de **groep** te tonen in de kaart-tooltip van elke apotheek-marker;
- de **groep** te tonen als extra kolom in de tabel "Uitsplitsing per apotheek"
  (uit `farmaflux_individueel`), zodat je ook daar meteen ziet bij welke groep
  een apotheek hoort.

Apotheken die hier niet in vermeld staan, vallen automatisch onder **"Overige"**
(in de tooltip) of tonen een streepje (in de tabel) — ze verdwijnen dus nooit
uit beeld. De groepnamen in dit tabblad gebruik je best **consistent** met de
groepnamen die je in `farmaflux_groep` invult (zelfde spelling), zodat alles
logisch samenhangt — maar dit tabblad hoeft hiervoor niet aangepast te worden
als FarmaFlux zelf met groepsindelingen werkt.

---

## 5. Belangrijk: exacte apotheeknamen

De naam in de kolom `apotheek` (in `farmaflux_individueel` én `apotheek_groepen`)
moet **letterlijk** overeenkomen met de naam die het dashboard gebruikt voor de
kaart-markers. Let op hoofdletters, spelling en het woord "Apotheek" vs.
"Apoteek" (sommige staan er met een typfout in, dat is bewust zo overgenomen
uit de brondata):

| Apotheeknaam (exact te gebruiken) | Adres | Stadsdeel |
|---|---|---|
| Apotheek Vanhaesebrouck Bvba | Hoogstraat 100 | Genk-Centrum |
| Apotheek Solidaris Genk | Winterslagstraat 34 | Genk-Centrum |
| Apotheek Bijnens Centrum Cvba | Stationsstraat 26 | Genk-Centrum |
| Apotheek Paredis Genk | Bochtlaan 58 | Genk-Centrum |
| Apoteek Mertens | Zaveldriesstraat 14 | Genk-Noord |
| Apotheek Goddeeris Bvba | Torenlaan 10 | Genk-Noord |
| Apotheek Jenné | Hoevenzavellaan 57 | Genk-Noord |
| Apotheek Duchateau Bvba | Stalenstraat 19 | Genk-Oost |
| Apotheek André Dumont Bvba | André Dumontlaan 50 | Genk-Oost |
| Apoteek G & J Bijnens Bvba | Stalenstraat 52 | Genk-Oost |
| Apotheek Winterslag | Acacialaan 30 | Genk-West |
| Apotheek Geraerts Bvba | Vennestraat 165 | Genk-West |
| Apoteek Lauwers | Boxbergstraat 90 | Genk-West |
| Apotheek Boxberg | Landwaartslaan 34 A | Genk-Midden |
| Apotheek Bollen Els | Sledderloweg 26 | Kolderbos-Langerlo |
| Apotheek Synaps Park | Synaps Park 1190 | Kolderbos-Langerlo |

Typfout in de naam? Dan duikt die apotheek gewoon op als apart, verkeerd
"vakje" in de tabel (bv. onder "Onbekend") — er komt geen foutmelding.
Controleer dus bij twijfel de tabel in het dashboard na het invullen.

---

## 6. Wat zie je dan in het dashboard?

- **Bestaande KPI's, balkgrafiek, trendgrafiek, detailtabel "Detail per periode
  & type"**: blijven altijd het Genk-totaal tonen uit `farmaflux_groep` —
  ongeacht of je groep- of individuele data toevoegt. Dit blijft ongewijzigd.
- **Nieuwe tabel "Uitsplitsing per groep"**: verschijnt automatisch zodra
  minstens één rij in `farmaflux_groep` een `groep`-waarde heeft.
- **Nieuwe tabel "Uitsplitsing per apotheek"**: verschijnt automatisch zodra
  `farmaflux_individueel` minstens één rij heeft. Toont ook de groep
  (uit `apotheek_groepen`) als extra kolom.
- **Kaart**: elke apotheek-marker toont in de tooltip de groep, als die is
  toegekend via `apotheek_groepen`.
- **Niets ingevuld**: er staat een duidelijke tekst die uitlegt welke
  kolommen/tabbladen nodig zijn — zo weet iedereen die er later bijkomt exact
  wat te doen.

---

## 7. Kort samengevat

| Wil je... | Doe dit |
|---|---|
| Alles op Genk-niveau laten (huidige situatie) | Niets doen |
| Per groep zien (FarmaFlux levert dit aan) | Kolom `groep` invullen in `farmaflux_groep` |
| Per apotheek zien (apotheek levert dit zelf aan) | Tabblad `farmaflux_individueel` invullen |
| De groep van elke apotheek tonen op kaart/tabel | Tabblad `apotheek_groepen` invullen |
