---
title: 'Oktatóanyag: webalkalmazás migrálása a Bing Maps szolgáltatásból | Microsoft Azure térképek'
description: Útmutató a webalkalmazások Bing Maps szolgáltatásból Microsoft Azure Maps rendszerbe való áttelepítéséhez.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 2b072107275fba1ff83ab3ddac63ed8bf7766356
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389110"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>Oktatóanyag: webalkalmazás migrálása a Bing Mapsből

A Bing Maps szolgáltatást használó webalkalmazások gyakran a Bing Maps V8 JavaScript SDK-t használják. A Azure Maps web SDK a megfelelő Azure-alapú SDK, amelybe migrálni lehet. A Azure Maps web SDK lehetővé teszi, hogy az interaktív térképeket saját tartalmakkal és képekkel testreszabja a webes vagy mobil alkalmazásaiban való megjelenítéshez. Ez a vezérlő a WebGL-t használja, amely lehetővé teszi nagy adatkészletek nagy teljesítményű renderelését. Fejlessze az SDK-t JavaScript vagy írógéppel használatával. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Térkép betöltése
> * Térkép honosítása
> * Pushpins, vonalláncok és sokszögek hozzáadása.
> * Információk megjelenítése előugró ablakban vagy bezárása
> * KML-és GeoJSON-információk betöltése és megjelenítése
> * Fürt pushpins
> * Csempe rétegének átfedése
> * Forgalomadatok megjelenítése
> * Vízszintes átfedés hozzáadása

Ha egy meglévő webalkalmazást telepít át, ellenőrizze, hogy a nyílt forráskódú Térkép vezérlőelem-függvénytárat, például a céziumot, a betegtájékoztatót és a OpenLayers használja-e. Ha így van, és továbbra is használni szeretné ezt a könyvtárat, akkor a Azure Maps csempe-szolgáltatásokhoz (a[közúti csempék](/rest/api/maps/render/getmaptile) \| [műholdas csempéi](/rest/api/maps/render/getmapimagerytile)) csatlakoztatható. Az alábbi hivatkozásokkal megtudhatja, hogyan használhatja a Azure Maps-t néhány gyakran használt nyílt forráskódú Térkép vezérlőelem-függvénytárban.

