---
title: Oktatóanyag – az API-válaszok modellezése API Managementban – Azure Portal | Microsoft Docs
description: Ebben az oktatóanyagban az API Management használatával állít be egy szabályzatot egy API-ra, így egy kigúnyolt választ ad vissza, ha a háttér nem érhető el valódi válaszok küldéséhez.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 75727d139242e1b537505d2ed907ae20fc5479f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100547253"
---
# <a name="tutorial-mock-api-responses"></a>Oktatóanyag: API-válaszok modellezése

A háttérbeli API-k importálhatók egy API Management (APIM) API-ba, vagy manuálisan is létrehozható és felügyelhető. Az ebben az oktatóanyagban szereplő lépések bemutatják, hogyan hozhat létre üres API-t az APIM használatával, és hogyan kezelheti manuálisan, majd beállíthat egy szabályzatot egy API-ra, hogy egy kigúnyolt választ ad vissza. Ezzel a metódussal a fejlesztők akkor is folytathatják az APIM-példány megvalósítását és tesztelését, ha a háttérrendszer nem elérhető valós válaszok visszaadása céljából. 

A válaszok kimodellezésének képessége számos esetben hasznos lehet:

+ Ha előbb az API előtérrendszere készül el, és a háttérrendszer csak később lesz megvalósítva, vagy ha a háttérrendszer fejlesztése párhuzamosan zajlik.
+ Ha a háttérrendszer ideiglenesen üzemen kívül van vagy nem méretezhető.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési API létrehozása 
> * Művelet hozzáadása a tesztelési API-hoz
> * Válaszutánzás engedélyezése
> * Az utánzott API tesztelése


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Kigúnyolt API-válasz":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Az [Azure API Management-szabályzatok alapelveinek](api-management-howto-policies.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tesztelési API létrehozása 

A jelen szakaszban ismertetett lépések bemutatják, hogyan hozható létre egy háttérrendszer nélküli üres API. 


1. Jelentkezzen be a Azure Portalba, és navigáljon a API Management-példányhoz.
1. Válassza **az API-** k  >  **+ API-k hozzáadása**  >  **üres API** elemet.
1. Az **üres API létrehozása** ablakban válassza a **teljes** lehetőséget.
1. Adja meg a **megjelenítendő névhez** tartozó *teszt API* -t.
1. Válassza a **korlátlan** lehetőséget a **termékek** számára.
1. Győződjön meg arról, hogy a **felügyelt** be van jelölve az **átjárók** között
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Üres API létrehozása":::

## <a name="add-an-operation-to-the-test-api"></a>Művelet hozzáadása a tesztelési API-hoz

