---
title: A Azure Maps Indoor Maps modul használata a Microsoft Creator Services (előzetes verzió) szolgáltatással
description: Megtudhatja, hogyan használhatja az Microsoft Azure Maps beltéri térképek modult a térképek megjelenítéséhez a modul JavaScript-kódtárainak beágyazásával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e527cf5fa6a7caaeaf56ea19d684dd0830d5ca8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708679"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>A Azure Maps Indoor Maps modul használata

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Maps web SDK tartalmazza a *Azure Maps beltéri* modult. A  *Azure Maps beltéri* modul lehetővé teszi a Azure Maps Creator Services (előzetes verzió) szolgáltatásban létrehozott beltéri térképek megjelenítését 

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Létrehozó (előzetes) erőforrás létrehozása](how-to-manage-creator.md)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.
4. A `tilesetId` és a a beszerzése `statesetId` a [beltéri térképek létrehozásával foglalkozó oktatóanyag](tutorial-creator-indoor-maps.md)elvégzésével.
 Ezeket az azonosítókat kell használnia a beltéri leképezések megjelenítéséhez a Azure Maps Indoor Maps modullal.

## <a name="embed-the-indoor-maps-module"></a>A beltéri térképek modul beágyazása

A *Azure Maps beltéri* modult kétféleképpen telepítheti és ágyazhatja be.

Ha a *Azure Maps beltéri* modul globálisan üzemeltetett Azure Content Delivery Network verzióját szeretné használni, a `<head>` HTML-fájl elemében a következő JavaScript-és stíluslap-hivatkozásokra kell hivatkoznia:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Vagy letöltheti a *Azure Maps beltéri* modult is. A *Azure Maps beltéri* modul tartalmaz egy ügyféloldali kódtárat a Azure Maps szolgáltatások eléréséhez. Az alábbi lépéseket követve telepítheti és betöltheti a *beltéri* modult a webalkalmazásba.  
  
  1. Telepítse az [Azure-Maps-Indoor csomagot](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. A HTML-fájl elemében a *Azure Maps beltéri* modul JavaScript-és stíluslapjának hivatkozása `<head>` :

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>A Térkép objektumának példánya

Először hozzon létre egy *Térkép objektumot*. A *map objektum* a következő lépésben lesz használva a *Indoor Manager* -objektum létrehozásához.  Az alábbi kód bemutatja, hogyan hozhatja létre a *Térkép objektumot*:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13203, 47.63645],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>A Indoor Manager példányának példányai

