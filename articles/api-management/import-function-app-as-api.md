---
title: Azure-függvényalkalmazás importálása API-ként a API Management
titleSuffix: Azure API Management
description: Ez a cikk bemutatja, hogyan importálhat Azure-függvényalkalmazást API API Management azure-ba.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599288"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure-függvényalkalmazás importálása API-ként az Azure API Management használatával

Az Azure API Management az Azure-függvényalkalmazások új API-ként való importálását vagy a meglévő API-khoz való hozzáfűzését támogatja. A folyamat automatikusan létrehoz egy gazdagépkulcsot az Azure-függvényalkalmazásban, amelyet aztán az Azure API Management hozzárendel egy névvel ellátott értékhez.

Ez a cikk egy Azure-függvényalkalmazás API-ként történő importálását és tesztelését API Management. 

Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Azure-függvényalkalmazás importálása API-ként
> * Azure-függvényalkalmazás hozzáfűzése egy API-hoz
> * Az új Azure-függvényalkalmazás gazdagépkulcsának és az Azure API Management névvel ellátott értékének megtekintése
> * Az API tesztelése az Azure Portalon

## <a name="prerequisites"></a>Előfeltételek

* A Create [an Azure API Management instance (Azure-példány API Management létrehozása)](get-started-create-service-instance.md) rövid útmutatót.
* Győződjön meg arról, hogy az előfizetése tartalmaz egy Azure Functions-alkalmazást. További információkért lásd az [Azure-függvényalkalmazás létrehozását](../azure-functions/functions-get-started.md) ismertető cikket. A függvények http-eseményindítóval, az engedélyezési szintnek *pedig Névtelen vagy Függvény beállítással* kell *működnie.*

> [!NOTE]
> Az API-k importálására és API Management Code-hoz Visual Studio bővítményt használhatja. Kövesse a [API Management bővítmény oktatóanyagát a](visual-studio-code-tutorial.md) telepítéshez és az első lépésekhez.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Azure-függvényalkalmazás importálása új API-ként

Kövesse az alábbi lépéseket egy új API létrehozásához egy Azure-függvényalkalmazásból.

1. Lépjen a API Management szolgáltatásra a Azure Portal, és válassza az **API-k** lehetőséget a menüből.

2. Az **Új API hozzáadása** listából válassza ki a **Függvényalkalmazást**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="A Függvényalkalmazás csempét bemutató képernyőkép.":::

3. Kattintson a **Tallózás** gombra az importálni kívánt függvények kiválasztásához.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Képernyőkép a Tallózás gomb kiemelésről.":::

4. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Képernyőkép a Függvényalkalmazás szakasz kiemeléséhez.":::

5. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Képernyőkép a Függvények importálásához importálni kívánt függvényalkalmazásról és a Kijelölés gombról.":::

6. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.
    * Függvényeket csak névtelen vagy függvényengedélyezési szintekkel működő HTTP-eseményindító alapján *importálhat.* 

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Képernyőkép az importálni kívánt függvények és a Kijelölés gomb kiemelésről.":::

