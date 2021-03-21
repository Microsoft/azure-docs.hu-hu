---
title: Kapcsolódás a web Feature Service (WFS) szolgáltatáshoz | Microsoft Azure térképek
description: Ismerje meg, hogyan csatlakozhat egy WFS szolgáltatáshoz, majd hogyan kérdezheti le a WFS szolgáltatást a Azure Maps web SDK és a térbeli IO modul használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891449"
---
# <a name="connect-to-a-wfs-service"></a>Kapcsolódás WFS-szolgáltatáshoz

A webszolgáltatások (WFS) egy webszolgáltatás, amely a Nyílt térinformatikai konzorcium (OGC) által definiált, szabványosított API-k segítségével kérdezi le a térbeli adatainak lekérdezését. A `WfsClient` térbeli IO modulban található osztály lehetővé teszi a fejlesztők számára a WFS-szolgáltatásokhoz való kapcsolódást és a szolgáltatás adatainak lekérdezését.

Az osztály a következő funkciókat támogatja `WfsClient` :

- Támogatott verziók: `1.0.0` , `1.1.0` és `2.0.0`
- Támogatott szűrési operátorok: bináris összehasonlítás, logika, matematikai, érték és `bbox` .
- A kérelmek csak a használatával készültek `HTTP GET` .
- Támogatott műveletek:

    | Művelet | Description |
    | :-- | :-- |
    | GetCapabilities | Metaadat-dokumentum létrehozása érvényes WFS-műveletekkel és-paraméterekkel |
    | GetFeature | Egy adatforrás funkcióinak kiválasztását adja vissza. |
    | DescribeFeatureType | A támogatott szolgáltatások típusait adja vissza. |

## <a name="using-the-wfs-client"></a>A WFS-ügyfél használata

A `atlas.io.ogc.WfsClient` térbeli IO-modul osztálya megkönnyíti a WFS-szolgáltatás lekérdezését és a válaszok GeoJSON objektummá alakítását. Ezt a GeoJSON-objektumot ezután más leképezési célokra is használhatja.

A következő kód lekérdez egy WFS szolgáltatást, és megjeleníti a visszaadott funkciókat a térképen.

<br/>

<iframe height='700' scrolling='no' title='Egyszerű WFS példa' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>egyszerű WFS példáját</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Támogatott szűrők

A WFS standard specifikációja OGC-szűrőket használ. Az alábbi szűrőket a WFS-ügyfél támogatja, feltéve, hogy a meghívott szolgáltatás is támogatja ezeket a szűrőket. Egyéni szűrő sztringek adhatók át az `CustomFilter` osztályba.

**Logikai operátorok**

- `And`
- `Or`
- `Not`

**Érték operátorok**

- `GmlObjectId`
- `ResourceId`

**Matematikai operátorok**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Összehasonlító operátorok**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

A következő kód a különböző szűrők használatát mutatja be a WFS-ügyféllel.

<br/>

<iframe height='500' scrolling='no' title= 'Példák a WFS szűrésére' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS-szűrési példákat</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS Service Explorer

A következő kód a WFS-ügyfelet használja a WFS szolgáltatások megismerésére. Válasszon egy tulajdonság típusú réteget a szolgáltatásban, és tekintse meg a társított jelmagyarázatot.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS Service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS Service explorert</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>-on.
</iframe>

A nem CORS engedélyezett végpontokon üzemeltetett WFS-szolgáltatások eléréséhez a CORS-kompatibilis proxy szolgáltatás átadható a `proxyService` WFS-ügyfél számára az alábbi ábrán látható módon. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Alapvető műveletek előnyeinek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátumokra vonatkozó részletek](spatial-io-supported-data-format-details.md)