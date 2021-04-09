---
title: Azure Maps Rajzolási hibák láthatóvá való használata
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a Creator (előzetes verzió) átalakítási API által visszaadott figyelmeztetéseket és hibákat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: db88e347e12783205ea8c31fed0bb374fccb4736
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903581"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator-preview"></a>A Azure Maps rajzolási hiba megjelenítése a Creator használatával (előzetes verzió)

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


A felderített rajzolási hiba egy önálló webalkalmazás, amely megjeleníti a [rajzi csomagok figyelmeztetéseit és](drawing-conversion-error-codes.md) az átalakítási folyamat során észlelt hibákat. A nem lapozható webalkalmazás egy statikus oldalból áll, amelyet az internethez való csatlakozás nélkül használhat.  A hibák és figyelmeztetések a [rajzolási csomag követelményeinek](drawing-requirements.md)megfelelően történő kijavításához a hiba láthatóvá tételét használhatja. A [Azure Maps átalakítási API](/rest/api/maps/conversion) csak akkor ad vissza választ, ha a hiba észlelésekor csak a hiba észlelhető.

## <a name="prerequisites"></a>Előfeltételek

A rajzolási hiba megjelenítésének megkezdése előtt a következőket kell tennie:

1. [Azure Maps-fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.
3. [Létrehozó (előzetes) erőforrás létrehozása](how-to-manage-creator.md)

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="download"></a>Letöltés

1. Töltse fel a rajz-csomagot a Azure Maps Creator Service-be (előzetes verzió) a feltöltött csomag beszerzéséhez `udid` . A csomagok feltöltésének lépéseiért lásd: [rajzfájl feltöltése](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Most, hogy a rajzfájl feltöltése megtörtént, a `udid` rendszer a feltöltött csomagra konvertálja a csomagot a leképezési adatként. A csomagok konvertálásának lépéseiért lásd: [rajzfájl konvertálása](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Ha az átalakítási folyamat sikeres, nem fog kapni egy hivatkozást a hiba-megjelenítő eszközre.

3. A Poster alkalmazás válasz- **fejlécek** lapján keresse meg az `diagnosticPackageLocation` átalakítási API által visszaadott tulajdonságot. A válasznak a következő JSON-ként kell kinéznie:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Töltse le a rajzolási csomag hibájának láthatóvá tételét `HTTP-GET` az URL-címre vonatkozó kérelem elküldésével `diagnosticPackageLocation` .

## <a name="setup"></a>Beállítás

A hivatkozáson belül a letöltött tömörített csomagban `diagnosticPackageLocation` két fájl található.

* _VisualizationTool.zip_: a rajzolási hibákhoz tartozó forráskódot, adathordozót és weblapot tartalmazza.
* _ConversionWarningsAndErrors.jsbekapcsolva_: a (z) tartalmaz egy formázott listát a figyelmeztetések, a hibák és a rajzolási hiba által megjelenített további részletekről.

Bontsa ki a _VisualizationTool.zip_ mappát. A következő elemeket tartalmazza:

* _eszközök_ mappa: képeket és médiafájlokat tartalmaz
* _statikus_ mappa: forráskód
* _index.html_ fájl: a webalkalmazás.

Nyissa meg a _index.html_ fájlt az alábbi böngészők bármelyikével, a megfelelő verziószámmal. Más verziót is használhat, ha a verzió ugyanilyen kompatibilis viselkedést biztosít, mint a felsorolt verzió.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>A rajzolási hiba Láthatóváása eszköz használata

Miután elindította a rajzolási hiba megjelenítő eszközét, megjelenik a feltöltés oldal. A feltöltési oldal egy fogd és vidd mezőt tartalmaz. A drag & drop (húzás) jelölőnégyzet a fájlkezelő párbeszédpanelt indító gombként is működik.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Rajzolási hiba-megjelenítő alkalmazás – Kezdőlap":::

A fájl  _ConversionWarningsAndErrors.js_ a letöltött könyvtár gyökeréhez lett helyezve. A _ConversionWarningsAndErrors.jsa_ betöltéséhez húzza & dobja a fájlt a mezőbe, vagy kattintson a mezőre, keresse meg a fájlt a Fájlkezelőben, majd töltse fel a fájlt.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Rajzolási hiba megjeleníthető alkalmazás – húzással betöltheti az adatterhelést":::

Ha a fájl _ConversionWarningsAndErrors.js_ betöltődik, megjelenik a rajzolási csomagok hibái és figyelmeztetései listája. Az egyes hibákat vagy figyelmeztetéseket a réteg, a szint és a részletes üzenet határozza meg. A hiba vagy figyelmeztetés részletes adatainak megtekintéséhez kattintson a **részletek** hivatkozásra. Egy megoldhatatlan szakasz jelenik meg a lista alatt. Mostantól megtekintheti az egyes hibákat, és további információkat tudhat meg a hiba megoldásával kapcsolatban.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Rajzolási hiba – az alkalmazás – hibák és figyelmeztetések":::

## <a name="next-steps"></a>Következő lépések

Ha a [rajzfájl megfelel a követelményeknek](drawing-requirements.md), a [Azure Maps adatkészlet szolgáltatással](/rest/api/maps/conversion) átalakíthatja a rajzi csomagot egy adatkészletbe. Ezután használhatja a beltéri térképek webes modult az alkalmazás fejlesztéséhez. További információért olvassa el a következő cikkeket:

> [!div class="nextstepaction"]
> [Rajzolási konvertálási hibakódok](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Creator (előzetes verzió) beltéri térképekhez](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [A beltéri térképek modul használata](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Beltéri Térkép dinamikus stílusának megvalósítása](indoor-map-dynamic-styling.md)