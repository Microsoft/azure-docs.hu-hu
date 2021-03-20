---
title: Térbeli információk olvasása és írása | Microsoft Azure térképek
description: Megtudhatja, hogyan olvashatja és írhatja az információkat a Azure Maps web SDK által biztosított térbeli IO-modul használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891329"
---
# <a name="read-and-write-spatial-data"></a>Térbeli adatok beolvasása és írása

Az alábbi táblázat azokat a térbeli fájlformátumokat sorolja fel, amelyek a térbeli IO-modullal végzett műveletek olvasásához és írásához támogatottak.

| Adatformátum       | Olvasás | Írás |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Térbeli CSV       | ✓  |  ✓  |
| Well-Known szöveg   | ✓  |  ✓  |

Ezek a következő fejezetek a térbeli IO modul használatával ismertetik az összes olyan eszközt, amely a térbeli információk olvasására és írására használható.

## <a name="read-spatial-data"></a>Térbeli információk olvasása

A `atlas.io.read` függvény az általános térbeli adatformátumok, például a KML-, GPX-, GeoRSS-, GeoJSON-és CSV-fájlok a térbeli adattal való olvasására szolgáló fő függvény. Ez a függvény a formátumok tömörített verzióit is beolvashatja zip-fájlként vagy KMZ-fájlként. A KMZ fájlformátum a KML tömörített verziója, amely olyan objektumokat is tartalmazhat, mint például a képek. Azt is megteheti, hogy az olvasási függvény olyan URL-címet is igénybe vehet, amely az adott formátumú fájlokra mutat. Az URL-címeket egy CORS-kompatibilis végponton kell tárolni, vagy az olvasási beállításokban proxy szolgáltatást kell megadni. A proxy szolgáltatás a nem CORS engedélyezett tartományokban lévő erőforrások betöltésére szolgál. Az olvasási függvény egy ígéretet ad vissza, amely a képikonokat hozzáadja a térképhez, és aszinkron módon dolgozza fel az adatfeldolgozást a felhasználói felületi szálra gyakorolt hatás csökkentése érdekében.

Tömörített fájl (zip vagy KMZ) beolvasásakor a rendszer kibontja és beolvassa az első érvényes fájlt. Például a doc. KML vagy más érvényes kiterjesztésű fájl, például:. kml,. XML, geojson,. JSON,. csv,. TSV vagy. txt. Ezután a KML-és GeoRSS-fájlokban hivatkozott lemezképek előre be vannak töltve, hogy azok elérhetők legyenek. A nem elérhető képadatok egy másik tartalék képet is betölthetnek, vagy eltávolíthatók a stílusokból. A KMZ-fájlokból kinyert rendszerképeket a rendszer az adaturi-ra konvertálja.

Az olvasási függvény eredménye egy `SpatialDataSet` objektum. Ez az objektum kiterjeszti a GeoJSON FeatureCollection osztályt. Könnyedén átadható a `DataSource` szolgáltatásnak, hogy egy térképen jelenítse meg a funkcióit. A `SpatialDataSet` nem csak a szolgáltatással kapcsolatos információkat tartalmaz, de tartalmazhat KML-fedéseket, feldolgozási mérőszámokat és egyéb részleteket is, az alábbi táblázatban leírtak szerint.

| Tulajdonság neve | Típus | Description | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Az adatkészletben lévő összes adat határoló mezője. |
| `features` | `Feature[]` | Az adatkészleten belüli GeoJSON-funkciók. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | A KML-GroundOverlays tömbje. |
| `icons` | Rekord &lt; karakterlánca, karakterlánc&gt; | Ikon URL-címek készlete Kulcs = ikon neve, érték = URL. |
| properties | bármelyik | A térbeli adatkészletek dokumentum szintjén megadott tulajdonság-információk. |
| `stats` | `SpatialDataSetStats` | A térbeli adatkészletek tartalmával és feldolgozási idejével kapcsolatos statisztikák. |
| `type` | `'FeatureCollection'` | Írásvédett GeoJSON-típus értéke. |

## <a name="examples-of-reading-spatial-data"></a>Példák a térbeli információk olvasására

