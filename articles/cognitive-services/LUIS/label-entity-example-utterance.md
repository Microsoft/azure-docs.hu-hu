---
title: Címke-entitás – példa Kimondás
description: Megtudhatja, hogyan címkézheti a gépi tanulási entitásokat alentitásokkal a LUIS-portálon található, a szándékok részleteinek kimondása mellett.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019735"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Címke gépi tanulásra szolgáló entitása egy példa Kimondás esetén

Ha egy entitást címkéz egy példa kiírásakor, az a LUIS példa arra, hogy mi is az entitás, és hogy az entitás hol jelenhet meg a teljes tartalomban.

Felcímkézheti a géppel megtanult entitásokat és alentitásokat.

Mivel nem címkézheti a reguláris kifejezéseket, a listákat vagy az előre felépített entitásokat, létrehozhat egy entitást vagy alentitást, majd felveheti ezeket az entitásokat szolgáltatásként, ha alkalmazható, az entitásra vagy alentitásra.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Címke – példa hosszúságú kimondott szöveg a szándék részletei lapról

Ha címkén belüli entitásokra vonatkozó példákat szeretne felcímkézni, válassza ki a teljes szándékot.

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Válassza ki azt a szándékot, amelynek a példája alapján ki szeretné jelölni a kinyerési hosszúságú kimondott szöveg egy entitással.
1. Válassza ki a címkézni kívánt szöveget, majd válassza ki az entitást.

## <a name="two-techniques-to-label-entities"></a>Két módszer az entitások címkézésére