Az API-k egy vagy több műveletet tesznek elérhetővé. Ebben a szakaszban adjon hozzá egy műveletet a létrehozott üres API-hoz. A szakasz lépéseinek végrehajtása után a művelet meghívása hibát ad vissza. A válaszok kiírásának [engedélyezése](#enable-response-mocking) szakasz későbbi lépéseinek elvégzése után nem fog hibaüzeneteket kapni.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki az előző lépésben létrehozott API-t.
1. Válassza a **+ Művelet hozzáadása** lehetőséget.
1. A **frontend** ablakban adja meg a következő értékeket.

     | Beállítás             | Érték                             | Leírás                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Megjelenített név**    | *Teszthívás*                       | A [fejlesztői portálon](api-management-howto-developer-portal.md)megjelenő név.                                                                                                                                       |
    | **URL** (HTTP-művelet) | GET                               | Válassza ki az előre definiált HTTP-műveletek egyikét.                                                                                                                                         |
    | **URL-cím**             | */test*                           | Az API URL-címe.                                                                                                                                                                       |
    | **Leírás**     |                                   |  A művelet opcionális leírása, amely az API-t használó fejlesztők számára biztosít dokumentációt a fejlesztői portálon.                                                    |
    
1. Válassza a **válaszok** lapot az URL-cím, a megjelenítendő név és a Leírás mezők alatt. Adja meg az ezen a lapon megjelenő beállításokat a válasz állapotkódok, a tartalomtípusok, a példák és a sémák meghatározásához.
1. Válassza a **+ válasz hozzáadása** lehetőséget, majd a listából válassza a **200 OK** elemet.
1. A jobb oldali **Ábrázolások** fejléc alatt válassza a **+ Ábrázolás hozzáadása** lehetőséget.
1. Adja meg az *Application/JSON* kifejezést a keresőmezőbe, és válassza ki az **alkalmazás/JSON** tartalomtípust.
1. A **Minta** szövegmezőbe írja be a következőt: `{ "sampleField" : "test" }`.
1. Kattintson a **Mentés** gombra.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="API-művelet hozzáadása" border="false":::

Bár ehhez a példához nem szükséges, az API-műveletek további beállításait más lapokon is konfigurálhatja, többek között a következőket:


|Tab      |Description  |
|---------|---------|
|**Lekérdezés**     |  Lekérdezési paraméterek hozzáadása A név és a Leírás megadása mellett a lekérdezési paraméterekhez rendelt értékeket is megadhat. Az értékek egyike megjelölhető alapértelmezettként (választható).        |
|**Kérés**     |  A kérelem tartalmának típusai, példák és sémák definiálása.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdéséhez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Ha műveletet szeretne hozzáadni a tesztelési API-hoz, futtassa az az [APIM API Operation Create](/cli/azure/apim/api/operation#az_apim_api_operation_create) parancsot:

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Futtassa az az [APIM API Operation List](/cli/azure/apim/api/operation#az_apim_api_operation_list) parancsot az API-k összes műveletének megtekintéséhez:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Egy művelet eltávolításához használja az az [APIM API Operation delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) parancsot. A művelet AZONOSÍTÓjának beolvasása az előző parancsból.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Tartsa meg ezt a műveletet a cikk további részében való használatra.

---

## <a name="enable-response-mocking"></a>Válaszutánzás engedélyezése

1. Válassza ki a [tesztelési API létrehozása](#create-a-test-api)területen létrehozott API-t.
1. Válassza ki a hozzáadott tesztműveletet.
1. A jobb oldali ablakban ellenőrizze, hogy a **tervezés** lap van-e kiválasztva.
1. A **bejövő feldolgozás** ablakban válassza a **+ házirend hozzáadása** lehetőséget.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Feldolgozási házirend hozzáadása" border="false":::

1. Válassza ki a katalógusbeli **válaszokat**  a gyűjteményből.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Válaszok utánzása szabályzat csempéje" border="false":::

1. **Az API Management válaszának** szövegmezőjébe írja be a következőt: **200 OK, alkalmazás/json**. Ez a beállítás azt jelzi, hogy az API az előző szakaszban definiált mintaválaszt adja vissza.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Kigúnyolás válaszának beállítása":::

1. Kattintson a **Mentés** gombra.

    > [!TIP]
    > Az API-hoz **tartozó szöveg kiírásával rendelkező sárga** sáv azt jelzi, hogy az API Management által visszaadott válaszokat a kiírási [szabályzat](api-management-advanced-policies.md#mock-response) kigúnyolja, és nem a háttérrendszer állítja elő.

## <a name="test-the-mocked-api"></a>Az utánzott API tesztelése

1. Válassza ki a [tesztelési API létrehozása](#create-a-test-api)területen létrehozott API-t.
1. Kattintson a **Teszt** fülre.
1. Győződjön meg róla, hogy a **Teszthívás** API van kiválasztva. Teszthívás indításához válassza a **Küldés** lehetőséget.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Az utánzott API tesztelése":::

1. A **HTTP-válasz** mező az oktatóanyag első szakaszában mintaként megadott JSON-t jeleníti meg.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="HTTP-válasz modellezése":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tesztelési API létrehozása
> * Művelet hozzáadása a tesztelési API-hoz
> * Válaszutánzás engedélyezése
> * Az utánzott API tesztelése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
