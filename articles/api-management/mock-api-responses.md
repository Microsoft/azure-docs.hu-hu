---
title: Oktatóanyag – API-válaszok utánozása a API Management – Azure Portal | Microsoft Docs
description: Ebben az oktatóanyagban a API Management beállít egy szabályzatot egy API-n, így az utánzatos választ ad vissza, ha a háttér nem érhető el valós válaszok küldhetővé.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6214594f825908388762ac35cc8c8722ff9135f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874350"
---
# <a name="tutorial-mock-api-responses"></a>Oktatóanyag: API-válaszok utánzata

A háttérbeli API-k importálhatók egy API Management (APIM) API-ba, vagy manuálisan is létrehozhatók és kezelhetők. Az oktatóanyag lépéseiből megtudhatja, hogyan hozhat létre és kezelhet üres API-t az APIM használatával, majd hogyan állíthat be szabályzatot egy API-n, hogy az utánzatos választ ad vissza. Ezzel a metódussal a fejlesztők akkor is folytathatják az APIM-példány megvalósítását és tesztelését, ha a háttérrendszer nem elérhető valós válaszok visszaadása céljából. 

A válaszok utánzható képessége számos forgatókönyvben hasznos lehet:

+ Ha előbb az API előtérrendszere készül el, és a háttérrendszer csak később lesz megvalósítva, vagy ha a háttérrendszer fejlesztése párhuzamosan zajlik.
+ Ha a háttérrendszer ideiglenesen üzemen kívül van vagy nem méretezhető.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési API létrehozása 
> * Művelet hozzáadása a tesztelési API-hoz
> * Válaszutánzás engedélyezése
> * Az utánzott API tesztelése


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Utánzatos API-válasz":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Az [Azure API Management-szabályzatok alapelveinek](api-management-howto-policies.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tesztelési API létrehozása 

A jelen szakaszban ismertetett lépések bemutatják, hogyan hozható létre egy háttérrendszer nélküli üres API. 


1. Jelentkezzen be a Azure Portal, és lépjen a API Management példányhoz.
1. Válassza **az API-k**  >  **+ API-üres API** hozzáadása  >  **lehetőséget.**
1. Az Üres **API létrehozása ablakban** válassza a Teljes **lehetőséget.**
1. A *Megjelenítendő névként adja* meg a Test API **(API tesztelése) nevet.**
1. A **Termékek beállításhoz** válassza a **Korlátlan lehetőséget.**
1. Győződjön meg **arról, hogy** az Átjárók **elemben a Felügyelt van kiválasztva.**
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Üres API létrehozása":::

## <a name="add-an-operation-to-the-test-api"></a>Művelet hozzáadása a tesztelési API-hoz

