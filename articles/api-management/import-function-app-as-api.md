---
title: Azure-függvényalkalmazás importálása API-ként API Management
titleSuffix: Azure API Management
description: Ebből a cikkből megtudhatja, hogyan importálhat Azure-függvényalkalmazást API-ként az Azure API Managementba.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: f66395b1e0f45f1e80cd0ac93bf8c9ae8674a0f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732962"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure-függvényalkalmazás importálása API-ként az Azure API Management használatával

Az Azure API Management az Azure-függvényalkalmazások új API-ként való importálását vagy a meglévő API-khoz való hozzáfűzését támogatja. A folyamat automatikusan létrehoz egy gazdagépkulcsot az Azure-függvényalkalmazásban, amelyet aztán az Azure API Management hozzárendel egy névvel ellátott értékhez.

Ez a cikk bemutatja az Azure-függvényalkalmazás importálását API-ként az Azure API Management használatával. Ezen kívül a tesztelési folyamatot is ismerteti.

Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Azure-függvényalkalmazás importálása API-ként
> * Azure-függvényalkalmazás hozzáfűzése egy API-hoz
> * Az új Azure-függvényalkalmazás gazdagépkulcsának és az Azure API Management névvel ellátott értékének megtekintése
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

* Tekintse át az [Azure API Management-példány létrehozását](get-started-create-service-instance.md) ismertető rövid útmutatót.
* Győződjön meg arról, hogy az előfizetése tartalmaz egy Azure Functions-alkalmazást. További információkért lásd az [Azure-függvényalkalmazás létrehozását](../azure-functions/functions-get-started.md) ismertető cikket. Tartalmaznia kell egy HTTP-triggerrel ellátott függvényt, és az engedélyszintnek *Névtelenre* vagy *Függvényre* állítva kell lennie.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Azure-függvényalkalmazás importálása új API-ként

Kövesse az alábbi lépéseket egy új API létrehozásához egy Azure-függvényalkalmazásból.

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.

2. Az **Új API hozzáadása** listából válassza ki a **Függvényalkalmazást**.

    ![Képernyőkép, amely megjeleníti a függvényalkalmazás csempét.](./media/import-function-app-as-api/add-01.png)

3. Kattintson a **Tallózás** gombra az importálni kívánt függvények kiválasztásához.

    ![Képernyőkép, amely kiemeli a Tallózás gombot.](./media/import-function-app-as-api/add-02.png)

4. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    ![A függvényalkalmazás szakaszt kiemelő képernyőkép.](./media/import-function-app-as-api/add-03.png)

5. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    ![Képernyőfelvétel: az függvényalkalmazás, amelyből a függvényeket importálni kívánja, és a Select (kiválasztás) gombra kattinthat.](./media/import-function-app-as-api/add-04.png)

6. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    ![Képernyőfelvétel: az importálandó függvények és a kiválasztás gomb.](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Kizárólag olyan függvényeket importálhat, amelyek rendelkeznek HTTP-triggerrel, és az engedélyszintjük *Névtelenre* vagy *Függvényre* van állítva.

7. Váltson **teljes** nézetre, és rendelje hozzá a **terméket** az új API-hoz. Szükség esetén más mezőket is megadhat a létrehozás során, vagy később konfigurálhatja őket a **Beállítások** lapon. A beállításokat az [első API-oktatóanyag importálásával és közzétételével](import-and-publish.md#import-and-publish-a-backend-api) foglalkozó cikkben ismertetjük.
8. Kattintson a **Létrehozás** lehetőségre.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Azure-függvényalkalmazás hozzáfűzése egy már létező API-hoz

Kövesse az alábbi lépéseket, hogy egy Azure-függvényalkalmazást hozzáfűzzön egy már létező API-hoz.

1. Az **Azure API Management** szolgáltatáspéldányban válassza az **API-k** lehetőséget a bal oldali menüből.

2. Válassza ki az API-t, amelyet az Azure-függvényalkalmazásba szeretne importálni. Kattintson a **...** elemre, majd válassza ki az **Importálás** elemet a helyi menüből.

    ![Képernyőkép, amely kiemeli az importálás menüpontot.](./media/import-function-app-as-api/append-01.png)

3. Kattintson a **Függvényalkalmazás** csempére.

    ![Képernyőkép a függvényalkalmazás csempe kiemeléséhez.](./media/import-function-app-as-api/append-02.png)

4. Az előugró ablakban kattintson a **Tallózás** gombra.

    ![A Tallózás gombot megjelenítő képernyőkép.](./media/import-function-app-as-api/append-03.png)

5. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    ![Képernyőkép, amely kiemeli a Function apps listáját.](./media/import-function-app-as-api/add-03.png)

6. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    ![Képernyőfelvétel: az a függvényalkalmazás, amelyből a függvényeket importálni kívánja.](./media/import-function-app-as-api/add-04.png)

7. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    ![Képernyőkép, amely kiemeli az importálni kívánt functnios.](./media/import-function-app-as-api/add-05.png)

8. Kattintson az **Importálás** gombra.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Engedély

Az Azure-függvényalkalmazás importáláskor automatikusan létrehozza a következőket:

* A függvényalkalmazáson belüli APIM-{az *Azure API Management szolgáltatási példányának neve*} nevű gazdagép kulcsa,
* Megnevezett érték az Azure API Management példányban, amelynek neve {az *azure függvényalkalmazás példánynév*} – kulcs, amely a létrehozott gazdagép kulcsát tartalmazza.

A 2019. április 4. után létrehozott API-k esetében a gazdagép kulcsát a rendszer a API Management lévő HTTP-kérésekben továbbítja a fejlécben található függvényalkalmazás. A régebbi API-k [lekérdezési paraméterként](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)adják át a gazdagép kulcsát. Ez a viselkedés a `PATCH Backend` függvényalkalmazáshoz társított *háttér* -entitás [REST API hívásával](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) módosítható.

> [!WARNING]
> Az új Azure-függvényalkalmazás gazdagépkulcsának vagy az Azure API Management névvel ellátott értékének eltávolítása vagy megváltoztatása megszakítja a szolgáltatások közti kommunikációt. Az értékek nem szinkronizálódnak automatikusan.
>
> Ha rotálnia kell a gazdagépkulcsot, győződjön meg arról, hogy az Azure API Management névvel ellátott értéke is megváltozik.

### <a name="access-azure-function-app-host-key"></a>Hozzáférés az Azure-függvényalkalmazás gazdagépkulcshoz

1. Nyissa meg az Azure-függvényalkalmazás példányát.

2. Válassza ki a **Függvényalkalmazás-beállítások** lehetőséget az áttekintésből.

    ![Képernyőkép, amely kiemeli a Function apps beállítások lehetőséget.](./media/import-function-app-as-api/keys-02-a.png)

3. A kulcs a **Gazdagépkulcsok** szakaszban található.

    ![Képernyőkép, amely kiemeli a gazdagép kulcsai szakaszt.](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Hozzáférés az Azure API Management névvel ellátott értékéhez

Az Azure API Management példányában válassza a **Névvel ellátott értékek** lehetőséget a bal oldali menüből. Az Azure-függvényalkalmazás kulcsa itt található.

![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Az új API tesztelése a Azure Portal

Az Azure Portalról közvetlenül meghívhat műveleteket. Az Azure Portalon kényelmesen megtekintheti és tesztelheti az API-k műveleteit.  

1. Válassza ki az előző szakaszban létrehozott API-t.

2. Kattintson a **Teszt** fülre.

3. Válasszon ki egy művelet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az egyik fejléc a **OCP-APIM-Subscription-Key**, amely az ehhez az API-hoz társított termék előfizetési kulcsához tartozik. Ha Ön hozta létre az API Management-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 

4. Kattintson a **Küldés** gombra.

    A háttérrendszer a **200 OK** üzenetet és néhány adatot küld válaszként.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