* [Cézium](https://cesiumjs.org/) – a webes 3D Térkép vezérlőelem. [Kód mintái](https://azuremapscodesamples.azurewebsites.net/?search=Cesium) \| A [beépülő modul]() adattára
* [Szórólap](https://leafletjs.com/) – kis méretű 2D Térkép vezérlőelem a weben. [Kód mintái](https://azuremapscodesamples.azurewebsites.net/?search=leaflet) \| A [beépülő modul]() adattára
* [OpenLayers](https://openlayers.org/) – a kivetítéseket támogató webes 2D Térkép vezérlőelem. [Kód mintái](https://azuremapscodesamples.azurewebsites.net/?search=openlayers) \| A [beépülő modul]() adattára

Ha JavaScript-keretrendszer használatával fejleszt, a következő nyílt forráskódú projektek egyike hasznos lehet:

* [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -szögletes 10 burkoló az Azure Maps-ben.
* [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – Azure Maps Blazer-összetevő.
* [Azure Maps reagáló összetevő](https://github.com/WiredSolutions/react-azure-maps) – a Azure Maps vezérlőre reagáló burkoló.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – egy Azure Maps összetevő a Vue alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="key-features-support"></a>A főbb funkciók támogatása

Az alábbi táblázat a Bing Maps V8 JavaScript SDK legfontosabb API-funkcióit és a Azure Maps web SDK-ban található hasonló API támogatását tartalmazza.

| Bing Maps szolgáltatás        | Webes SDK-támogatás Azure Maps                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Pushpins                 | ✓                                                                                      |
| Gombostű-fürtözés       | ✓                                                                                      |
| Vonalláncok & sokszögek     | ✓                                                                                      |
| A felszín alatti átfedések          | ✓                                                                                      |
| Heat Maps                | ✓                                                                                      |
| Csempe rétegei              | ✓                                                                                      |
| KML-réteg                | ✓                                                                                      |
| Kontúr réteg            | [Példák](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Adatdobozolási réteg       | A nyílt forráskódú Azure Maps [kockás adatforrási moduljában](https://github.com/Azure-Samples/azure-maps-gridded-data-source) szerepel       |
| Animált csempe réteg      | A nyílt forráskódú Azure Maps [animációs modul](https://github.com/Azure-Samples/azure-maps-animations) része |
| Rajzeszközök            | ✓                                                                                      |
| Geocoder szolgáltatás         | ✓                                                                                      |
| Directions szolgáltatás       | ✓                                                                                      |
| Távolsági mátrix szolgáltatás  | ✓                                                                                      |
| Térbeli adatszolgáltatás     | N/A                                                                                    |
| Satellite/antenna-képek | ✓                                                                                      |
| Madarak szemének képei         | N/A                                                                                |
| Utcai-képek       | N/A                                                                                |
| GeoJSON-támogatás          | ✓                                                                                      |
| GeoXML-támogatás           | ✓ [Térbeli IO-modul](how-to-use-spatial-io-module.md)                                                                                     |
| Well-Known szöveg támogatása  | ✓                                                                                      |
| Egyéni Térkép stílusa        | Részleges                                                                                |

A Azure Maps számos további [nyílt forráskódú modult is tartalmaz a webes SDK](open-source-projects.md#open-web-sdk-modules) -hoz, amely kibővíti a képességeit.

## <a name="notable-differences-in-the-web-sdks"></a>Jelentős különbségek a webes SDK-k között

A Bing Maps és a Azure Maps web SDK-k közötti főbb különbségek a következők:

* Amellett, hogy a Azure Maps web SDK eléréséhez üzemeltetett végpontot biztosít, egy NPM-csomag is elérhető a web SDK alkalmazásba való beágyazásához, ha az előnyben részesített. További információkért tekintse meg ezt a [dokumentációt](./how-to-use-map-control.md) . Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz.
* A Bing Maps az SDK két üzemeltetett ágát biztosítja. Kiadás és kísérleti. A kísérleti ág naponta több frissítést is kaphat, amikor új fejlesztés zajlik. Azure Maps csak a kiadási ágakat üzemelteti, de a kísérleti funkciók egyéni modulokként jönnek létre a nyílt forráskódú Azure Maps-mintakód projektben. A Bing Maps-ben egy fagyasztott ág is volt, amely ritkábban frissült, így csökkentve a kiadás miatti változtatások kockázatát. Itt Azure Maps használhatja a NPM modult, és rámutathat bármely korábbi alverzióra.

> [!TIP]
> Azure Maps közzéteszi az SDK minified és unminified verzióit is. Egyszerű eltávolítás `.min` a fájlnevek közül. A unminified verziója hasznos lehet a hibák hibakeresése során, de ügyeljen arra, hogy a minified-verziót éles környezetben használja, hogy kihasználhassa a kisebb fájlméretet.

* Miután létrehozta a Térkép osztály egy példányát a Azure Mapsban, a kódnak meg kell várnia, amíg a térképek `ready` vagy az `load` esemény bekerül a térképre. Ezek az események biztosítják, hogy a Térkép összes erőforrása be legyen töltve, és készen álljon a hozzáférésre.
* Mindkét platform egy hasonló csemperendszer használatát használja az alaptérképekhez, azonban a Bing Maps csempéi a dimenzióban 256 képpont méretűek, míg a Azure Maps csempéi 512 képpont méretűek. Ennek megfelelően, ha ugyanazt a leképezési nézetet szeretné levonni a Bing Maps Azure Maps, a Bing Maps-ben használt nagyítási szintet egy Azure Maps kell kivonnia.
* A Bing Maps-koordinátákat úgy nevezzük, ahogy `latitude, longitude` Azure Maps használja `longitude, latitude` . Ez a formátum a `[x, y]` legtöbb GIS-platform által követett szabványhoz igazodik.

* A Azure Maps web SDK alakzatai a GeoJSON sémán alapulnak. A segítő osztályok az [Atlas. adatnévtéren](/javascript/api/azure-maps-control/atlas.data)keresztül érhetők el. Az Atlas is rendelkezésre áll [. ](/javascript/api/azure-maps-control/atlas.shape) Az GeoJSON objektumok becsomagolásához használható Shape osztály, amely lehetővé teszi, hogy a rendszer könnyen frissítse és karbantartsa azokat az adatkezelési módszerekkel.
* A Azure Mapsban lévő koordináták olyan pozicionálási objektumok, amelyek egyszerű szám tömbként adhatók meg a formátumban `[longitude, latitude]` vagy `new atlas.data.Position(longitude, latitude)` .

> [!TIP]
> A position osztály statikus segítő funkcióval rendelkezik a formátumú koordináták importálásához `latitude, longitude` . Az [Atlas. adat. position. fromLatLng](/javascript/api/azure-maps-control/atlas.data.position)függvény gyakran helyettesítheti a `new Microsoft.Maps.Location` függvényt a Bing Maps Code-ban.

* Ahelyett, hogy a térképhez hozzáadott összes alakzaton adja meg a stílusra vonatkozó információkat, Azure Maps elkülöníti az adatok stílusait. Az adatforrások tárolják az adatforrásokat, és olyan renderelési rétegekhez kapcsolódnak, amelyeket Azure Maps kód használ az adatmegjelenítéshez. Ez a megközelítés nagyobb teljesítménybeli előnyt biztosít. Emellett számos réteg támogatja az adatvezérelt stílust, ahol az üzleti logika hozzáadhatók a Rétegstílus beállításaihoz, amely megváltoztatja az egyes alakzatok rétegen belüli megjelenítését az alakzatban definiált tulajdonságok alapján.
* A Azure Maps számos hasznos térbeli matematikai függvényt biztosít a `atlas.math` névtérben, azonban ezek eltérnek a Bing Maps térbeli matematikai moduljában található adatoktól. Az elsődleges különbség az, hogy Azure Maps nem biztosít beépített függvényeket olyan bináris műveletekhez, mint például a Union és az metszet, azonban mivel a Azure Maps a nyílt szabványú GeoJSON alapul, számos nyílt forráskódú függvénytár érhető el. Egy népszerű lehetőség, amely jól működik Azure Maps és egy tonna térbeli matematikai képességet biztosít a [Turf js](http://turfjs.org/)-nek.

Lásd még a [Azure Maps szószedetet](./glossary.md) a Azure Mapshoz társított terminológia részletes listájához.

## <a name="web-sdk-side-by-side-examples"></a>A web SDK párhuzamos példái

A következő gyűjtemény az egyes platformokra vonatkozik, amelyek általános használati eseteket mutatnak be a webalkalmazás a Bing Maps V8 JavaScript SDK-ból a Azure Maps web SDK-ba való migrálása érdekében. A webalkalmazásokhoz kapcsolódó kódok a JavaScriptben érhetők el; a Azure Maps azonban a [NPM-modulon](./how-to-use-map-control.md)keresztül kiegészítő lehetőségként is biztosít írógéppel-definíciókat.

**Témakörök**

* [Térkép betöltése](#load-a-map)
* [A Térkép honosítása](#localizing-the-map)
* [A Térkép nézet beállítása](#setting-the-map-view)
* [Gombostű hozzáadása](#adding-a-pushpin)
* [Egyéni gombostű hozzáadása](#adding-a-custom-pushpin)
* [Vonallánc hozzáadása](#adding-a-polyline)
* [Sokszög hozzáadása](#adding-a-polygon)
* [Bezárása megjelenítése](#display-an-infobox)
* [Gombostű-fürtözés](#pushpin-clustering)
* [Hő-Térkép hozzáadása](#add-a-heat-map)
* [Csempe rétegének átfedése](#overlay-a-tile-layer)
* [Forgalomadatok megjelenítése](#show-traffic-data)
* [Vízszintes átfedés hozzáadása](#add-a-ground-overlay)
* [KML-adatértékek hozzáadása a térképhez](#add-kml-data-to-the-map)
* [Rajzolási eszközök hozzáadása](#add-drawing-tools)

### <a name="load-a-map"></a>Térkép betöltése

A Térkép betöltése mindkét SDK-ban ugyanezeket a lépéseket követi;

* Adjon hozzá egy hivatkozást a Map SDK-hoz.
* Adjon hozzá egy `div` címkét az oldal törzséhez, amely helyőrzőként fog működni a térképen.
* Hozzon létre egy JavaScript-függvényt, amely akkor lesz meghívva, amikor a lap betöltődött.
* Hozza létre a megfelelő Térkép osztály egy példányát.

**Néhány kulcsfontosságú különbség**

* A Bing Maps megköveteli az API-k vagy térképi beállítások parancsfájl-hivatkozásában megadott fiók kulcsát. A Azure Maps hitelesítési hitelesítő adatai a Map osztály beállításaiként vannak megadva. Ez lehet előfizetés-kulcs vagy Azure Active Directory-információ.
* A Bing Maps egy visszahívási függvényt vesz igénybe az API parancsfájl-hivatkozásában, amely egy inicializálási függvény hívására szolgál a Térkép betöltéséhez. A Azure Maps a lap onload eseményét kell használni.
* Ha AZONOSÍTÓval hivatkozik arra az `div` elemre, amelyet a Térkép megjelenít, a Bing Maps egy HTML-választót (azaz) használ `#myMap` , míg a Azure Maps csak az azonosító értéket (azaz) használja `myMap` .
* A Azure Mapsban lévő koordináták olyan pozicionálási objektumok, amelyek egyszerű szám tömbként adhatók meg a formátumban `[longitude, latitude]` .
* A Azure Maps nagyítási szintje a Bing Maps-hez képest egy szinttel alacsonyabb, a platformok közötti mozaik-rendszerméretek különbsége miatt.
* Alapértelmezés szerint a Azure Maps nem ad hozzá navigációs vezérlőket a Térkép vászonhoz, például a nagyítás gombokat és a Térkép stílusa gombokat. A Térkép stílusú választó, a nagyítási gombok, az iránytű vagy a rotációs vezérlők hozzáadására, valamint a Pitch vezérlőelemre vonatkozó vezérlők is használhatók.
* A rendszer egy eseménykezelőt ad a Azure Maps a `ready` Térkép példánya eseményének figyeléséhez. Ez akkor fog megjelenni, amikor a Térkép befejezte a WebGL-környezet és az összes szükséges erőforrás betöltését. Ebben az eseménykezelőben bármilyen utólagos betöltési kód is hozzáadható.

Az alábbi példák bemutatják, hogyan tölthető be egy olyan alapszintű Térkép, amely egy New York-i koordinátáin (földrajzi hosszúság:-73,985, szélesség: 40,747) található, és a 12. nagyítási szint a Bing Maps-ben.

**Előtte: Bing Maps**

A következő kód egy példa arra, Hogyan jeleníthető meg a Bing Térkép középre, és hogyan nagyítható meg egy adott helyen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

A kód böngészőben való futtatásakor megjelenik egy Térkép, amely a következő képhez hasonlóan néz ki:

![Bing Maps Térkép](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**Utána: Azure Maps**

A következő kód bemutatja, hogyan tölthetők be egy Térkép a Azure Maps azonos nézettel, valamint a Térkép stílusa vezérlőelem és a nagyítás gombokkal.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

A kód böngészőben való futtatásakor megjelenik egy Térkép, amely a következő képhez hasonlóan néz ki:

![Azure Maps Térkép](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

[Itt](how-to-use-map-control.md)talál részletes dokumentációt arról, hogyan kell beállítani és használni a Azure Maps Térkép vezérlőelemet egy webalkalmazásban.

> [!TIP]
> Azure Maps közzéteszi az SDK minified és unminified verzióit is. Távolítsa el `.min` a fájlneveket. A unminified verziója hasznos lehet a hibák hibakeresése során, de ügyeljen arra, hogy a minified-verziót éles környezetben használja, hogy kihasználhassa a kisebb fájlméretet.

**További források**

* A Azure Maps navigációs vezérlőket is biztosít a Térkép nézet elforgatásához és feldobásához az [itt](map-add-controls.md)dokumentált módon.

### <a name="localizing-the-map"></a>A Térkép honosítása

Ha a célközönség több ország között oszlik meg, vagy különböző nyelveket beszél, a honosítás fontos.

**Előtte: Bing Maps**

A Bing Maps honosítása érdekében a nyelv és a régió a és paraméterek használatával van megadva az `setLang` `UR` API-hoz való `<script>` címkézési hivatkozáshoz. A Bing Maps bizonyos funkciói csak bizonyos piacokon érhetők el, mivel a felhasználó piaca a paraméter használatával van megadva `setMkt` .

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Itt látható egy példa arra, hogy a Bing Maps a "fr-FR" nyelvre van állítva.

![Honosított Bing Maps Térkép](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**Utána: Azure Maps**

Azure Maps csak a Térkép nyelvének és regionális nézetének beállítására vonatkozó beállításokat biztosít. A szolgáltatások korlátozására a piac paraméterei nem használhatók. A Térkép nyelvének és regionális nézetének beállítása két különböző módon lehetséges. Az első lehetőség az, hogy hozzáadja ezt az információt a globális `atlas` névtérhez, amely az alkalmazás összes leképezés-vezérlési példányát eredményezi az alapértelmezett beállításokkal. A következő nyelvre állítja be a franciát ("fr-FR") és a regionális nézetet `"Auto"` :

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A második lehetőség az, hogy ezeket az információkat a térképi beállításokba adja át a Térkép betöltése során, például:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> A Azure Maps több térképes példányt is betölthet ugyanazon a lapon, különböző nyelvi és területi beállításokkal. Emellett az is lehetséges, hogy a térképen a betöltés után is frissítheti ezeket a beállításokat. A Azure Maps támogatott nyelveinek részletes listáját [itt](./supported-languages.md)találja.

Itt látható egy példa arra, hogy a "FR" és a "fr-FR" értékre beállított nyelv Azure Maps.

![Honosított Azure Maps Térkép](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>A Térkép nézet beállítása

A Bing és a Azure Maps dinamikus leképezései programozott módon áthelyezhetők az új földrajzi helyekre úgy, hogy meghívja a megfelelő függvényeket a JavaScriptben. Az alábbi példák azt mutatják be, hogyan lehet a Térkép műhold antennát megjeleníteni, középre állítani a térképet koordinátákkal (hosszúság:-111,0225, szélesség: 35,0272), és a nagyítási szint 15 értékre változik a Bing Maps-ben.

> [!NOTE]
> A Bing Maps 256 képpont méretű csempéket használ, míg Azure Maps nagyobb 512 képpont csempét használ. Ez csökkenti a Azure Maps által igényelt hálózati kérések számát, hogy a Térkép területét a Bing Maps-ként töltse be. Ahhoz azonban, hogy a csempe-piramisok hogyan működnek a Térkép vezérlőelemekben, a Azure Maps nagyobb csempéje azt jelenti, hogy ugyanazon megtekinthető területnek a Bing Maps-ben való leképezése esetén a Bing Maps-ben használt nagyítási szintet a Azure Maps használatakor ki kell vonnia 1 értékre.

**Előtte: Bing Maps**

A Bing Maps Térkép vezérlője programozott módon áthelyezhető a `setView` függvény használatával, amely lehetővé teszi a Térkép középpontjának és a nagyítási szint megadását.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Bing Maps-set Térkép nézet](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**Utána: Azure Maps**

Azure Maps a Térkép pozíciója programozott módon módosítható a `setCamera` Térkép funkciójának használatával, és a Térkép stílusa a függvény használatával módosítható `setStyle` . Vegye figyelembe, hogy a Azure Maps koordinátái a "hosszúság, szélesség" formátumban vannak megadva, a nagyítási szint értéke pedig 1.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Azure Maps térkép nézetének beállítása](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**További források**

-   [Térképstílus kiválasztása](./choose-map-style.md)
-   [Támogatott térképstílusok](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Gombostű hozzáadása

Azure Maps több módon is lehet megjeleníteni az adatpontot a térképen;

* HTML-jelölők – a pontokat a hagyományos DOM-elemek használatával jeleníti meg. A HTML-jelölők támogatják a húzást.
* Szimbólum réteg – egy ikont és/vagy szöveget ábrázoló pontokat jelenít meg a WebGL környezetben.
* Buborék réteg – a pontokat körökként jeleníti meg a térképen. A körök sugarait az Adattulajdonságok alapján lehet méretezni.

Mindkét szimbólum és buborék réteg a WebGL-környezeten belül jelenik meg, és képes a térképen nagyon nagy mennyiségű pont megjelenítésére. Ezeknek a rétegeknek az adatforrásokban való tárolásához szükségesek az adatforrások. Az esemény elkezdése után az adatforrásokat és a renderelési rétegeket fel kell venni a térképbe `ready` . A HTML-jelölők DOM-elemekként jelennek meg az oldalon belül, és nem használhatnak adatforrást. Minél több DOM-elem van a lapon, annál lassabb lesz az oldal. Ha a térképeken több száz pontot is megjelenít, javasolt inkább a renderelési rétegek egyikét használni.

Az alábbi példákban adjon hozzá egy jelölőt a térképhez (hosszúság:-0,2, szélesség: 51,5), amelynek a 10-es számú címkének kell lennie.

**Előtte: Bing Maps**

A Bing Maps használatával a jelölők a (*) osztállyal lesznek hozzáadva a térképhez `Microsoft.Maps.Pushpin` . A pushpins ezután a következő két függvény egyikével lesznek hozzáadva a leképezéshez.

Első lépésként hozzon létre egy réteget, szúrja be a gombostű, majd adja hozzá a réteget a Térkép `layers` tulajdonságához.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

A második az, hogy hozzáadja azt a Térkép `entities` tulajdonságának használatával. Ez a függvény elavultként van megjelölve a Bing Maps V8 dokumentációjában, azonban az alapszintű forgatókönyvek esetében részben működőképes marad.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![Bing Maps – puspin hozzáadása](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**Utána: Azure Maps HTML-jelölők használatával**

Azure Maps a HTML-jelölők egyszerűen megjeleníthetők a térképen, és olyan egyszerű alkalmazásokhoz ajánlottak, amelyeknek csak kis számú pontot kell megjeleníteniük a térképen. Ha HTML-jelölőt szeretne használni, hozza létre az osztály egy példányát `atlas.HtmlMarker` , állítsa be a szöveg és a pozíció beállításait, majd adja hozzá a jelölőt a térképhez a `map.markers.add` függvény használatával.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps jelölő hozzáadása](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Utána: Azure Maps egy szimbólum réteget használva**

Ha szimbólum réteget használ, az adatforráshoz és a réteghez csatolt adatforráshoz is hozzá kell adni az adatforrást. Emellett az esemény elindítását követően az adatforrást és a réteget is fel kell venni a térképbe `ready` . Ha egyedi szöveges értéket szeretne megjeleníteni egy szimbólum felett, a szöveges adatokat az adatpont tulajdonságának kell tárolnia, és a rétegben hivatkozott tulajdonságot kell megadni `textField` . Ez valamivel több munkát tesz lehetővé, mint a HTML-jelölők használata, de a teljesítmény számos előnnyel jár.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps a szimbólum hozzáadása réteget](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**További források**

-   [Adatforrás létrehozása](./create-data-source-web-sdk.md)
-   [Szimbólum réteg hozzáadása](./map-add-pin.md)
-   [Buborék réteg hozzáadása](./map-add-bubble-layer.md)
-   [Fürt pontadatai](./clustering-point-data-web-sdk.md)
-   [HTML-jelölők hozzáadása](./map-add-custom-html.md)
-   [Adatvezérelt stíluskifejezések használata](./data-driven-style-expressions-web-sdk.md)
-   [Szimbólum réteg ikonjának beállításai](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Szimbólum réteg szövege beállítás](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML-jelölő osztálya](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML-jelölő beállításai](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Egyéni gombostű hozzáadása

Az egyéni rendszerképeket egy térképen ábrázoló pontok ábrázolására lehet használni. Az alábbi ábra az alábbi példákban szerepel, és egy egyéni rendszerképet használ egy pont megjelenítésére a térképen (szélesség: 51,5, hosszúság:-0,2), és kiegyenlíti a jelölő pozícióját, hogy a gombostű ikon a Térkép helyes helyére legyen igazítva.

| ![Azure Maps puspin hozzáadása](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**Előtte: Bing Maps**

A Bing Maps szolgáltatásban egy egyéni jelölő jön létre, amely egy adott gombostű mutató URL-címet továbbít egy rendszerképnek `icon` . `anchor`Ezzel a beállítással lehet a gombostű-kép pontját a térképen összehangolni. A Bing Maps-beli horgony értéke a rendszerkép bal felső sarkához viszonyítva.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![Bing Maps – egyéni puspin hozzáadása](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**Utána: Azure Maps HTML-jelölők használatával**

A HTML-jelölők testreszabásához Azure Maps egy HTML-kódot, `string` vagy `HTMLElement` átadható a `htmlContent` jelölőhöz. Azure Maps esetén a `anchor` jelölő relatív pozíciója a kilenc megadott hivatkozási pont egyikének használatával adható meg a pozícióhoz viszonyítva. a "Center", a "Top", az "bottom", a "Left", a "Right", a "top-left", a "top-right", a "bottom-Left", a "bottom-Right". A tartalom rögzített, és alapértelmezés szerint az "alsó" értékre van állítva, amely a HTML-tartalom alsó középpontja. Ha könnyebben át szeretné telepíteni a kódot a Bing Mapsből, állítsa a horgonyt "bal felső" értékre, majd használja a `offset` beállítást a Bing Maps-ben használt eltolással. A Azure Maps eltolásai a Bing Maps ellentétes irányába mozdulnak el, tehát mínusz eggyel.

> [!TIP]
> A `pointer-events:none` HTML-tartalom stílusként való hozzáadásával letilthatja az alapértelmezett húzási viselkedést az MS Edge-ben, amely nem kívánt ikont jelenít meg.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps egyéni jelölő hozzáadása](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**Utána: Azure Maps egy szimbólum réteget használva**

A Azure Maps az egyéni rendszerképeket is támogatja, de a lemezképet először be kell tölteni a Térkép erőforrásaiba, és hozzá kell rendelni egy egyedi azonosítót. A szimbólum réteg ezután hivatkozhat erre az AZONOSÍTÓra. A szimbólum eltolással állítható be úgy, hogy a megfelelő pontra legyen igazítva a képen az ikon `offset` lehetőség használatával. A Azure Mapsban egy `anchor` beállítással adható meg a szimbólum relatív helyzete a pozícióhoz viszonyítva a kilenc megadott hivatkozási pont egyikének használatával. a "Center", a "Top", az "bottom", a "Left", a "Right", a "top-left", a "top-right", a "bottom-Left", a "bottom-Right". A tartalom rögzített, és alapértelmezés szerint "bottom" értékre van állítva, amely a HTML-tartalom alsó középpontja. Ha könnyebben át szeretné telepíteni a kódot a Bing Mapsből, állítsa a horgonyt "bal felső" értékre, majd használja a `offset` beállítást a Bing Maps-ben használt eltolással. A Azure Maps eltolásai a Bing Maps ellentétes irányába mozdulnak el, tehát mínusz eggyel.

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Maps egyéni szimbólum hozzáadása réteg](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> A pontok speciális renderelésének létrehozásához használjon több renderelési réteget együtt. Ha például több olyan pushpins szeretne, amelyeknek ugyanaz az ikonja különböző színű körökön, ahelyett, hogy minden egyes színhez több lemezképet hozna létre, az egyes színeknél egy szimbólum réteget kell létrehoznia, és hivatkozni kell ugyanarra az adatforrásra. Ez sokkal hatékonyabbá válik, mint a létrehozás, és a Térkép több különböző lemezképet tart fenn.

**További források**

-   [Adatforrás létrehozása](./create-data-source-web-sdk.md)
-   [Szimbólum réteg hozzáadása](./map-add-pin.md)
-   [HTML-jelölők hozzáadása](./map-add-custom-html.md)
-   [Adatvezérelt stíluskifejezések használata](./data-driven-style-expressions-web-sdk.md)
-   [Szimbólum réteg ikonjának beállításai](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Szimbólum réteg szövege beállítás](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML-jelölő osztálya](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML-jelölő beállításai](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Vonallánc hozzáadása

A vonalláncok egy vonal vagy elérési út ábrázolására szolgálnak a térképen. Az alábbi példák bemutatják, hogyan hozhat létre szaggatott vonalláncot a térképen.

**Előtte: Bing Maps**

A Bing Maps-ben a vonallánc osztály a helyszínek tömbjét és a lehetőségek egy halmazát veszi figyelembe.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Bing Maps-vonallánc](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**Utána: Azure Maps**

Azure Maps a vonalláncok a gyakoribb földrajzi kifejezésekre `LineString` vagy `MultiLineString` objektumokra hivatkoznak. Ezek az objektumok hozzáadhatók egy adatforráshoz, és egy sor réteg használatával is megjeleníthető. Az ecsetvonás szín, a szélesség és a kötőjel tömb beállításai közel azonosak a platformok között.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Azure Maps vonal](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**További források**

-   [Sorok hozzáadása a térképhez](./map-add-line-layer.md)
-   [Vonal rétegének beállításai](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Adatvezérelt stíluskifejezések használata](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Sokszög hozzáadása

A sokszögek egy területnek a térképen való ábrázolására szolgálnak. A Azure Maps és a Bing Maps nagyon hasonló támogatást biztosít a sokszögekhez. Az alábbi példák bemutatják, hogyan hozhat létre olyan sokszöget, amely a Térkép középpontjának koordinátái alapján alkotja a háromszöget.

**Előtte: Bing Maps**

A Bing Maps-ben a sokszög osztály a koordinátákat vagy a koordináta-gyűrűk tömbjét és a különböző beállításokat veszi figyelembe.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Bing Maps polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Utána: Azure Maps**

Azure Maps a sokszög és a többsokszögű objektumok hozzáadhatók egy adatforráshoz, és a térképen a rétegek használatával is megjeleníthető. A sokszög területe egy sokszög rétegben jeleníthető meg. A sokszög körvonalait egy vonal réteggel lehet megjeleníteni.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Azure Maps polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**További források**

-   [Sokszög hozzáadása a térképhez](./map-add-shape.md#use-a-polygon-layer)
-   [Kör hozzáadása a térképhez](./map-add-shape.md#add-a-circle-to-the-map)
-   [Sokszög réteg beállításai](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Vonal rétegének beállításai](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Adatvezérelt stíluskifejezések használata](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Bezárása megjelenítése

Az entitásokra vonatkozó további információk a térképen a `Microsoft.Maps.Infobox` Bing Maps-osztályként is megjeleníthetők, Azure Maps ez az osztály használatával érhető el `atlas.Popup` . Az alábbi példákban hozzáadhat egy gombostű/jelölőt a térképhez, és amikor rákattintanak, megjelenik egy bezárása/popup.

**Előtte: Bing Maps**

A Bing Maps segítségével létrejön egy bezárása a konstruktor használatával `Microsoft.Maps.Infobox` .

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Bing Maps bezárása](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**Utána: Azure Maps**

Azure Maps az előugró ablakban további információk jeleníthetők meg egy adott helyről. Egy HTML `string` vagy `HTMLElement` objektum átadható a `content` felugró ablakban. Az előugró ablakok az alakzattól függetlenül is megjeleníthetők, ha szükséges, és így meg kell `position` adni egy értéket. Egy előugró ablak megjelenítéséhez hívja `open` meg a függvényt, és adja meg azt a térképet, amelyen a felugró ablak jelenik meg.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Azure Maps előugró ablak](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> Ha ugyanezt a karaktert egy szimbólummal, buborékmal, vonal vagy sokszög réteggel szeretné elvégezni, adja át a réteget a Maps-eseménynek a jelölő helyett.

**További források**

-   [Előugró ablak hozzáadása](./map-add-popup.md)
-   [Multimédiás tartalommal rendelkező előugró ablak](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Felugró ablakok az alakzatokon](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Felugró ablak újrahasználata több PIN-kód használatával](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Felugró osztály](/javascript/api/azure-maps-control/atlas.popup)
-   [Előugró beállítások](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Gombostű-fürtözés

Ha a Térkép számos adatpontját jeleníti meg, a pontok átfedésben vannak egymással, a Térkép zsúfoltnak tűnik, és nehéz lesz megtekinteni és használni. A pontszerű adatmennyiségek fürtözése a felhasználói élmény javításához és a teljesítmény javításához használható. A fürtözési pontra vonatkozó adatgyűjtési folyamat a pontok egymáshoz közel lévő és a térképen való megjelenítését jelenti egyetlen fürtözött adatpontként. Ahogy a felhasználó nagyítja a térképet, a fürtök az egyes adatpontokon kívülre kerülnek.

Az alábbi példák betöltenek egy GeoJSON-hírcsatornát az elmúlt héten, és felveszik azt a térképbe. A fürtök méretezési és színes körökként jelennek meg az általuk tartalmazott pontok számától függően.

> [!NOTE]
> A gombostű-fürtözéshez több különböző algoritmus is használatos. A Bing Maps egy egyszerű, rácson alapuló függvényt használ, míg Azure Maps egy fejlettebb és vizuálisan vonzó, pont-alapú fürtszolgáltatási módszert használ.

**Előtte: Bing Maps**

A Bing Maps-ben a GeoJSON adatai a GeoJSON modul használatával tölthetők be. A pushpins fürtözött modulba való betöltéssel és a benne található fürtözött réteg használatával csoportosítható.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Maps-fürtözés](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**Utána: Azure Maps**

A Azure Mapsban az adatforrások felveszik és kezelik az adatforrásokat. A rétegek az adatforrásokhoz csatlakoznak, és a bennük tárolt adatmegjelenítést is lehetővé teszik. A `DataSource` Azure Maps osztály számos fürtszolgáltatási lehetőséget kínál.

* `cluster` – Megadja az adatforrást a fürt pontjának. 
* `clusterRadius` – A fürthöz tartozó, képpontban megadott sugár együtt.
* `clusterMaxZoom` – A fürtözés során felmerülő maximális nagyítási szint. Ha ennél nagyobb nagyítást végez, a rendszer az összes pontot szimbólumként jeleníti meg.
* `clusterProperties` – Meghatározza a kiszámított egyéni tulajdonságokat az egyes fürtökön lévő összes pontra vonatkozó kifejezések használatával, és hozzáadja az egyes fürtcsomópontok tulajdonságaihoz.

Ha a fürtözés engedélyezve van, akkor az adatforrás fürtözött és nem fürtözött adatpontokat küld a renderelési rétegeknek. Az adatforrás több száz ezer adatpont fürtözésére képes. A fürtözött adatpontok a következő tulajdonságokkal rendelkeznek:

| Tulajdonság neve               | Típus    | Description                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Azt jelzi, hogy a szolgáltatás egy fürtöt jelöl-e.     |
| `cluster_id`                | sztring  | A fürt egyedi azonosítója, amely az `DataSource` osztályokkal `getClusterExpansionZoom` , valamint a függvények használatával használható `getClusterChildren` `getClusterLeaves` . |
| `point_count`               | szám  | A fürt által tartalmazott pontok száma.     |
| `point_count_abbreviated`   | sztring  | Egy karakterlánc, amely hosszabb ideig rövidíti az `point_count` értéket. (például 4 000-es lesz 4K) |

Az `DataSource` osztály a következő segítő függvényt használja a fürttel kapcsolatos további információk eléréséhez a használatával `cluster_id` .

| Függvény       | Visszatérési típus        | Description     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | A következő nagyítási szinten kéri le a megadott fürt gyermekeit. Ezek a gyermekek alakzatokat és alfürtöket is tartalmazhatnak. Az alfürtök a fürt tulajdonságaival egyező tulajdonságokkal rendelkező funkciók lesznek. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Kiszámítja azt a nagyítási szintet, amelyet a fürt elkezd kibővíteni vagy bontani.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Egy fürt összes pontjának lekérése. A `limit` gombra kattintva adja vissza a pontok egy részhalmazát, és használja a `offset` to oldalt a pontokon keresztül.    |

Amikor fürtözött adatmegjelenítést alkalmaz a térképen, gyakran a legegyszerűbb, ha két vagy több réteget használ. Az alábbi példa három réteget használ, egy buborék réteget a fürtök mérete alapján méretezett színes körök rajzolásához, egy szimbólum réteget, amely a fürt méretét szövegként jeleníti meg, és egy második szimbólum réteget a nem fürtözött pontok megjelenítéséhez. Számos más módon is megjelenítheti a fürtözött adatmennyiségeket a Azure Maps kiemelve a [fürt pont](./clustering-point-data-web-sdk.md) adatdokumentációjában.

A GeoJSON-adat közvetlenül importálható Azure Maps az `importDataFromUrl` osztályban található függvény használatával `DataSource` .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps fürtözés](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**További források**

-   [Szimbólum réteg hozzáadása](./map-add-pin.md)
-   [Buborék réteg hozzáadása](./map-add-bubble-layer.md)
-   [Fürt pontadatai](./clustering-point-data-web-sdk.md)
-   [Adatvezérelt stíluskifejezések használata](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Hő-Térkép hozzáadása

A Heat Maps, más néven a pont sűrűsége térképek olyan adatvizualizációk, amelyek az adatsűrűséget jelölik az adatmennyiség színskálájának használatával. Gyakran használják a "gyors elérésű pontok" megjelenítésére a térképen, és nagyszerű módszer a nagyméretű adathalmazok megjelenítésére.

Az alábbi példák betöltenek egy GeoJSON-hírcsatornát az elmúlt hónap során az USGS-ből, és egy hőt képeznek.

**Előtte: Bing Maps**

A Bing Maps-ben hozzon létre egy hő-térképet, és töltse be a Heat Map modulba. Hasonlóképpen a GeoJSON modul is betöltődik, hogy támogatást adjon a GeoJSON-adatbázishoz.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Maps hő](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**Utána: Azure Maps**

A Azure Mapsban töltse be a GeoJSON egy adatforrásba, és kapcsolja össze az adatforrást egy hő-Térkép réteggel. A GeoJSON-adat közvetlenül importálható Azure Maps az `importDataFromUrl` osztályban található függvény használatával `DataSource` .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps hő](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**További források**

-   [Hőtérképréteg hozzáadása](./map-add-heat-map-layer.md)
-   [Heat Térkép réteg osztálya](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Hő-Térkép réteg beállításai](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Adatvezérelt stíluskifejezések használata](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Csempe rétegének átfedése

A csempe rétegek lehetővé teszik a nagyméretű képek átfedését, amelyek a Maps-csempe rendszerhez igazított kisebb csempés képekre vannak bontva. Ez egy gyakori módja a nagyméretű képek vagy nagyon nagy adathalmazok átfedésének.

Az alábbi példákban egy, az Iowa állam egyetemének Iowa környezeti Mesonet származó időjárási radar csempe réteg látható, amely egy X, Y, zoom csempézés elnevezési sémát használ.

**Előtte: Bing Maps**

A Bing Maps szolgáltatásban a csempe rétegek a osztály használatával hozhatók létre `Microsoft.Maps.TileLayer` .

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![A Bing Maps súlyozott hő](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**Utána: Azure Maps**

Azure Maps a csempék rétegét ugyanúgy lehet a térképhez adni, mint bármely más rétegben. Egy formázott URL-cím, amely x, y és nagyítási helyőrzőben található. `{x}`, `{y}` `{z}` vagy annak a rétegnek a használatával kell eldönteni, hogy hová fér hozzá a csempék. Azure Maps csempe rétegek is támogatják `{quadkey}` a `{bbox-epsg-3857}` és a `{subdomain}` helyőrzőket.

> [!TIP]
> Azure Maps rétegekben könnyen megjeleníthető más rétegek alatt, beleértve az alapszintű leképezési rétegeket is. Gyakran érdemes a Térkép feliratai alatt megjeleníteni a csempe rétegeit, hogy azok könnyen olvashatók legyenek. A `map.layers.add` függvény a második réteg azonosítóját veszi át, amely az alábbi új réteget szúrja be. A Térkép felirat alatti csempe réteg beszúrásához a következő kód használható:
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Súlyozott hő Azure Maps](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> A csempe-kéréseket a `transformRequest` Térkép lehetőségével lehet rögzíteni. Ez lehetővé teszi, hogy szükség esetén módosítsa vagy adja hozzá a fejléceket a kéréshez.

**További források**

-   [Csemperétegek hozzáadása](./map-add-tile-layer.md)
-   [Csempe réteg osztálya](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Csempe rétegének beállításai](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Forgalomadatok megjelenítése

A forgalmi adatok a Bing és az Azure Maps is felhelyezhetők.

**Előtte: Bing Maps**

A Bing Maps szolgáltatásban a forgalmi adatok áthelyezhetők a térképen a forgalmi modul használatával.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Bing Maps-forgalom](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**Utána: Azure Maps**

Azure Maps számos különböző lehetőséget biztosít a forgalom megjelenítéséhez. A forgalmi incidensek, például a közúti bezárások és a balesetek ikonként is megjeleníthetők a térképen. A forgalom, a színkódolt utak a térképen is behelyezhetők, és a színek a feladott sebességkorlátozás alapján módosíthatók, a normál várható késéshez képest, vagy abszolút késleltetéssel. Az incidensek Azure Maps percenként frissülnek, és az adatforgalom 2 percenként történik.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Azure Maps forgalom](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Ha a Azure Maps egyik forgalmi ikonjára kattint, további információk jelennek meg a felugró ablakban.

![Azure Maps forgalmi felugró ablak](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**További források**

-   [Forgalom megjelenítése a térképen](./map-show-traffic.md)
-   [Forgalom átfedési beállításai](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Forgalom-ellenőrzés](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Vízszintes átfedés hozzáadása

Mind a Bing, mind az Azure Maps támogatja a térképeken a georeferens-lemezképek átfedését, így azok mozgatása és méretezése is lehetővé teszi a Térkép pásztázását és nagyítását. A Bing Maps-ben ezek az úgynevezett rendszerátfedések, miközben a Azure Maps képrétegként hivatkoznak rájuk. Ezek kiválóan használhatók az alapszintű tervek létrehozásához, a régi térképek és a képek átfedéséhez egy drone-ből.

**Előtte: Bing Maps**

Amikor a Bing Mapsben hoz létre egy terepi átfedést, meg kell adnia az átfedő képhez tartozó URL-címet és egy határoló mezőt, amely a képet a térképhez köti. Ez a példa egy [Newark New Jersey](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) térképi képét fedi fel a térképen a 1922-es számon.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

A kód böngészőben való futtatásakor megjelenik egy Térkép, amely a következő képhez hasonlóan néz ki:

![Bing Maps – alapátfedés](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**Utána: Azure Maps**

Azure Maps a georeferens képeket a osztály használatával lehet betakarni `atlas.layer.ImageLayer` . Ehhez az osztályhoz a rendszerkép egy URL-címe és egy koordináták halmaza szükséges a rendszerkép négy sarkánál. A rendszerképet ugyanazon a tartományon kell tárolni, vagy engedélyezve kell lennie a CORs.

> [!TIP]
> Ha csak Észak-, Dél-, Kelet-, Nyugat-és rotációs információkkal rendelkezik, és a képek minden sarkánál nem található koordináták, használhatja a statikus [Atlas. Layer. ImageLayer. getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) függvényt.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**További források**

-   [Kép átfedése](./map-add-image-layer.md)
-   [Képréteg osztálya](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>KML-adatértékek hozzáadása a térképhez

Mind az Azure-, mind a Bing Maps képes a KML-, KMZ-, GeoRSS-, GeoJSON-és Well-Known Text (WKT) típusú adatleképezések importálására és megjelenítésére. A Azure Maps támogatja a GPX, a GML, a térbeli CSV-fájlok, a Web-Mapping-szolgáltatások (WMS), a Web-Mapping csempe-szolgáltatások (WMTS) és a web feature Services (WFS) használatát is.

**Előtte: Bing Maps**

A kód böngészőben való futtatásakor megjelenik egy Térkép, amely a következő képhez hasonlóan néz ki:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Maps – KML](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**Utána: Azure Maps**

Azure Maps GeoJSON a web SDK-ban használt fő adatformátum, a további térbeli adatformátumok könnyen integrálhatók a [térbeli IO modul](/javascript/api/azure-maps-spatial-io/)használatával. Ez a modul a térbeli adatok olvasását és írását is magában foglalja, valamint egy egyszerű adatréteget is tartalmaz, amely könnyen megjelenítheti ezeket a térbeli adatformátumokból származó adatok bármelyikét. A térbeli adatfájlokban lévő információk beolvasásához adjon meg egy URL-címet vagy egy nyers adat karakterláncként vagy blobként a `atlas.io.read` függvénybe. Ezzel visszaadja a fájl összes elemzett adatait, amelyet aztán hozzáadhat a térképhez. A KML egy kicsit összetettebb, mint a legtöbb térbeli adatformátum, mivel sokkal több stílussal kapcsolatos információt tartalmaz. Az `SpatialDataLayer` osztály támogatja a stílusok renderelési többségét, azonban a szolgáltatásba való betöltés előtt az ikonokat be kell tölteni a térképbe, és a rendszernek külön fel kell vennie a terepeket a térképbe. Ha egy URL-címen keresztül tölt be egy adatkészletet, azt egy CORs-kompatibilis végponton kell üzemeltetni, vagy a proxy szolgáltatást az olvasási függvénynek kell átadnia.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**További források**

-   [Atlas. IO. Read függvény](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Rajzolási eszközök hozzáadása

A Bing és a Azure Maps egyaránt biztosít egy modult, amely lehetővé teszi, hogy a felhasználó az egér vagy más beviteli eszköz használatával rajzolja meg és szerkessze az alakzatokat a térképen. Mindkettő támogatja a rajzolási pushpins, vonalakat és sokszögeket. A Azure Maps a körök és négyszögek rajzolási lehetőségeit is tartalmazza.

**Előtte: Bing Maps**

A Bing Maps `DrawingTools` szolgáltatásban a modul a függvény használatával töltődik be `Microsoft.Maps.loadModule` . A betöltés után a DrawingTools osztály egy példánya hozható létre, és a `showDrawingManager` függvény neve egy eszköztár hozzáadása a térképhez.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Bing Maps Rajzeszközök](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**Utána: Azure Maps**

Azure Maps a rajzolási eszközök modulját be kell tölteni a JavaScript és a CSS fájlok betöltésével az alkalmazásban. A Térkép betöltését követően a osztály egy példánya hozható `DrawingManager` létre, és egy `DrawingToolbar` példány csatolható.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Azure Maps rajzolási eszközök](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> Azure Maps rétegekben a rajzolási eszközök több módszert biztosítanak a felhasználók számára az alakzatok rajzolására. Például egy sokszög rajzolásakor a felhasználó rákattinthat az egyes pontok hozzáadására, vagy lenyomva tartja a bal egérgombot, és az egér húzásával rajzolja meg az elérési utat. Ezt a `interactionType` beállításával módosíthatja `DrawingManager` .

**További források**

-   [Dokumentáció](./set-drawing-options.md)
-   [Kódminták](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>További források

Tekintse meg a [nyílt forráskódú Azure Maps web SDK-modulokat](open-source-projects.md#open-web-sdk-modules). Ezek a modulok rengeteg további funkciót biztosítanak, és teljes mértékben testreszabhatók.

Tekintse át a Bing Maps egyéb funkcióinak áttelepítéséhez kapcsolódó kód mintáit:

**Adatmegjelenítések**

> [!div class="nextstepaction"]
> [Kontúr réteg](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Az adatdobozolási](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Szolgáltatások**

> [!div class="nextstepaction"]
> [A Azure Maps Services modul használata](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Hasznos helyek keresése](./map-search-location.md)

> [!div class="nextstepaction"]
> [Adatok lekérése egy koordinátaből (fordított geocode)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](./map-route.md)

> [!div class="nextstepaction"]
> [Automatikus javaslat keresése a JQuery felhasználói felülettel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

További információ a Azure Maps web SDK-ról.

> [!div class="nextstepaction"]
> [A Térkép vezérlőelem használata](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [A szolgáltatások modul használata](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [A rajzolási eszközök modul használata](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kódminták](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure Maps web SDK szolgáltatás API-referenciájának dokumentációja](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincs kitakarítható erőforrás.

## <a name="next-steps"></a>Következő lépések

További információ a Bing Maps rendszerről Azure Mapsre való áttelepítésről.

> [!div class="nextstepaction"]
> [Webszolgáltatás migrálása](migrate-from-bing-maps-web-services.md)