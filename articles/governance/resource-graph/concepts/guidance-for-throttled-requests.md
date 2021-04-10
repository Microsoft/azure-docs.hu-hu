---
title: Útmutatás szabályozott kérésekhez
description: Megtudhatja, hogyan csoportosíthat, lépcsőzetesen, oldalszámozást és lekérdezéseket végezhet párhuzamosan, hogy elkerülje az Azure Resource Graph által szabályozott kérelmeket.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd3cf4d411733e831c94039c3bc9aeaf0e95271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917707"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Útmutató a szabályozott kérelmekhez az Azure Resource Graph-ban

Ha programozott és gyakori Azure Resource Graph-alapú adathasználatot hoz létre, érdemes figyelembe venni, hogy a szabályozás milyen hatással van a lekérdezések eredményeire. Az adatkérések módosításának megváltoztatásával a szervezet elkerülheti a szabályozást, és megtarthatja az Azure-erőforrásokkal kapcsolatos időben továbbított adatmennyiséget.

Ez a cikk az Azure Resource Graph lekérdezések létrehozásával kapcsolatos négy területet és mintázatot ismerteti:

- Szabályozási fejlécek ismertetése
- Lekérdezések csoportosítása
- Lekérdezések eltolása
- A tördelés hatása

## <a name="understand-throttling-headers"></a>Szabályozási fejlécek ismertetése

Az Azure Resource Graph az egyes felhasználók számára egy adott időtartományon alapuló kvóta-számot foglal le. A felhasználók például legfeljebb 15 lekérdezést küldhetnek minden 5 másodperces ablakban a szabályozás nélkül. A kvóta értékét számos tényező határozza meg, és a változás változhat.

Az Azure Resource Graph minden lekérdezési válaszban két szabályozási fejlécet hoz létre:

- `x-ms-user-quota-remaining` (int): a felhasználó fennmaradó erőforrás-kvótája. Ez az érték leképezi a lekérdezések darabszámát.
- `x-ms-user-quota-resets-after` (óó: PP: mm): az időtartam, amíg a felhasználó kvótájának felhasználását vissza nem állítja.

Ha egy rendszerbiztonsági tag több mint 5000 előfizetéshez fér hozzá a bérlői vagy a felügyeleti csoport [lekérdezési hatókörén](./query-language.md#query-scope)belül, a válasz az első 5000-előfizetésre korlátozódik, és a `x-ms-tenant-subscription-limit-hit` fejlécet adja vissza `true` .

Ha szeretné bemutatni, hogyan működnek a fejlécek, nézzük meg egy lekérdezési választ, amely a és a fejlécét és értékeit is tartalmazta `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03` .

- A következő 3 másodpercen belül legfeljebb 10 lekérdezés lehet elküldve a szabályozás nélkül.
- 3 másodpercen belül a és a értékeket a és a értékre `x-ms-user-quota-remaining` `x-ms-user-quota-resets-after` állítja vissza a rendszer `15` `00:00:05` .

Ha szeretné megtekinteni, hogyan használhatja a fejléceket a lekérdezési kérelmek _leállítási_ , tekintse meg [párhuzamosan a lekérdezésben](#query-in-parallel)szereplő mintát.

## <a name="grouping-queries"></a>Lekérdezések csoportosítása

A lekérdezéseket az előfizetés, az erőforráscsoport vagy az egyes erőforrások hatékonyabban csoportosítják, mint a tetszés-lekérdezések. A nagyobb lekérdezések kvóta-díja általában kisebb, mint a több kis-és a célként megadott lekérdezések kvótájának díja. A csoport mérete ajánlott _300_-nál kisebb.

- Példa a rosszul optimalizált megközelítésre

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Példa az optimalizált csoportosítási megközelítés #1ára

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Példa #2 egy optimalizált csoportosítási megközelítésre egy lekérdezés több erőforrásának beolvasásához

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Lekérdezések eltolása

A szabályozás érvénybe léptetése miatt a lekérdezéseket érdemes lépcsőzetesen megfogalmazni. Vagyis a 60-lekérdezések egyidejű küldése helyett a lekérdezéseket a 4 5-Second Windowsba kell osztani:

- Nem lépcsőzetes lekérdezés ütemezése

  | Lekérdezések száma         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Időtartam (mp) | 0-5 | 5-10 | 10-15 | 15-20 |

- Lépcsőzetes lekérdezés ütemezése

  | Lekérdezések száma         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Időtartam (mp) | 0-5 | 5-10 | 10-15 | 15-20 |

Íme egy példa arra, hogy miként kell betartani a szabályozási fejléceket az Azure Resource Graph lekérdezése során:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Lekérdezés párhuzamosan

Annak ellenére, hogy a csoportosítás ajánlott a párhuzamos-on keresztül, vannak olyan időpontok, amikor a lekérdezések nem könnyen csoportosíthatók. Ezekben az esetekben érdemes lehet lekérdezni az Azure Resource Graphot több lekérdezés párhuzamos módon történő elküldésével. Íme egy példa arra, hogyan _leállítási_ az ilyen helyzetekben a fejlécek szabályozása:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Oldalra tördelés

Mivel az Azure Resource Graph egyetlen lekérdezési válaszban legfeljebb 1000 bejegyzést ad vissza, lehetséges, hogy a lekérdezéseket [oldalszámozással](./work-with-data.md#paging-results) kell megadnia, hogy megkapja a teljes adatkészletet. Egyes Azure Resource Graph-ügyfelek azonban eltérő módon kezelik a tördelést.

- C# SDK

  A ResourceGraph SDK használatakor a tördelést úgy kell kezelni, hogy az előző lekérdezés válaszában visszaadott kihagyási tokent átadja a következő többoldalas lekérdezésre. Ez a kialakítás azt jelenti, hogy az összes többoldalas hívás eredményét össze kell gyűjteni, és a végén össze kell kapcsolni őket. Ebben az esetben minden elküldött többoldalas lekérdezés egy lekérdezési kvótát fog kapni:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  Az Azure CLI vagy a Azure PowerShell használatakor a rendszer automatikusan lekérdezi az Azure Resource Graph lekérdezéseit, hogy a legtöbb 5000 bejegyzést beolvassa. A lekérdezés eredménye a bejegyzések összesített listáját jeleníti meg az összes többoldalas hívásból. Ebben az esetben a lekérdezési eredmény bejegyzéseinek számától függően egy többoldalas lekérdezés egynél több lekérdezési kvótát is használhat. Például az alábbi példákban a lekérdezés egyetlen futtatása akár öt lekérdezési kvótát is felhasználhat:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Továbbra is szabályozható?

Ha a fenti javaslatok gyakorlása után is szabályozza a szabályozást, lépjen kapcsolatba a csapatával a következő címen: [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com) .

Adja meg az alábbi adatokat:

- A speciális használati eset és az üzleti illesztőprogram magasabb szabályozási korlátot igényel.
- Hány erőforráshoz férhet hozzá? Hányat ad vissza egyetlen lekérdezésből?
- Milyen típusú erőforrások érdeklik?
- Mi a lekérdezési minta? X lekérdezés/Y másodpercenként, és így tovább.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- További információ az [erőforrások feltárásáról](explore-resources.md).