A csempék beltéri tilesets és térképes stílusának betöltéséhez a *beltéri kezelőt* kell létrehoznia. Hozza létre a *Indoor Managert* a *Térkép objektum* és a megfelelő hozzáadásával `tilesetId` . Ha támogatni szeretné a [dinamikus leképezések stílusát](indoor-map-dynamic-styling.md), meg kell adnia a következőt: `statesetId` . A `statesetId` változó neve megkülönbözteti a kis-és nagybetűket. A kódnak a JavaScripthez hasonlóan kell lennie.

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});
```

Az Ön által megadott állapotadatok lekérdezésének engedélyezéséhez meg kell adnia a `statesetId` és a hívást `indoorManager.setDynamicStyling(true)` . Az állapotadatok lekérdezése lehetővé teszi dinamikus tulajdonságok *vagy állapotok* állapotának dinamikus frissítését. Például egy olyan szolgáltatás, mint a szoba, a dinamikus tulajdonság (*állapot*) nevű lehet `occupancy` . Előfordulhat, hogy az alkalmazás a vizualizáción belüli változást tükröző *állapotra* vonatkozó módosításokat kíván lekérdezni. Az alábbi kód bemutatja, hogyan engedélyezheti az állapot-lekérdezéseket:

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Beltéri szintű választó vezérlése

 A *beltéri szintű választó* vezérlőelem lehetővé teszi a megjelenített Térkép szintjének módosítását. A beltéri *szintű választó* vezérlését a *beltéri Manager* használatával is inicializálhatja. Itt látható a szint Control választó inicializálására szolgáló kód:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Beltéri események

 A *Azure Maps beltéri* modul támogatja az objektum-események *leképezését* . A *Térkép objektum* eseményeinek figyelői meghívásakor megtörténik a szint vagy a létesítmény megváltozása. Ha egy szint vagy egy létesítmény megváltozásakor kódot szeretne futtatni, helyezze a kódot az esemény-figyelőbe. Az alábbi kód azt mutatja be, hogyan adhatók hozzá esemény-figyelők a *Térkép objektumhoz*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

A `eventData` változó a vagy az eseményt meghívó szintre vagy lehetőségre vonatkozó információkat tárol `levelchanged` `facilitychanged` . Ha egy szint megváltozik, az `eventData` objektum a `facilityId` , az új és az `levelNumber` egyéb metaadatokat fogja tartalmazni. A létesítmény változásakor az `eventData` objektum az új `facilityId` , az új `levelNumber` és az egyéb metaadatokat fogja tartalmazni.

## <a name="example-use-the-indoor-maps-module"></a>Példa: a beltéri térképek modul használata

Ebből a példából megtudhatja, hogyan használhatja a *Azure Maps beltéri* modult a webalkalmazásban. Bár a példa korlátozott a hatókörben, ismerteti a *Azure Maps beltéri* modul használatának megkezdéséhez szükséges alapismereteket. A teljes HTML-kód a következő lépések alá esik.

1. A *Azure Maps beltéri* modul telepítéséhez használja az Azure Content Delivery Network [lehetőséget](#embed-the-indoor-maps-module) .

2. Új HTML-fájl létrehozása

3. A HTML-fejlécben hivatkozzon a *Azure Maps beltéri* modul JavaScript-és stíluslap-stílusára.

4. *Leképezési objektum* inicializálása. A *map objektum* a következő beállításokat támogatja:
    - `Subscription key` a Azure Maps elsődleges előfizetési kulcs.
    - `center` meghatározza a beltéri Térkép középpontjának földrajzi szélességét és hosszúságát. Adjon meg egy értéket, `center` Ha nem kíván értéket megadni a következőhöz: `bounds` . A formátumnak a következőképpen kell megjelennie `center` : [-122,13315, 47,63637].
    - `bounds` az a legkisebb téglalap alakú alakzat, amely a tileset térképi adatleképezési adattárat foglalja magában. Állítsa be a értéket, `bounds` Ha nem kíván értéket beállítani `center` . A Térkép határai a [tileset List API](/rest/api/maps/tileset/listpreview)meghívásával találhatók meg. A tileset-lista API a () értéket adja vissza `bbox` , amelyet elemezni és hozzárendelni lehet a következőhöz: `bounds` . A formátumot a következőképpen kell megjelennie `bounds` : [# West, # Dél, # Kelet, # North].
    - `style` lehetővé teszi a háttér színének megadását. Fehér háttér megjelenítéséhez adja meg `style` az "üres" értéket.
    - `zoom` lehetővé teszi a Térkép minimális és maximális nagyítási szintjeinek megadását.

5. Ezután hozza létre a *Indoor Manager* modult. Rendelje hozzá a *Azure Maps beltéri* `tilesetId` és opcionálisan adja hozzá a elemet `statesetId` .

6. Hozza létre a *beltéri szintű választó* vezérlőt.

7. *Leképezési objektumhoz* tartozó esemény-figyelők hozzáadása.  

A fájlnak most az alábbi HTML-hez hasonlóan kell kinéznie.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl: levelControl, //level picker
          tilesetId: tilesetId,
          statesetId: statesetId // Optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Ha szeretné megtekinteni a beltéri térképet, töltse be azt egy webböngészőbe. Az alábbi képen láthatóhoz hasonlónak kell lennie. Ha a lépcsőház funkcióra kattint, a *szint választó* megjelenik a jobb felső sarokban.

  ![beltéri Térkép képe](media/how-to-use-indoor-module/indoor-map-graphic.png)

[Lásd: élő bemutató](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Következő lépések

Olvassa el a *Azure Maps beltéri* modulhoz kapcsolódó API-kat:

> [!div class="nextstepaction"]
> [Rajzolási csomag követelményei](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Creator (előzetes verzió) beltéri térképekhez](creator-indoor-maps.md)

További információ a térképhez tartozó további információk hozzáadásáról:

> [!div class="nextstepaction"]
> [Beltéri térképek dinamikus stílusa](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Kódminták](/samples/browse/?products=azure-maps)