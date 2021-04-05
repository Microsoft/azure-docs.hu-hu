---
title: 'Rövid útmutató: az első REST API lekérdezés'
description: Ebben a rövid útmutatóban követheti az erőforrás-gráf végpontjának meghívásához REST API és az első lekérdezés futtatásához szükséges lépéseket.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920054"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a REST API használatával

Az Azure Resource Graph REST API használatával történő használatának első lépése annak a lehetősége, hogy az elérhető REST API-k meghívására szolgáló eszközzel rendelkezzen. Ez a rövid útmutató végigvezeti a lekérdezés futtatásának folyamatán, valamint az eredmények lekérésének lépésein az Azure Resource Graph REST API-végpontjának meghívásával.

A folyamat végén a REST API végpontok meghívásához és az első Resource Graph-lekérdezés futtatásához szükséges eszközök állnak rendelkezésére.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Ismerkedés a REST API-val

Ha még nem ismeri a REST API-t, első lépésként tekintse át az [Azure REST API-referenciát](/rest/api/azure/) a REST API általános bemutatásáért, és fordítson különös figyelmet a kérések URI-azonosítójára és törzsére. Ez a cikk ezeket a fogalmakat használja az Azure Resource Graph használatához szükséges utasítások megadásához, és feltételezi a velük kapcsolatos ismereteket. Az [ARMClient](https://github.com/projectkudu/ARMClient) és hasonló eszközökkel automatikusan kezelhető az engedélyezés, ezért a kezdők számára mindenképp javasoljuk ezek használatát.

Az Azure Resource Graph specifikációi: [Azure Resource graph REST API](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>A REST API és a PowerShell

Ha még nem választott eszközt a REST API-hívások kezeléséhez, ennek az útmutatónak a keretében érdemes a PowerShellt használnia. Az alábbi mintakód egy fejlécet kap az Azure-beli hitelesítéshez. Hozzon létre egy hitelesítési fejlécet, vagy más néven **tulajdonosi jogkivonatot**, és adja meg a kapcsolódáshoz szükséges REST API URI-t a paraméterekkel vagy egy **kérelemtörzzsel**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Cserélje le `{subscriptionId}` a `$restUri` változót az előfizetésre vonatkozó információk lekéréséhez.
A `$response` változó a parancsmag eredményét tartalmazza `Invoke-RestMethod` , amely olyan parancsmagokkal értelmezhető, mint például a [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json). Ha a REST API szolgáltatásvégpontja **kérelemtörzset** vár, adjon meg egy JSON formátumú változót az `Invoke-RestMethod``-Body` paraméterében.

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Ha a környezetéhez hozzáadott REST API eszközökkel rendelkezik, itt az ideje, hogy kipróbáljon egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

Az egyes REST API hívásokhoz tartozó kérelem törzsében van egy változó, amelyet a saját értékkel kell helyettesíteni:

- `{subscriptionID}` – Cserélje le az előfizetése azonosítójára

1. Futtassa az első Azure Resource Graph-lekérdezést a REST API és a `resources` végpont használatával:

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Kérelem törzse

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem tartalmaz olyan rendezési módosítót, mint a `order by` , a lekérdezés többszöri futtatása valószínűleg egy másik erőforrás-készletet eredményez.

1. Frissítse a hívást a `resouces` végpontra, és módosítsa a **lekérdezést** a `order by` Name ( **név** ) tulajdonságra:

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Kérelem törzse

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Frissítse a végpontra irányuló hívást, `resources` és módosítsa a **lekérdezést** a `order by` Name ( **név** ) tulajdonságra, majd `limit` az első öt találatra:

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Kérelem törzse

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

Az Azure Resource Graph REST API-hívásával kapcsolatos további példákért tekintse meg az [Azure Resource Graph Rest-példákat](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

REST API nem rendelkezik az eltávolításhoz szükséges könyvtárakkal vagy modulokkal. Ha olyan eszközt telepített, mint például a _ARMClient_ vagy a _Poster_ a hívások elvégzéséhez, és a továbbiakban nincs szüksége rá, akkor az eszközt most már el is távolíthatja.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megismerte az erőforrás-gráf REST API végpontját, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
