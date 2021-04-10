---
title: 'Gyors útmutató: alkalmazás üzembe helyezése a LUIS-portálon'
description: Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy alkalmazást az előrejelzési végpontok erőforrásának létrehozásával, az erőforrás hozzárendelésével, a képzéssel és az alkalmazás közzétételével.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: f7d5d473b8fe24d8dd5018a718ea96f286b1c030
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275838"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Gyors útmutató: alkalmazás üzembe helyezése a LUIS portálon

Ha a LUIS-alkalmazás készen áll arra, hogy egy ügyfélalkalmazás (például egy csevegési robot) teljes beolvasását adja vissza, az alkalmazást az előrejelzési végpontra kell telepítenie.

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy alkalmazást. Létrehoz egy előrejelzési végpont-erőforrást, hozzárendeli az erőforrást az alkalmazáshoz, betanítja az alkalmazást, és közzéteszi az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Azure- [előfizetés](https://azure.microsoft.com/free)beszerzése.
* Fejezze be az [előző portál](get-started-portal-build-app.md) rövid útmutatóját [, vagy töltse le és importálja az alkalmazást](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Ha olyan alkalmazásokkal rendelkezik, amelyek előzetes Azure-erőforrás-hitelesítést végeznek, váltson [át egy Azure-erőforrásra](luis-migration-authoring.md). Egyes portál oldalak eltérőek, ha az e-mail-hitelesítés érvényben van.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Az erőforrás-kulcs kiosztása a LUIS-alkalmazáshoz a LUIS-portálon

Minden alkalommal, amikor új szerzői vagy lekérdezési előrejelzési erőforrást hoz létre a LUIS számára, hozzá kell rendelnie az erőforrást a LUIS alkalmazáshoz. A hozzárendelés után ezt a lépést csak akkor kell végrehajtania, ha új erőforrást hoz létre. Létrehozhat egy új erőforrást az alkalmazás régiói kibontásához, vagy nagyobb számú előrejelzési lekérdezés támogatásához.

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai) , és válassza ki a **myEnglishApp** alkalmazást az alkalmazások listából.

1. Válassza a **kezelés** lehetőséget a jobb felső menüben, majd válassza az **Azure-erőforrások** lehetőséget.

1. A LUIS hozzáadásához válassza az **előrejelzési erőforrás hozzáadása** lehetőséget.

    ![A LUIS-előrejelzési erőforrás hozzáadásához válassza az előrejelzési erőforrás hozzáadása lehetőséget.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Válassza ki a bérlőt, az előfizetést és az erőforrás nevét. Válassza az **erőforrás kiosztása** elemet.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás kiosztása az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Hajtsa végre ugyanezen lépéseket a szerzői kulcs alkalmazáshoz való hozzáadásához.

1. Keresse meg az új sort a táblában az új előrejelzési erőforráshoz, és másolja a végpont URL-címét. A rendszer megfelelően van kiépítve, hogy `HTTP GET` a Luis API-végpontra irányuló kérést hozzon létre egy előrejelzéshez.

> [!TIP]
> Ha a LUIS-alkalmazás fejlesztéséhez aktív tanulást szeretne használni, válassza a **lekérdezési paraméterek módosítása** lehetőséget, majd a **naplók mentése** lehetőséget. Ez a művelet megváltoztatja a példa URL-címét a `log=true` querystring paraméter hozzáadásával. Másolja ki és használja a megváltozott példa lekérdezési URL-címét, amikor előrejelzési lekérdezéseket készít a futásidejű végpontra.

## <a name="train-the-app"></a>Az alkalmazás betanítása

Ha módosította az alkalmazást, mert az utolsó [tanítása](get-started-portal-build-app.md) után betanítja az alkalmazást.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Az alkalmazás közzététele az előrejelzési végponton

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Előrejelzési végpont kérése

A portálon, az `query=` URL végén, ahol a felhasználóhoz tartozó kifejezés a Get kérelemhez lesz hozzáfűzve. A ( `query=` z) után adja meg ugyanazt a felhasználói kiírást, amelyet az előző rövid útmutató végén használ:

```Is there a form named hrf-234098```

Győződjön meg arról, hogy a lekérdezési karakterlánc a következő párokat tartalmazza:

* `show-all-intents=true`
* `verbose=true`

A böngésző megjeleníti a választ:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
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

Ha ugyanezt az információt szeretné megtekinteni a teszt ablaktáblán, közzé kell tennie az alkalmazást. Az alkalmazás közzététele után válassza az **összehasonlítás a közzétételsel** a teszt ablaktáblán. A közzétett teszt ablaktáblán a **JSON megjelenítése nézet** használatával megtekintheti az előző lépéssel MEGegyező JSON-t. Így összehasonlíthatja a jelenlegi alkalmazás azon módosításait, amelyeket a végponton közzétett alkalmazással használ.

[![Az alkalmazás aktuális szerkesztésének és közzétett verziójának összehasonlítása](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, válassza a **saját alkalmazások** lehetőséget a felső navigációs menüből. Jelölje be az alkalmazás jelölőnégyzetét a listából, majd válassza a **Törlés** lehetőséget a lista fölötti helyi eszköztáron.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A LUIS-hez készült iterációs alkalmazások fejlesztése](./luis-concept-app-iteration.md)