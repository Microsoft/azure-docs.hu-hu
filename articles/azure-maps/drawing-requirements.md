---
title: A csomagra vonatkozó követelmények a Microsoft Azure Maps Creatorben (előzetes verzió)
description: Ismerje meg, hogy a rendszer hogyan konvertálja a konstrukciós fájlokat az adat-előkészítési csomag követelményeire
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/08/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 2a37e716b7804b11ab396909f746af84294bb4e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895271"
---
# <a name="drawing-package-requirements"></a>Rajzolási csomag követelményei


> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A feltöltött rajzok leképezési adatai a [Azure Maps átalakítási szolgáltatás](/rest/api/maps/conversion)használatával alakíthatók át. Ez a cikk az átalakítási API-hoz tartozó rajzi csomagok követelményeit ismerteti. Egy minta csomag megtekintéséhez letöltheti a minta [rajzolási csomagot](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Előfeltételek

A rajzfájl DWG formátumban mentett rajzokat tartalmaz, amely az Autodesk AutoCAD® szoftver natív fájlformátuma.

Bármelyik CAD-szoftvert kiválaszthatja a rajzok létrehozásához a rajzolási csomagban.  

A [Azure Maps átalakítási szolgáltatás](/rest/api/maps/conversion) átalakítja a rajzolási csomagot a leképezési adatként. Az átalakítási szolgáltatás az AutoCAD DWG fájlformátumával működik. `AC1032` a a DWG-fájlok belső formátumának verziója, ezért érdemes kiválasztani a `AC1032` belső DWG fájlformátum verzióját.  

## <a name="glossary-of-terms"></a>Kifejezések jegyzéke

Az egyszerű hivatkozáshoz itt talál néhány olyan kifejezést és definíciót, amelyek fontosak a cikk elolvasása során.

| Időszak  | Definíció |
|:-------|:------------|
| Réteg | Egy AutoCAD DWG-réteg.|
| Level | Egy épület területe egy beállított jogosultságszint-emeléssel. Például egy épület padlója. |
| Xref  |Egy AutoCAD DWG fájlformátumban (. DWG) található fájl, amely külső hivatkozásként van csatolva az elsődleges rajzhoz.  |
| Szolgáltatás | Egy olyan objektum, amely több metaadat-információval kombinálja a geometriát. |
| Szolgáltatási osztályok | A funkciók közös tervrajza. Egy *egység* például egy szolgáltatás osztály, az *Office* pedig egy szolgáltatás. |

## <a name="drawing-package-structure"></a>Rajzolási csomag szerkezete

A rajzfájl egy. zip-archívum, amely a következő fájlokat tartalmazza:

* DWG-fájlok az AutoCAD DWG-fájlformátumban.
* A rajzfájl DWG-fájljait leíró fájl _manifest.js_ .

A rajzfájl csak egyetlen archív fájlba tömöríthető, a. zip kiterjesztéssel. A DWG-fájlok bármilyen módon rendezhetők a csomagban belül, de a jegyzékfájlnak a tömörített csomag gyökérkönyvtárában kell futnia. A következő részekben részletesen ismertetjük a DWG-fájlokra, a jegyzékfájlokra és a fájlok tartalmára vonatkozó követelményeket.

## <a name="dwg-files-requirements"></a>DWG-fájlok követelményei

A létesítmény egyes szintjeihez egyetlen DWG-fájl szükséges. A szintnek csak egyetlen DWG-fájlban kell szerepelnie. A külső hivatkozásokat (_xrefeket_) a fölérendelt rajzhoz kell kötni. Továbbá minden DWG-fájl:

* Meg kell határoznia a _külső_ és az _egység_ rétegeit. A következő választható rétegeket is megadhatja: _Wall_, _Door_, _UnitLabel_, _Zone_ és _ZoneLabel_.
* A nem tartalmazhat több szintből származó szolgáltatásokat.
* A nem tartalmazhat több létesítményből származó szolgáltatásokat.
* Ugyanarra a mérőrendszer-egységre és mértékegységre kell hivatkoznia, mint a rajzfájl többi DWG-fájlja.

A [Azure Maps konverziós szolgáltatás](/rest/api/maps/conversion) a következő szolgáltatási osztályokat tudja kibontani egy DWG-fájlból:

* Szintek
* Egység
* Zóna
* Nyílások
* Falvastagságát
* Vertikális behatolások

Az összes konverziós feladat az alapértelmezett kategóriák minimális készletét eredményezi: Room, Structure. Wall, Opening. Door, Zone és Facility. Az objektumok által hivatkozott egyes kategóriákhoz további kategóriák tartoznak.  

A DWG-rétegeknek egyetlen osztály funkcióit kell tartalmazniuk. Az osztályok nem oszthatnak meg réteget. Az egységek és a falak például nem oszthatnak meg réteget.

A DWG-rétegeknek a következő feltételeket is követniük kell:

* Az összes DWG-fájlhoz tartozó rajzok eredetét ugyanahhoz a szélességhez és hosszúsághoz kell igazítani.
* Minden szintnek ugyanabban a tájolásban kell lennie, mint a többi szintnek.
* Az önmetsző sokszögek automatikusan kijavításra kerülnek, és a [Azure Maps konverziós szolgáltatás](/rest/api/maps/conversion) figyelmeztetést vált ki. Célszerű manuálisan megvizsgálni a kijavított eredményeket, mert előfordulhat, hogy nem egyeznek a várt eredményekkel.

Minden rétegbeli entitásnak a következő típusok egyikének kell lennie: vonal, vonallánc, sokszög, körkörös ív, kör, ellipszis (lezárt) vagy szöveg (egyetlen vonal). A rendszer figyelmen kívül hagyja a többi entitás típusát.

Az alábbi táblázat az egyes rétegekhez tartozó támogatott entitások típusait és átalakított leképezési funkcióit ismerteti. Ha egy réteg nem támogatott entitás-típusokat tartalmaz, akkor a [Azure Maps konverziós szolgáltatás](/rest/api/maps/conversion) figyelmen kívül hagyja ezeket az entitásokat.  

| Réteg | Entitástípusok | Konvertált funkciók |
| :----- | :-------------------| :-------
| [Külső](#exterior-layer) | Sokszög, vonallánc (lezárt), kör, ellipszis (lezárva) | Szintek
| [Unit (Egység)](#unit-layer) |  Sokszög, vonallánc (lezárt), kör, ellipszis (lezárva) | Vertikális behatolások, egység
| [Fal](#wall-layer)  | Sokszög, vonallánc (lezárt), kör, ellipszis (lezárva) | Nem alkalmazható. További információért lásd a [fal réteget](#wall-layer).
| [Ajtó](#door-layer) | Sokszög, vonallánc, vonal, CircularArc, kör | Nyílások
| [Zóna](#zone-layer) | Sokszög, vonallánc (lezárt), kör, ellipszis (lezárva) | Zóna
| [UnitLabel](#unitlabel-layer) | Szöveg (egyetlen sor) | Nem alkalmazható. Ez a réteg csak tulajdonságokat adhat hozzá az egység-funkciókhoz az egység rétegből. További információ: [UnitLabel réteg](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Szöveg (egyetlen sor) | Nem alkalmazható. Ez a réteg csak a ZonesLayer származó tulajdonságokat adhat hozzá a zónák funkcióihoz. További információ: [ZoneLabel réteg](#zonelabel-layer).

A következő részek részletezik az egyes rétegekre vonatkozó követelményeket.

### <a name="exterior-layer"></a>Külső réteg

Az egyes szintekhez tartozó DWG-fájlnak tartalmaznia kell egy réteget, amely meghatározza a szint kerületét. Ezt a réteget *külső* rétegnek nevezzük. Ha például egy létesítmény két szintet tartalmaz, akkor két DWG-fájllal kell rendelkeznie, amelyek mindegyike külső réteggel rendelkezik.

Függetlenül attól, hogy hány entitásos rajz van a külső rétegben, az [eredményül kapott létesítmény adatkészlete](tutorial-creator-indoor-maps.md#create-a-feature-stateset) csak egyetlen szintű szolgáltatást fog tartalmazni az egyes DWG-fájlokhoz. Továbbá:

* A külsőket sokszögként, vonalláncként (zárt), körként vagy Ellipszisként kell kirajzolni (lezárva).
* A külső adatok átfedésben lehetnek, de egy geometriában vannak feloldva.
* Az eredményül kapott szint szolgáltatásnak legalább 4 négyzetméterből kell állnia.
* Az eredményül kapott szint funkció nem lehet nagyobb 400 000 négyzetméternél.

Ha a réteg több átfedésben lévő vonalláncot tartalmaz, a vonalláncok egyetlen szintű szolgáltatásba vannak feloldva. Ha a réteg több nem átfedésben lévő vonalláncot tartalmaz, akkor az eredményül kapott szint funkció több sokszöges ábrázolással rendelkezik.

Megtekintheti a külső réteg példáját, mint a vázlat réteget a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Egység réteg

Az egyes szintek DWG-fájlja az egységeket tartalmazó réteget határozza meg. Az egységek az épületben navigálható szóközök, például irodák, folyosók, lépcsők és felvonók. Ha a `VerticalPenetrationCategory` tulajdonság definiálva van, a több szintet, például a felvonókat és a lépcsőket átölelő navigáló egységeket a rendszer vertikális behatolási funkciókra konvertálja. Az egymással átfedésben lévő vertikális bevezetési funkciókhoz hozzá van rendelve egy `setid` .

Az egység rétegnek az alábbi követelményeknek kell megfelelnie:

* Az egységeket sokszögként, vonalláncként (zárt), körként vagy Ellipszisként kell kirajzolni (lezárva).
* Az egységeknek a létesítmény külső kerületének határain belül kell esniük.
* Az egységek nem lehetnek részben átfedésben.
* Az egységek nem tartalmazhatnak önmetsző geometriát.

Az egység elnevezéséhez hozzon létre egy Text objektumot a UnitLabel rétegben, majd helyezze el az objektumot az egység határain belül. További információ: [UnitLabel réteg](#unitlabel-layer).

Láthatja az egység réteg példáját a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Fal réteg

Az egyes szintekhez tartozó DWG-fájl tartalmazhat olyan réteget, amely meghatározza a falak, oszlopok és egyéb építési struktúra fizikai egységeit.

* A falakat sokszögként, vonalláncként (zárt), körként vagy Ellipszisként kell kirajzolni (lezárva).
* A fal rétegének vagy rétegeinek csak olyan geometriát kell tartalmazniuk, amelyet építési struktúraként kell értelmezni.

A [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples)láthatók a falak rétegre mutató példa.

### <a name="door-layer"></a>Ajtó rétege

Tartalmazhat ajtókat tartalmazó DWG-réteget is. Mindegyik ajtónak átfedésben kell lennie egy egység szélével az egység rétegben.

A Azure Maps adatkészletben lévő ajtók megnyitása egysoros szegmens, amely több egység határait fedi le. Az alábbi képek bemutatják, hogyan alakíthatja át a geometriát az ajtó rétegében egy adatkészlet szolgáltatásainak megnyitásához.

![Négy grafikus elem, amely bemutatja a megnyitások létrehozásának lépéseit](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zóna réteg

Az egyes szintek DWG-fájlja tartalmazhat olyan zóna réteget, amely meghatározza a zónák fizikai egységeit. A zóna egy nem navigálható terület, amely elnevezhető és megjeleníthető. A zónák több szintre is terjedhetnek, és a zoneSetId tulajdonsággal együtt vannak csoportosítva.

* A zónákat sokszögként, vonalláncként (zárt) vagy Ellipszisként (lezárva) kell kirajzolni.
* A zónák átfedésben lehetnek.
* A zónák a létesítmény külső peremén belül vagy kívül esnek.

A zóna elnevezéséhez hozzon létre egy Text objektumot a ZoneLabel rétegben, és helyezze a szöveg objektumot a zóna határain belülre. További információ: [ZoneLabel réteg](#zonelabel-layer).

Láthatja a zóna réteget a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>UnitLabel réteg

Az egyes szintek DWG-fájlja UnitLabel réteget tartalmazhat. A UnitLabel réteg hozzáadja a Name (név) tulajdonságot az egység rétegből kinyert egységekhez. A name tulajdonsággal rendelkező egységek további részleteket adhatnak meg a jegyzékfájlban.

* Az egység feliratának egysoros szöveges entitásnak kell lennie.
* Az egység címkéjének az egység határain belül kell esnie.
* Az egységek nem tartalmazhatnak több szöveges entitást a UnitLabel rétegben.

Láthat egy példát a UnitLabel rétegre a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel réteg

Az egyes szintek DWG-fájlja ZoneLabel réteget tartalmazhat. Ez a réteg hozzáadja a Name (név) tulajdonságot a zóna rétegből kinyert zónákhoz. A name tulajdonsággal rendelkező zónák további részleteket adhatnak meg a jegyzékfájlban.

* A zónák címkéjének egysoros szöveges entitásnak kell lennie.
* A zónák címkéjének a zóna határain belül kell esnie.
* A zónák nem tartalmazhatnak több szöveges entitást a ZoneLabel rétegben.

Láthat egy példát a ZoneLabel rétegre a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Manifest-fájlra vonatkozó követelmények

A zip-mappának tartalmaznia kell egy jegyzékfájlt a könyvtár legfelső szintjén, és a fájlnak **manifest.js** nevűnek kell lennie. Leírja azokat a DWG-fájlokat, amelyek lehetővé teszik a [Azure Maps átalakítási szolgáltatás](/rest/api/maps/conversion) számára a tartalom elemzését. Csak a jegyzékfájl által azonosított fájlok kerülnek betöltésre. A rendszer figyelmen kívül hagyja a zip mappában található, de a jegyzékfájlban nem szereplő fájlokat.

A jegyzékfájl objektumában lévő fájlelérési utaknak a `buildingLevels` zip-mappa gyökeréhez viszonyítva kell lenniük. A DWG-fájl nevének pontosan egyeznie kell a létesítmény szintjének nevével. Például az "alagsor" szinthez tartozó DWG-fájl "alagsor. DWG". A 2. szintű DWG-fájl neve "level_2. DWG". Ha a szint neve szóközt tartalmaz, használjon aláhúzást.

Bár a jegyzékfájl-objektumok használatakor követelmények vannak, nem minden objektumra van szükség. A következő táblázat a [Azure Maps átalakítási szolgáltatás](/rest/api/maps/conversion)1,1-es verziójához szükséges és választható objektumokat tartalmazza.

| Objektum | Kötelező | Leírás |
| :----- | :------- | :------- |
| `version` | true |Jegyzékfájl-séma verziója. Jelenleg csak a 1,1-es verzió támogatott.|
| `directoryInfo` | true | Felvázolja a létesítmény földrajzi és kapcsolattartási adatait. Az utas földrajzi és kapcsolattartási adatainak tagolására is használható. |
| `buildingLevels` | true | Meghatározza az épületek szintjét és a szintek kialakítását tartalmazó fájlokat. |
| `georeference` | true | Numerikus földrajzi adatokat tartalmaz a létesítmény rajzolásához. |
| `dwgLayers` | true | Felsorolja a rétegek nevét, és mindegyik réteg felsorolja a saját szolgáltatásainak nevét. |
| `unitProperties` | hamis | Az egység funkcióinak további metaadatainak beszúrására használható. |
| `zoneProperties` | hamis | További metaadatok beszúrására használható a zóna szolgáltatásaihoz. |

A következő részek részletezik az egyes objektumok követelményeit.

### `directoryInfo`

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
| `name`      | sztring | true   |  Az építési név. |
| `streetAddress`|    sztring |    hamis    | Az építési címe. |
|`unit`     | sztring    |  hamis    |  Egység az épületben. |
| `locality` |    sztring |    hamis |    Terület, környék vagy régió neve. Például: "túltó" vagy "központi körzet". A helység nem része a levelezési címnek. |
| `adminDivisions` |    Karakterláncok JSON-tömbje |    hamis     | A címek megjelölését (ország, állam, város) vagy (ország, prefektúra, város, város) tartalmazó tömb. Az ISO 3166 országkódok és az ISO 3166-2 állapot/terület kódok használata. |
| `postalCode` |    sztring    | hamis    | A levél rendezési kódja. |
| `hoursOfOperation` |    sztring |     hamis | Betartja az [OSM nyitvatartási idejét](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| `phone`    | sztring |    hamis |    A létrehozáshoz társított telefonszám. Tartalmaznia kell az országkódot. |
| `website`    | sztring |    hamis    | Az épületben társított webhely. Http vagy https előtaggal kell kezdődnie. |
| `nonPublic` |    logikai    | hamis | Jelölő, amely megadja, hogy az épület nyitva van-e a nyilvánosság számára. |
| `anchorLatitude` | numerikus |    hamis | A létesítményi horgony (gombostű) földrajzi szélessége |
| `anchorLongitude` | numerikus |    hamis | A létesítményi horgony (gombostű) földrajzi hosszúsága. |
| `anchorHeightAboveSeaLevel`  | numerikus | hamis | A létesítmény talajszintének magassága a tengerszint felett, méterben. |
| `defaultLevelVerticalExtent` | numerikus | hamis | A létesítmény szintjének alapértelmezett magassága (vastagsága), ha egy szint `verticalExtent` nincs meghatározva. |

### `buildingLevels`

Az `buildingLevels` objektum az épületek szintjeinek JSON-tömbjét tartalmazza.

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|`levelName`    |sztring    |true |    Leíró szint neve. Például: Floor 1, lobby, Blue parkoló vagy alagsor.|
|`ordinal` | egész szám |    true | Meghatározza a szintek függőleges sorrendjét. Minden létesítménynek 0 sorszámú szinten kell lennie. |
|`heightAboveFacilityAnchor` | numerikus | hamis |    A rögzítési magasság meghaladja a mérőórákat. |
| `verticalExtent` | numerikus | hamis | A szint maximális magassága (vastagsága) méterben. |
|`filename` |    sztring |    true |    A CAD-rajz fájlrendszerbeli elérési útja egy építési szinthez. Az értéknek az épületben található zip-fájl gyökeréhez viszonyítva kell lennie. |

### `georeference`

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|`lat`    | numerikus |    true |    A mértékek szélességének decimális ábrázolása a létesítmény-rajz forrásaként. A forrás koordinátáinak a WGS84 web Mercator () értékkel kell rendelkezniük `EPSG:3857` .|
|`lon`    |numerikus|    true|    A fok hosszúságának decimális ábrázolása a létesítmény-rajz forrásaként. A forrás koordinátáinak a WGS84 web Mercator () értékkel kell rendelkezniük `EPSG:3857` . |
|`angle`|    numerikus|    true|   Az óramutató és a rajz függőleges (Y) tengelye közötti szögben, fokban megadva.   |

### `dwgLayers`

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|`exterior`    |sztringek tömbje|    true|    A külső építési profilt meghatározó rétegek nevei.|
|`unit`|    sztringek tömbje|    true|    Az egységeket meghatározó rétegek nevei.|
|`wall`|    sztringek tömbje    |hamis|    A falakat meghatározó rétegek nevei.|
|`door`    |sztringek tömbje|    hamis   | Az ajtókat meghatározó rétegek nevei.|
|`unitLabel`    |sztringek tömbje|    hamis    |Az egységek nevét meghatározó rétegek nevei.|
|`zone` | sztringek tömbje    | hamis    | A zónákat meghatározó rétegek nevei.|
|`zoneLabel` | sztringek tömbje |     hamis |    A zónák nevét meghatározó rétegek nevei.|

### `unitProperties`

Az `unitProperties` objektum az egység tulajdonságainak JSON-tömbjét tartalmazza.

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|`unitName`    |sztring    |true    |A rekorddal társítandó egység neve `unitProperty` . Ez a rekord csak akkor érvényes, ha a rétegekben címkével egyező címke `unitName` található `unitLabel` . |
|`categoryName`|    sztring|    hamis    |Kategória neve A kategóriák teljes listájáért tekintse meg a [kategóriákat](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| sztringek tömbje |    hamis    |Az egységet áthaladó navigáló ügynökök típusát jelöli. Ez a tulajdonság tájékoztatja a wayfinding képességeit. A megengedett értékek a következők:,,,,,,,,,, `pedestrian` `wheelchair` `machine` `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` és `disallowed` .|
|`routeThroughBehavior`|    sztring|    hamis    |Az egység viselkedési útvonala. A megengedett értékek: `disallowed` , `allowed` és `preferred` . Az alapértelmezett érték `allowed`.|
|`occupants`    |directoryInfo objektumok tömbje |hamis    |Az egység utasainak listája. |
|`nameAlt`|    sztring|    hamis|    Az egység alternatív neve. |
|`nameSubtitle`|    sztring    |hamis|    Az egység alcíme. |
|`addressRoomNumber`|    sztring|    hamis|    Az egységhez tartozó helyiség, egység, lakás vagy csomag száma.|
|`verticalPenetrationCategory`|    sztring|    hamis| Ha ez a tulajdonság meg van adva, az eredményül kapott funkció egy függőleges behatolás (VRT), nem pedig egy egység. A VRTs segítségével más VRT-funkciókhoz is hozzáférhet a fenti vagy alatti szinteken. A függőleges behatolás a [Kategória](https://aka.ms/pa-indoor-spacecategories) neve. Ha ez a tulajdonság meg van adva, a `categoryName` rendszer felülbírálja a tulajdonságot `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    sztring|    hamis    |Ha `verticalPenetrationCategory` meg van adva, nem kötelezően megadhatja az utazás érvényes irányát. A megengedett értékek a következők: `lowToHigh` ,, `highToLow` `both` és `closed` . Az alapértelmezett érték `both`.|
| `nonPublic` | logikai | hamis | Azt jelzi, hogy az egység nyitva van-e a nyilvános számára. |
| `isRoutable` | logikai | hamis | Ha ez a tulajdonság a értékre van beállítva `false` , nem mehet az egységre vagy az-ra. Az alapértelmezett érték `true`. |
| `isOpenArea` | logikai | hamis | Lehetővé teszi, hogy a navigáló ügynök belépjen az egységbe anélkül, hogy szükség lenne az egységhez csatolt megnyitásra. Alapértelmezés szerint ez az érték olyan egységekhez van beállítva, `true` amelyek nincsenek nyitottak, és `false` a nyitó egységeket. Ha manuálisan állítja be a beállítást `isOpenArea` `false` egy olyan egységre, amely nem rendelkezik megnyitásokkal, a rendszer figyelmeztetést jelenít meg, mert az eredményül kapott egység nem érhető el egy navigáló ügynöknél.|

### `zoneProperties`

Az `zoneProperties` objektum a zóna tulajdonságainak JSON-tömbjét tartalmazza.

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|zoneName        |sztring    |true    |A rekordhoz társítandó zóna neve `zoneProperty` . Ez a rekord csak akkor érvényes, ha a `zoneName` zóna rétegében szerepel a címke egyezése `zoneLabel` .  |
|categoryName|    sztring|    hamis    |Kategória neve A kategóriák teljes listájáért tekintse meg a [kategóriákat](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    sztring|    hamis    |A zóna alternatív neve.  |
|zoneNameSubtitle|    sztring |    hamis    |A zóna alcíme. |
|zoneSetId|    sztring |    hamis    | Állítsa be az azonosítót úgy, hogy kapcsolatot hozzon létre több zóna között, hogy azok lekérdezéssel vagy csoportként legyenek kijelölve. Például több szintet átölelő zónák. |

### <a name="sample-drawing-package-manifest"></a>Minta rajzolási csomag jegyzékfájlja

Alább látható a minta rajzolási csomag jegyzékfájlja. A teljes csomag letöltéséhez lásd: [minta rajzolási csomag](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Jegyzékfájl

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

Ha a rajzfájl megfelel a követelményeknek, a [Azure Maps átalakítási szolgáltatással](/rest/api/maps/conversion) átalakíthatja a csomagot térképi adatkészletbe. Ezt követően használhatja az adatkészletet egy beltéri Térkép létrehozásához a beltéri térképek modul használatával.

> [!div class="nextstepaction"]
>[Creator (előzetes verzió) beltéri térképekhez](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Creator (előzetes verzió) – beltéri Térkép létrehozása](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Beltéri térképek dinamikus stílusa](indoor-map-dynamic-styling.md)