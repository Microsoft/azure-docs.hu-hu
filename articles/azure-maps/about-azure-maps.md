---
title: A Microsoft Azure Maps áttekintése
description: Ismerkedjen meg Microsoft Azure Maps szolgáltatásaival és képességeivel, és hogyan használhatja őket az alkalmazásokban.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: b53af0fda8bb24bf03d0e02f61ce7d195ae69503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680832"
---
# <a name="what-is-azure-maps"></a>Mi az Azure Maps?

A Azure Maps térinformatikai szolgáltatások és SDK-k gyűjteménye, amelyek friss leképezési információt használnak a webes és mobil alkalmazások földrajzi kontextusának biztosításához. Azure Maps a következőket biztosítja:

* REST API-k a vektor-és raszteres térképek több stílusban és műholdképeken való megjelenítéséhez.
* A Creator Services (előzetes verzió) használatával térképeket hozhat létre és tehet elérhetővé a saját beltéri Térkép alapján.
* A keresési szolgáltatások segítségével megkeresheti a címeket, a helyeket és a világ érdekes pontjait.
* Különböző útválasztási lehetőségek; ilyenek például a pont-pont, a multipoint, a multipoint optimalizálása, a isochrone, az elektromos jármű, a kereskedelmi jármű, a forgalom befolyásolta és a mátrix-útválasztás.
* A forgalom és az incidensek nézet a valós idejű forgalmi adatokat igénylő alkalmazások esetében.
* A mobilitási szolgáltatások (előzetes verzió) a tömegközlekedési információk megadására, a különböző utazási módok és a valós idejű beérkezések megtervezésére szolgálnak.
* Időzóna és térinformatikai (előzetes) szolgáltatások.
* Jogosultságszint-emelési szolgáltatások (előzetes verzió) a digitális jogosultságszint-emelési modellel
* Az Azure-ban üzemeltetett geokerítések szolgáltatás és leképezési adatok tárolása.
* Helymeghatározási intelligencia a térinformatikai elemzéseken keresztül.

Emellett Azure Maps szolgáltatások a web SDK-val és az Android SDK-val is elérhetők. Ezek az eszközök segítenek a fejlesztőknek olyan megoldások gyors fejlesztésében és méretezésében, amelyek a helyadatok integrálását biztosítják az Azure-megoldásokba.

Regisztrálhat egy ingyenes [Azure Maps fiókra](https://azure.microsoft.com/services/azure-maps/) , és megkezdheti a fejlesztést.

A következő videó részletesen bemutatja az Azure Mapset:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Térképvezérlők

### <a name="web-sdk"></a>Web SDK

A Azure Maps web SDK lehetővé teszi az interaktív térképek egyéni tartalommal és képekkel való testreszabását. Ezt az interaktív térképet használhatja webes vagy mobil alkalmazásaihoz is. A Térkép vezérlőelem a WebGL használatát teszi lehetővé, így nagy teljesítményű nagyméretű adatkészleteket lehet megjeleníteni. Az SDK-t JavaScript vagy írógéppel használatával is fejlesztheti.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Példa a Azure Maps web SDK-val létrehozott lakossági változások leképezésére":::

### <a name="android-sdk"></a>Android SDK

Mobil leképezési alkalmazások létrehozásához használja a Azure Maps Android SDK-t.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Példák megjelenítése mobileszközön":::

## <a name="services-in-azure-maps"></a>Az Azure Maps szolgáltatásai

Azure Maps a következő szolgáltatásokból áll, amelyek földrajzi kontextust biztosítanak az Azure-alkalmazásokhoz.

### <a name="data-service-preview"></a>Adatszolgáltatás (előzetes verzió)

Az adatleképezések esetében elengedhetetlen az adatkezelés. Az adatszolgáltatással a térbeli adatok és a képek összeállításával használható térinformatikai adatok tölthetők fel és tárolhatók.  Az ügyféladatok a Azure Maps szolgáltatáshoz közelebb hozása csökkenti a késést, növeli a termelékenységet, és új forgatókönyveket hoz létre az alkalmazásaiban. A szolgáltatással kapcsolatos részletekért tekintse meg az [adatszolgáltatás dokumentációját](/rest/api/maps/data).

### <a name="geolocation-service-preview"></a>Térinformatikai szolgáltatás (előzetes verzió)

A térinformatikai szolgáltatással megtekintheti az IP-címek beolvasott kétbetűs ország/régió kódját. Ez a szolgáltatás segít a felhasználói élmény fokozásában azáltal, hogy a földrajzi hely alapján testreszabott alkalmazási tartalmakat biztosít.

További részletekért olvassa el a [térinformatikai szolgáltatás dokumentációját](/rest/api/maps/geolocation).

### <a name="mobility-services-preview"></a>Mobilitási szolgáltatások (előzetes verzió) 

A Azure Maps mobilitási szolgáltatások javítják a nyilvános átviteli funkciókkal rendelkező alkalmazások fejlesztési idejét, például a tranzit útválasztást és a közeli nyilvános átvitelek keresését. A felhasználók részletes információkat kérhetnek a továbbítási leállásokról, a sorokról és az ütemtervekről. A mobilitási szolgáltatás azt is lehetővé teszi, hogy a felhasználók leállítsák a leállítási és a vonalas geometriákat, a leállási, a soros és a szolgáltatási területekre vonatkozó riasztásokat, valamint a valós idejű, nyilvános tranzit Emellett a mobilitási szolgáltatások útválasztási lehetőségeket biztosítanak a multimodális kirándulások megtervezéséhez. A multimodális utazás megtervezése magában foglalja a gyaloglást, a kerékpározást és a nyilvános továbbítási lehetőségeket, mindezt egyetlen útra. A felhasználók a részletes multimodális lépésenkénti útvonalakat is használhatják.

A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [mobilitási szolgáltatások dokumentációját](/rest/api/maps/mobility).

### <a name="render-service"></a>Megjelenítő szolgáltatás

A [Render Service v2 (előzetes verzió)](/rest/api/maps/renderv2) bevezeti a [Get Map csempe v2 API](/rest/api/maps/renderv2/getmaptilepreview)új verzióját. A Get Map csempe v2 API mostantól lehetővé teszi az ügyfeleknek, hogy a Azure Maps Creator használatával létrehozott Azure Maps Road csempéket, időjárási csempéket vagy Térkép csempéket kérjenek. Javasoljuk, hogy használja az új Get Map csempe v2 API-t.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Példa a render Service v2-ből származó térképre":::

További részletekért olvassa el a [Render Service v2 dokumentációját](/rest/api/maps/renderv2).

A GA-ban (általános rendelkezésre állás) található Render Service v1 szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Render Service v1 dokumentációját](/rest/api/maps/render).  