A következő kód bemutatja, hogyan olvashatja el a térbeli adatkészleteket, és hogyan jelenítheti meg a térképen a `SimpleDataLayer` osztály használatával. A kód egy GPX-fájlt használ, amelyet egy URL-cím mutat.

<br/>

<iframe height='500' scrolling='no' title='Térbeli adatkészletek egyszerű betöltése' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az CodePen-on Azure Maps () segítségével egyszerűen megtekintheti a toll- <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>betöltési térbeli adathalmazt</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

A következő kód bemutatja, hogyan olvashatja és töltheti be a KML-t és a KMZ-t a térképre. A KML tartalmazhat olyan alapátfedéseket, amelyek a vagy a formában lesznek `ImageLyaer` `OgcMapLayer` . Ezeket a átfedéseket a szolgáltatásokból külön kell felvenni a térképen. Emellett, ha az adatkészlethez egyéni ikonok tartoznak, ezeket az ikonokat a szolgáltatások betöltése előtt be kell tölteni a Maps-erőforrásokba.

<br/>

<iframe height='500' scrolling='no' title='A KML betöltése a térképen' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A CodePen-on található Azure Maps () segítségével megtekintheti a KML-t a <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>térképre</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Opcionálisan megadhat egy proxy szolgáltatást a tartományok közötti olyan adategységek eléréséhez, amelyekhez nem engedélyezett a CORS. Az olvasási függvény megpróbál hozzáférni egy másik tartományban lévő fájlokhoz a CORS használatával. Miután az első alkalommal nem fér hozzá egy másik tartományhoz tartozó erőforráshoz a CORS használatával, csak további fájlokat fog igényelni, ha van megadva proxy szolgáltatás. Az olvasási függvény hozzáfűzi a fájl URL-címét a megadott proxy URL-cím végéhez. Ez a kódrészlet azt mutatja be, hogyan lehet átadni egy proxy szolgáltatást az olvasási függvénynek:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Az alábbi bemutató bemutatja, hogyan olvashat egy tagolt fájlt, és hogyan jelenítheti meg a térképen. Ebben az esetben a kód olyan CSV-fájlt használ, amely térbeli adatoszlopokat tartalmaz.

<br/>

<iframe height='500' scrolling='no' title='Tagolt fájl hozzáadása' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on lévő Azure Maps () által <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>határolt fájl hozzáadásával</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="write-spatial-data"></a>Térbeli adatainak írása

A térbeli IO modulban két fő írási függvény található. A `atlas.io.write` függvény létrehoz egy karakterláncot, míg a `atlas.io.writeCompressed` függvény tömörített zip-fájlt hoz létre. A tömörített ZIP-fájl egy szöveges fájlt tartalmaz, amelyben a térbeli érték szerepel. Mindkét függvény egy ígéretet ad vissza az adatok fájlba való felvételéhez. És mindkét esetben a következő adatok bármelyikét írhatják:,,,, `SpatialDataSet` `DataSource` `ImageLayer` `OgcMapLayer` szolgáltatások gyűjteménye, funkció, geometria vagy az adattípusok bármely kombinációjának tömbje. Ha függvények használatával ír, megadhatja a kívánt fájlformátumot. Ha nincs megadva a fájl formátuma, a rendszer a KML-ként írja be az adatfájlokat.

Az alábbi eszköz bemutatja a függvénnyel használható írási lehetőségek többségét `atlas.io.write` .

<br/>

<iframe height='700' scrolling='no' title='Térbeli adatírási beállítások' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>térbeli adatainak írási beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Példa térbeli adatainak írására

A következő minta lehetővé teszi a térbeli fájlok húzását, majd a térképen való betöltését. A térképről exportálhatja a GeoJSON-adatait, és a támogatott térbeli adatformátumok egyikében megírhatja karakterláncként vagy tömörített fájlként is.

<br/>

<iframe height='700' scrolling='no' title='Térbeli fájlok húzása a térképre' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>térbeli fájlok rajzsablonról</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcionálisan megadhat egy proxy szolgáltatást a tartományok közötti olyan adategységek eléréséhez, amelyekhez nem engedélyezett a CORS. Ez a kódrészlet egy proxy szolgáltatás beépítését mutatja be:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Well-Known szöveg olvasása és írása (WKT)

