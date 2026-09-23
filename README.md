# Projeksjonsinstrumentet

Et interaktivt verktøy for å utforske hvordan verdenskartet endrer form avhengig av
hvilken kartprojeksjon man velger. Samme landegrenser, samme graticule — bare projisert
på over 30 forskjellige måter, fra Web Mercator til det hjerteformede Bonne-kartet fra
1500-tallet.

Leveres som **selvstendige HTML-filer**. Ingen
installasjon, ingen build-steg — bare åpne filene i en nettleser.

## EPSG-guessr — spillet

`epsg-guessr.html` er et frittstående GeoGuessr-inspirert gjettespill bygget på samme
projeksjonskatalog og landedata. Du får se et lite, tilfeldig utsnitt av et verdenskart
— noen ganger hele projeksjonen, noen ganger bare et hjørne med land og gradnett — og
skal gjette hvilken EPSG-/ESRI-kode kartet er projisert i, enten ved å velge blant fire
alternativer eller (i **Geonerd-modus**) skrive inn koden fra hukommelsen uten
alternativer å støtte seg på. Katalogen har 51 projeksjoner (verdensprojeksjoner,
polare/regionale systemer og en håndfull UTM-/Lambert-/Albers-soner rundt om i verden),
og hver runde krymper og panorerer tilfeldig for å gi stor variasjon selv med samme
projeksjon. Se lenken øverst i `index.html`, eller åpne `epsg-guessr.html` direkte.

## Hva verktøyet gjør

- Viser 180 land (forenklet GeoJSON, ~110 m oppløsning) projisert med valgt projeksjon
- Søk på EPSG- eller ESRI-kode, eller bla gjennom kort gruppert etter projeksjonsfamilie
- Regionale/nasjonale systemer (UTM-soner, Lambert-93-type konforme kart osv.) tilpasses
  automatisk til sitt relevante geografiske utsnitt, med nabolandene synlige
- Zoom og panorering (scrollhjul, dra, klyp på mobil, eller +/−/nullstill-knapper)
- Valgfri visning av **Tissots indikatrix** — sirkler med lik radius på jordoverflaten,
  som gjør lokal forvrengning (vinkel vs. areal) synlig direkte på kartet
- Responsivt design, lys/mørk fargemodus etter systeminnstilling

## Prosjeksjonskatalog

32 oppføringer, gruppert etter familie. Koder uten offisiell EPSG/ESRI-kode (klassiske/
historiske projeksjoner, eller — for AuthaGraph — en patentert projeksjon uten
registrert kode) vises med navn i stedet for kode.

### Sylindriske
| Kode | Navn |
|---|---|
| EPSG:4326 | WGS84 — geografisk (Plate Carrée) |
| EPSG:3857 | WGS84 / Pseudo-Mercator (Web Mercator) |
| EPSG:3395 | WGS84 / World Mercator |
| EPSG:4087 | WGS84 / World Equidistant Cylindrical |
| EPSG:6933 | WGS84 / NSIDC EASE-Grid 2.0 |
| GALLPETERS | Gall-Peters |
| ESRI:MILLER¹ | Miller Cylindrical |

### Pseudosylindriske
| Kode | Navn |
|---|---|
| ESRI:54030 | Robinson |
| ESRI:54009 | Mollweide |
| EPSG:8857 | WGS84 / Equal Earth Greenwich |

### Asimutale
| Kode | Navn |
|---|---|
| EPSG:2163 | US National Atlas Equal Area (avviklet, se EPSG:9311) |
| EPSG:3035 | ETRS89-extended / LAEA Europe |
| EPSG:3413 | WGS84 / NSIDC Polar Stereographic North |
| EPSG:3031 | WGS84 / Antarctic Polar Stereographic |
| EPSG:6931 | WGS84 / NSIDC EASE-Grid 2.0 North |
| EPSG:6932 | WGS84 / NSIDC EASE-Grid 2.0 South |

### Pseudoasimutale
| Kode | Navn |
|---|---|
| AITOFF | Aitoff |
| HAMMER | Hammer |

### Koniske
| Kode | Navn |
|---|---|
| EPSG:5070 | NAD83 / Conus Albers |
| EPSG:3577 | GDA94 / Australian Albers |
| BONNE | Bonne (kordiform/hjerteform) |

### Tverraksiale (Transverse Mercator / UTM)
| Kode | Navn |
|---|---|
| EPSG:25832 | ETRS89 / UTM sone 32N |
| EPSG:25833 | ETRS89 / UTM sone 33N |
| EPSG:32633 | WGS84 / UTM sone 33N |
| EPSG:32718 | WGS84 / UTM sone 18S |
| EPSG:32736 | WGS84 / UTM sone 36S |
| EPSG:27700 | OSGB36 / British National Grid |
| EPSG:3006 | SWEREF99 TM |