### <a name="route-service"></a>Útvonal szolgáltatás

Az útválasztási szolgáltatások segítségével kiszámíthatja az egyes kért útvonalak becsült érkezési idejét (ETA). Az útválasztási API-k olyan tényezőket vizsgálnak, mint például a valós idejű forgalmi információk és a korábbi adatforgalmi adatok, például a hét kért napjának és a nap időpontjának a szokásos országúti sebessége. Az API-k a legrövidebb vagy leggyorsabb útvonalakat adják vissza több célhelyre egyszerre, sorrendben vagy optimalizált sorrendben, az idő vagy a távolság alapján. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy különböző utazási módokon, például autón, teherautón, kerékpáron vagy gyalogláson, illetve elektromos járművön is kiszámítsanak útvonalakat. A szolgáltatás a bemeneteket is figyelembe veszi, például az indulási időt, a súlyozási korlátozásokat vagy a veszélyes anyagok átvitelét.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Példa egy térképre az útvonal-szolgáltatásból":::

A Route szolgáltatás speciális készlet-funkciókat kínál, például:

* Több útválasztási kérelem kötegelt feldolgozása.
* Az utazási idő és a távolságok egy készlet és a célhely között.
* Azon útvonalak vagy távolságok megkeresése, amelyeket a felhasználók az idő-vagy üzemanyag-követelmények alapján utazhatnak.

Az útválasztási képességekkel kapcsolatos részletekért olvassa el az [útvonal-szolgáltatás dokumentációját](/rest/api/maps/route).

### <a name="search-service"></a>Keresési szolgáltatás

A Search szolgáltatás segítségével a fejlesztők megkereshetik a címeket, a helyeket, az üzleti listákat név vagy kategória alapján, valamint egyéb földrajzi információkat. Emellett a szolgáltatások a földrajzi szélesség és a hosszúságok alapján [fordított geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) -címeket és több utcákat is használhatnak.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Térképes keresés – példa":::

A Search szolgáltatás olyan speciális funkciókat is kínál, mint például a következők:

* Keresés az útvonalon.
* Keresés egy szélesebb területeken.
* Kötegelt keresési kérelmek csoportja.
* Keressen az elektromos járműveket betöltő állomások és a hasznos helyek (POI-EK) számára a márkanév alapján.

A keresési funkciókkal kapcsolatos további információkért olvassa el a [keresési szolgáltatás dokumentációját](/rest/api/maps/search).

### <a name="spatial-service"></a>Térbeli szolgáltatás