A [Well-known Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) egy nyílt TÉRINFORMATIKAI konzorcium (OGC) szabvány, amely a térbeli geometriák szövegként való ábrázolására szolgál. Számos térinformatikai rendszer támogatja a WKT, például az Azure SQL és az Azure PostgreSQL használatát a PostGIS beépülő modullal. A legtöbb OGC-szabványhoz hasonlóan a koordináták "hosszúsági szélesség" formátumban vannak formázva az "x y" konvencióval való igazításhoz. Például a hosszúság-110 és a szélesség 45 pont `POINT(-110 45)` a WKT formátum használatával írható.

A függvény használatával a jól ismert szöveg olvasható, `atlas.io.ogc.WKT.read` és a függvény használatával írható `atlas.io.ogc.WKT.write` .

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Példák Well-Known szöveg olvasására és írására (WKT)

A következő kód bemutatja, hogyan olvashatja el a jól ismert szöveges karakterláncot `POINT(-122.34009 47.60995)` , és hogyan jelenítheti meg a térképen a buborék réteg használatával.

<br/>

<iframe height='500' scrolling='no' title='Well-Known szöveg olvasása' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on Azure Maps () <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Well-Known szöveggel</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

A következő kód a jól ismert szövegek olvasását és írását mutatja be előre és vissza.

<br/>

<iframe height='700' scrolling='no' title='Well-Known szöveg olvasása és írása' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a tollat <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>olvasás és írás Well-Known szöveg</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>GML olvasása és írása

A GML egy térbeli XML-fájl specifikációja, amelyet gyakran használnak más XML-specifikációk kiterjesztésére. A GeoJSON-alapú adat a függvény használatával XML-ként és GML-címkékkel is írható `atlas.io.core.GmlWriter.write` . A GML tartalmazó XML-fájl olvasható a függvény használatával `atlas.io.core.GmlReader.read` . Az olvasási függvénynek két lehetősége van:

- A `isAxisOrderLonLat` "szélesség, hosszúság" vagy "hosszúság, szélesség" koordinátáinak tengelyes sorrendje eltérő lehet az adathalmazok között, és nem mindig megfelelően van definiálva. Alapértelmezés szerint a GML-olvasó a "szélesség, hosszúság" értékként beolvassa a koordináta-adatokat, de a beállítás True értékre állítása "hosszúsági fok".
- Ez a beállítás a `propertyTypes` kulcs értékének keresési táblázata, ahol a kulcs az adathalmaz egyik tulajdonságának a neve. Az érték az az Objektumtípus, amelybe a rendszer az elemzés során átadja az értéket. A támogatott típusú értékek a következők: `string` ,, `number` `boolean` és  `date` . Ha egy tulajdonság nem szerepel a keresési táblában, vagy a típus nincs definiálva, a tulajdonság karakterláncként lesz értelmezve.

A `atlas.io.read` függvény alapértelmezés szerint a `atlas.io.core.GmlReader.read` függvényt fogja észlelni, amikor azt észleli, hogy a bemeneti adatok XML-formátumúak, de az adatok nem a további támogatási térbeli XML-formátumok egyike.

A a `GmlReader` következő srid azonosítókat egyikével rendelkező koordinátákat elemzi:

- EPSG: 4326 (előnyben részesített)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, EPSG: 4308, EPSG: 4230, EPSG: 4272, EPSG: 4271, EPSG: 4267, EPSG: 4608, EPSG: 4674, valószínűleg kis mennyiségű hibával.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="more-resources"></a>További erőforrások

További információ a cikkben használt osztályokról és módszerekről:

[atlas.io statikus függvények](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[Atlas. IO. OGC. WKT függvények](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Kapcsolódás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

[Alapvető műveletek előnyeinek kihasználása](spatial-io-core-operations.md)

[Támogatott adatformátumokra vonatkozó részletek](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

[OGC-térképréteg hozzáadása](spatial-io-add-ogc-map-layer.md)