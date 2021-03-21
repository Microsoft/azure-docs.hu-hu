---
title: Hő-Térkép réteg hozzáadása térképhez | Microsoft Azure térképek
description: Megtudhatja, hogyan hozhat létre hő-térképet. Tekintse meg, hogyan adhat hozzá egy Heat Map-réteget egy térképhez a Azure Maps web SDK használatával. Megtudhatja, hogyan szabhatja testre a Heat Térkép rétegeit.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b15ee7091a68f7fcc79c71877c4af28b511b84de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680149"
---
# <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

A Heat Maps (más néven a pont sűrűsége) az adatvizualizáció típusa. A rendszer az adatok sűrűségét ábrázolja egy színtartományon belül, és a "gyors elérésű pontok" megjelenítését jeleníti meg a térképen. A Heat Maps nagyszerű módja a nagy mennyiségű pontot tartalmazó adatkészletek megjelenítésének. 

Több tízezer pont megjelenítésével a szimbólumok a legtöbb térképi területre vonatkozhatnak. Ebben az esetben valószínű, hogy számos szimbólum átfedésben van. Megnehezíti az információk jobb megismerését. Ugyanakkor ha ugyanezt az adatkészletet szeretné megjeleníteni, a hő-térkép segítségével könnyen megtekintheti az egyes adatpontok sűrűségét és relatív sűrűségét.

A Heat Maps számos különböző forgatókönyvben használható, beleértve a következőket:

- **Hőmérsékleti információ**: a két adatpont közötti hőmérséklet-érték közelítését biztosítja.
- A **zaj-érzékelők adatai**: nem csak az érzékelőt tartalmazó zaj erősségét mutatja, de a távolságon keresztül is biztosítható az elszóródás. Előfordulhat, hogy a zajszint egy adott helyen nem magas. Ha a zaj lefedettségi területe több érzékelővel átfedésben van, lehetséges, hogy ez az átfedési terület magasabb zajszintet eredményezhet. Így az átfedésben lévő terület látható lesz a Heat térképen.
- **GPS-nyomkövetés**: magában foglalja a sebességét súlyozott magassági térképként, ahol az egyes adatpontok intenzitása a sebességen alapul. Ez a funkció például lehetővé teszi, hogy megtekintse a jármű sebességének helyét.