7. Váltson **teljes** nézetre, és rendelje hozzá a **terméket** az új API-hoz. 
8. Szükség esetén a létrehozás során adjon meg más mezőket, vagy konfigurálja őket később a **Beállítások lapon.** 
    * A beállításokat az első API importálását és közzétételét [ismertető oktatóanyagban ismertetheti.](import-and-publish.md#import-and-publish-a-backend-api)

    >[!NOTE]
    > A termékek a fejlesztők számára a fejlesztői portálon keresztül kínált egy vagy több API társításai. Először is a fejlesztőknek elő kell fizetniük egy termékre az API-hoz való hozzáféréshez. Az előfizetés után a termék bármely API-jának előfizetői kulcsát lekértük. A API Management létrehozója rendszergazda, és alapértelmezés szerint minden termékre elő van fizetve.
    >
    > Minden API Management-példány két alapértelmezett mintaterméket tartalmaz:
    > - **Kezdő**
    > - **Korlátlan**

9. Kattintson a **Létrehozás** lehetőségre.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Azure-függvényalkalmazás hozzáfűzése egy már létező API-hoz

Kövesse az alábbi lépéseket, hogy egy Azure-függvényalkalmazást hozzáfűzzön egy már létező API-hoz.

1. Az **Azure API Management** szolgáltatáspéldányban válassza az **API-k** lehetőséget a bal oldali menüből.

2. Válassza ki az API-t, amelyet az Azure-függvényalkalmazásba szeretne importálni. Kattintson a **...** elemre, majd válassza ki az **Importálás** elemet a helyi menüből.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Képernyőkép az Importálás menüelem kiemelésről.":::

3. Kattintson a **Függvényalkalmazás** csempére.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Képernyőkép a Függvényalkalmazás csempéről.":::

4. Az előugró ablakban kattintson a **Tallózás** gombra.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="A Tallózás gombot bemutató képernyőkép.":::

5. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Képernyőkép a függvényalkalmazások listájáról.":::

6. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Képernyőkép arról a függvényalkalmazásról, amelyből függvényeket kíván importálni.":::

7. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Képernyőkép az importálni szükséges függvények kiemelésről.":::

8. Kattintson az **Importálás gombra.**

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Hozzáfűzés függvényalkalmazásból":::

## <a name="authorization"></a><a name="authorization"></a> Engedélyezési

Az Azure-függvényalkalmazás importáláskor automatikusan létrehozza a következőket:

* Gazdakulcs a függvényalkalmazásban apim-{*az Azure API Management-példány neve*},
* Az Azure-függvénypéldányon belüli neves API Management{*az Azure-függvényalkalmazás* példányának neve }-key névvel, amely a létrehozott gazdakulcsot tartalmazza.

A 2019. április 4. után létrehozott API-k esetén a gazdakulcsot a rendszer a API Management http-kéréseiben egy fejlécben továbbadja a függvényalkalmazásnak. A régebbi API-k lekérdezési paraméterként [adja át a gazdagépkulcsot.](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization) Ezt a viselkedést a `PATCH Backend` [függvényalkalmazáshoz](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) társított  háttérentitás REST API keresztül módosíthatja.

> [!WARNING]
> Ha eltávolítja vagy módosítja az Azure-függvényalkalmazás gazdagépkulcsának értékét vagy az Azure API Management nevű érték, az megszakítja a szolgáltatások közötti kommunikációt. Az értékek nem szinkronizálódnak automatikusan.
>
> Ha rotálnia kell a gazdagépkulcsot, győződjön meg arról, hogy az Azure API Management névvel ellátott értéke is megváltozik.

### <a name="access-azure-function-app-host-key"></a>Hozzáférés az Azure-függvényalkalmazás gazdagépkulcshoz

1. Nyissa meg az Azure-függvényalkalmazás példányát.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Képernyőkép a függvényalkalmazás-példány kiválasztásáról.":::

2. Az oldalsó **navigációs menü Függvények** szakaszában válassza az **Alkalmazáskulcsok lehetőséget.**

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Képernyőkép a Függvényalkalmazások beállítási lehetőség kiemeléséhez.":::

3. Keresse meg a kulcsokat a **Gazdagépkulcsok szakaszban.**

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Képernyőkép a Gazdagépkulcsok szakaszról.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Hozzáférés az Azure API Management névvel ellátott értékéhez

Az Azure API Management példányában válassza a **Névvel ellátott értékek** lehetőséget a bal oldali menüből. Az Azure-függvényalkalmazás kulcsa itt található.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Hozzáadás függvényalkalmazásból":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Az új API tesztelése a Azure Portal

Az Azure Portalról közvetlenül meghívhat műveleteket. Az Azure Portalon kényelmesen megtekintheti és tesztelheti az API-k műveleteit.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Képernyőkép a tesztelési eljárás kiemeléséről.":::

1. Válassza ki az előző szakaszban létrehozott API-t.

2. Kattintson a **Teszt** fülre.

3. Válassza ki a tesztelni kívánt műveletet.

    * Az oldal megjeleníti a lekérdezési paraméterek és fejlécek mezőit. 
    * Az egyik fejléc az "Ocp-Apim-Subscription-Key" az EHHEZ az API-hoz társított termék-előfizetői kulcshoz. 
    * A API Management létrehozója már rendszergazda, így a kulcs automatikusan ki lesz töltve. 

4. Kattintson a **Küldés** gombra.

    * Ha a teszt sikeres, a háttér **200 OK** és néhány adat alapján válaszol.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
