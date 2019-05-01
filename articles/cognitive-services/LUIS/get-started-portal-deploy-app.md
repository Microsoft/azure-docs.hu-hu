---
title: 'Gyors útmutató: A LUIS-portál alkalmazás telepítése'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, hogyan helyezhet üzembe az előrejelzési végpont a LUIS-alkalmazás után az alkalmazás készen áll a utterance (kifejezés) előrejelzéseket térjen vissza egy ügyfélalkalmazás, például csevegőrobotot. Ez a rövid útmutató végigvezeti az alkalmazások üzembe helyezése a előrejelzési végponti erőforrás létrehozása, az erőforrás hozzárendelése az alkalmazáshoz, az alkalmazás képzés és az alkalmazás közzététele.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9a54cad9212bdb514f7742121909221863ba75e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713526"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Gyors útmutató: A LUIS-portál alkalmazás telepítése

Ha a LUIS-alkalmazás készen áll az utterance (kifejezés) előrejelzéseket térjen vissza egy ügyfélalkalmazás (például csevegőrobotot), az alkalmazás üzembe helyezése az előrejelzési végpont kell.

Ebben a rövid útmutatóban megismerheti az alkalmazás üzembe helyezése. Előrejelzési végponti erőforrás létrehozása, az erőforrás rendelni az alkalmazást, az alkalmazás betanítása és tegye közzé az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Get- [Azure-előfizetés](https://azure.microsoft.com/free).
* Végezze el a [előző portal rövid útmutatójának](get-started-portal-build-app.md) vagy [letöltése és importálása az alkalmazás](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>A végponti erőforrás létrehozása

Az Azure Portalon hoz létre az előrejelzési végponti erőforrás. Ehhez az erőforráshoz csak használandó végpont előrejelzési lekérdezések. Ne használja ezt az erőforrást módosítások az alkalmazás szerzői műveletekhez részben.

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).

1. Válassza ki a zöld **+** jelentkezzen be a bal felső panel. Keresse meg `Cognitive Services` a Marketplace-en, és jelölje ki.

1. Az előfizetés konfigurálása a következő beállításokkal:

   |Beállítás|Érték|Cél|
   |--|--|--|
   |Name (Név)|`my-cognitive-service-resource`|Az Azure-erőforrás neve. Ha az erőforrás rendel az alkalmazáshoz, a LUIS portálon kell ezt a nevet.|
   |Előfizetés|Az Ön előfizetése|Válasszon ki egy a a fiókjához társított előfizetéseket.|
   |Location egység|**USA nyugati régiója**|Az Azure-régióban ehhez az erőforráshoz.|
   |Tarifacsomag|**S0**|Az alapértelmezett tarifacsomag ehhez az erőforráshoz.|
   |Erőforráscsoport|`my-cognitive-service-resource-group`|Hozzon létre egy új erőforráscsoportot, a cognitive Services-szolgáltatás-erőforrások. Ha elkészült, az erőforrások, törölheti az erőforráscsoportot törölni az előfizetést. |
   | | | |

   ![Az Azure API kiválasztása](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Válassza ki **létrehozás** az Azure-erőforrás létrehozásához.

   A következő szakaszban megismerheti, hogyan az új erőforrás csatlakozni a LUIS-alkalmazások, a LUIS-portálon.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Az erőforráskulcs hozzárendelése a LUIS-alkalmazás a LUIS-portálon

Minden alkalommal, amikor egy új erőforrást hoz létre a LUIS, akkor hozzá kell rendelni az erőforrás a LUIS-alkalmazás. Miután van hozzárendelve, nem kell újra el ezt a lépést, ha nem hoz létre egy új erőforrást. Létrehozhat egy új erőforrást, bontsa ki a régiók, az alkalmazás vagy egy előrejelzési lekérdezések nagyobb számának támogatásához.

1. Jelentkezzen be a [LUIS portál](https://www.luis.ai) , és válassza a **myEnglishApp** alkalmazást a listáról.

1. Válassza ki **kezelés** a jobb felső menüben, és válassza ki a **kulcsokat és a végpontok**.

1. A LUIS hozzáadásához válassza **erőforrás hozzárendelése +**.

   [![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Válassza ki a bérlő, az előfizetés és az erőforráscsoport nevét. Válassza ki **erőforrás hozzárendelése**.

   ![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Keresse meg az új sort a táblázatban, és másolja a végpont URL-címe. Helyesen kialakítani, győződjön meg arról, hogy egy `HTTP GET` kérelem előrejelzési az intelligens HANGFELISMERÉSI API-végponthoz.

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele

Ha készen áll, tesztelése, betanítása az alkalmazás. Tegye közzé az alkalmazást, ha azt szeretné, hogy az ügyfélalkalmazások számára elérhetőnek lennie az előrejelzési végpont modul jelenleg betanított verziójára.

1. Ha az alkalmazás kellő, jelölje be **Train** a menüben kattintson a jobb felső sarokban.

1. Válassza ki **közzététel** a menüből. Fogadja el az alapértelmezett környezet beállításokat, és válassza ki **közzététel**.

1. A zöld, sikeres értesítési sáv felső részén a böngészőablakban megjelenésekor válassza **tekintse meg a végpontok listáját**.

   ![Alkalmazás közzététele sikerült értesítési sáv böngészőben](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. A a **kulcsokat és a végpont beállításait** lapon, a hozzárendelt erőforrások és a kapcsolódó végponti URL-címek listáját találja a lap alján.

1. Válassza ki a végponthoz társított URL-címet az új erőforrás neve. Ez a művelet megnyit egy webböngészőt, győződjön meg arról, hogy helyesen kialakítani URL-címet egy `GET` az előrejelzési végpont modul irányuló kérelem.

1. A `q=` az URL-cím végén található rövid a **lekérdezés** , és ahol a felhasználó utterance (kifejezés) a rendszer hozzáfűzi a GET-kérés. Miután a `q=`, adja meg az ugyanazon felhasználói utterance (kifejezés) használt a korábbi rövid útmutató végén:

    ```Is there a form named hrf-234098```

    A böngészőben a választ, amely az ügyfélalkalmazásban fog kapni a azonos JSON mutatja be:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Ez a válasz több információt a alapértelmezett ablaktáblán az előző oktatóanyagban biztosít. Ez a teszt ablaktábla azonos szintű jelenik meg, közzé kell tennie az alkalmazást. Az alkalmazás közzététele után válassza ki a **hasonlítsa össze a közzétett** a ablaktáblán. Használat **megjelenítése JSON-nézet** ugyanabban a JSON tekintheti meg az előző lépésben a közzétett ablaktáblán. Ezzel a módszerrel összehasonlíthatja az aktuális alkalmazás, amelyen dolgozik, hogy a végponthoz való közzétételekor.

    [![A jelenleg szerkesztett és alkalmazás közzétett verziójának összehasonlítása](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett, az ebben a rövid, válassza ki a **saját alkalmazások** a felső navigációs menüjében. Válassza ki a jelölőnégyzetet az alkalmazást a listából, majd **törlése** a környezet eszköztáron az eszközlista feletti.

[![Alkalmazás törlése a saját alkalmazások listája](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közös szándékok és entitások azonosítása](luis-tutorial-prebuilt-intents-entities.md)
