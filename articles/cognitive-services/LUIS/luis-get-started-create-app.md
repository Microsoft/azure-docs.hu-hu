---
title: 'Gyors útmutató: alkalmazás létrehozása – LUIS'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy olyan LUIS-alkalmazást, amely az előre elkészített tartományt használja a `HomeAutomation` fények és a készülékek be-és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: e0972998fad647c1c70be814cb7c13f14a6e1141
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277028"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat.

### <a name="create-an-application"></a>Alkalmazás létrehozása

Alkalmazás létrehozásához kattintson az  **+ új alkalmazás** lehetőségre. 

A megjelenő ablakban adja meg a következő adatokat:

|Név  |Leírás  |
|---------|---------|
|AName     | Az alkalmazás neve. Például: "Home Automation".        |
|Kultúra     | A nyelv, amelyet az alkalmazás megértett és beszél.   |
|Leírás | Az alkalmazás leírása.
|Előrejelzési erőforrás | Az előrejelzési erőforrás, amely a lekérdezéseket fogja kapni. |

Válassza a **Kész** lehetőséget.

>[!NOTE]
>A kulturális környezet az alkalmazás létrehozása után nem módosítható.

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. A bal oldali navigációs sávon válassza az **előre elkészített tartományok** elemet.
1. **HomeAutomation** keresése.
1. Válassza a **tartomány hozzáadása** elemet a HomeAutomation kártyán.

    > [!div class="mx-imgBorder"]
    > ![Válassza az "előre elkészített tartományok" lehetőséget, majd keressen rá a "HomeAutomation" kifejezésre. Válassza a "tartomány hozzáadása" lehetőséget a HomeAutomation kártyán.](media/luis-quickstart-new-app/home-automation.png)

    A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

## <a name="intents-and-entities"></a>Szándékok és entitások

1. A bal oldali navigációs menüben válassza a **leképezések** lehetőséget a HomeAutomation-tartomány leképezésének megtekintéséhez. Például hosszúságú kimondott szöveg, például `HomeAutomation.QueryState` és     `HomeAutomation.SetDevice` .

    > [!NOTE]
    > A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak.

1. Válassza a **HomeAutomation.TurnOff** szándékot. A szándék az entitásokkal jelölt hosszúságú kimondott szöveg listáját tartalmazza.

    > [!div class="mx-imgBorder"]
    > [![A HomeAutomation.TurnOff szándék képernyőképe](media/luis-quickstart-new-app/home-automation-turnoff.png "A HomeAutomation.TurnOff szándék képernyőképe")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Ha meg szeretné tekinteni az alkalmazás entitásait, válassza az **entitások** lehetőséget. Ha az egyik entitásra kattint, például a **HomeAutomation. DeviceName** , megjelenik a hozzá társított értékek listája. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Képet helyettesítő szöveg" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is.

1. Válassza a **teszt** lehetőséget a jobb felső navigációs sávon.

1. Írjon be egy tesztet `Turn off the lights` , például az interaktív teszt ablaktáblába, és nyomja le az ENTER billentyűt. *Kikapcsolhatja például a fények kikapcsolását*.

    Ebben a példában `Turn off the lights` helyesen van azonosítva, mint a **HomeAutomation. kanyart** legfelső pontozási szándéka.

    ![A tesztelési ablaktábla képernyőképe a kiemelt kimondott szöveggel](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Az előrejelzéssel kapcsolatos további információk megtekintéséhez válassza a **vizsgálat** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A teszt panel képernyőképe ellenőrzési információkkal](media/luis-quickstart-new-app/test.png)

1. Zárjuk be a teszt panelt.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

Ahhoz, hogy a LUIS-előrejelzést egy csevegési robot vagy más ügyfélalkalmazás fogadja, közzé kell tennie az alkalmazást az előrejelzési végponton.

1. Válassza a **Közzététel** lehetőséget az ablak jobb felső sarkában.

1. Válassza ki az **üzemi** tárolóhelyet, majd válassza a **kész** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a LUIS-közzétételi végpontról](media/howto-publish/publish-app-popup.png)

1. Válassza ki az értesítésben a **végpont URL-címeire** mutató hivatkozást az **Azure-erőforrások** lapra való ugráshoz. A végpont URL-címei a **példa lekérdezésként** jelennek meg.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>A V3 API előrejelzési végpontjának lekérdezése

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. A böngésző címsorába a lekérdezési karakterlánchoz ellenőrizze, hogy a következő név és érték sávok szerepelnek-e az URL-címben. Ha nem szerepelnek a lekérdezési karakterláncban, adja hozzá őket:

    |Név/érték párok|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. A böngésző címsorába lépjen az URL-cím végére, és adja meg a `turn off the living room light` _lekérdezési_ értéket, majd nyomja le az ENTER billentyűt.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A LUIS-hez készült iterációs alkalmazások fejlesztése](./luis-concept-app-iteration.md)