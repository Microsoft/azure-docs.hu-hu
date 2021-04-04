---
title: Nyílt térinformatikai konzorcium (OGC) Térkép réteg hozzáadása | Microsoft Azure térképek
description: Megtudhatja, hogyan fedi le egy OGC-Térkép réteget a térképen, és hogyan használhatja a OgcMapLayer osztály különböző lehetőségeit.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891665"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Térkép réteg hozzáadása a Nyílt térinformatikai konzorciumból (OGC)

Az `atlas.layer.OgcMapLayer` osztály a térképeken a web Map Services (WMS) képi és a web Map csempe-(WMTS-) képek ábrázolását is elvégezheti. A WMS egy szabványos protokoll, amelyet a OGC fejlesztett ki a georeferens-leképezési lemezképek interneten keresztüli kiszolgálásához. A rendszerkép georeferencing a képek földrajzi helyre való társításának folyamatai. A WMTS a OGC által fejlesztett szabványos protokoll is. A szolgáltatás az előre megjelenített és a georeferens Térkép csempék kiszolgálására szolgál.

A következő részekben a osztály által támogatott webszolgáltatás-funkciók szerepelnek `OgcMapLayer` .

**Web Map szolgáltatás (WMS)**

- Támogatott verziók: `1.0.0` , `1.1.0` , `1.1.1` és `1.3.0`
- A szolgáltatásnak támogatnia kell a `EPSG:3857` kivetítési rendszer vagy az újravetítések kezelését.
- A GetFeatureInfo használatához a szolgáltatásnak támogatnia `EPSG:4326` vagy kezelnie kell a kivetítéseket. 
- Támogatott műveletek:

    | Művelet | Leírás |
    | :-- | :-- |
    | GetCapabilities | A szolgáltatás metaadatainak beolvasása a támogatott funkciókkal |
    | GetMap | Egy adott régióhoz tartozó térképi rendszerkép lekérése |
    | GetFeatureInfo | `feature_info`A szolgáltatás alapjául szolgáló adatokat tartalmazó lekérések |

**Web Map csempe szolgáltatás (WMTS)**

- Támogatott verziók: `1.0.0`
- A csempének szögletesnek kell lennie, például: `TileWidth == TileHeight` .
- A CRS támogatott: `EPSG:3857` vagy `GoogleMapsCompatible` 
- A TileMatrix azonosítójának olyan egész számnak kell lennie, amely megfelel a Térkép nagyítási szintjének. Az Azure-térképen a nagyítási szint a és a közötti `"0"` érték `"22"` . Tehát `"0"` támogatott, de `"00"` nem támogatott.
- Támogatott műveletek:

    | Művelet | Leírás |
    | :-- | :-- |
    | GetCapabilities | A támogatott műveletek és funkciók beolvasása |
    | GetTile | Képek lekérése egy adott csempéhez |

## <a name="overlay-an-ogc-map-layer"></a>OGC-Térkép rétegének átfedése

A `url` lehet a szolgáltatás alap URL-címe vagy egy teljes URL-cím a szolgáltatás képességeinek beszerzéséhez. A megadott adatoktól függően előfordulhat, hogy a WFS-ügyfél számos szabványos URL-formátumot próbál megállapítani a szolgáltatás kezdeti eléréséhez.

A következő kód bemutatja, hogyan fedi le az OGC Térkép rétegét.

<br/>

<iframe height='700' scrolling='no' title='Példa OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC Térkép rétegét</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>OGC Térkép rétegének beállításai

Az alábbi minta bemutatja a különböző OGC-Térkép réteg beállításait. A kód toll szerkesztéséhez kattintson a jobb felső sarokban található kód toll gombra.

<br/>

<iframe height='700' scrolling='no' title='OGC Térkép rétegének beállításai' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC</a> Azure Maps () elemét a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC web Map Service Explorer

Az alábbi eszköz a webes térképi szolgáltatásokból (WMS) és a web Map csempe-szolgáltatásokból (WMTS) származó képeket fedi fel rétegként. Megadhatja, hogy a szolgáltatás mely rétegeit jelenítse meg a térképen. Ezen rétegek társított jelmagyarázatait is megtekintheti.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC web Map Service Explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC web Map Service explorert</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>-on.
</iframe>

A Térkép beállításait a proxy szolgáltatás használatára is megadhatja. A proxy szolgáltatás lehetővé teszi az olyan tartományokban tárolt erőforrások betöltését, amelyeken nincs engedélyezve a CORS.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Tekintse meg az alábbi cikkeket, amelyek tartalmazzák a térképekhez felvehető mintakód-mintákat:

> [!div class="nextstepaction"]
> [Kapcsolódás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alapvető műveletek előnyeinek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátumokra vonatkozó részletek](spatial-io-supported-data-format-details.md)