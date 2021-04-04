---
title: Nyelvi modell testreszabása Video Indexer webhellyel
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan szabhatja testre a nyelvi modellt a Video Indexer webhelyén.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: dd8b36340deb6c785989107461dd420e7fc0d985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722572"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Nyelvi modell testreszabása a Video Indexer webhellyel

A Video Indexer lehetővé teszi, hogy egyéni nyelvi modelleket hozzon létre a beszédfelismerés testre szabásához az adaptációs szöveg feltöltésével, azaz olyan tartományból származó szöveggel, amelynek a szókincsét szeretné a motorhoz igazítani. A modell betanítása után a rendszer az adaptációs szövegben szereplő új szavakat fogja felismerni.

Az egyéni nyelvi modellekkel kapcsolatos részletes áttekintést és ajánlott eljárásokat lásd: [nyelvi modell testreszabása video Indexer](customize-language-model-overview.md)használatával.

A következő témakörben leírtak szerint használhatja a Video Indexer webhelyét egyéni nyelvi modellek létrehozásához és szerkesztéséhez a fiókjában. Az API-t is használhatja az API-k [használata a nyelvi modell testreszabása](customize-language-model-with-api.md)című témakörben leírtak szerint.

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

1. Lépjen a [video Indexer](https://www.videoindexer.ai/) webhelyére, és jelentkezzen be.
1. Ha testre szeretné szabni a fiókban lévő modelleket, válassza ki a **tartalom modell testreszabása** gombot a lap bal oldalán.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="A tartalmi modell testreszabása Video Indexer":::
1. Válassza a **nyelv** fület.

    Ekkor megjelenik a támogatott nyelvek listája.
1. A kívánt nyelven válassza a **modell hozzáadása** elemet.
1. Írja be a nyelvi modell nevét, és nyomja le az ENTER billentyűt.

    Ez a lépés létrehozza a modellt, és lehetőséget biztosít szöveges fájlok feltöltésére a modellbe.
1. Szövegfájl hozzáadásához válassza a **fájl hozzáadása** lehetőséget. Megnyílik a fájlkezelő.
1. Navigáljon a fájlhoz, és válassza ki a szövegfájlt. Több szövegfájlt is hozzáadhat egy nyelvi modellhez.

    Szövegfájl hozzáadásához kattintson a nyelvi modell jobb oldalán található **...** gombra, majd a **fájl hozzáadása** lehetőségre.
1. Ha elkészült a szövegfájlok feltöltésével, válassza a zöld **vonat** lehetőséget.

A betanítási folyamat eltarthat néhány percig. A képzés elvégzése után a modell **melletti képzést** láthatja. Megtekintheti, letöltheti és törölheti a fájlt a modellből.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="A modell betanítása":::

### <a name="using-a-language-model-on-a-new-video"></a>Nyelvi modell használata új videón

Ha a nyelvi modellt új videón szeretné használni, hajtsa végre az alábbi műveletek egyikét:

* Kattintson a lap tetején található **feltöltés** gombra.

    ![Feltöltés gomb Video Indexer](./media/customize-language-model/upload.png)
* Dobja el hang-vagy videofájl-fájlját, vagy tallózással keresse meg a fájlt.

Lehetősége van kiválasztani a **videó forrásának nyelvét**. Válassza ki a legördülő listát, és válasszon ki egy, a listából létrehozott nyelvi modellt. Meg kell mondania a nyelvi modell nyelvét, valamint azt a nevet, amelyet zárójelben adott meg. Például:

![A videó forrása nyelvének kiválasztása – a videó újraindexelése a Video Indexer](./media/customize-language-model/reindex.png)

Válassza ki a **feltöltés** lehetőséget az oldal alján, és az új videó a nyelvi modell alapján lesz indexelve.

### <a name="using-a-language-model-to-reindex"></a>Nyelvi modell használata az újraindexeléshez

Ha a nyelvi modellt szeretné használni a gyűjteményben található videók újraindexeléséhez, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [video Indexer](https://www.videoindexer.ai/) kezdőlapjára.
1. Kattintson a videóban található **.** .. gombra, és válassza az **újraindexelés** lehetőséget.
1. Lehetősége van kiválasztani a **videó forrásának nyelvét** , hogy újra indexelje a videót a használatával. Válassza ki a legördülő listát, és válasszon ki egy, a listából létrehozott nyelvi modellt. Meg kell mondania a nyelvi modell nyelvét, valamint azt a nevet, amelyet zárójelben adott meg.
1. Válassza az **újraindexelés** gombot, és a videó újraindexelve lesz a nyelvi modell használatával.

## <a name="edit-a-language-model"></a>Nyelvi modell szerkesztése

A nyelvi modell szerkesztéséhez módosítsa a nevét, adjon hozzá fájlokat, és törölje a fájlokat.

Ha a nyelvi modellből fájlokat ad hozzá vagy töröl, a zöld **vonat** lehetőség kiválasztásával újra be kell tanítania a modellt.

### <a name="rename-the-language-model"></a>A nyelvi modell átnevezése

A nyelvi modell nevét a nyelvi modell jobb oldalán található három pont (**..**.) gombra kattintva módosíthatja, majd az **Átnevezés** lehetőséget választva.

Írja be az új nevet, és nyomja le az ENTER billentyűt.

### <a name="add-files"></a>Fájlok hozzáadása

Szövegfájl hozzáadásához válassza a **fájl hozzáadása** lehetőséget. Megnyílik a fájlkezelő.

Navigáljon a fájlhoz, és válassza ki a szövegfájlt. Több szövegfájlt is hozzáadhat egy nyelvi modellhez.

Szövegfájlt is hozzáadhat a nyelvi modell jobb oldalán lévő három pont (**..**.) gomb kiválasztásával, majd a **fájl hozzáadása** lehetőség kiválasztásával.

### <a name="delete-files"></a>Fájlok törlése

Ha törölni szeretne egy fájlt a nyelvi modellből, kattintson a három pont (**..**.) gombra a szövegfájl jobb oldalán, majd válassza a **Törlés** lehetőséget. Egy új ablak jelenik meg, amely azt jelzi, hogy a törlés nem vonható vissza. Válassza a **Törlés** lehetőséget az új ablakban.

Ez a művelet teljesen eltávolítja a fájlt a nyelvi modellből.

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

Ha törölni szeretne egy nyelvi modellt a fiókjából, válassza a nyelvi modell jobb oldalán található három pontot (**..**.), majd a **Törlés** lehetőséget.

Egy új ablak jelenik meg, amely azt jelzi, hogy a törlés nem vonható vissza. Válassza a **Törlés** lehetőséget az új ablakban.

Ez a művelet teljesen eltávolítja a nyelvi modellt a fiókjából. A törölt nyelvi modellt használó összes videó esetében Ugyanez az index marad, amíg újra nem indexeli a videót. Ha újraindexeli a videót, hozzárendelhet egy új nyelvi modellt a videóhoz. Ellenkező esetben a Video Indexer az alapértelmezett modellt fogja használni a videó újraindexeléséhez.

## <a name="customize-language-models-by-correcting-transcripts"></a>Nyelvi modellek testreszabása átiratok kijavítani

A Video Indexer támogatja a nyelvi modellek automatikus testreszabását a felhasználók által a videók átírásakor végzett tényleges javítások alapján.

1. Ha meg szeretne javítani egy átiratot, nyissa meg azt a videót, amelyet szerkeszteni szeretne a fiókjának videói alapján. Válassza az **Idősor** fület.

    ![Nyelvi modell idővonalának testreszabása lap – Video Indexer](./media/customize-language-model/timeline.png)

1. Válassza a ceruza ikont az átirat átiratának szerkesztéséhez.

    ![Nyelvi modell testreszabásának módosítása – Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer rögzíti a videó átírásával korrigált összes sort, és automatikusan hozzáadja azokat egy "from átirat szerkesztések" nevű szövegfájlhoz. Ezek a módosítások a videó indexeléséhez használt nyelvi modell újratanítására szolgálnak. 
    
    A rendszer a [widget](video-indexer-embed-widgets.md) idővonalán elvégzett módosításokat is tartalmazza.
    
    Ha a videó indexelése során nem adott meg nyelvi modellt, a videó összes szerkesztését a "fiók módosítása" nevű alapértelmezett nyelvi modell fogja tárolni a videó észlelt nyelvén belül.
    
    Ha ugyanarra a sorra több szerkesztés lett létrehozva, akkor a nyelvi modell frissítésére csak a javított vonal utolsó verziója lesz felhasználva.  
    
    > [!NOTE]
    > A testreszabáshoz csak szöveges helyesbítések használatosak. A tényleges szavakat nem érintő javítások (például írásjelek vagy szóközök) nem szerepelnek benne.
    
1. A tartalmi modell testreszabása lap Language (nyelv) lapján láthatók a megjelenő átiratok.

   Ha meg szeretné nézni az egyes nyelvi modellekhez tartozó "feladói szerkesztések" fájlt, válassza ki azt a megnyitásához.

    ![Az átirat szerkesztése – Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Következő lépések

[Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)
