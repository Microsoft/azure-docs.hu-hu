---
title: Leképezések hozzáadása – LUIS
titleSuffix: Azure Cognitive Services
description: Vegyen fel szándékokat a LUIS-alkalmazásba, hogy azonosítsa az azonos szándékkal rendelkező kérdések vagy parancsok csoportjait.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018939"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Leképezések hozzáadása a hosszúságú kimondott szöveg felhasználói szándékának meghatározásához

Vegyen fel [leképezéseket](luis-concept-intent.md) a Luis-alkalmazásba, hogy azonosítsa az azonos szándékú kérdések vagy parancsok csoportjait.

A LUIS-portálon a leképezések a felső navigációs sáv **Build** szakaszán, majd a bal oldali panel **céljainak** megfelelően kezelhetők.

## <a name="add-an-intent-to-your-app"></a>Szándék hozzáadása az alkalmazáshoz

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. A **leképezések** lapon válassza a **+ Létrehozás** elemet.
1. Az **Új leképezés létrehozása** párbeszédpanelen adja meg a leképezés nevét (például `ModifyOrder` ), majd válassza a **kész** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Leképezés hozzáadása](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A szándéknak [például hosszúságú kimondott szöveg](luis-concept-utterance.md) kell lennie ahhoz, hogy előre hosszúságú kimondott szöveg a közzétett előrejelzési végponton.

## <a name="add-an-example-utterance"></a>Példa Kimondás hozzáadása

Például a hosszúságú kimondott szöveg a felhasználói kérdések és parancsok szöveges példái. Ha meg szeretné tanítani Language Understanding (LUIS), hogy mikor kell megjósolni ezt a szándékot, hozzá kell adnia egy példa hosszúságú kimondott szöveg egy szándékhoz. A LUIS-nek 15 és 30 közötti hosszúságú kimondott szöveg kell lennie, hogy megkezdje a szándék megértését. Ne vegyen fel több példát a hosszúságú kimondott szöveg. Minden Kimondás után körültekintően kell kiválasztani, hogy miben különbözik a már a szándéknál látható példáktól.

1. A szándék részletei lapon adja meg a felhasználóktól várt megfelelő értéket, például a `Deliver a large cheese pizza` cél neve alatti szövegmezőben, majd nyomja le az ENTER billentyűt.

    > [!div class="mx-imgBorder"]
    > ![A szándékok részletei oldal képernyőképe, a teljes kiemeléssel](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    A LUIS az összes hosszúságú kimondott szöveg kisbetűsre alakítja, és szóközöket [, például](luis-language-support.md#tokenization) kötőjeleket hoz létre.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Szándék-előrejelzési hibák

A rendszer következtetési előrejelzési hibát határoz meg, ha a rendszer nem Jósolja meg a szándékot a kiképzett alkalmazással.

1. A teljes előrejelzési hibák és a kijavításuk megkereséséhez használja a helytelen és a nem egyértelmű **szűrési** beállításokat.

    > [!div class="mx-imgBorder"]
    > ![A teljes előrejelzési hibák és a kijavításuk megkereséséhez használja a Filter (szűrő) beállítást.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Ha meg szeretné jeleníteni a pontszám értékét a megjelenő Részletek lapon, válassza a **Részletek megjelenítése pontok megjelenítése** lehetőséget a **nézet** beállításai menüből.

    Ha a szűrők és a nézet alkalmazva van, és vannak például hosszúságú kimondott szöveg hibák, a példaként szolgáló lista a hosszúságú kimondott szöveg és a problémákat jeleníti meg.

Az egyes sorok az aktuális képzés előrejelzési pontszámát jelenítik meg a példához, a legközelebbi rivális pontszámát, amely a két pontszám különbsége.

### <a name="fixing-intents"></a>Leképezések kijavítása

Ha meg szeretné tudni, hogyan lehet kijavítani a szándék-előrejelző hibákat, használja az [összegző irányítópultot](luis-how-to-use-dashboard.md). Az összegző irányítópult elemzést nyújt az aktív verzió utolsó betanításáról, és a legjobb javaslatokat kínálja a modell kijavításához.

## <a name="using-the-contextual-toolbar"></a>A környezetfüggő eszköztár használata

A környezeti eszköztár más műveleteket is tartalmaz:

* Példa szerkesztése vagy törlése
* Példa kiosztásának ismételt társítása egy másik szándékhoz
* Szűrők és nézetek: csak a szűrt entitásokat tartalmazó hosszúságú kimondott szöveg megjelenítése vagy a választható részletek megtekintése
* Keresés a példa hosszúságú kimondott szöveg

## <a name="train-your-app-after-changing-model-with-intents"></a>Az alkalmazás betanítása a modell leképezésekkel való módosítása után

Miután hozzáadta, szerkeszti vagy eltávolítja a leképezéseket, [betanítja](luis-how-to-train.md) és [közzéteszi](luis-how-to-publish-app.md) az alkalmazást, hogy a módosítások a végponti lekérdezésekre legyenek alkalmazva. Ne végezze el a betanítást minden egyes módosítás után. Betanítás egy csoport módosítása után.

## <a name="next-steps"></a>Következő lépések

További információ a [példa hosszúságú kimondott szöveg](./luis-how-to-add-entities.md) az entitásokkal való hozzáadásáról.