---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a különböző SDK-k hibáit, miközben Azure-erőforrásokat kérdez le az Azure Resource Graph használatával.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0d783b9cb4fa30e3c3e0ff82536b4878c29c7a0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916701"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Hibák elhárítása az Azure Resource Graph használatával

Az Azure-erőforrások Azure Resource Graph-ban való lekérdezése során hibák léphetnek fel. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

A legtöbb hiba az Azure Resource Graph-lekérdezés futtatásakor felmerülő probléma eredménye. Ha egy lekérdezés meghiúsul, az SDK részletesen ismerteti a sikertelen lekérdezés részleteit. Ez az információ azt jelzi, hogy a probléma kijavítható, és egy későbbi lekérdezés sikeres lehet.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>Forgatókönyv: szabályozott kérelmek

#### <a name="issue"></a>Probléma

Azok az ügyfelek, amelyek nagy vagy gyakori erőforrás-lekérdezéseket igényelnek, szabályozva vannak.

#### <a name="cause"></a>Ok

Az Azure Resource Graph az egyes felhasználók számára egy adott időtartományon alapuló kvóta-számot foglal le. A felhasználók például legfeljebb 15 lekérdezést küldhetnek minden 5 másodperces ablakban a szabályozás nélkül. A kvóta értékét számos tényező határozza meg, és a változás változhat. További információ: [szabályozás az Azure Resource Graph-ban](../overview.md#throttling).

#### <a name="resolution"></a>Feloldás

Több módszer is létezik a szabályozott kérelmek kezelésére:

- [Lekérdezések csoportosítása](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [Lekérdezések eltolása](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [Lekérdezés párhuzamosan](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [Tördelés](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Forgatókönyv: túl sok előfizetés

#### <a name="issue"></a>Probléma

Azok az ügyfelek, akik több mint 1000 előfizetéshez férnek hozzá, beleértve az [Azure Lighthouse](../../../lighthouse/overview.md)-val való több-bérlős előfizetést, az Azure Resource Graph egyetlen hívásával nem hívhatják le az összes előfizetést.

#### <a name="cause"></a>Ok

Az Azure CLI és a PowerShell csak az első 1000-előfizetést továbbítja az Azure Resource Graph-ba. Az Azure Resource Graph REST API maximális számú előfizetést fogad el a lekérdezés végrehajtásához.

#### <a name="resolution"></a>Feloldás

Batch-kérelmek a lekérdezéshez az előfizetések egy olyan részhalmazával, amely az 1000-os előfizetési korlát alatt marad. A megoldás az **előfizetés** paramétert használja a PowerShellben.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Forgatókönyv: nem támogatott Content-Type REST-fejléc

#### <a name="issue"></a>Probléma

Az Azure Resource Graph REST API lekérdező ügyfelek megkapják a _500_ (belső kiszolgálóhiba) kapott választ.

#### <a name="cause"></a>Ok

Az Azure Resource Graph REST API csak `Content-Type` az **alkalmazás-vagy JSON-** t támogatja. Néhány REST-eszköz vagy-ügynök alapértelmezett értéke **text/plain**, amelyet a REST API nem támogat.

#### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy az Azure Resource Graph lekérdezéséhez használt eszköz vagy ügynök rendelkezik-e az `Content-Type` **alkalmazáshoz/JSON-** hoz konfigurált REST API fejléctel.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Forgatókönyv: nincs olvasási engedély a listában szereplő összes előfizetéshez

#### <a name="issue"></a>Probléma

Azok az ügyfelek, akik explicit módon átadják egy Azure Resource Graph-lekérdezéssel rendelkező előfizetések listáját, _403_ (tiltott) választ kapnak.

#### <a name="cause"></a>Ok

Ha az ügyfél nem rendelkezik olvasási engedéllyel az összes megadott előfizetéshez, a rendszer megtagadja a kérést a megfelelő biztonsági jogosultságok hiánya miatt.

#### <a name="resolution"></a>Feloldás

Vegyen fel legalább egy előfizetést az előfizetések listájában, amelyet a lekérdezést futtató ügyfélnek legalább olvasási hozzáféréssel kell rendelkezniük. További információ: [engedélyek az Azure Resource Graph-ban](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal csatlakozhat a felhasználói élmény fokozásához: válaszok, támogatás és szakértők.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.