---
title: 'Oktatóanyag: webalkalmazás migrálása a Google Maps szolgáltatásból | Microsoft Azure térképek'
description: Webalkalmazások áttelepítésének módja a Google Maps szolgáltatásból Microsoft Azure Maps-be.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: b954c812bea6c2abf4376c2cee38a3789461ad01
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208743"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Webalkalmazás migrálása a Google Maps szolgáltatásból

A Google Maps-et használó webalkalmazások többsége a Google Maps v3 JavaScript SDK-t használja. A Azure Maps web SDK a megfelelő Azure-alapú SDK, amelybe migrálni lehet. A Azure Maps web SDK lehetővé teszi az interaktív térképek egyéni tartalommal és képekkel való testreszabását. Az alkalmazást webes vagy mobil alkalmazásokon is futtathatja. Ez a vezérlő a WebGL-t használja, amely lehetővé teszi nagy adatkészletek nagy teljesítményű renderelését. Fejlessze az SDK-t JavaScript vagy írógéppel használatával.

Ha egy meglévő webalkalmazást telepít át, ellenőrizze, hogy a nyílt forráskódú Térkép vezérlő függvénytárat használ-e. Példák a nyílt forráskódú Térkép vezérlőelem-függvénytárra: cézium, betegtájékoztató és OpenLayers. Az alkalmazást továbbra is áttelepítheti, még akkor is, ha nyílt forráskódú Térkép vezérlőelem-függvénytárat használ, és nem kívánja használni a Azure Maps web SDK-t. Ilyen esetben az alkalmazást a Azure Maps csempe-szolgáltatásokhoz (a[közúti csempék](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| a [műholdas csempék](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)) kapcsolja össze. A következő pontok részletesen ismertetik, hogyan használhatja a Azure Maps-t néhány gyakran használt nyílt forráskódú Térkép vezérlőelem-függvénytárban.

- Cézium – a webes 3D Térkép vezérlőelem. [Mintakód \|](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) [dokumentáció](https://cesiumjs.org/)
- Szórólap – kis méretű 2D Térkép vezérlőelem a weben. [Mintakód \|](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) [dokumentáció](https://leafletjs.com/)
- OpenLayers – a kivetítéseket támogató webes 2D Térkép vezérlőelem. [Mintakód \|](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) [dokumentáció](https://openlayers.org/)

## <a name="key-features-support"></a>A főbb funkciók támogatása

A táblázat a Google Maps v3 JavaScript SDK legfontosabb API-funkcióit és a Azure Maps web SDK által támogatott API-funkciót sorolja fel.

| Google Maps szolgáltatás     | Webes SDK-támogatás Azure Maps |
|-------------------------|:--------------------------:|
| Markerek                 | ✓                          |
| Jelölő fürtszolgáltatása       | ✓                          |
| Vonalláncok & sokszögek    | ✓                          |
| Adatrétegek             | ✓                          |
| A felszín alatti átfedések         | ✓                          |
| Heat Maps               | ✓                          |
| Csempe rétegei             | ✓                          |
| KML-réteg               | ✓                          |
| Rajzeszközök           | ✓                          |
| Geocoder szolgáltatás        | ✓                          |
| Directions szolgáltatás      | ✓                          |
| Távolsági mátrix szolgáltatás | ✓                          |
| Jogosultságszint-emelési szolgáltatás       | Tervezve                    |

## <a name="notable-differences-in-the-web-sdks"></a>Jelentős különbségek a webes SDK-k között

A következőkben a Google Maps és a Azure Maps web SDK-k közötti főbb különbségek vannak:

- A Azure Maps web SDK elérésére szolgáló üzemeltetett végpontok biztosításán kívül egy NPM-csomag is elérhető. Beágyazza a webes SDK-csomagot az alkalmazásokba. További információkért tekintse meg ezt a [dokumentációt](how-to-use-map-control.md). Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz.
- Először létre kell hoznia a Map osztály egy példányát Azure Mapsban. Várjon, amíg a Maps `ready` vagy `load` eseményt, mielőtt programozott módon kommunikál a térképpel. Ez a sorrend biztosítja, hogy a Térkép összes erőforrása be legyen töltve, és készen álljon a hozzáférésre.
- Mindkét platform egy hasonló csempe-rendszer használatát használja az alaptérképekhez. A Google Maps csempéi 256 képpont méretűek; a Azure Maps csempéi azonban 512 képpont méretűek. Ha ugyanazt a térképes nézetet szeretné megjeleníteni Azure Maps a Google Maps-ben, akkor a Google Maps nagyítási szintjét a Azure Maps számával kivonja.
- A Google Maps-koordinátákat a "szélesség, hosszúság" értékre nevezik, míg Azure Maps a "hosszúság, szélesség" kifejezést használja. A Azure Maps formátum a standard `[x, y]`hoz van igazítva, amelyet a legtöbb GIS-platform követ.
- A Azure Maps web SDK alakzatai a GeoJSON sémán alapulnak. A segítő osztályok az [ *Atlas.* adatnévtéren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)keresztül érhetők el. Van még az [*Atlas is. Alakzat*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) osztálya Ezt az osztályt a GeoJSON objektumok becsomagolásához használhatja, így egyszerűen frissítheti és kezelheti az adatkötési módot.
- A Azure Mapsban lévő koordináták a pozíció objektumokként vannak meghatározva. A koordináta a `[longitude,latitude]`formátumú tömbként van megadva. Vagy az új Atlas. reposition (hosszúság, szélesség) értékkel van megadva.
    > [!TIP]
    > A position osztály statikus segítő módszert biztosít a "szélesség, hosszúság" formátumú koordináták importálásához. Gyakran lecserélheti az [Atlas. reposition. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) metódust a Google Maps Code `new google.maps.LatLng` metódusával.
- Azure Maps elkülöníti a stílusokat az adatokból. Az adatok és a stílusok elválasztása nagyobb teljesítményű, mint a térképhez hozzáadott egyes alakzatokon a formázási információk megadását. Az adatforrásokban tárolt adatforrások a renderelési rétegekhez vannak csatlakoztatva. Azure Maps a kód adatforrásokat használ az információk megjelenítéséhez. Ez a megközelítés nagyobb teljesítménybeli előnyt biztosít. Emellett számos réteg támogatja az olyan adatvezérelt stílust, ahol az üzleti logika hozzáadhatók a Rétegstílus beállításaihoz. Ez a támogatás megváltoztatja az egyes alakzatok rétegen belüli megjelenítését az alakzatban definiált tulajdonságok alapján.

## <a name="web-sdk-side-by-side-examples"></a>A web SDK párhuzamos példái

Ez a gyűjtemény az egyes platformokhoz tartalmaz kód-mintákat, és mindegyik minta általános használati esetet takar. Célja, hogy a webalkalmazást a Google Maps v3 JavaScript SDK-ból telepítse át a Azure Maps web SDK-ba. A webalkalmazásokhoz kapcsolódó kódok a JavaScriptben érhetők el. A Azure Maps azonban a [NPM-modulon](how-to-use-map-control.md)keresztül kiegészítő lehetőségként is biztosít írógéppel-definíciókat.

### <a name="load-a-map"></a>Térkép betöltése

A Térkép betöltéséhez mindkét SDK-nak ugyanaz a lépései vannak:

- Adjon hozzá egy hivatkozást a Map SDK-hoz.
- Adjon hozzá egy `div` címkét az oldal törzséhez, amely helyőrzőként fog működni a térképen.
- Hozzon létre egy JavaScript-függvényt, amely akkor lesz meghívva, amikor a lap betöltődött.
- Hozza létre a megfelelő Térkép osztály egy példányát.

**Néhány kulcsfontosságú különbség**

- A Google Maps szolgáltatáshoz meg kell adni egy fiókot, amely az API parancsfájl-hivatkozásában meg van adva. A Azure Maps hitelesítési hitelesítő adatai a Map osztály beállításaiként vannak megadva. Ez a hitelesítő adat lehet előfizetési kulcs vagy Azure Active Directory információ.
- A Google Maps fogad egy visszahívási függvényt az API parancsfájl-hivatkozásában, amely egy inicializálási függvény hívására szolgál a Térkép betöltéséhez. A Azure Maps a lap onload eseményét kell használni.
- Amikor a leképezést megjelenítő `div` elemre hivatkozik, a `Map` osztálynak Azure Maps csak a `id` értéket kell megadnia, amíg a Google Maps-nek `HTMLElement` objektumra van szüksége.
- A Azure Mapsban lévő koordináták olyan pozicionálási objektumok, amelyek egyszerű szám tömbként adhatók meg `[longitude, latitude]`formátumban.
- A Azure Maps nagyítási szintje a Google Maps-ben a nagyítási szintnél alacsonyabb szinten van. Ez az eltérés azért van, mert a két platform mozaikszerű elrendezési rendszerének különbsége.
- Azure Maps nem ad hozzá navigációs vezérlőket a Térkép vászonhoz. Így alapértelmezés szerint a Térkép nem rendelkezik nagyítási gombokkal és a Térkép stílusa gombokkal. Azonban lehetőség van a Térkép stílusú választó, a nagyítási gombok, az iránytű vagy a rotációs vezérlők hozzáadására, valamint egy pitch vezérlőelemre.
- A rendszer egy eseménykezelőt ad a Azure Maps a Térkép példány `ready` eseményének figyeléséhez. Ez az esemény akkor indul el, ha a Térkép befejezte a WebGL-környezet és az összes szükséges erőforrás betöltését. Adja hozzá a Térkép befejezése után futtatni kívánt kódokat az eseménykezelőhöz.

Az alábbi alapszintű példák a Google Maps szolgáltatást használják, hogy a koordinátákon keresztül középre igazított térképet töltsön be. A hosszúság:-73,985, szélesség: 40,747, és a Térkép a nagyítási szint 12.

**Előtte: Google Maps**

Megjelenítheti a Google Térkép középre igazított és nagyított helyét.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

A kód böngészőben való futtatásakor megjelenik egy Térkép, amely a következő képhez hasonlóan néz ki:

<center>

![egyszerű Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Utána: Azure Maps**

Töltsön be egy térképet ugyanazzal a nézettel Azure Maps együtt a Térkép stílusa vezérlőelem és a nagyítás gombokkal.

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

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
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

<center>

Egyszerű Azure Maps ![](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Részletes dokumentációt talál arról, hogyan állíthatja be és használhatja a Azure Maps Térkép vezérlőelemet egy webalkalmazásban, ehhez kattintson [ide](how-to-use-map-control.md).

> [!NOTE]
> A Google Maps-től eltérően a Azure Maps nem igényel kezdeti központot és nagyítási szintet a Térkép betöltéséhez. Ha ez az információ nem jelenik meg a Térkép betöltésekor, az Azure Maps megpróbálja megállapítani a felhasználó városát. A Térkép középpontba kerül, majd nagyítja a térképet.

**További források:**

- A Azure Maps navigációs vezérlőket is biztosít a Térkép nézet elforgatásához és feldobásához, az [itt](map-add-controls.md)leírtak szerint.

### <a name="localizing-the-map"></a>A Térkép honosítása

Ha a célközönség több ország között oszlik meg, vagy különböző nyelveket beszél, a honosítás fontos.

**Előtte: Google Maps**

A Google Maps honosítása érdekében adjon hozzá nyelvi és területi paramétereket.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Itt látható egy példa arra, hogy a Google Maps a "fr-FR" nyelvre van állítva.

<center>

![Google Maps honosítás](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Utána: Azure Maps**

Azure Maps két különböző módszert biztosít a Térkép nyelvének és regionális nézetének beállításához. Az első lehetőség az adatok hozzáadása a globális *atlasz* -névtérhez. Ennek eredményeképpen az alkalmazás minden Térkép vezérlőelem-példánya alapértelmezés szerint ezekkel a beállításokkal fog szerepelni. A következő nyelvre állítja be a franciát ("fr-FR") és a regionális nézetet az "Auto" értékre:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A második lehetőség az, hogy ezeket az információkat a térképre való betöltéskor átadja a térképi beállításoknak. tetszik:

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
> A Azure Maps használatával több leképezési példányt is betölthet ugyanazon az oldalon különböző nyelvi és területi beállításokkal. A beállításokat a betöltés után is frissítheti a térképen. 

A Azure Maps [támogatott nyelveinek](supported-languages.md) részletes listáját itt találja.

Itt látható egy példa arra, hogy a "FR" és a "fr-FR" értékre beállított nyelv Azure Maps.

<center>

![Azure Maps honosítás](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>A Térkép nézet beállítása

Az Azure Maps és a Google Maps szolgáltatásban a dinamikus térképek programozott módon áthelyezhetők új földrajzi helyekre. Ehhez hívja meg a megfelelő függvényeket a JavaScriptben. A példa bemutatja, hogyan jelenítheti meg a Térkép műhold antennáját, középpontba veheti a térképet egy adott helyen, és módosíthatja a nagyítási szintet. A rendszer a következő földrajzi koordinátákat használja: hosszúság:-111,0225 és szélesség: 35,0272.

> [!NOTE]
> A Google Maps a méretekben 256 képpontos csempéket használ, míg a Azure Maps nagyobb 512 képpont csempét használ. Így a Azure Maps kevesebb hálózati kérést igényel, hogy ugyanazt a térképi terület legyen betöltve a Google Maps szolgáltatásban. Mivel a csempe-piramisok a Térkép vezérlőelemekben működnek, ki kell vonnia a Google Maps-ben használt nagyítási szintet Azure Maps használatakor. Ez az aritmetikai művelet biztosítja, hogy a Azure Maps nagyobb csempéi a Google Maps-ben azonos ábrázolási területtel rendelkeznek.

**Előtte: Google Maps**

Helyezze át a Google Maps Térkép vezérlőelemet a `setOptions` metódus használatával. Ez a módszer lehetővé teszi a Térkép középpontjának és a nagyítási szint megadását.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps-készlet nézet](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Utána: Azure Maps**

A Azure Mapsban módosítsa a Térkép pozícióját a `setCamera` metódussal, és módosítsa a Térkép stílusát a `setStyle` metódus használatával. A (z) Azure Maps koordinátái "hosszúságú, szélesség" formátumúak, és a nagyítási szint értékének kivonása eggyel történik.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure Maps beállított nézet](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**További források:**

- [Válasszon egy leképezési stílust](choose-map-style.md)
- [Támogatott Térkép stílusa](supported-map-styles.md)

### <a name="adding-a-marker"></a>Jelölő hozzáadása

Azure Maps több módon is lehet megjeleníteni az adatpontot a térképen:

- **HTML-jelölők** – a pontokat a hagyományos DOM-elemek használatával jeleníti meg. A HTML-jelölők támogatják a húzást.
- **Szimbólum réteg** – az WebGL-környezetben található ikonokkal vagy szöveggel jeleníti meg a pontokat.
- **Buborék réteg** – a pontokat körökként jeleníti meg a térképen. A körök sugarait az Adattulajdonságok alapján lehet méretezni.

A szimbólum rétegeinek és buborékdiagram-rétegeinek megjelenítése a WebGL környezetben. Mindkét réteg képes nagy mennyiségű pontot megjeleníteni a térképen. Ezeknek a rétegeknek az adatforrásokban való tárolásához szükségesek az adatforrások. Az adatforrásokat és a renderelési rétegeket a `ready` esemény után hozzá kell adni a térképhez. A HTML-jelölők DOM-elemekként jelennek meg az oldalon belül, és nem használnak adatforrást. Minél több DOM-elem van a lapon, annál lassabb lesz az oldal. Ha a térképen több száz pont is van, akkor azt javasoljuk, hogy használja helyette a renderelési rétegek egyikét.

Vegyünk fel egy jelölőt a térképhez a 10. számú címkével. Használja a hosszúság:-0,2 és a szélesség: 51,5.

**Előtte: Google Maps**

A Google Maps használatával adja hozzá a jelölőket a térképhez a `google.maps.Marker` osztály segítségével, és adja meg a térképet az egyik lehetőség közül.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps-jelölő](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Utána: Azure Maps HTML-jelölők használatával**

A Azure Mapsban HTML-jelölők használatával jeleníthető meg egy pont a térképen. Olyan alkalmazásokhoz ajánlott HTML-jelölőket használni, amelyeknek csak kis számú pontot kell megjeleníteniük a térképen. Ha HTML-jelölőt szeretne használni, hozza létre a `atlas.HtmlMarker` osztály egy példányát. Állítsa be a szöveg és a pozíció beállításait, és adja hozzá a jelölőt a térképhez a `map.markers.add` metódus használatával.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps HTML-jelölő](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Utána: Azure Maps egy szimbólum réteget használva**

Szimbólum réteg esetén adja hozzá az adatforráshoz az adatforrást. Csatolja az adatforrást a réteghez. Emellett az adatforrást és a réteget a `ready` esemény után hozzá kell adni a térképhez. Egy szimbólum feletti egyedi szöveges érték megjelenítéséhez a szöveges adatokat az adatpont tulajdonságának kell tárolnia. A tulajdonságot a réteg `textField` lehetőségében kell hivatkozni. Ez a módszer valamivel több munkát jelent, mint a HTML-jelölők használata, de jobb teljesítményt nyújt.

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

<center>

![Azure Maps szimbólum réteg](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**További források:**

- [Adatforrás létrehozása](create-data-source-web-sdk.md)
- [Szimbólum réteg hozzáadása](map-add-pin.md)
- [Buborék réteg hozzáadása](map-add-bubble-layer.md)
- [A fürt pontjának adatterülete](clustering-point-data-web-sdk.md)
- [HTML-jelölők hozzáadása](map-add-custom-html.md)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)
- [Szimbólum réteg ikonjának beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Szimbólum réteg szövege beállítás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-jelölő osztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML-jelölő beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Egyéni jelölő hozzáadása

Az egyéni rendszerképeket a térképen ábrázoló pontok ábrázolására is használhatja. Az alábbi Térkép egyéni rendszerképet használ egy pont megjelenítéséhez a térképen. A pont a következő szélességben jelenik meg: 51,5 és hosszúság:-0,2. A horgony kitolja a jelölő pozícióját, hogy a gombostű ikon a térképen a megfelelő pozícióval legyen igazítva.

<center>

![sárga gombostű-rendszerkép](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_gombostű. png</center>

**Előtte: Google Maps**

Hozzon létre egy egyéni jelölőt egy olyan `Icon` objektum megadásával, amely a rendszerképet tartalmazó `url` tartalmazza. `anchor` pontot kell megadnia a gombostű-rendszerkép pontjának a térképen való igazításához. A Google Maps-ben a horgony értéke a rendszerkép bal felső sarkához képest relatív.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google Maps egyéni jelölő](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Utána: Azure Maps HTML-jelölők használatával**

HTML-jelölők testreszabásához adjon át egy HTML-`string`, vagy `HTMLElement` a jelölő `htmlContent` beállítására. Használja a `anchor` lehetőséget a jelölő relatív helyzetének megadásához, a pozíció koordinátáihoz viszonyítva. Rendeljen hozzá kilenc megadott hivatkozási pontot az `anchor` lehetőséghez. Ezek a meghatározott pontok a következők: "Center", "Top", "bottom", "Left", "Right", "top-left", "top-right", "bal alsó", "bottom-Right". Alapértelmezés szerint a tartalom a HTML-tartalom alsó középpontjához van rögzítve. Ahhoz, hogy könnyebb legyen áttelepíteni a kódot a Google Maps szolgáltatásból, állítsa a `anchor` "bal felső" értékre, majd használja a `pixelOffset` lehetőséget a Google Maps-ben használt eltolással. A Azure Maps eltolásai a Google Maps-ben lévő eltolások ellentétes irányába mozdulnak el. Tehát az eltolásokat szorozzuk meg mínusz eggyel.

> [!TIP]
> Ha le szeretné tiltani az alapértelmezett húzási viselkedést a Microsoft Edge-ben, akkor a HTML-tartalomhoz adja hozzá a `pointer-events:none`t, amely nem kívánt ikont jelenít meg.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps egyéni HTML-jelölő](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Utána: Azure Maps egy szimbólum réteget használva**

A Azure Maps az egyéni rendszerképeket is támogatja. Először töltse be a képet a Térkép erőforrásaiba, és rendelje hozzá egy egyedi AZONOSÍTÓhoz. Hivatkozzon a képre a szimbólum rétegben. A `offset` kapcsolóval igazíthatja a képet a térképen a megfelelő pontra. Használja a `anchor` lehetőséget a szimbólum relatív helyzetének megadásához a pozíció koordinátáihoz viszonyítva. A kilenc megadott hivatkozási pont egyikét használja. Ezek a pontok a következők: "Center", "Top", "bottom", "Left", "Right", "top-left", "top-right", "bal alsó", "bottom-Right". Alapértelmezés szerint a tartalom a HTML-tartalom alsó középpontjához van rögzítve. Ahhoz, hogy könnyebb legyen áttelepíteni a kódot a Google Maps szolgáltatásból, állítsa a `anchor` "bal felső" értékre, majd használja a `offset` lehetőséget a Google Maps-ben használt eltolással. A Azure Maps eltolásai a Google Maps-ben lévő eltolások ellentétes irányába mozdulnak el. Tehát az eltolásokat szorozzuk meg mínusz eggyel.

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

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

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

<center>

![Azure Maps egyéni ikon szimbólumának rétege](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> A speciális egyéni pontok megjelenítéséhez használjon több renderelési réteget együtt. Tegyük fel például, hogy több olyan pushpins szeretne, amelyeknek ugyanaz az ikonja a különböző színű körökben. Ahelyett, hogy az egyes színátfedésekhez hozzon létre egy csomó képet, adjon hozzá egy szimbólum réteget a buborék réteghez. A pushpins hivatkozhat ugyanarra az adatforrásra. Ez a megközelítés hatékonyabb lesz, mint a különböző rendszerképek létrehozása és karbantartása.

**További források:**

- [Adatforrás létrehozása](create-data-source-web-sdk.md)
- [Szimbólum réteg hozzáadása](map-add-pin.md)
- [HTML-jelölők hozzáadása](map-add-custom-html.md)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)
- [Szimbólum réteg ikonjának beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Szimbólum réteg szövege beállítás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-jelölő osztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML-jelölő beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Vonallánc hozzáadása

A Térkép vonalának vagy elérési útjának ábrázolásához használjon vonalláncokat. Hozzunk létre egy szaggatott vonalláncot a térképen.

**Előtte: Google Maps**

A vonallánc osztály a lehetőségek készletét fogadja el. Adja át a koordináták tömbjét a vonallánc `path` lehetőségében.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps-vonallánc](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Utána: Azure Maps**

A vonalláncok neve `LineString` vagy `MultiLineString` objektum. Ezek az objektumok hozzáadhatók egy adatforráshoz, és egy sor réteg használatával is megjeleníthető. Adja hozzá `LineString` egy adatforráshoz, majd adja hozzá az adatforrást egy `LineLayer`hoz a megjelenítéséhez.

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

<center>

![Azure Maps vonallánc](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**További források:**

- [Sorok hozzáadása a térképhez](map-add-line-layer.md)
- [Vonal rétegének beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Sokszög hozzáadása

A Azure Maps és a Google Maps hasonló támogatást biztosít a sokszögekhez. A sokszögek egy területnek a térképen való ábrázolására szolgálnak. Az alábbi példák bemutatják, hogyan hozhat létre olyan sokszöget, amely a Térkép középpontjának koordinátái alapján alkotja a háromszöget.

**Előtte: Google Maps**

A sokszög osztály a lehetőségek készletét fogadja el. Adja át a koordináták tömbjét a sokszög `paths` lehetőségének.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps-sokszög](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Utána: Azure Maps**

Adjon hozzá egy `Polygon` vagy egy `MultiPolygon` objektumot egy adatforráshoz. Az objektum leképezése rétegek használatával. Sokszög területének megjelenítése sokszög réteg használatával És a sokszög körvonalait egy vonal réteggel jeleníti meg.

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

<center>

![Azure Maps sokszög](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**További források:**

- [Sokszög hozzáadása a térképhez](map-add-shape.md)
- [Kör hozzáadása a térképhez](map-add-shape.md#add-a-circle-to-the-map)
- [Sokszög réteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Vonal rétegének beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Információs ablak megjelenítése

Az entitások további információi a térképen `google.maps.InfoWindow` osztályként jeleníthetők meg a Google Maps-ben. Azure Maps ez a funkció a `atlas.Popup` osztály használatával érhető el. A következő példákban egy jelölőt adunk hozzá a térképhez. Ha a jelölőre kattint, megjelenik egy információs ablak vagy egy előugró ablak.

**Előtte: Google Maps**

Információs ablak létrehozása a `google.maps.InfoWindow` konstruktor használatával.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps felugró ablak](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Utána: Azure Maps**

A felugró ablak használatával további információkat jeleníthet meg a helyről. Adjon át egy HTML-`string` vagy `HTMLElement` objektumot az előugró ablak `content` beállítására. Ha szeretné, az előugró ablakok bármilyen alakzattól függetlenül is megjeleníthetők. Így az előugró ablakokhoz meg kell adni `position` értéket. A `position` értékének megadása. Az előugró ablak megjelenítéséhez hívja meg a `open` metódust, és adja meg azt a `map`, amelyben a felugró ablak jelenik meg.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps előugró](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Ugyanezt megteheti egy szimbólummal, buborékmal, vonal vagy sokszög réteggel is, ha a kiválasztott réteget a Maps (Térkép) esemény kódjába továbbítja a jelölő helyett.

**További források:**

- [Felugró ablak hozzáadása](map-add-popup.md)
- [Multimédiás tartalommal rendelkező előugró ablak](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Felugró ablakok az alakzatokon](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Felugró ablak újrahasználata több PIN-kód használatával](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Felugró osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Előugró beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>GeoJSON-fájl importálása

A Google Maps támogatja a GeoJSON-adatgyűjtést és a dinamikus formázást a `google.maps.Data` osztályon keresztül. Ennek az osztálynak a funkciója sokkal többet igazít a Azure Maps adatvezérelt stílusához. Van azonban egy fontos különbség. A Google Maps szolgáltatásban megadhat egy visszahívási függvényt. Az üzleti logika a KEZELŐFELÜLETi szálban egyenként feldolgozott funkciók formázásához Azure Maps azonban a rétegek támogatják az adatvezérelt kifejezések formázási lehetőségként való megadását. Ezeket a kifejezéseket a rendszer a renderelési időben dolgozza fel külön szálon. A Azure Maps megközelítés javítja a renderelési teljesítményt. Ez az előny akkor jelenik meg, ha a nagyobb adatkészleteket gyorsan kell megjeleníteni.

Az alábbi példák betöltenek minden földrengés GeoJSON-csatornáját az USGS-ből az elmúlt hét napban. A földrengések adathalmaza méretezhető körökként jelenik meg a térképen. Az egyes körök színe és méretezése az egyes földrengések nagyságán alapul, amely az adathalmaz egyes funkcióinak `"mag"` tulajdonságában van tárolva. Ha a magnitúdó nagyobb vagy egyenlő, mint öt, akkor a kör piros lesz. Ha a háromnál nagyobb vagy egyenlő, de kevesebb, mint öt, a kör narancssárga lesz. Ha kevesebb, mint három, a kör zöld színnel jelenik meg. Az egyes körök sugara a magnitúdó exponenciálisan megszorozva a 0,1-as értékkel.

**Előtte: Google Maps**

Egyetlen visszahívási függvényt kell megadnia a `map.data.setStyle` metódusban. A visszahívási függvényen belül alkalmazza az üzleti logikát az egyes szolgáltatásokra. Töltse be az GeoJSON-csatornát a `map.data.loadGeoJson` metódussal.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Utána: Azure Maps**

A GeoJSON az Azure Maps alapadatának típusa. Importálja egy adatforrásba a `datasource.importFromUrl` metódus használatával. Buborék-réteg használata A buborék réteg az adatforrás szolgáltatásainak tulajdonságai alapján méretezhető körök megjelenítésére szolgáló funkciókat biztosít. A visszahívási függvény helyett az üzleti logikát egy kifejezésre alakítja át a rendszer, és átadja a stílus beállításait. A kifejezések határozzák meg az üzleti logika működését. A kifejezések átadhatók egy másik szálnak, és kiértékelhető a szolgáltatásra vonatkozó adattal. Több adatforrást és réteget is hozzáadhat a Azure Mapshoz, amelyek mindegyike különböző üzleti logikával rendelkezik. Ez a funkció lehetővé teszi több adatkészlet megjelenítését a térképen különböző módokon.

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
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
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

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**További források:**

- [Szimbólum réteg hozzáadása](map-add-pin.md)
- [Buborék réteg hozzáadása](map-add-bubble-layer.md)
- [A fürt pontjának adatterülete](clustering-point-data-web-sdk.md)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Jelölő fürtszolgáltatása

Ha sok adatpontot jelenít meg a térképen, a pontok átfedésben lehetnek egymással. Az átfedések miatt a Térkép zsúfoltnak tűnik, és a Térkép nehezen olvasható és használható lesz. A fürtözési pontra vonatkozó információk az egymáshoz közel lévő adatpontok egyesítésének, valamint a térképen való megjelenítésének folyamata egyetlen fürtözött adatpontként. Ahogy a felhasználó nagyítja a térképet, a fürtök az egyes adatpontokon kívülre kerülnek. A fürt adatpontjai a felhasználói élmény fokozása és a teljesítmény leképezése érdekében.

A következő példákban a kód betölt egy GeoJSON-hírcsatornát az elmúlt héten, és hozzáadja azt a térképhez. A fürtök méretezhető és színes körökként jelennek meg. A körök skálája és színe a bennük lévő pontok számától függ.

> [!NOTE]
> A Google Maps és a Azure Maps némileg eltérő csoportosítási algoritmusokat használnak. Ez azt eredményezi, hogy a fürtök pont eloszlása eltérő lehet.

**Előtte: Google Maps**

A MarkerCluster-függvénytárat használja a fürt jelölői számára. A fürt ikonjai csak a képekre korlátozódnak, amelyek neve a következő számokat tartalmazhatja. Ugyanabban a címtárban futnak.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps-fürtszolgáltatás](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Utána: Azure Maps**

Adatforrásban lévő adatforrások hozzáadása és kezelése. Az adatforrások és a rétegek összekötése, majd az adathalmazok megjelenítése. A Azure Maps `DataSource` osztály számos fürtszolgáltatási beállítást biztosít.

- `cluster` – megadja az adatforrást a fürt pontjának.
- `clusterRadius` – a fürtben lévő, képpontban megadott sugár együtt mutat.
- `clusterMaxZoom` – a fürtözés során felmerülő maximális nagyítási szint. Ha ennél a szintnél nagyobb nagyítást végez, a rendszer az összes pontot szimbólumként jeleníti meg.
- `clusterProperties` – a kiszámított egyéni tulajdonságokat definiálja az egyes fürtök összes pontjára vonatkozó kifejezésekkel, és az egyes fürtcsomópontok tulajdonságaihoz adja őket.

Ha a fürtözés engedélyezve van, akkor az adatforrás fürtözött és nem fürtözött adatpontokat küld a renderelési rétegeknek. Az adatforrás több száz ezer adatpont fürtözésére képes. A fürtözött adatpontok a következő tulajdonságokkal rendelkeznek:

| Tulajdonság neve             | Típus    | Leírás   |
|---------------------------|---------|---------------|
| `cluster`                 | logikai | Azt jelzi, hogy a szolgáltatás egy fürtöt jelöl-e. |
| `cluster_id`              | sztring  | A fürt egyedi azonosítója, amely a DataSource `getClusterExpansionZoom`, `getClusterChildren`és `getClusterLeaves` metódusokkal használható. |
| `point_count`             | szám  | A fürt által tartalmazott pontok száma.  |
| `point_count_abbreviated` | sztring  | Egy karakterlánc, amely a `point_count` értéket rövidíti, ha hosszú. (például 4 000-es lesz 4K)  |

A `DataSource` osztály a következő segítő függvényt használja a fürttel kapcsolatos további információk eléréséhez a `cluster_id`használatával.

| Módszer | Visszatérési típus | Leírás |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Megígéri, hogy&lt;Array&lt;&lt;geometriát, bármely&gt; \| alakzatot&gt;&gt; | A következő nagyítási szinten kéri le a megadott fürt gyermekeit. Ezek a gyerekek az alakzatok és alfürtek kombinációja lehet. Az alfürtek a ClusteredProperties megfelelő tulajdonságokkal rendelkező funkciók lesznek. |
| `getClusterExpansionZoom(clusterId: number)` | Ígéret&lt;szám&gt; | Kiszámítja azt a nagyítási szintet, amelynél a fürt megkezdi a kibővítését vagy szétbontását. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Megígéri, hogy&lt;Array&lt;&lt;geometriát, bármely&gt; \| alakzatot&gt;&gt; | Egy fürt összes pontjának lekérése. Állítsa be úgy a `limit`, hogy a pontok egy részhalmazát adja vissza, és használja a `offset` a pontokon keresztül. |

Amikor fürtözött adatmegjelenítést alkalmaz a térképen, általában két vagy több réteget érdemes használni. Az alábbi példa három réteget használ. A fürtök mérete alapján méretezett színes körök rajzolására szolgáló buborék réteg. Egy szimbólum réteget, amely a fürt méretét szövegként jeleníti meg. És egy második szimbólum réteget használ a nem fürtözött pontok megjelenítéséhez. A fürtözött adatmennyiség számos más módon is megjeleníthető. További információ: a [fürt pont adatai](clustering-point-data-web-sdk.md) dokumentációja.

Közvetlenül importálhatja a GeoJSON-fájlokat a `importDataFromUrl` függvény használatával a `DataSource` osztályban, Azure Maps térképen belül.

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
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

<center>

![Azure Maps fürtszolgáltatás](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**További források:**

- [Szimbólum réteg hozzáadása](map-add-pin.md)
- [Buborék réteg hozzáadása](map-add-bubble-layer.md)
- [A fürt pontjának adatterülete](clustering-point-data-web-sdk.md)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Hő-Térkép hozzáadása

A Heat Maps (más néven a pont sűrűsége) az adatvizualizáció típusa. Ezek az adatmennyiség színskáláját jelölik. Emellett gyakran használják a "gyors elérésű pontok" megjelenítését a térképen. A Heat Maps nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére.

Az alábbi példák betöltenek egy GeoJSON-hírcsatornát az elmúlt hónapban az összes földrengésről az USGS-ből, és súlyozott hő-térképként jeleníti meg őket. A `"mag"` tulajdonság a súlyozásként használható.

**Előtte: Google Maps**

Heat Map létrehozásához töltse be a "vizualizáció" könyvtárat az API-szkript URL-címéhez `&libraries=visualization` hozzáadásával. A Google Maps Heat Map rétege nem támogatja közvetlenül a GeoJSON-adatgyűjtést. Először töltse le az adatmennyiséget, és alakítsa át a súlyozott adatpontok tömbbe:

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

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps Heat Térkép](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Utána: Azure Maps**

Töltse be a GeoJSON az adatforrásba, és kapcsolja össze az adatforrást egy Heat Map-réteggel. A súlyozáshoz használt tulajdonságot kifejezés használatával lehet átadni a `weight` kapcsolónak. A `DataSource` osztályban található `importDataFromUrl` függvénnyel közvetlenül importálhatja a GeoJSON-Azure Maps.

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
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                    weight: ['get', 'mag'],
                    intensity: 0.005,
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

<center>

![Azure Maps Heat Térkép](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**További források:**

- [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md)
- [Heat Térkép réteg osztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Hő-Térkép réteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Csempe rétegének átfedése

A Azure Maps csempéi a Google Maps-ben képátfedésként ismertek. A csempe rétegek lehetővé teszik olyan nagyméretű képek átfedését, amelyeket kisebb csempés képekre bontottak, amelyek a Maps csempe rendszeréhez igazodnak. Ez a megközelítés általában nagyméretű képek vagy nagyméretű adathalmazok átfedésére szolgál.

Az alábbi példák egy időjárási radar csempe réteget fedik fel az Iowa State University Iowa környezeti Mesonet.

**Előtte: Google Maps**

A Google Maps szolgáltatásban a csempe rétegek a `google.maps.ImageMapType` osztály használatával hozhatók létre.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Maps csempe réteg](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Utána: Azure Maps**

Vegyen fel egy csempe réteget a térképhez hasonlóan, mint bármely más réteget. Használjon olyan formázott URL-címet, amely x, y és nagyítási helyőrzőket tartalmaz. `{x}`, `{y}``{z}`, hogy tájékoztassa a réteget a csempék eléréséről. Azure Maps csempe rétegek `{quadkey}`, `{bbox-epsg-3857}`és `{subdomain}` helyőrzőket is támogatnak.

> [!TIP]
> Azure Maps rétegekben könnyen megjeleníthető más rétegek alatt, beleértve az alapszintű leképezési rétegeket is. Gyakran érdemes a Térkép feliratai alatt megjeleníteni a csempe rétegeit, hogy azok könnyen olvashatók legyenek. A `map.layers.add` metódus egy második paramétert vesz igénybe, amely annak a rétegnek az azonosítója, amelyben az alábbi új réteget be kell szúrni. Ha a Térkép feliratai alá szúr be egy csempe réteget, használja a következő kódot: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps csempe rétege](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> A csempe-kéréseket a Térkép `transformRequest` lehetőségével lehet rögzíteni. Ez lehetővé teszi, hogy szükség esetén módosítsa vagy adja hozzá a fejléceket a kéréshez.

**További források:**

- [Csempe-rétegek hozzáadása](map-add-tile-layer.md)
- [Csempe réteg osztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Csempe rétegének beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Forgalom megjelenítése

A forgalmi adatok az Azure és a Google Maps is felhelyezhetők.

**Előtte: Google Maps**

Átfedő forgalmi adatok a térképen a forgalmi réteg használatával.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps Traffic](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Utána: Azure Maps**

Azure Maps számos különböző lehetőséget biztosít a forgalom megjelenítéséhez. Az adatforgalmi incidensek, például a közúti bezárások és a balesetek ikonjainak megjelenítése a térképen. Átfedésben lévő forgalom és színkódolt utak a térképen. A színek a feladott sebesség korlátja alapján módosíthatók, a normál várható késéshez képest, vagy abszolút késleltetéssel. Az incidensek Azure Maps percenként frissülnek, és két percenként frissülnek az adatforgalom.

Rendelje hozzá a kívánt értékeket `setTraffic` beállításokhoz.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps forgalom](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Ha a Azure Maps egyik forgalmi ikonjára kattint, további információk jelennek meg a felugró ablakban.

<center>

![Azure Maps forgalmi incidens](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**További források:**

- [Forgalom megjelenítése a térképen](map-show-traffic.md)
- [Forgalom átfedési beállításai](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Vízszintes átfedés hozzáadása

Az Azure és a Google Maps is támogatja a georeferens-lemezképek átfedését a térképen. A georeferens-képek mozgatása és méretezése a Térkép pásztázása és nagyítása révén. A Google Maps-ben a georeferens rendszerképeket nevezzük, miközben Azure Maps képrétegként hivatkoznak rájuk. Kiválóan használhatók alapszintű csomagok létrehozásához, a régi térképek és képek átfedéséhez egy droneből.

**Előtte: Google Maps**

Itt adhatja meg az átfedő képre mutató URL-címet, valamint egy határoló mezőt, amely a képet a térképen köti össze. Ez a példa egy [Newark New Jersey](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) térképi képét fedi fel a térképen a 1922-es számon.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

A kód böngészőben való futtatásakor megjelenik egy Térkép, amely a következő képhez hasonlóan néz ki:

<center>

![Google Maps képátfedés](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Utána: Azure Maps**

Használja a `atlas.layer.ImageLayer` osztályt a georeferens képek átfedéséhez. Ehhez az osztályhoz a rendszerkép egy URL-címe és egy koordináták halmaza szükséges a rendszerkép négy sarkánál. A rendszerképet ugyanazon a tartományon kell tárolni, vagy engedélyezve kell lennie a CORs.

> [!TIP]
> Ha csak Észak-, Dél-, Kelet-, nyugati és rotációs információkkal rendelkezik, és nem rendelkezik koordinátákkal a rendszerkép minden sarkához, használhatja a statikus [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) módszert.

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

<center>

![Azure Maps-rendszerkép átfedésben](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**További források:**

- [Rendszerkép átfedése](map-add-image-layer.md)
- [Képréteg osztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>További példák a kódokra

Az alábbiakban néhány további, a Google Maps áttelepítéssel kapcsolatos kódrészletet találhat:

- [Rajzolási eszközök](map-add-drawing-toolbar.md)
- [Leképezés korlátozása két ujjal való Pásztázásra](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Görgős nagyítás korlátozása](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Teljes képernyős vezérlő létrehozása](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Services**

- [A Azure Maps Services modul használata](how-to-use-services-module.md)
- [Hasznos helyek keresése](map-search-location.md)
- [Adatok lekérése egy koordinátaből (fordított geocode)](map-get-information-from-coordinate.md)
- [Irányok megjelenítése a-tól B-be](map-route.md)
- [Automatikus javaslat keresése a JQuery felhasználói felülettel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps v3 – Azure Maps web SDK osztály-hozzárendelés

A következő függelék a leggyakrabban használt osztályok kereszthivatkozásait tartalmazza a Google Maps v3 és az azzal egyenértékű Azure Maps web SDK-ban.

### <a name="core-classes"></a>Alapvető osztályok

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlaszi. Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlaszi. Lakosság](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlaszi. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. Rea. position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlaszi. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlaszi. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Átfedési osztályok

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlaszi. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. repoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlaszi. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlaszi. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlaszi. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [az Atlas. Rea. Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlaszi. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlaszi. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlaszi. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Lásd: [kör hozzáadása a térképhez](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlaszi. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlaszi. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlaszi. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Szolgáltatási osztályok

A Azure Maps web SDK tartalmaz egy Services modult, amely külön tölthető be. Ez a modul a Azure Maps REST-szolgáltatásokat egy webes API-val csomagolja, és JavaScript-, írógéppel-és Node. js-alkalmazásokban is használható.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlaszi. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlaszi. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlaszi. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Kódtárak

A tárak további funkciókat is hozzáadhatnak a térképhez. Ezen könyvtárak többsége a Azure Maps alapvető SDK-ban található. Íme néhány egyenértékű osztály, amelyet a Google Maps-kódtárak helyett használhat

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Rajzolási könyvtár       | [Rajzeszközök modul](set-drawing-options.md) |
| Geometriai könyvtár      | [Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Vizualizációs könyvtár | [Heat Térkép réteg](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps web SDK-ról.

> [!div class="nextstepaction"]
> [A térkép vezérlőelem használata](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [A szolgáltatások modul használata](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [A rajzolási eszközök modul használata](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