> [!TIP]
> Az adatforrásban lévő összes geometriá koordinátáit alapértelmezés szerint a térképi rétegek rétege jeleníti meg. Ha úgy szeretné korlátozni a réteget, hogy az csak a pont geometriájának funkcióit jelenítse meg, állítsa a réteg tulajdonságát a következőre: `filter` `['==', ['geometry-type'], 'Point']` . Ha azt szeretné, hogy a multipoint-funkciók is szerepeljenek, állítsa a réteg tulajdonságát a következőre: `filter` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` .

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Ha a pontok egy adatforrását szeretné megjeleníteni hő-hozzárendelésként, adja át az adatforrást az osztály egy példányának `HeatMapLayer` , és adja hozzá a térképhez.

A következő kódban minden egyes hőforrás 10 képpont sugarú, minden nagyítási szinten. Ha jobb felhasználói élményt szeretne biztosítani, a Heat Térkép a címke réteg alatt van. A feliratok jól láthatók maradnak. Az ebben a példában szereplő adatok az [USGS földrengés veszélyei programból](https://earthquake.usgs.gov/)származnak. Ez az elmúlt 30 napban történt jelentős földrengések esetében jelent meg.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Itt látható az előző kód teljes futtatási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű Heat Map-réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>egyszerű Heat Térkép réteget</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>A Heat Map-réteg testreszabása

Az előző példában a sugár-és opacitási beállítások megadásával testre szabta a Heat térképet. A Heat Map réteg számos testreszabási lehetőséget kínál, többek között a következőket:

* `radius`: Az egyes adatpontok megjelenítéséhez használandó képpont sugarat határozza meg. A sugarat rögzített számként vagy kifejezésként is beállíthatja. Egy kifejezés használatával méretezheti a sugarat a nagyítási szint alapján, és a térképen egy konzisztens térbeli terület (például egy 5 mérföldes sugár) látható.
* `color`: Megadja, hogyan legyen színezetve a Heat map. A színátmenetek a Heat Maps gyakori funkciói. A hatás egy kifejezéssel valósítható meg `interpolate` . Használhat egy kifejezést is a `step` Heat Térkép színezéséhez, a sűrűséget pedig vizuálisan kibontva olyan tartományokra, amelyek egy kontúr vagy egy radar stílusú térképhez hasonlítanak. Ezek a színpalettaek a minimális és a maximális sűrűség értékének színét határozzák meg. 

  A Heat Maps színértékeit kifejezésként adja meg az `heatmap-density` értéken. Annak a területnek a színe, amelyben nem szerepelnek az adathalmazok a "interpoláció" kifejezés 0. indexében, vagy egy "lépcsőzetes" kifejezés alapértelmezett színe. Ennek az értéknek a használatával megadhatja a háttér színét. Ez az érték gyakran átlátszó vagy félig átlátszó feketére van állítva. 
   
  Példák a színkifejezésekre:

  | Interpoláció színe kifejezés | Lépcsőzetes szín kifejezése | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpoláció",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineáris" \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség" \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség" \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Navy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zöld",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "piros"<br/>\] |   

- `opacity`: Azt határozza meg, hogy a Heat Térkép rétege milyen átlátszatlan vagy átlátszó legyen.
- `intensity`: Szorzót alkalmaz az egyes adatpontok súlyozására, hogy növelje a hő összesített intenzitását. Az adatpontok súlyozásával különbséget okoz, így könnyebben láthatóvá válik.
- `weight`: Alapértelmezés szerint az összes adatponthoz 1 érték tartozik, és a súlyozása egyenlő. A súlyozási beállítás szorzóként funkcionál, és beállíthatja számként vagy kifejezésként. Ha egy szám a súlyozáshoz van beállítva, akkor a rendszer az egyes adatpontokat a térképen kétszer helyezi el. Ha például a súlyozás 2, akkor a sűrűség megduplázódik. Ha a súlyozási beállítást egy számra állítja, akkor a rendszer hasonló módon jeleníti meg a hő-hozzárendelést az intenzitás lehetőség használatával. 

  Ha azonban egy kifejezést használ, az egyes adatpontok súlyozása az egyes adatpontok tulajdonságai alapján lehetséges. Tegyük fel például, hogy az egyes adatpontok földrengést jelentenek. A magnitúdó értéke az egyes földrengés-adatpontok fontos mérőszáma volt. A földrengések minden alkalommal történnek, de a legtöbb esetben alacsony a jelentősége, és a rendszer nem észlelt. Egy kifejezésben szereplő magnitúdó értékkel rendelheti hozzá a súlyozást az egyes adatpontokhoz. A súlyozás kiosztásához a magnitúdó értékének használatával jobban áttekinthető a földrengések jelentősége a hő-térképen belül.
- `source` és `source-layer` : lehetővé teszi az adatforrás frissítését.

Itt talál egy eszközt a különböző Heat Térkép réteg beállításainak teszteléséhez.

<br/>

<iframe height='700' scrolling='no' title='Hő-Térkép réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Térkép rétegének beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzisztens nagyítású Heat-Térkép

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. A Térkép nagyítása után az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. 

Egy `zoom` kifejezéssel méretezheti a sugarat az egyes nagyítási szintekhez, így az egyes adatpontok a Térkép ugyanazon fizikai területére terjednek ki. Ez a kifejezés a Heat Map réteget statikus és konzisztensvé teszi. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. 

A sugár skálázása úgy, hogy az minden nagyítási szinten megduplázódik, létrehoz egy hő-térképet, amely minden nagyítási szinten konzisztensnek tűnik. Ha alkalmazni szeretné ezt a skálázást, használja a ( `zoom` 2 `exponential interpolation` . alap) kifejezést, a minimális nagyítási szinthez pedig a képpont sugarat, a maximális nagyítási szinthez pedig a következő mintában látható méretet `2 * Math.pow(2, minZoom - maxZoom)` . A Térkép nagyításával megtekintheti, hogy a Heat Térkép hogyan méretezhető a nagyítási szinttel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzisztens nagyítású Heat-Térkép" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg az CodePen-on található, Azure Maps () által <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konzisztens, kinagyítható hő-hozzárendelést</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

> [!TIP]
> Ha engedélyezi a fürtözést az adatforráson, akkor a egymáshoz közeledő pontok fürtözött pontként vannak csoportosítva. Az egyes fürtökhöz tartozó pontok számát használhatja a Heat Map súlyozási kifejezésének. Ez jelentősen csökkentheti a megjelenítendő pontok számát. A fürt pontok számát `point_count` a pont funkció egy tulajdonsága tárolja: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Ha a fürtözési sugár csak néhány képpont, a renderelés kisebb vizualizációs különbséget eredményezne. A nagyobb RADIUS-csoportok több pontot mutatnak az egyes fürtökre, és javítják a hő teljesítményét.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)