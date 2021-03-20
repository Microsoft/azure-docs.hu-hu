---
title: Hivatkozási réteg hozzáadása a Azure Maps Power BI vizualizációhoz | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatja a hivatkozási réteget a Microsoft Azure Maps-vizualizációban Power BIhoz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261944"
---
# <a name="add-a-reference-layer"></a>Referenciaréteg hozzáadása

A hivatkozási réteg funkció lehetővé teszi, hogy a másodlagos térbeli adatkészletet feltöltse a vizualizációba, és betakarja a térképen a további kontextus biztosításához. Ezt az adatkészletet Power BI üzemelteti, és egy [GeoJSON-fájlnak](https://wikipedia.org/wiki/GeoJSON) kell lennie, `.json` vagy `.geojson` fájl kiterjesztéssel kell rendelkeznie.

Egy **GeoJSON** -fájl hivatkozási rétegként való hozzáadásához nyissa meg a **Formátum** ablaktáblát, bontsa ki a **hivatkozási réteg** szakaszt, majd kattintson a **+ helyi fájl hozzáadása** gombra.

Miután hozzáadta a GeoJSON-fájlt a hivatkozási réteghez, a fájl neve megjelenik a **+ helyi fájl hozzáadása** gomb helyett egy **X** mellett. Az **X** gomb megnyomásával távolítsa el az adatait a vizualizációból, és törölje a GeoJSON fájlt a Power BIból.

A következő Térkép az [2016-es népszámlálást jeleníti meg a Colorado számára](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), populáció szerint színezve.

> [!div class="mx-imgBorder"]
> ![Egy Térkép 2016-es népszámlálást jelenít meg a Colorado számára, a populációk pedig hivatkozási rétegként színezve](media/power-bi-visual/reference-layer-CO-census-tract.png)

A következő beállítások a **hivatkozási réteg** szakaszban elérhető **Format (formátum** ) ablaktáblán találhatók.

| Beállítás              | Leírás   |
|----------------------|---------------|
| Hivatkozási rétegbeli adatértékek | Az adatGeoJSON-fájl, amelyet a rendszer a vizualizáción lévő további rétegként tölt fel a térképen. A **+ helyi fájl hozzáadása** gomb megnyit egy fájl párbeszédpanelt, amellyel a felhasználó kiválaszthat egy `.json` vagy fájlkiterjesztés GeoJSON fájlt `.geojson` . |

> [!NOTE]
> A Azure Maps vizualizáció ezen előnézetében a hivatkozási réteg csak az első 5 000 alakú funkciót tölti be a térképre. Ezt a korlátot egy jövőbeli frissítés fogja növelni.

## <a name="styling-data-in-a-reference-layer"></a>A hivatkozási rétegben tárolt adatstílusok

A tulajdonságok a GeoJSON fájl minden egyes szolgáltatásához hozzáadhatók, így testre szabhatja, hogy a Térkép milyen stílusú. Ez a szolgáltatás a Azure Maps web SDK-ban az egyszerű adatréteg szolgáltatást használja. További információkért tekintse meg ezt a dokumentumot a [támogatott stílus tulajdonságainál](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Az egyéni ikon rendszerképek nem támogatottak a Azure Maps vizualizáción belül biztonsági óvintézkedésként.

A következő példa egy GeoJSON pont funkcióra mutat, amely a megjelenített színt vörösre állítja.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Következő lépések

További környezet hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Valós idejű forgalom megjelenítése](power-bi-visual-show-real-time-traffic.md)
