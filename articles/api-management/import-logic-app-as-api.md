---
title: Logikai alkalmazás importálása API-ként az Azure Portal használatával  | Microsoft Docs
description: Ez a cikk bemutatja, hogyan használható a API Management (APIM) a logikai alkalmazások API-ként történő importálásához.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 0dd460bce01b86c72d0b8fd70351fdcc85c69bd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146960"
---
# <a name="import-a-logic-app-as-an-api"></a>Logikai alkalmazás importálása API-ként

Ez a cikk bemutatja, hogyan importálhat egy logikai alkalmazást API-ként, és tesztelheti az importált API-t.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> -   Logikai alkalmazás importálása API-ként
> -   Az API tesztelése az Azure Portalon
> -   Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

-   Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
-   Ellenőrizze, hogy van-e olyan logikai alkalmazás az előfizetésben, amely egy HTTP-végpontot tesz elérhetővé. További információ: [munkafolyamatok indítása http-végpontokkal](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Háttérbeli API importálása és közzététele

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.
2. Válasszon ki egy **logikai alkalmazást** az **Új API hozzáadása** listából.

    ![Logikai alkalmazás](./media/import-logic-app-as-api/logic-app-api.png)

3. A **Tallózás** gombra kattintva megtekintheti a http-triggerrel rendelkező Logic apps listáját az előfizetésében. (Vegye figyelembe, hogy a HTTP-trigger nélküli Logic Apps nem jelennek meg a listában.)
4. Válassza ki az alkalmazást. API Management megkeresi a kiválasztott alkalmazáshoz társított hencegés, beolvassa és importálja.
5. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az API Management-példányban. Ebben az API Management-példányban egyedinek kell lennie.
6. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az „_Unlimited_” terméket használjuk. Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.

    A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha létrehozta a API Management példányt, akkor Ön már rendszergazda, így alapértelmezés szerint minden termékre előfizetett.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    - **Kezdő**
    - **Korlátlan**

7. Adja meg az egyéb API-beállításokat. Megadhatja az értékeket a létrehozás során, vagy később konfigurálhatja őket a **Beállítások** lapon. A beállításokat az [első API-oktatóanyag importálásával és közzétételével](import-and-publish.md#import-and-publish-a-backend-api) foglalkozó cikkben ismertetjük.
8. Válassza a **Létrehozás** lehetőséget.

## <a name="test-the-api-in-the-azure-portal"></a>Az API tesztelése az Azure Portalon

A műveleteket meg lehet hívni közvetlenül az Azure Portalról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Válasszon ki egy műveletet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az API Management-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer.

4. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Mindegyik logikai alkalmazás rendelkezik **manual-invoke** művelettel. Ha egy API több logikai alkalmazásból áll, az ütközések elkerülése érdekében át kell neveznie a függvényt.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>
> [Közzétett API átalakítása és védelemmel való ellátása](transform-api.md)