Egy API egy vagy több műveletet is elérhetővé teszi. Ebben a szakaszban egy műveletet ad hozzá a létrehozott üres API-hoz. A szakasz lépéseinek végrehajtása után a művelet meghívása hibát ad vissza. A válasz utánzás engedélyezése című szakasz későbbi részében található lépések befejezése után nem fog [hibaüzeneteket](#enable-response-mocking) kapni.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki az előző lépésben létrehozott API-t.
1. Válassza a **+ Művelet hozzáadása** lehetőséget.
1. Az **Előtere ablakban** adja meg a következő értékeket.

     | Beállítás             | Érték                             | Leírás                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Megjelenített név**    | *Teszthívás*                       | A fejlesztői portálon [megjelenő név.](api-management-howto-developer-portal.md)                                                                                                                                       |
    | **URL** (HTTP-művelet) | GET                               | Válasszon egyet az előre definiált HTTP-műveletek közül.                                                                                                                                         |
    | **URL-cím**             | */test*                           | Az API URL-címe.                                                                                                                                                                       |
    | **Leírás**     |                                   |  A művelet nem kötelező leírása, amely az API-t használó fejlesztőknek biztosít dokumentációt a fejlesztői portálon.                                                    |
    
1. Válassza **az** URL, a Megjelenítendő név és a Leírás mezők alatt található Válaszok lapot. Adjon meg beállításokat ezen a lapon a válasz állapotkódok, tartalomtípusok, példák és sémák meghatározásához.
1. Válassza **a + Válasz hozzáadása** lehetőséget, majd válassza a **200 OK** lehetőséget a listából.
1. A jobb oldali **Ábrázolások** fejléc alatt válassza a **+ Ábrázolás hozzáadása** lehetőséget.
1. Írja *be az application/json (alkalmazás/json)* mezőt a keresőmezőbe, és válassza ki **az alkalmazás/json** tartalomtípust.
1. A **Minta** szövegmezőbe írja be a következőt: `{ "sampleField" : "test" }`.
1. Kattintson a **Mentés** gombra.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="API-művelet hozzáadása" border="false":::

Bár ebben a példában nem kötelező megadni, az API-műveletek további beállításai más lapokon is konfigurálhatóak, például:


|Tab      |Description  |
|---------|---------|
|**Lekérdezés**     |  Lekérdezési paraméterek hozzáadása. A név és a leírás megadása mellett a lekérdezési paraméterekhez rendelt értékeket is meg lehet adni. Az értékek egyike megjelölhető alapértelmezettként (választható).        |
|**Kérés**     |  A kérések tartalomtípusának, példáinak és sémáinak meghatározása.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdése:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Ha műveletet szeretne hozzáadni a tesztelési API-hoz, futtassa [az az apim api operation create](/cli/azure/apim/api/operation#az_apim_api_operation_create) parancsot:

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Futtassa [az az apim api operation list parancsot](/cli/azure/apim/api/operation#az_apim_api_operation_list) az API összes műveletének a listájához:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Egy művelet eltávolításához használja az [az apim api operation delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) parancsot. Szerezze be a műveletazonosítót az előző parancsból.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Ezt a műveletet a cikk további részében is használhatja.

---

## <a name="enable-response-mocking"></a>Válaszutánzás engedélyezése

1. Válassza ki a Teszt API létrehozása során [létrehozott API-t.](#create-a-test-api)
1. Válassza ki a hozzáadott tesztműveletet.
1. Győződjön meg arról, hogy a jobb oldalon található ablakban a **Tervezés** lap van kiválasztva.
1. A Bejövő **feldolgozás ablakban** válassza a + Szabályzat **hozzáadása lehetőséget.**

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Feldolgozási szabályzat hozzáadása" border="false":::

1. Válassza **a válaszok utánzata**  lehetőséget a katalógusból.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Válaszok utánzása szabályzat csempéje" border="false":::

1. **Az API Management válaszának** szövegmezőjébe írja be a következőt: **200 OK, alkalmazás/json**. Ez a beállítás azt jelzi, hogy az API az előző szakaszban definiált mintaválaszt adja vissza.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Utánzás válaszának beállítása":::

1. Kattintson a **Mentés** gombra.

    > [!TIP]
    > Ha az API-hoz engedélyezve van az Utánzás szöveg, egy sárga sáv jelzi, [](api-management-advanced-policies.md#mock-response) hogy a API Management által visszaadott válaszokat az utánzás szabályzat utánzata utánzata jelzi, és a háttér nem hoz létre. 

## <a name="test-the-mocked-api"></a>Az utánzott API tesztelése

1. Válassza ki a Teszt API létrehozása során [létrehozott API-t.](#create-a-test-api)
1. Kattintson a **Teszt** fülre.
1. Győződjön meg róla, hogy a **Teszthívás** API van kiválasztva. Teszthívás indításához válassza a **Küldés** lehetőséget.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Az utánzott API tesztelése":::

1. A **HTTP-válasz** mező az oktatóanyag első szakaszában mintaként megadott JSON-t jeleníti meg.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="HTTP-válasz utánzata":::

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
