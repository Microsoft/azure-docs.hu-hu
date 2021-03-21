---
title: 'Oktatóanyag: webszolgáltatások migrálása a Bing Maps szolgáltatásból | Microsoft Azure térképek'
description: Útmutató a webszolgáltatások Bing Maps rendszerből Microsoft Azure Maps-be történő áttelepítéséhez.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6024aae68183fbe02125ef4207e9fbce8abd6a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679065"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>Oktatóanyag: webszolgáltatás migrálása a Bing Mapsből

Az Azure és a Bing Maps is hozzáférést biztosít a térbeli API-khoz a REST-alapú webszolgáltatásokon keresztül. A platformok API-felületei hasonló funkciókat hajtanak végre, de eltérő elnevezési konvenciókat és válasz-objektumokat használnak. Az oktatóanyag során a következőket fogja elsajátítani:

> * Helymeghatározáshoz továbbítása és sztornírozása
> * Hasznos helyek keresése
> * Útvonalak és irányok kiszámítása
> * Térkép rendszerképének beolvasása
> * Távolsági mátrix kiszámítása
> * Időzóna részleteinek beolvasása

Az alábbi táblázat tartalmazza a Azure Maps Service API-kat, amelyek hasonló funkciókat biztosítanak a felsorolt Bing Maps Service API-khoz.