A leképezési Részletek lapon két címkézési módszer támogatott.
* Válasszon ki egy entitást vagy alentitást az [entitás palettáján](#label-with-the-entity-palette-visible) , majd válassza ki a példa a teljes szövegre. Ez az ajánlott eljárás, mert a séma alapján vizuálisan ellenőrizheti, hogy a megfelelő entitást vagy alentitást dolgozik-e.
* Először válassza ki a példa teljes szövege szövegét. Ha ezt teszi, megjelenik egy előugró menü a [címkézési lehetőségek](#how-to-label-entity-from-in-place-menu) közül.

## <a name="label-with-the-entity-palette-visible"></a>Felirat az Entity paletta látható

Miután [megtervezte a sémát az entitásokkal](luis-how-plan-your-app.md), megtarthatja az **entitás palettáját** a címkézés során. Az **entitás-paletta** a kinyerni tervezett entitások emlékeztetője.

Az entitás- **paletta** eléréséhez válassza ki a **@** szimbólumot a környezetfüggő eszköztáron a példa-megjelölések listáján.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Entity paletta a szándék részletei lapon.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Entitás kicímkézése az entitások palettáján

Az entitás-paletta a korábbi címkézési felület alternatívájaként nyújt alternatívát. Lehetővé teszi a szöveg átméretezését, ha azonnal címkével látja el azt egy entitással.

1. Nyissa meg az entitás-palettát a kiválasztási **@** tábla jobb felső sarkában található szimbólumra kattintva.

2. Válassza ki a felcímkézni kívánt paletta entitását. Ez a művelet vizuálisan egy új kurzorral van megjelölve. A kurzor az egérmutatót követi a LUIS-portálon való áthelyezés során.

3. A példában a Kimondás elemre _festi_ az entitást a kurzorral.

    > [!div class="mx-imgBorder"]
    > ![A képernyőképen a kurzorral festett entitás látható.](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Entitás hozzáadása szolgáltatásként az entitás palettáján

Az Entity paletta alsó szakasza lehetővé teszi szolgáltatások hozzáadását a jelenleg kijelölt entitáshoz. A meglévő entitások és kifejezések listája közül választhat, vagy létrehozhat egy új kifejezést.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Entity paletta szolgáltatásról](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Entitás szerepköreinek címkézése

Az entitások szerepkörei az **Entity paletta** használatával vannak megjelölve.

1. A leképezés részletei lapon válassza ki az Entity ( **entitás) palettát** a helyi eszköztárból.
1. Az entitás-paletta megnyitása után válassza ki az entitást az entitás listából.
1. Az entitások listájának alatt válasszon ki egy meglévő szerepkört.
1. A példában a Kimondás szövegében címkézze fel a szöveget az entitás szerepkörrel.

## <a name="how-to-label-entity-from-in-place-menu"></a>Az entitás helyi menüből való címkézése

A helyben történő címkézés lehetővé teszi, hogy gyorsan kiválassza a szövegen belüli szöveget, és címkézze azt. A címkével ellátott szövegből létrehozhat egy Machine learning-entitást vagy egy entitást is.

Gondolja át a példát `hi, please I want a cheese pizza in 20 minutes` .

Válassza ki a bal szélső szöveget, majd válassza ki az entitás jobb szélső szövegét, majd a helyi menüben válassza ki a címkével ellátni kívánt entitást.

> [!div class="mx-imgBorder"]
> ![Címke teljes gépi tanulási entitás](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Címkézett szöveg áttekintése

A címkézést követően tekintse át a példa teljes számát, és gondoskodjon arról, hogy a kijelölt szöveg a kiválasztott entitással legyen aláhúzva. A folytonos vonal azt jelzi, hogy a szöveg címkével van ellátva.

> [!div class="mx-imgBorder"]
> ![Teljes gépi tanulási entitás címkézve](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Előre jelzett entitás megerősítése

Ha a szöveg tartománya körül egy pontozott vonallal tagolt mező van, akkor a szöveg előre látható, de _még nincs megjelölve_. Az előrejelzés címkévé való bekapcsolásához jelölje ki a teljes sort, majd válassza az **entitások megerősítése** lehetőséget a környezetfüggő eszköztáron.

## <a name="relabeling-over-existing-entities"></a>Újracímkézés meglévő entitások felett

Ha a már címkézett szöveget újracímkézi, a LUIS megoszthatja vagy egyesítheti a meglévő címkéket.

## <a name="labeling-for-punctuation"></a>Írásjelek címkézése

Nem kell megcímkéznie a központozás felcímkézését. Az [Alkalmazásbeállítások](luis-reference-application-settings.md) segítségével szabályozhatja, hogy a központozás hogyan befolyásolja a kizáró előrejelzéseket.

## <a name="unlabel-entities"></a>Entitások címkézésének kicsomagolása

> [!NOTE]
> Csak a gépi megtanult entitások lehetnek címkézve. Nem címkézheti vagy címkézheti a reguláris kifejezések entitásait, vagy listázhatja az entitásokat vagy az előre elkészített entitásokat.

Entitás kijelölésének megjelöléséhez válassza ki az entitást, majd a helyi menüben válassza a **jelöletlen** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Az entitások címkézését ábrázoló képernyőkép](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Automatikus címkézés szülő-és gyermek entitásokhoz

Ha szülő entitást címkéz, akkor a jelenleg betanított verzió alapján megjósolható alentitások címkével lesznek ellátva.

Alentitások címkézése esetén a szülő automatikusan címkével lesz ellátva.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Automatikus címkézés a nem gépi megtanult entitásokhoz

A nem gépi megtanult entitások közé tartoznak az előre elkészített entitások, a reguláris kifejezések entitásai, az entitások listázása és a minta. bármely entitás. Ezeket a rendszer automatikusan a LUIS címkével jelölte meg, így a felhasználóknak nem kell manuálisan megadniuk a címkét.

## <a name="intent-prediction-errors"></a>Szándék-előrejelzési hibák

A leképezési előrejelzési hiba azt jelzi, hogy a jelenlegi betanított alkalmazáshoz tartozó példa kimondása nem lenne előre jelezve a szándék miatt.

Megtudhatja, hogyan [tekintheti meg ezeket a hibákat](luis-how-to-add-intents.md#intent-prediction-errors) a szándék részletei lapon.

## <a name="entity-prediction-errors"></a>Entitás-előrejelzési hibák

Az entitás-előrejelzési hibák azt jelzik, hogy az előre jelzett entitás nem egyezik a címkével ellátott entitással. Ezt a rendszer a kiírás melletti óvatossági mutatóval ábrázolja.

> [!div class="mx-imgBorder"]
> ![A gépi tanulási entitás entitás-palettája](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Következő lépések

Használja az [irányítópultot](luis-how-to-use-dashboard.md) , és [tekintse át a végpont hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) az alkalmazás előrejelzési minőségének javítása érdekében.