A térbeli szolgáltatás gyorsan elemzi a tartózkodási hely adatait, hogy az időben és a térben zajló eseményekről tájékoztassa az ügyfeleket. Lehetővé teszi a közel valós idejű elemzést és az események prediktív modellezését.

A szolgáltatás lehetővé teszi, hogy az ügyfelek a közös térinformatikai matematikai számítások könyvtára révén fokozzák a helyük felderítését. Gyakori számítások például a legközelebbi pont, a nagy kör távolsága és a pufferek. Ha többet szeretne megtudni a szolgáltatásról és a különböző funkciókról, olvassa el a [térbeli szolgáltatás dokumentációját](/rest/api/maps/spatial).

### <a name="timezone-service"></a>Időzóna szolgáltatás

Az időzóna-szolgáltatás lehetővé teszi a jelenlegi, korábbi és jövőbeli időzóna-információk lekérdezését. A szélességi és a hosszúsági párokat, illetve egy [IANA-azonosítót](https://www.iana.org/) is használhat bemenetként. Az időzóna szolgáltatás a következőket is lehetővé teszi:

* A Microsoft Windows időzóna-azonosítóinak átalakítása az IANA időzónára.
* Időzóna eltolásának beolvasása UTC-be.
* Az aktuális idő beolvasása a kiválasztott időzónában.

Az időzóna szolgáltatás lekérdezésére jellemző JSON-válasz az alábbi példához hasonlóan néz ki:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

A szolgáltatással kapcsolatos részletekért olvassa el az [időzóna szolgáltatás dokumentációját](/rest/api/maps/timezone).

### <a name="traffic-service"></a>Forgalom szolgáltatás

A Traffic szolgáltatás olyan webszolgáltatások csomagja, amelyeket a fejlesztők a forgalmi adatokat igénylő webes vagy mobil alkalmazások számára használhatnak. A szolgáltatás két adattípust biztosít:

* Forgalmi folyamat: valós idejű megfigyelt sebesség és utazási idő a hálózaton lévő összes kulcsfontosságú útvonalon.
* Forgalmi incidensek: a forgalmi torlódások és a közúti hálózatokra vonatkozó incidensek naprakész áttekintését.

![Példa a forgalmi adatokat tartalmazó térképre](media/about-azure-maps/intro_traffic.png)

További információt a [Traffic Service dokumentációjában](/rest/api/maps/traffic)talál.

### <a name="weather-services-preview"></a>Időjárási szolgáltatások (előzetes verzió) 

Az időjárási szolgáltatások olyan API-kat kínálnak, amelyeket a fejlesztők az adott hely időjárási adatainak lekérésére használhatnak. Az információk olyan részleteket tartalmaznak, mint például a megfigyelési dátum és idő, az időjárási feltételek rövid leírása, az időjárási ikon, a csapadék jelzői, a hőmérséklet és a Szélsebesség. A rendszer további részleteket, például a RealFeel™ hőmérsékletét és az UV-indexet is visszaadja.

A fejlesztők az [útvonal-API beolvasása](/rest/api/maps/weather/getweatheralongroutepreview) lehetőséggel használhatják az időjárási információkat egy adott útvonalon. Emellett a szolgáltatás támogatja az olyan iránypontok időjárási értesítéseinek létrehozását, amelyeket az időjárási veszélyek, például az árvizek vagy a nagy esőzések érintenek.

A [Get Map csempe v2 API](/rest/api/maps/renderv2/getmaptilepreview) lehetővé teszi a múltbeli, aktuális és jövőbeli radar-és műholdas csempék kérését.

![Példa valós idejű időjárási radar csempéket tartalmazó térképre](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Maps Creator Service (előzetes verzió) 

A Maps Creator Service olyan webszolgáltatások csomagja, amelyekkel a fejlesztők a térképes funkciók alapján hozhatnak létre fedett térképi adatokon alapuló alkalmazásokat.

A Maps Creator három alapvető szolgáltatást nyújt:

* [Adatkészlet szolgáltatás](/rest/api/maps/dataset). Az adatkészlet szolgáltatással hozzon létre egy adatkészletet egy konvertált rajz-csomag adatainak használatával. A csomagra vonatkozó követelményekkel kapcsolatos információkért lásd: rajzi csomag követelményei.

* [Átalakítási szolgáltatás](/rest/api/maps/dataset). A konverziós szolgáltatás használatával a DWG-kialakítási fájlokat átalakíthatja a beltéri térképek számára.

* [Tileset szolgáltatás](/rest/api/maps/tileset). Az tileset szolgáltatás használatával hozzon létre egy adatkészletet vektoros ábrázolással. Az alkalmazások használhatnak egy tileset az adatkészlet vizualizációs csempe alapú nézetének megjelenítéséhez.

* [Szolgáltatás állapota szolgáltatás](/rest/api/maps/featurestate). A szolgáltatás állapota szolgáltatással támogatja a dinamikus leképezések stílusát. A dinamikus Térkép stílusa lehetővé teszi, hogy az alkalmazások valós idejű eseményeket tükrözzék a IoT rendszerek által biztosított tárhelyeken.

* [WFS szolgáltatás](/rest/api/maps/featurestate). A WFS szolgáltatás segítségével lekérdezheti a beltéri Térkép adatait. A WFS szolgáltatás a [nyílt TÉRINFORMATIKAI konzorcium API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) -szabványokat követi egyetlen adatkészlet lekérdezéséhez.

### <a name="elevation-service-preview"></a>Jogosultságszint-emelési szolgáltatás (előzetes verzió)

A Azure Maps jogosultságszint-emelési szolgáltatás egy webszolgáltatás, amellyel a fejlesztők a föld felületéről bárhonnan lekérhetik a jogosultságszint-emelési adatait.

A jogosultságszint-emelési szolgáltatás lehetővé teszi, hogy két formátumban olvassa be a jogosultságszint-emelési kéréseket:

* **GeoTIFF raszter formátuma** Használja a [Render v2 – Map csempe API-](/rest/api/maps/renderv2) t a jogosultságszint-emelési adatfájlok mozaik formátumban való lekéréséhez.

* **GeoJSON formátuma**. A [jogosultságszint-emelési API](/rest/api/maps/elevation) -k használatával mintavételes jogosultságszint-emelési adat adható meg az elérési utakon, egy meghatározott határolókereton vagy adott koordinátákon belül. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Példa a térképre jogosultságszint-emelési adattal":::


## <a name="programming-model"></a>Programozási modell

A Azure Maps a mobilitáshoz készült, és több platformon futó alkalmazások fejlesztéséhez is segítséget nyújt. Olyan programozási modellt használ, amely az agnosztikus nyelvét használja, és támogatja a JSON-kimenetet a [REST API](/rest/api/maps/)-kon keresztül.

A Azure Maps egy egyszerű programozási modellel is kényelmes [JavaScript-leképezési vezérlőt](/javascript/api/azure-maps-control) biztosít. A fejlesztés a webes és a mobil alkalmazások esetében is gyorsan és egyszerűen elvégezhető.





## <a name="power-bi-visual"></a>Power BI-vizualizáció

A Power BI Azure Maps vizualizációja az adatvizualizációk gazdag készletét kínálja a térbeli adatvizualizációk számára a térképen felül. A becslések szerint az üzleti adatmennyiség több mint 80%-a rendelkezik helyi környezettel. A Azure Maps vizualizáció egy kód nélküli megoldást kínál arra, hogy betekintést nyerjen a helyi környezet és az üzleti adatai hatására.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI asztal a Azure Maps vizualizáció üzleti adataival":::

További információ: a [Azure Maps Power bi Visual](power-bi-visual-getting-started.md) dokumentációjának első lépései.

## <a name="usage"></a>Használat

Azure Maps szolgáltatások eléréséhez nyissa meg a [Azure Portal](https://portal.azure.com) , és hozzon létre egy Azure Maps fiókot.

Az Azure Maps kulcsalapú hitelesítési sémát használ. A fiók létrehozásakor két kulcs jön létre. Azure Maps-szolgáltatások hitelesítéséhez bármelyik kulcsot használhatja.

Megjegyzés: a Azure Maps az ügyfél által megadott címekre és helyekre vonatkozó lekérdezéseket ("lekérdezéseket") a harmadik féltől származó TomTom-tól a leképezési funkciókhoz. A lekérdezések nem kapcsolódnak az ügyfélhez vagy a végfelhasználóhoz, ha a TomTom megosztva van, és nem használható az egyéni felhasználók azonosítására. A mobilitási és időjárási szolgáltatások, köztük a Moovit-mel való integráció és az AccuWeather jelenleg [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

A Microsoft jelenleg folyamatban van a TomTom, a Moovit és a AccuWeather hozzáadására az Online Services alvállalkozói listához.

## <a name="supported-regions"></a>Támogatott régiók

A Azure Maps szolgáltatások jelenleg csak a következő országokban/régiókban érhetők el:

* Kína
* Dél-Korea

Ellenőrizze, hogy az aktuális IP-cím helye támogatott országban/régióban található-e.

## <a name="next-steps"></a>Következő lépések

Próbálja ki a Azure Mapst bemutató minta alkalmazást:

[Rövid útmutató: Webalkalmazás létrehozása](quick-demo-map-app.md)

Naprakész Azure Maps:

[Azure Maps blog](https://azure.microsoft.com/blog/topics/azure-maps/)