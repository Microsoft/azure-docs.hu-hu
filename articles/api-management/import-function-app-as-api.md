---
title: Azure-függvényalkalmazás importálása API-ként az Azure API Management használatával | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet az Azure API Management használatával Azure-függvényalkalmazásokat API-ként importálni.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: apimpm
ms.openlocfilehash: f9e5c531e387ba8d2c61c6d46b1e8935bc7d42cf
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67429039"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure-függvényalkalmazás importálása API-ként az Azure API Management használatával

Az Azure API Management az Azure-függvényalkalmazások új API-ként való importálását vagy a létező API-khoz való hozzáfűzését támogatja. A folyamat automatikusan létrehoz egy gazdagépkulcsot az Azure-függvényalkalmazásban, amelyet aztán az Azure API Management hozzárendel egy névvel ellátott értékhez.

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
* Győződjön meg arról, hogy az előfizetése tartalmaz egy Azure Functions-alkalmazást. További információkért lásd az [Azure-függvényalkalmazás létrehozását](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) ismertető cikket. Tartalmaznia kell egy HTTP-triggerrel ellátott függvényt, és az engedélyszintnek *Névtelenre* vagy *Függvényre* állítva kell lennie.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Azure-függvényalkalmazás importálása új API-ként

Kövesse az alábbi lépéseket egy új API létrehozásához egy Azure-függvényalkalmazásból.

1. Az **Azure API Management** szolgáltatáspéldányban válassza az **API-k** lehetőséget a bal oldali menüből.

2. Az **Új API hozzáadása** listából válassza ki a **Függvényalkalmazást**.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-01.png)

3. Kattintson a **Tallózás** gombra az importálni kívánt függvények kiválasztásához.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-02.png)

4. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-03.png)

5. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-04.png)

6. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Kizárólag olyan függvényeket importálhat, amelyek rendelkeznek HTTP-triggerrel, és az engedélyszintjük *Névtelenre* vagy *Függvényre* van állítva.

7. Váltson **teljes** nézetre, és rendelje hozzá a **terméket** az új API-hoz. Szerkessze az előre kitöltött mezőket, ha szükséges.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-06.png)

8. Kattintson a **Create** (Létrehozás) gombra.

## <a name="append-azure-function-app-to-api"></a> Azure-függvényalkalmazás hozzáfűzése egy már létező API-hoz

Kövesse az alábbi lépéseket, hogy egy Azure-függvényalkalmazást hozzáfűzzön egy már létező API-hoz.

1. Az **Azure API Management** szolgáltatáspéldányban válassza az **API-k** lehetőséget a bal oldali menüből.

2. Válassza ki az API-t, amelyet az Azure-függvényalkalmazásba szeretne importálni. Kattintson a **...** elemre, majd válassza ki az **Importálás** elemet a helyi menüből.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-01.png)

3. Kattintson a **Függvényalkalmazás** csempére.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-02.png)

4. Az előugró ablakban kattintson a **Tallózás** gombra.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-03.png)

5. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-03.png)

6. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-04.png)

7. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-05.png)

8. Kattintson az **Importálás** elemre.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a> Engedélyezési

Az Azure-függvényalkalmazás importáláskor automatikusan létrehozza a következőket:

* a függvény alkalmazásban, az a név az apim - állomás kulcsát {*az Azure API Management szolgáltatás példánynév*},
* megnevezett érték belül az Azure API Management-példány nevét {*az Azure-Függvényalkalmazás példánynév*}-kulcs, amely tartalmazza a létrehozott állomás kulcsát.

Április 4. 2019 után létrehozott API-t a-állomás kulcsát kerül átadásra a HTTP-kérések az API Management egy fejléc a Függvényalkalmazást. Régebbi API-k adja át a gazdagép kulcsot [egy lekérdezési paraméter](../azure-functions/functions-bindings-http-webhook.md#api-key-authorization). Ez a viselkedés keresztül lehet módosítani a `PATCH Backend` [REST API-hívás](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend/update#backendcredentialscontract) a a *háttérrendszer* a Függvényalkalmazás társított entitás.

> [!WARNING]
> Az új Azure-függvényalkalmazás gazdagépkulcsának vagy az Azure API Management névvel ellátott értékének eltávolítása vagy megváltoztatása megszakítja a szolgáltatások közti kommunikációt. Az értékek nem szinkronizálódnak automatikusan.
>
> Ha rotálnia kell a gazdagépkulcsot, győződjön meg arról, hogy az Azure API Management névvel ellátott értéke is megváltozik.

### <a name="access-azure-function-app-host-key"></a>Hozzáférés az Azure-függvényalkalmazás gazdagépkulcshoz

1. Nyissa meg az Azure-függvényalkalmazás példányát.

2. Válassza ki a **Függvényalkalmazás-beállítások** lehetőséget az áttekintésből.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-02-a.png)

3. A kulcs a **Gazdagépkulcsok** szakaszban található.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Hozzáférés az Azure API Management névvel ellátott értékéhez

Az Azure API Management példányában válassza a **Névvel ellátott értékek** lehetőséget a bal oldali menüből. Az Azure-függvényalkalmazás kulcsa itt található.

![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a> Az új API Management API tesztelése az Azure Portalon

Az Azure Portalról közvetlenül meghívhat műveleteket. Az Azure Portalon kényelmesen megtekintheti és tesztelheti az API-k műveleteit.  

1. Válassza ki az előző szakaszban létrehozott API-t.

2. Kattintson a **Teszt** fülre.

3. Válasszon ki egy művelet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az egyik fejléc az **Ocp-Apim-Subscription-Key**, amely az ehhez az API-hoz társított termék előfizetési kulcsára vonatkozik. Ha Ön hozta létre az API Management-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 

4. Kattintson a **Küldés** gombra.

    A háttérrendszer a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="test-in-developer-portal"></a> Művelet meghívása a fejlesztői portálról

A műveletek meghívhatók a fejlesztői portálról is az API-k teszteléséhez. 

1. Válassza ki az importálás létrehozott API- és a egy háttérrendszeri API közzététele.

2. Válassza ki a **fejlesztői portált**.

    Megnyílik a fejlesztői portál webhelye.

3. Jelölje ki a létrehozott **API**-t.

4. Válassza ki a tesztelni kívánt műveletet.

5. Kattintson a **Kipróbálás** lehetőségre.

6. Kattintson a **Küldés** gombra.
    
    A művelet meghívása után a fejlesztői portál megjeleníti a **Válasz állapota**, a **Válasz fejlécei** és a **Válasz tartalma** minden információját.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