| Bing Maps Service API                 | Azure Maps Service API      |
|---------------------------------------|-----------------------------|
| Automatikus kiegészítés                           | [Search](/rest/api/maps/search)     |
| Irányok (beleértve a teherautót)          | [Útvonal iránya](/rest/api/maps/route/getroutedirections)                          |
| Távolsági mátrix                       | [Útvonal-mátrix](/rest/api/maps/route/postroutematrixpreview)                          |
| Képek – statikus Térkép                  | [Megjelenítés](/rest/api/maps/render/getmapimage)                                   |
| Izokrón                            | [Útvonal tartománya](/rest/api/maps/route/getrouterange)                                    |
| Local Insights                        | [Keresés](/rest/api/maps/search)  +  [Útvonal tartománya](/rest/api/maps/route/getrouterange)    |
| Helyi keresés                          | [Search](/rest/api/maps/search)     |
| Hely felismerése (POI)           | [Search](/rest/api/maps/search)     |
| Helyszínek (továbbítás/fordított helymeghatározáshoz) | [Search](/rest/api/maps/search)                                               |
| Elérési út                          | [Útvonal irányának elküldése](/rest/api/maps/route/postroutedirections)                         |
| Térbeli Data Services (SDS)           | [Keresés](/rest/api/maps/search)  +  [Route](/rest/api/maps/route) + más Azure-szolgáltatások |
| Időzóna                             | [Időzóna](/rest/api/maps/timezone)  |
| Forgalmi incidensek                     | [Forgalmi incidens részletei](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Jogosultságszint                             | [Jogosultságszint-emelés (előzetes verzió)](/rest/api/maps/elevation)

A következő szolgáltatási API-k jelenleg nem érhetők el Azure Mapsban:

-   Optimalizált útvonal-útvonalak – tervezett. A Azure Maps Route API egyetlen jármű esetében támogatja az utazási ügynökök optimalizálását.
-   Képi metaadatok – elsősorban a csempe URL-címeinek a Bing Maps-ben való beolvasásához használatos. A Azure Maps önálló szolgáltatással rendelkezik a térképes csempék közvetlen eléréséhez.

A Azure Maps számos további REST-webszolgáltatással rendelkezik, amelyek érdekesek lehetnek;

-   [Azure Maps Creator (előzetes verzió) ](./creator-indoor-maps.md) – hozzon létre egy egyéni, privát digitális, különálló épületeket és szóközöket.
-   [Térbeli műveletek](/rest/api/maps/spatial) – összetett térbeli számítások és műveletek (például geokerítések) kiszervezése szolgáltatáshoz.
-   [Csempék leképezése](/rest/api/maps/render/getmaptile) – a Azure Maps a raszteres és vektoros csempék elérését a közúti és a képi csempéket.
-   [Batch-útválasztás](/rest/api/maps/route/postroutedirectionsbatchpreview) – lehetővé teszi, hogy legfeljebb 1 000 útválasztási kérelem legyen egy kötegben egy adott időszakban. Az útvonalakat párhuzamosan kell kiszámítani a kiszolgálón a gyorsabb feldolgozás érdekében.
-   [Forgalom](/rest/api/maps/traffic) Flow – a valós idejű adatforgalomra vonatkozó adatokat raszteres és vektoros csempék is elérheti.
-   [Térinformatikai API (előzetes verzió)](/rest/api/maps/geolocation/getiptolocationpreview) – egy IP-cím helyének beolvasása.
-   [Időjárási szolgáltatások](/rest/api/maps/weather) – hozzáférhet a valós idejű és az előrejelzési időjárási értékekhez.

Ügyeljen arra, hogy az alábbi ajánlott eljárásokat ismertető útmutatók is megtekinthetők:

-   [Ajánlott keresési eljárások](./how-to-use-best-practices-for-search.md)
-   [Ajánlott eljárások az útválasztáshoz](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Helymeghatározáshoz-címek

A helymeghatározáshoz a címek (például `"1 Microsoft way, Redmond, WA"` ) egy koordináta (például a következő hosszúság:-122,1298, szélesség: 47,64005) átalakításának folyamata. A koordinátákat a rendszer gyakran arra használja, hogy a gombostű a térképen vagy középre mutasson.

A Azure Maps számos módszert biztosít a helymeghatározáshoz-címekhez.

-   [Szabad formátumú helymeghatározáshoz](/rest/api/maps/search/getsearchaddress): egyetlen címnek (például) kell megadnia `"1 Microsoft way, Redmond, WA"` , és azonnal fel kell dolgoznia a kérést. Ez a szolgáltatás akkor ajánlott, ha gyorsan kell geocode az egyes címeket.
-   [Strukturált helymeghatározáshoz](/rest/api/maps/search/getsearchaddressstructured): egyetlen cím részeit adja meg, például az utca nevét, a várost, az országot és az irányítószámot, és azonnal feldolgozza a kérést. Ez a szolgáltatás akkor javasolt, ha gyorsan kell geocode az egyes címeket, és az adatai már az egyes címekre is bekerülnek.
-   [Batch-helymeghatározáshoz](/rest/api/maps/search/postsearchaddressbatchpreview): hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. A rendszer az összes címet párhuzamosan geokódolva a kiszolgálón, és amikor elkészült, a teljes eredményhalmazt letöltheti. Ez a szolgáltatás nagyméretű adatkészletek helymeghatározáshoz ajánlott.
-   [Fuzzy keresés](/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, a hasznos hely vagy a kamat kategóriája, és azonnal feldolgozhatja a kérést. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók az azonos szövegmezőből származó címekre vagy érdekes pontokra kereshetnek.
-   [Fuzzy batch-keresés](/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

Az alábbi táblázatok a Bing Maps API paramétereit a strukturált és a szabad formátumú helymeghatározáshoz Azure Maps hasonló API-paraméterekkel hivatkoznak.

**Hely címe szerint (strukturált címek)**

| Bing Maps API-paraméter              | Hasonló Azure Maps API-paraméter                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` vagy `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` és `countryCode`                     |
| `locality`                         | `municipality` vagy `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N/A – mindig a Azure Maps által visszaadott, ha elérhető.   |
| `include` (`incl`)               | N/A – az ország ISO2-kódját mindig Azure Maps adja vissza. |
| `key`                              | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)                  | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját. |
| `userRegion` (`ur`)              | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

A Azure Maps a következőket is támogatja:

* `countrySecondarySubdivision` – Megye, körzet
* `countryTertiarySubdivision` -Nevesített területek; kerületek, kantonok, közigazgatási területek
* `ofs` – Az eredmények kombinációja a `maxResults` paraméterrel együtt.

**Hely lekérdezés szerint (Free-Form címe sztring)**

| Bing Maps API-paraméter              | Hasonló Azure Maps API-paraméter      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N/A – mindig a Azure Maps által visszaadott, ha elérhető.  |
| `include` (`incl`)               | N/A – az ország ISO2-kódját mindig Azure Maps adja vissza.  |
| `key`                              | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)                  | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.  |
| `userRegion` (`ur`)              | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

A Azure Maps a következőket is támogatja:

* `typeahead` -Faj, ha a lekérdezés részleges bemenetként lesz értelmezve, és a keresés a prediktív módot (automatikus javaslat/automatikus kiegészítés) adja meg.
* `countrySet` – A ISO2-országok azon kódjai vesszővel tagolt listája, amelyekben a keresést korlátozni szeretné.
* `lat`/`lon`, `topLeft` / `btmRight` , `radius` – Itt adhatja meg a felhasználói helyet és a körzetet, hogy az eredmények továbbra is helyileg legyenek.
* `ofs` – Az eredmények kombinációja a `maxResults` paraméterrel együtt.

A Search szolgáltatás használatának példáját [itt](./how-to-search-for-address.md)találja. Mindenképpen tekintse át az [ajánlott eljárásokat a keresési](./how-to-use-best-practices-for-search.md) dokumentációhoz.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Koordináta fordított geocode (hely keresése pont alapján)

A fordított helymeghatározáshoz az a folyamat, amelynek során a rendszer a földrajzi koordinátákat (például a hosszúság:-122,1298, a Latitude: 47,64005) átalakítja a megközelítő címmé (például `"1 Microsoft way, Redmond, WA"` ).

Azure Maps több fordított helymeghatározáshoz módszert biztosít;

-   [Fordított geocoder](/rest/api/maps/search/getsearchaddressreverse): egyetlen földrajzi koordináta megadásával megtekintheti a hozzávetőleges címeit, és azonnal feldolgozhatja a kérést.
-   [Cross Street fordított geocoder](/rest/api/maps/search/getsearchaddressreversecrossstreet): egyetlen földrajzi koordinátat határozhat meg a közeli Cross Street-információk eléréséhez (például 1 & Main), és azonnal feldolgozza a kérést.
-   [Fordított geocoder](/rest/api/maps/search/postsearchaddressreversebatchpreview): hozzon létre egy legfeljebb 10 000 koordinátákat tartalmazó kérelmet, amely egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter              | Hasonló Azure Maps API-paraméter       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – Lásd az entitás típusának összehasonlító táblázatát alább.    |
| `includeNeighborhood` (`inclnb`)     | N/A – mindig a Azure Maps által visszaadott, ha elérhető.         |
| `include` (`incl`)                   | N/A – az ország ISO2-kódját mindig Azure Maps adja vissza.    |
| `key`                                | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)                      | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.   |
| `userRegion` (`ur`)                  | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

Mindenképpen tekintse át az [ajánlott eljárásokat a keresési](./how-to-use-best-practices-for-search.md) dokumentációhoz.

A Azure Maps fordított helymeghatározáshoz API olyan további funkciókat tartalmaz, amelyek nem érhetők el a Bing Maps-ben, amelyek hasznosak lehetnek az alkalmazások áttelepítésekor való integráláshoz:

* Sebességkorlátozás beolvasása.
* Közúti használati információk beolvasása; helyi út, artériás, korlátozott hozzáférés, rámpa stb.
* A koordináta azon oldala, ahová a koordináta tartozik.

**Entitás típusú összehasonlító táblázat**

A következő táblázat a Bing Maps entitás típusú értékeket a Azure Mapsban található egyenértékű tulajdonságokra hivatkozik.

| Bing Maps-entitás típusa | Hasonló Azure Maps entitás típusa               | Description                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Cím*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Környékén*                             |
| `PopulatedPlace`      | `Municipality` vagy `MunicipalitySubdivision`     | *Város*, *város vagy* alhálózat, vagy a *Super City*     |
| `Postcode1`           | `PostalCodeArea`                                | *Irányítószám* vagy *Irányítószám*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Állam* vagy *megye*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Megye* vagy *körzetek*                    |
| `CountryRegion`       | `Country`                                       | *Ország neve*                             |
|                       | `CountryTertiarySubdivision`                    | *Kerületek*, *kantonok*, *közigazgatási területek*          |

## <a name="get-location-suggestions-autosuggest"></a>Helyekre vonatkozó javaslatok beszerzése (automatikus javaslat)

A Azure Maps Search API számos olyan prediktív módot támogat, amely az automatikus kiegészítési forgatókönyvekhez használható. A Azure Maps [fuzzy Search](/rest/api/maps/search/getsearchfuzzy) API a legtöbbet a Bing Maps automatikus KIEGÉSZÍTÉSi API-jával. A következő API támogatja a prediktív módot is, Hozzáadás `&typeahead=true` a lekérdezéshez;

-   [Szabad formátumú helymeghatározáshoz](/rest/api/maps/search/getsearchaddress): egyetlen címnek (például) kell megadnia `"1 Microsoft way, Redmond, WA"` , és azonnal fel kell dolgoznia a kérést. Ez a szolgáltatás akkor ajánlott, ha gyorsan kell geocode az egyes címeket.
-   [Fuzzy keresés](/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, a hasznos hely vagy a kamat kategóriája, és azonnal feldolgozhatja a kérést. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók az azonos szövegmezőből származó címekre vagy érdekes pontokra kereshetnek.
-   [POI-keresés](/rest/api/maps/search/getsearchpoi): érdeklődési pontok keresése név alapján. Például: `"starbucks"`.
-   [POI-kategória keresése](/rest/api/maps/search/getsearchpoicategory): az érdeklődési pontok kategória szerint kereshetők. Például: "étterem".

## <a name="calculate-routes-and-directions"></a>Útvonalak és irányok kiszámítása

Az útvonalak és irányok kiszámításához Azure Maps használható. Azure Maps a Bing Maps útválasztási szolgáltatása számos különböző funkciót tartalmaz, például:;

* érkezés és távozás időpontja
* valós idejű és prediktív forgalmi útvonalak
* különböző szállítási módok; vezetés, gyaloglás, kamion
* Útpont Order Optimization (Utazási ügynökök)

> [!NOTE]
> Azure Maps az összes Útpontot koordinálni kell. Először a címeket kell geokódolva.

A Azure Maps útválasztási szolgáltatás a következő API-kat biztosítja az útvonalak kiszámításához:

-   [Útvonal kiszámítása](/rest/api/maps/route/getroutedirections): kiszámítja az útvonalat, és azonnal feldolgozza a kérést. Ez az API a GET és a POST kérelmeket is támogatja. A POST kérések használata nagy számú útpont megadása esetén ajánlott, vagy ha sok útvonal-beállítást használ, hogy az URL-cím kérése ne legyen túl hosszú, és problémákat okozzon.
-   [Batch Route](/rest/api/maps/route/postroutedirectionsbatchpreview): hozzon létre egy legfeljebb 1 000 útválasztási kérelmet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.
-   [Mobilitási szolgáltatások (előzetes verzió) ](/rest/api/maps/mobility): útvonalak és irányok kiszámítása a nyilvános átvitel használatával.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter                                    | Hasonló Azure Maps API-paraméter               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` vagy `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N/A                                               |
| `distanceUnit` (`du`)                                      | N/A – Azure Maps csak a metrikus rendszer használatát használja.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType` , `minDeviationDistance` és `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` és `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` vagy `arriveAt`                          |
| `tolerances` (`tl`)                                        | N/A                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` ( `wp.n` ) vagy `viaWaypoint.n` (`vwp.n`)         | `query` – Koordináták formátumban `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)                                            | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját. |
| `userRegion` (`ur`)                                        | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

A Azure Maps Routing API a tehergépkocsi-útválasztást is támogatja ugyanazon az API-n belül. Az alábbi táblázat a további Bing Maps Truck-útválasztási paramétereket a Azure Maps hasonló API-paramétereivel hivatkozik.

| Bing Maps API-paraméter                  | Hasonló Azure Maps API-paraméter        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N/A – a méretek csak a mérőműszereken támogatottak. |
| `weightUnit` (`wu`)                      | N/A – a súlyok csak kilogrammban támogatottak. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/A**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Alapértelmezés szerint a Azure Maps Route API csak összegzést (távolságot és időpontokat) és az útvonal útvonalának koordinátáit adja vissza. Használja a `instructionsType` paramétert a lépésenkénti utasítások lekéréséhez. A `routeRepresentation` paraméter használatával kiszűrhetők az összefoglalás és az útvonal útvonala.

Mindenképpen tekintse át az [útválasztási dokumentáció ajánlott eljárásait](./how-to-use-best-practices-for-routing.md) is.

A Azure Maps Routing API számos további funkcióval nem érhető el a Bing Maps-ben, amelyek hasznosak lehetnek az alkalmazás áttelepítéséhez:

* Az útvonal típusának támogatása: legrövidebb, leggyorsabb, trilling és a legtöbb üzemanyag-takarékos.
* További utazási módok támogatása: kerékpár, busz, motorkerékpár, taxi, tehergépkocsi és Furgon.
* 150-es útpontok támogatása.
* Több utazási idő kiszámítása egyetlen kérelemben; korábbi forgalom, élő forgalom, forgalom nélkül.
* Kerülje a további utak típusát: telekocsi utak, nem burkolt utak, már használatban lévő utak.
* A motor specifikáció-alapú útválasztása. Kiszámítja az égési vagy az elektromos járművek útvonalait a fennmaradó tüzelőanyag/díj és a motor előírásai alapján.
* A jármű maximális sebességének meghatározása.

## <a name="snap-coordinates-to-road"></a>Koordináták igazítása a úthoz

Több módon is összehangolhatja a koordinátákat Azure Maps-utakon.

* A Route Directions API-val egy logikai útvonal koordinátáit a közúti hálózaton keresztül igazíthatja.
* A Azure Maps web SDK-val az egyes koordinátákat a vektor csempén lévő legközelebbi úthoz igazíthatja.
* Az egyes koordinátákat közvetlenül a Azure Maps vektor csempével lehet összepattintani.

**Koordináták illesztése az útvonal irányának API használatával**

A Azure Maps az [Route Directions](/rest/api/maps/route/postroutedirections) API használatával összehangolhatja az utak koordinátáit. Ez a szolgáltatás felhasználható egy, a koordináták halmaza közötti logikai útvonal újraösszeállítására, és hasonló a Bing Maps-hez a Road API-hoz.

Az Route Directions API-val kétféleképpen lehet összehangolni az utakat.

* Ha 150 koordináták vagy kevesebb van, azok a GET Route Directions API-ban adhatók át útpontokként. Ennek a módszernek a használatával két különböző típusú harapós adattípust lehet beolvasni; az útvonalra vonatkozó utasítások az egyes felhagyott Útpontokat tartalmazzák, míg az útvonal elérési útja olyan koordináta-készlettel fog rendelkezni, amely a koordináták közötti teljes elérési utat tölti ki.
* Ha több mint 150 koordináta van, a POST Route Directions API használható. A koordináták kezdő és záró koordinátáit át kell adni a lekérdezési paraméternek, de az összes koordinátáit át lehet adni a `supportingPoints` post kérelem törzsében lévő paraméterbe, és a GeoJSON geometriai gyűjteményét formázhatja. Az ezzel a módszerrel elérhető egyetlen csattant adat az útvonal útvonala, amely a koordináták interpolált készletét adja meg, amely a koordináták közötti teljes elérési utat tölti ki. [Íme egy példa](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) erre a megközelítésre a Azure Maps web SDK Services moduljának használatával.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter    | Hasonló Azure Maps API-paraméter                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – továbbítsa ezeket a pontokat a post kérelem törzsébe  |
| `interpolate`              | N.A.                                                                 |
| `includeSpeedLimit`        | N.A.                                                                 |
| `includeTruckSpeedLimit`   | N.A.                                                                 |
| `speedUnit`                | N.A.                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)            | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.   |
| `userRegion` (`ur`)        | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját.   |

A Azure Maps Routing API a logikai útvonalak kiszámításának biztosítása érdekében a Truck Routing paramétert is támogatja ugyanazon az API-n belül. Az alábbi táblázat a további Bing Maps Truck-útválasztási paramétereket a Azure Maps hasonló API-paramétereivel hivatkozik.

| Bing Maps API-paraméter                 | Hasonló Azure Maps API-paraméter        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N/A – a méretek csak a mérőműszereken támogatottak. |
| `weightUnit` (`wu`)                     | N/A – a súlyok csak kilogrammban támogatottak. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/A**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Mivel ez a megközelítés az Route Directions API-t használja, a szolgáltatás teljes készlete használható a koordináta utakhoz illesztéséhez használt logika testreszabásához. Például a távozási idő megadása azt eredményezi, hogy a korábbi forgalmi adatokat figyelembe vesszük.

A Azure Maps Route Directions API jelenleg nem ad vissza sebességkorlátozás-adatkorlátot, azonban a Azure Maps fordított helymeghatározáshoz API-val lekérhető.

**Koordináták összeillesztése a web SDK használatával**

A Azure Maps web SDK vektoros csempéket használ a Maps megjelenítéséhez. Ezek a vektoros csempék a nyers út geometriájának információit tartalmazzák, és az egyes koordináták egyszerű illesztéséhez egy koordináta számára a legközelebbi út kiszámításához használhatók. Ez akkor hasznos, ha azt szeretné, hogy a koordináták vizuálisan jelenjenek meg az utakon, és már használja a Azure Maps web SDK-t az adat megjelenítéséhez.

Ez a megközelítés azonban csak a Térkép nézeten belül betöltött közúti szegmensekhez fog illeszkedni. Ha az ország szintjén nagyítja fel a nagyítást, előfordulhat, hogy nem áll rendelkezésre az adatelérési út, ezért az illesztés nem hajtható végre, mert a nagyítási szinten egyetlen képpont képviselheti a különböző városi blokkok területét, így az illesztés nem szükséges. Ennek megoldásához az illesztési logika minden alkalommal alkalmazható, amikor a Térkép befejezte a mozgatást. [Itt látható egy](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) mintakód, amely ezt mutatja be.

**A Azure Maps vektor csempék közvetlen használata a koordináták illesztéséhez**

A Azure Maps Vector csempék tartalmazzák a nyers út geometriájának azon adatait, amelyek segítségével kiszámítható a legközelebbi pont egy adott úton egy koordináta számára az egyes koordináták alapvető illesztésének elvégzéséhez. Az összes országúti szegmens a 15. nagyítási szinten jelenik meg a szektorokban, ezért a csempéket onnan kell lekérni. Ezután a [quadtree csempe piramis Math](./zoom-levels-and-tile-grid.md) használatával megállapíthatja, hogy szükség van-e csempére, és hogyan alakíthatja át a csempéket a geometriába. Innen a térbeli matematikai kódtár, például a [Turf js](http://turfjs.org/) vagy a [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) használható a legközelebbi sorok kiszámításához.

## <a name="retrieve-a-map-image-static-map"></a>Térképi rendszerkép beolvasása (statikus Térkép)

A Azure Maps egy API-t biztosít a statikus Térkép képének megjelenítéséhez. A Azure Maps [map képmegjelenítési](/rest/api/maps/render/getmapimagerytile) API a Bing Maps-ben a statikus Térkép API-hoz hasonló.

> [!NOTE]
> Azure Maps a középpontot, az összes pushpins és elérési utat a következő formátumban kell összehangolni, `longitude,latitude` míg a Bing Maps a `latitude,longitude` formátumot használja.</p>
<p>Először a címeket kell geokódolva.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter  | Hasonló Azure Maps API-paraméter            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – az URL elérési útjának részeként van megadva. Jelenleg csak a PNG támogatott.  |
| `heading`                | N/A – A utcai nem támogatott.                |
| `imagerySet`             | `layer` és `style` – lásd a [Térkép támogatott stílusainak](./supported-map-styles.md) dokumentációját.   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N/A                                            |
| `mapSize` (`ms`)         | `width` és `height` – akár 8192x8192 is lehet. |
| `declutterPins` (`dcl`)  | N.A.                                            |
| `dpi`                    | N.A.                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N.A.                                            |
| `pitch`                  | N/A – A utcai nem támogatott.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/A – közép vagy határolókeret mezőt kell használni.     |
| `highlightEntity` (`he`) | N.A.                                            |
| `style`                  | N.A.                                            |
| útvonal paraméterei         | N/A                                            |
| `key`                    | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)          | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.   |
| `userRegion` (`ur`)      | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

> [!NOTE]
> Azure Maps olyan csempét használ, amely a Bing Maps-ben használt Térkép csempék méretének kétszerese. Így a nagyítási szint értéke Azure Mapsban egy nagyítási szint jelenik meg Azure Maps a Bing Maps-hez képest. Csökkentse az 1. áttelepítési kérelmek nagyítási szintjét, hogy kompenzálja ezt.

További információ: [útmutató a Térkép rendszerképének leképezése API-hoz](./how-to-render-custom-data.md) .

Amellett, hogy képes létrehozni egy statikus térképi képet, a Azure Maps Render szolgáltatás lehetővé teszi a Térkép csempék közvetlen elérését a raszteres (PNG) és a vektoros formátumokban is.

-   [Térkép csempéje](/rest/api/maps/render/getmaptile) – lekéri a raszteres (PNG) és vektoros csempéket az alapszintű térképekhez (utak, határok, háttér).
-   [Térkép képének csempe](/rest/api/maps/render/getmapimagerytile) – antenna-és műhold-képek lekérése.

### <a name="pushpin-url-parameter-format-comparison"></a>Gombostű URL-paraméter formátumának összehasonlítása

**Előtte: Bing Maps**

A Bing Maps-ben a pushpins hozzáadhatók statikus térképes képhez az `pushpin` URL-cím paraméterének használatával. A `pushpin` paraméter egy `latitude,longitude` formátumot, egy ikont és egy szöveges címkét (legfeljebb három karakter) használ, az alábbi ábrán látható módon:

> `&pushpin=latitude,longitude;iconStyle;label`

További pushpins hozzáadásához további paramétereket adhat hozzá `pushpin` az URL-címhez más értékekkel. A gombostű-ikon stílusa a Bing Maps API-ban elérhető előre definiált stílusok egyikére korlátozódik.

A Bing Maps-ben például az "AB" címkével ellátott piros gombostű a Koordinátákban (földrajzi hosszúság:-110, szélesség: 45) a következő URL-címmel adhatók hozzá a térképhez:

> `&pushpin=45,-110;7;AB`

![Bing Maps – statikus Térkép PIN-kódja](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**Utána: Azure Maps**

Azure Maps a pushpins is hozzáadhatók egy statikus térképi képhez a `pins` paraméternek az URL-címben való megadásával. A Azure Maps pushpins az ikon stílusa és az ikon stílusát használó helyszínek listája határozza meg. A rendszer ezt az információt adja át a paraméternek, így több alkalommal is megadható `pins` , hogy támogassa a különböző stílusú pushpins.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

További stílusok használatával további paramétereket adhat hozzá `pins` az URL-címhez más stílussal és különböző helyszínekkel.

Amikor PIN-kódokkal rendelkezik, Azure Maps megköveteli a koordináták `longitude latitude` formátumát, míg a Bing Maps `latitude,longitude` formátumot használ. Azt is vegye figyelembe, hogy van **egy szóköz, nem pedig egy vesszővel** elválasztott hosszúság és szélesség a Azure Mapsban.

Az `iconType` érték határozza meg a létrehozandó PIN-kód típusát, és a következő értékeket veheti fel:

* `default` – Az alapértelmezett PIN-kód ikon.
* `none` – A ikon nem jelenik meg, csak a feliratok lesznek megjelenítve.
* `custom` – Meghatározza, hogy egyéni ikont kell használni. Az ikon képére mutató URL-címet a rendszer a `pins` PIN-kód helye információinak lejárta után is hozzáadhatja a paraméter végéhez.
* `{udid}` – Egyedi Adatazonosító (UDID) a Azure Maps adattárolási platformon tárolt ikonhoz.

A Azure Mapsban szereplő stílusokat a rendszer a formátummal adja hozzá `optionNameValue` , és több, pipe ( `|` ) karakterrel elválasztott stílust tartalmaz `iconType|optionName1Value1|optionName2Value2` . Megjegyzés: a beállítások nevei és értékei nincsenek elválasztva. A következő stílusú pushpins a Azure Mapsban használhatók:

* `al` – Megadja a pushpins opacitását (alpha). 0 és 1 közötti szám lehet.
* `an` – Megadja a PIN-kód horgonyát. A formátumban megadott X és y képpont értékeket adja meg `x y` .
* `co` – A PIN-kód színe. 24 bites hexadecimális színnek kell lennie: `000000` `FFFFFF` .
* `la` – Megadja a felirat horgonyát. A formátumban megadott X és y képpont értékeket adja meg `x y` .
* `lc` – A címke színe. A következőnek kell lennie: `000000` `FFFFFF` .
* `ls` – A felirat mérete képpontban megadva. 0-nál nagyobb szám lehet.
* `ro` – Az ikon elforgatására szolgáló fokban megadott érték. A-360 és a 360 közötti szám lehet.
* `sc` – A PIN-kód ikonjának méretezési értéke. 0-nál nagyobb szám lehet.

Az egyes PIN-kódokhoz meg kell adni a Label értékeket, nem pedig egyetlen feliratot, amely a helyek listájának összes pushpins érvényes. A címke értéke lehet több karakterből álló karakterlánc is, és az idézőjelek közé helyezhető, így biztosítható, hogy ne legyen helytelen a stílus vagy a hely értéke.

A Azure Maps például egy piros ( `FF0000` ) alapértelmezett ikon hozzáadásával, amely a "Space tű" címkével ellátott felirat (15 50), a Koordinátákban (hosszúság:-122,349300, Latitude: 47,620180) lévő ikon a következő URL-lel hajtható végre:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![Azure Maps statikus Térkép PIN-kódja](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

Az alábbi példában három PIN-kód szerepel az "1", a "2" és a "3" címke értékkel:

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Azure Maps statikus Térkép több PIN-kód](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>Rajzolási görbe URL-paramétereinek formátumának összehasonlítása

**Előtte: Bing Maps**

A Bing Maps, vonalak és sokszögek hozzáadhatók egy statikus térképi képhez az `drawCurve` URL-cím paraméterének használatával. A `drawCurve` paraméter egy alakzat típusát, a stílus típusát és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

> `&drawCurve=shapeType,styleType,location1,location2...`

További stílusok használatával további paramétereket adhat hozzá `drawCurve` az URL-címhez más stílussal és különböző helyszínekkel.

A Bing Maps-beli helyei a formátummal vannak megadva `latitude1,longitude1_latitude2,longitude2_…` . A helyszínek kódolása is lehetséges.

A Bing Maps típusú alakzatok a következők: vonalak, sokszögek, kör és görbe. A stílusok közé tartozik a vonal színe, a vonalvastagság, a körvonal színe, a kitöltés színe, a körvonal vastagsága és a körkörös sugár.

Például a Bing Maps-ben egy 50%-os opacitású kék vonal és négy képpont vastagsága adható hozzá a térképhez a koordináták között (hosszúság:-110, szélesség: 45 és hosszúság:-100, Latitude: 50) a következő URL paraméterrel:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Bing Maps – statikus Térkép sora](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**Utána: Azure Maps**

Azure Maps a vonalak és a sokszögek is hozzáadhatók egy statikus térképi képhez az URL-cím *path* paraméterének megadásával. A Bing Maps-hez hasonlóan a stílus és a helyszínek listája is megadható ebben a paraméterben, és a *path* paraméter többször is megadható több kör, vonal és sokszög megjelenítéséhez különböző stílusokkal.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Az elérési utak helyein Azure Maps megköveteli a koordináták `longitude latitude` formátumát, míg a Bing Maps `latitude,longitude` formátumot használ. Azt is vegye figyelembe, hogy van **egy szóköz, nem pedig egy vesszővel elválasztott** hosszúság és szélesség a Azure Mapsban. A Azure Maps jelenleg nem támogatja a kódolt elérési utakat. Nagyobb adatkészletek tölthetők fel, mivel a GeoJSON betöltik a Azure Maps adattárolási API-t az [itt](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)dokumentált módon.

A Azure Mapsban lévő elérésiút-stílusok a következő formátummal lesznek hozzáadva `optionNameValue` , és több stílussal vannak elválasztva, mint például a pipe ( `|` ) karakter `optionName1Value1|optionName2Value2` . Megjegyzés: a beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási nevek használhatók a Azure Mapsi elérési utak stílusához:

* `fa` – A sokszögek renderelése során használt kitöltési szín opacitása (alpha). 0 és 1 közötti szám lehet.
* `fc` – A sokszög területének megjelenítéséhez használt kitöltési szín.
* `la` – A vonalak és a sokszögek körvonalainak megjelenítéséhez használt vonal színopacitása (alpha). 0 és 1 közötti szám lehet.
* `lc` – A vonalak megjelenítéséhez és a sokszögek körvonalához használt vonal színe.
* `lw` – A vonal szélessége képpontban megadva.
* `ra` – A körök sugarát adja meg méterben.

Azure Maps például egy 50%-os opacitású kék vonal és négy képpont vastagsága adható hozzá a térképhez a koordináták között (hosszúság:-110, szélesség: 45 és hosszúság:-100, Latitude: 50) a következő URL-lel:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Azure Maps statikus leképezési sor](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>Távolsági mátrix kiszámítása

A Azure Maps API-t biztosít az utazási idő kiszámításához, valamint a különböző helyei közötti távolságot a távolsági mátrixként. A Azure Maps távolsági mátrix API a Bing Maps-ben a távolsági mátrix API-hoz hasonló.

-   [Útvonal-mátrix](/rest/api/maps/route/postroutematrixpreview): aszinkron módon számítja ki az utazási időpontokat és a távolságokat az egyes eredetek és célhelyek esetében. A rendszer legfeljebb 700 cellát támogat (az eredetek száma megszorozva a célhelyek számával). Ennek a korlátozásnak a szem előtt tartva a lehetséges mátrix-dimenziók példái a következők: `700x1` ,, `50x10` `10x10` , `28x25` , `10x70` .

> [!NOTE]
> A távolsági mátrix API-ra irányuló kérés csak POST-kéréssel hozható létre a kérelem törzsében, a forrás-és a célként megadott információval.</p>
<p>Emellett Azure Maps az összes eredetet és célhelyet koordinálni kell. Először a címeket kell geokódolva.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter | Hasonló Azure Maps API-paraméter                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – a POST kérelem törzsében válassza a GeoJSON.    |
| `destinations`          | `destination` – a POST kérelem törzsében válassza a GeoJSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N/A                                                         |
| `distanceUnit`          | N/A – minden távolság méterben.                              |
| `timeUnit`              | N/A – minden alkalommal másodpercben.                                 |
| `key`                   | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)         | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.  |
| `userRegion` (`ur`)     | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját.     |

> [!TIP]
> A Azure Maps útválasztási API-ban elérhető összes speciális útválasztási beállítás (tehergépkocsi-útválasztás, motor-specifikáció, elkerülés...) támogatja a Azure Maps távolsági mátrix API-t.

## <a name="calculate-an-isochrone"></a>Isochrone kiszámítása

A Azure Maps API-t biztosít egy isochrone kiszámításához, amely egy olyan területre vonatkozik, amely egy adott időtartamon belül, illetve az üzemanyag/díj alapján bármilyen irányban átutazható egy adott területre. A Azure Maps Route Range API hasonló a Bing Maps isochrone API-hoz.

-   [Útvonal](/rest/api/maps/route/getrouterange) Tartomány * *: olyan sokszög kiszámításához, amely egy olyan területre vonatkozik, amely egy adott időtartamon, távolságon vagy a rendelkezésre álló tüzelőanyagon vagy felszámított mennyiségen belül bármilyen irányban átutazható.

> [!NOTE]
> Azure Maps megköveteli a lekérdezési forrás koordinálását. Először a címeket kell geokódolva.</p>
<p>A Bing Maps a izokrón idő vagy távolság alapján is kiszámíthatja, míg a Azure Maps az idő, a távolság vagy a rendelkezésre álló tüzelőanyag/díj alapján izokrón számíthat.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter      | Hasonló Azure Maps API-paraméter            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/A – minden alkalommal másodpercben.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N/A – minden távolság méterben.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)              | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.  |
| `userRegion` (`ur`)          | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

> [!TIP]
> A Azure Maps útválasztási API-ban elérhető összes speciális útválasztási beállítás (tehergépkocsi-útválasztás, motor-specifikáció, elkerülés...) a Azure Maps isochrone API-ban támogatott.

## <a name="search-for-points-of-interest"></a>Hasznos helyek keresése

A következő API-k használatával kereshetők a fontos adatpontok a Bing Maps szolgáltatásban:

-   **Helyi keresés:** Megkeresi azokat az érdekes pontokat, amelyek a közelben vannak (sugárirányú keresés), név szerint vagy entitás típusa szerint (kategória). A Azure Maps [POI Search](/rest/api/maps/search/getsearchpoi) és a [POI kategóriájú keresési](/rest/api/maps/search/getsearchpoicategory) API-k a legtöbb esetben hasonlóak ehhez az API-hoz.
-   **Hely felismerése**: megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak. A [közeli Azure Maps keresési](/rest/api/maps/search/getsearchnearby) API a legtöbb hasonló ehhez az API-hoz.
-   **Helyi adatfelismerések:** Megkeresi azokat az érdeklődési pontokat, amelyek a megadott maximális vezetési időn vagy távolságon belül vannak egy adott koordináta alapján. Ez a Azure Maps a isochrone számításával érhető el, majd a geometriai API- [n belüli keresésbe](/rest/api/maps/search/postsearchinsidegeometry) kerül.

Azure Maps számos keresési API-t biztosít a hasznos helyek számára:

-   [POI-keresés](/rest/api/maps/search/getsearchpoi): érdeklődési pontok keresése név alapján. Például: `"starbucks"`.
-   [POI-kategória keresése](/rest/api/maps/search/getsearchpoicategory): az érdeklődési pontok kategória szerint kereshetők. Például: "étterem".
-   [Közeli keresés](/rest/api/maps/search/getsearchnearby): megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
-   [Fuzzy keresés](/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, a hasznos hely vagy a kamat kategóriája, és azonnal feldolgozhatja a kérést. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók az azonos szövegmezőből származó címekre vagy érdekes pontokra kereshetnek.
-   [Keresés a geometrián belül](/rest/api/maps/search/postsearchinsidegeometry): a megadott geometrián (sokszög) belül található érdeklődési pontok keresése.
-   [Keresés az útvonal mentén](/rest/api/maps/search/postsearchalongroute): keressen olyan pontokat, amelyek egy adott útvonal útvonalán találhatók.
-   [Fuzzy batch-keresés](/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

Mindenképpen tekintse át az [ajánlott eljárásokat a keresési](./how-to-use-best-practices-for-search.md) dokumentációhoz.

## <a name="get-traffic-incidents"></a>Forgalmi incidensek lekérése

Azure Maps több API-t biztosít a forgalmi adatok beolvasásához. Kétféle forgalmi adatok érhetők el;

* **Flow-adatok** – mérőszámokat biztosít a forgalom forgalmáról az utakon. Ezt gyakran használják a kód utak színezésére. Ezek az adatfrissítések 2 percenként frissülnek.
* **Incidensek** – adatokat biztosít az építkezésről, a közúti bezárásokról, a balesetekről és az olyan egyéb incidensekről, amelyek befolyásolhatják a forgalmat. Ezeket az adatfájlokat percenként frissíti a rendszer.

A Bing Maps az interaktív térkép vezérlőelemekben biztosítja a forgalom és az incidensek adatmennyiségét, valamint szolgáltatásként elérhetővé teszi az incidensek számára elérhető adatokat is.

A forgalmi adatok integrálva vannak a Azure Maps interaktív térkép vezérlőbe. Az Azure Maps a következő Traffic Services API-kat is biztosítja;

-   [Forgalmi folyamatok szegmensei](/rest/api/maps/traffic/gettrafficflowsegment): az adott koordinátákhoz legközelebb eső útszakasz sebességével és utazási időpontjaival kapcsolatos információkat nyújt.
-   [Forgalmi folyamatok csempéi](/rest/api/maps/traffic/gettrafficflowtile): a adatforgalomra vonatkozó adatokat tartalmazó raszteres és vektoros csempéket biztosít. Ezek használhatók a Azure Maps vezérlőkkel vagy a harmadik féltől származó Térkép vezérlőelemekkel, például a betegtájékoztatóval. A vektoros csempék speciális adatelemzéshez is használhatók.
-   [Forgalmi incidens részletei](/rest/api/maps/traffic/gettrafficincidentdetail): megadja a forgalmi incidens részleteit a határolókeret, a nagyítási szint és a forgalmi modellen belül.
-   [Forgalmi incidens csempéi](/rest/api/maps/traffic/gettrafficincidenttile): a forgalmi incidensek adatokat tartalmazó raszteres és vektoros csempéket biztosít.
-   [Forgalmi incidens nézőpontja](/rest/api/maps/traffic/gettrafficincidentviewport): lekéri a kérelemben ismertetett nézetablak jogi és műszaki információit, például a forgalmi modell azonosítóját.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps Traffic API-paraméterekre a Azure Maps hasonló forgalmi incidensek API-paramétereivel.

| Bing Maps API-paraméter  | Hasonló Azure Maps API-paraméter   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` és `boundingZoom`      |
| `includeLocationCodes`   | N/A                                   |
| `severity` (`s`)         | N/A – minden visszaadott adat               |
| `type` (`t`)             | N/A – minden visszaadott adat               |
| `key`                    | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)          | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját. |
| `userRegion` (`ur`)      | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját. |

## <a name="get-a-time-zone"></a>Időzóna beolvasása

A Azure Maps API-t biztosít a koordináta időzónájának lekéréséhez. A Azure Maps időzóna API hasonló a Bing Maps-beli Time Zone API-hoz.

-   [Időzóna a koordináta alapján](/rest/api/maps/timezone/gettimezonebycoordinates): megadásával megtekintheti a koordinátákat, és lekérheti az adott időzóna részleteit.

A következő táblázat kereszthivatkozásokat hivatkozik a Bing Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Bing Maps API-paraméter | Hasonló Azure Maps API-paraméter          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | Az N/A-helyszíneket először geokódolva kell.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N/A – mindig a Azure Maps válaszában szerepel. |
| `key`                   | `subscription-key` – Lásd még a [hitelesítés Azure Maps](./azure-maps-authentication.md) a dokumentációt. |
| `culture` (`c`)         | `language` – Lásd a [támogatott nyelvek](./supported-languages.md) dokumentációját.  |
| `userRegion` (`ur`)     | `view` – Lásd a [támogatott nézetek](./supported-languages.md#azure-maps-supported-views) dokumentációját.  |

A Azure Maps platformon kívül számos további időzóna API is rendelkezésre áll, amelyek segítenek az időzóna-nevekkel és-azonosítókkal való átalakításban.

-   [Időzóna azonosító szerint](/rest/api/maps/timezone/gettimezonebyid): a megadott IANA Időzóna-azonosító aktuális, korábbi és jövőbeli időzóna-információit adja vissza.
-   [Időzóna enumerálása IANA](/rest/api/maps/timezone/gettimezoneenumiana): az IANA időzóna-azonosítók teljes listáját adja vissza. Az IANA szolgáltatás frissítései egy napon belül megjelennek a rendszeren. 
-   [Időzóna enumerálása Windows](/rest/api/maps/timezone/gettimezoneenumwindows): a Windows időzóna-azonosítók teljes listáját adja vissza.
-   [Időzóna – IANA-verzió](/rest/api/maps/timezone/gettimezoneianaversion): az Azure Maps által használt aktuális IANA-verziószámot adja vissza. 
-   [Időzóna-ablakok az IANA számára](/rest/api/maps/timezone/gettimezonewindowstoiana): egy megfelelő IANA-azonosítót ad vissza, amely érvényes Windows időzóna-azonosítót adott meg. Egy Windows-AZONOSÍTÓhoz több IANA-azonosító is visszaküldhető.

## <a name="spatial-data-services-sds"></a>Térbeli Data Services (SDS)

A Bing Maps térbeli adatszolgáltatásai három kulcsfontosságú funkciót biztosítanak:

* Batch helymeghatározáshoz – egyetlen kéréssel dolgozza fel a geocodes nagy kötegét.
* Felügyeleti határokra vonatkozó adat beolvasása – egy koordináta használata, és egy adott entitás típusához tartozó metsző határ beolvasása.
* Térbeli üzleti adatok üzemeltetése és lekérdezése – töltsön fel egy egyszerű 2D-s adattáblázatot, és néhány egyszerű térbeli lekérdezéssel férhet hozzá.

### <a name="batch-geocode-data"></a>Batch geocode-adatbázis

A Batch helymeghatározáshoz az a folyamat, amelynek során nagy mennyiségű címet vagy helyet kell átadni, egyetlen kérésben egy szolgáltatásba, és mindegyiket párhuzamosan geokódolva, és az eredményeket egyetlen válaszban adják vissza.

A Bing Maps legfeljebb 200 000 címet adhat át egyetlen batch-geocode kérelemben. Ez a kérés egy várólistába kerül, és általában az adathalmaz méretétől és a szolgáltatás terhelésének függvényében, néhány perctől akár néhány óráig is eltarthat. A kérelemben szereplő minden egyes címnek tranzakciót generált.

Azure Maps rendelkezik egy batch helymeghatározáshoz szolgáltatással, de legfeljebb 10 000 címet adhat át egyetlen kérelemben, és másodpercek alatt elvégezhető az adathalmaz méretétől és a szolgáltatás terhelésének függvényében. A kérelemben szereplő minden egyes címnek tranzakciót generált. Azure Maps a Batch helymeghatározáshoz szolgáltatás csak az S1 szintet veheti igénybe.

Egy másik lehetőség, hogy nagy számú címet helymeghatározáshoz a Azure Maps a párhuzamos kérelmeket a szabványos keresési API-khoz. Ezek a szolgáltatások csak egyetlen címeket fogadnak el, de használhatók a S0 szintjével is, amely ingyenes használati korlátokat is biztosít. Az S0-réteg legfeljebb 50 kérést tesz lehetővé másodpercenként az Azure Maps platformra egyetlen fiókból. Így ha a korláton belül korlátozza ezeket a korlátozásokat, a geocode felfelé 180 000-re lehetséges. Az S1 csomagnak nincs dokumentált korlátja a fiókból elvégezhető lekérdezések másodpercenkénti számát illetően, így az árképzési szintek használata során sokkal több adatok is feldolgozhatók, azonban a Batch helymeghatározáshoz szolgáltatás használata segít csökkenteni az átvitt adatok teljes mennyiségét, és jelentősen csökkenti a hálózati forgalmat.

-   [Szabad formátumú helymeghatározáshoz](/rest/api/maps/search/getsearchaddress): egyetlen címnek (például) kell megadnia `"1 Microsoft way, Redmond, WA"` , és azonnal fel kell dolgoznia a kérést. Ez a szolgáltatás akkor ajánlott, ha gyorsan kell geocode az egyes címeket.
-   [Strukturált helymeghatározáshoz](/rest/api/maps/search/getsearchaddressstructured): egyetlen cím részeit adja meg, például az utca nevét, a várost, az országot és az irányítószámot, és azonnal feldolgozza a kérést. Ez a szolgáltatás akkor javasolt, ha gyorsan kell geocode az egyes címeket, és az adatai már az egyes címekre is bekerülnek.
-   [Batch-helymeghatározáshoz](/rest/api/maps/search/postsearchaddressbatchpreview): hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. A rendszer az összes címet párhuzamosan geokódolva a kiszolgálón, és amikor elkészült, a teljes eredményhalmazt letöltheti. Ez a szolgáltatás nagyméretű adatkészletek helymeghatározáshoz ajánlott.
-   [Fuzzy keresés](/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, a hasznos hely vagy a kamat kategóriája, és azonnal feldolgozhatja a kérést. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók az azonos szövegmezőből származó címekre vagy érdekes pontokra kereshetnek.
-   [Fuzzy batch-keresés](/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

### <a name="get-administrative-boundary-data"></a>Felügyeleti határokra vonatkozó adat beolvasása

A Bing Maps szolgáltatásban az országok, Államok, megyék, városok és postai kódok adminisztratív határai elérhetővé válnak a földrajzi adatok API-n keresztül. Ez az API a geocode koordinálása vagy lekérdezése során zajlik. Ha a rendszer egy lekérdezést ad át, a rendszer geokódolva, és az első eredmény koordinátáit használja. Ez az API fogadja a koordinátákat, és lekéri a megadott entitás típusának határát, amely metszi a koordinálást. Vegye figyelembe, hogy ez az API nem feltétlenül adja vissza az átadott lekérdezés határát. Ha egy lekérdezés `"Seattle, WA"` bekerül a szolgáltatásba, de az entitás típusa értéke ország régió, a rendszer az USA határát adja vissza.

A Azure Maps az adminisztrációs határok (országok, Államok, megyék, városok és postai kódok) elérését is biztosítja. Határ lekéréséhez le kell kérdezni a kívánt határhoz tartozó keresési API-k egyikét (azaz `Seattle, WA` ). Ha a keresési eredményhez társított határ tartozik, az eredmény válaszában meg kell adni egy geometriai azonosítót. A keresési sokszög API ezután egy vagy több geometriai azonosító pontos határainak lekérésére használható. Ez egy kicsit eltér a Bing Maps-től, mivel Azure Maps visszaadja a keresett érték határát, míg a Bing Maps egy adott típusú entitás típusának határát adja vissza egy megadott koordináta esetében. Emellett a Azure Maps által visszaadott GeoJSON formátuma is.

A bedugni:

1.  Adja át a következő keresési API-k egyikének a határát, amelyet szeretne kapni.
    -   [Szabad formátumú helymeghatározáshoz](/rest/api/maps/search/getsearchaddress)
    -   [Strukturált helymeghatározáshoz](/rest/api/maps/search/getsearchaddressstructured)
    -   [Batch-helymeghatározáshoz](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Intelligens keresés](/rest/api/maps/search/getsearchfuzzy)
    -   [Intelligens batch-keresés](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Ha a kívánt eredmény (ek) nek van geometria-azonosítója, adja át a [keresési sokszög API](/rest/api/maps/search/getsearchpolygon)-nak.

### <a name="host-and-query-spatial-business-data"></a>Térbeli üzleti adatainak üzemeltetése és lekérdezése

A Bing Maps térbeli adatszolgáltatásai egyszerű térbeli adattárolási megoldást biztosítanak az üzleti adatok üzemeltetéséhez és térbeli REST-szolgáltatásként való megjelenítéséhez. Ez a szolgáltatás négy fő lekérdezést biztosít; Keresse meg a tulajdonságot, keresse meg a közeli, keresse meg a határolókeret mezőben, és keresse meg az útvonal 1 mérföldét. Sok vállalat, akik ezt a szolgáltatást használják, gyakran már tárolják az üzleti állapotukat valahol egy adatbázisban, és egy kis részhalmazt töltenek be a szolgáltatásba az alkalmazások, például a tároló-lokátorok számára. Mivel a kulcs alapú hitelesítés alapvető biztonságot nyújt, ajánlott, hogy ez a szolgáltatás csak nyilvános adatokkal legyen használatban.

Az üzleti helyadatok többsége az adatbázisban elindul. Ezért ajánlott olyan meglévő Azure Storage-megoldásokat használni, mint az Azure SQL vagy az Azure PostgreSQL (a PostGIS beépülő modullal). Mindkét tárolási megoldás támogatja a térbeli adatmennyiséget, és a térbeli lekérdezési képességek széles körét biztosítja. Miután az adatai megfelelő tárolási megoldásban vannak, az alkalmazásba integrálható egy egyéni webszolgáltatás létrehozásával, vagy egy olyan keretrendszer használatával, mint például a ASP.NET vagy a Entity Framework. Ennek a megközelítésnek a használata több lekérdezési képességet és sokkal nagyobb biztonsági lehetőségeket biztosít.

A Azure Cosmos DB korlátozott térbeli képességeket is biztosít, amelyek a forgatókönyvtől függően elegendőek lehetnek.

Íme néhány hasznos erőforrás a térbeli információk Azure-beli üzemeltetéséhez és lekérdezéséhez.

-   [Az Azure SQL térbeli adattípusok áttekintése](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL térbeli – a legközelebbi szomszéd lekérdezése](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [A térinformatikai képességek Azure Cosmos DB áttekintése](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>Ügyfélkódtárak

A Azure Maps a következő programozási nyelvekhez biztosít ügyféloldali kódtárakat:

-   JavaScript, írógéppel, Node.js – [dokumentációs](./how-to-use-services-module.md) \| [NPM csomag](https://www.npmjs.com/package/azure-maps-rest)

Nyílt forráskódú ügyféloldali kódtárak más programozási nyelvekhez;

* .NET Standard 2,0 – [GitHub Project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet csomag](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincs kitakarítható erőforrás.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps REST Services szolgáltatásról.

> [!div class="nextstepaction"]
> [Ajánlott eljárások a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)