### Avbrutte projeksjoner
| Kode | Navn |
|---|---|
| GOODE | Goode Homolosine (avbrutt) |

### Andre projeksjoner
| Kode | Navn |
|---|---|
| EPSG:5880 | SIRGAS 2000 / Brazil Polyconic |
| ESRI:54029 | Van der Grinten I |

### Polyedriske
| Kode | Navn |
|---|---|
| AUTHAGRAPH¹ | AuthaGraph (Imago-tilnærming) |

¹ MILLER, GOODE, BONNE, AITOFF, HAMMER, GALLPETERS og AUTHAGRAPH har ingen offisiell
EPSG/ESRI-kode i søkbar forstand — de er interne identifikatorer i katalogen, ikke
registrerte koder. Vises i verktøyet uten kode-prefiks. AuthaGraph har i tillegg aldri
fått en registrerbar kode fordi den er en patentert, kommersielt lisensiert projeksjon
(se eget avsnitt under).

## Tekniske detaljer

- **Kartbibliotek:** [D3 v7](https://d3js.org/) (`d3-geo`) for selve projeksjonene,
  [d3-geo-projection v4](https://github.com/d3/d3-geo-projection) for de utvidede
  projeksjonene (Robinson, Mollweide, Bonne, Aitoff, Hammer, Goode Homolosine,
  Van der Grinten, Miller, Polyconic, Cylindrical Equal-Area) og
  [d3-geo-polygon v2](https://github.com/d3/d3-geo-polygon) for AuthaGraph/Imago
  (polyedrisk projeksjon med sfærisk polygon-klipping). Lastes fra cdnjs/jsDelivr.
- **Equal Earth (EPSG:8857)** finnes ikke i d3-geo-projection og er implementert direkte
  fra de publiserte likningene (Šavrič, Patterson & Jenny, 2018).
- **AuthaGraph** er ikke en åpen projeksjon: Hajime Narukawa har patent på metoden og har
  aldri publisert de eksakte likningene den ekte AuthaGraph bruker. Katalogen viser derfor
  «Imago» — en uavhengig, fritt publisert tetraeder-projeksjon av Justin Kunimune (2017)
  konstruert med samme grunnidé (dele kloden på et tetraeder, brette ut arealbevarende),
  men med egne, åpne likninger. Formen ligner AuthaGraph visuelt (parameteren `k=0.68`,
  som Kunimune selv oppgir som nærmeste visuelle tilnærming), men er ikke en kopi av det
  patenterte kartet.
- **Regionale/nasjonale projeksjoner** (alle med `extent` i katalogen) tilpasses (fittes)
  til et eget geografisk utsnitt i stedet for hele verden — ellers ville projeksjonen
  blitt ekstremt forvrengt langt fra sitt origo, og selve regionen ville krympet til et
  punkt på kartet.
- **Datakilde:** forenklet verdens-GeoJSON (180 land, avrundede koordinater) bakt rett
  inn i HTML-filen — ingen eksterne kall for selve kartdataene.

## Kjente forenklinger

Dette er et pedagogisk verktøy for å sammenligne projeksjonstyper — ikke et
produksjonsverktøy for geodetisk transformasjon. Konkret:

- **Ingen datumtransformasjon.** Projeksjonene bygges med d3s standardformler og samme
  sentrum/standardparalleller som den offisielle definisjonen, men uten ekte
  geodetisk datumskifte. For eksempel er forskjellen mellom WGS84 og ETRS89 (EPSG:32633
  vs. 25833) i dag omtrent én meter og vokser — det vises ikke i denne demoen.
- **Enkelte polare projeksjoner er forenklet.** 3413 og 3031 er definert med
  standardparalleller på hhv. 70°N/71°S i den offisielle spesifikasjonen, men vises her
  som ren (uskalert) stereografisk projeksjon.
- **EPSG:2163** er formelt avviklet (deprecated) til fordel for EPSG:9311, som har
  identisk geometri — begge er inkludert her under 2163 av pedagogiske grunner.
- For full geodetisk nøyaktighet: bruk [PROJ](https://proj.org/)/proj4 med de offisielle
  definisjonene fra [epsg.io](https://epsg.io/).

## Kjøre lokalt

Ingen server nødvendig — åpne `index.html` direkte i en nettleser.
Filen er selvstendig bortsett fra to CDN-script (D3 og d3-geo-projection) og Google
Fonts, så en internettforbindelse trengs ved første lasting.

## Lisens / bruk

Landegrensedata er en forenklet, offentlig tilgjengelig verdens-GeoJSON. Verktøyet er
laget som en undervisnings-/demonstrasjonsressurs.
