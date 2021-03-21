---
title: Nagy méretű adathalmazok kezelése
description: Megtudhatja, hogyan kérheti le, formázhatja, lapozhatja és kihagyhatja a nagyméretű adatkészletek rekordjait az Azure Resource Graph használata közben.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1eaabfdd78712966f3b21d869259a312db31b7bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917690"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Nagyméretű Azure-beli erőforrás-adatkészletek használata

Az Azure Resource Graph az Azure-környezetben található erőforrásokkal való munkavégzésre és információk beszerzésére szolgál. Az erőforrás-gráf az adatok gyors beolvasását is lehetővé teszi, még akkor is, ha több ezer rekordot kérdez le. Az erőforrás-gráf számos lehetőséget kínál a nagyméretű adatkészletek használatához.

A lekérdezések magas gyakorisággal történő kezelésével kapcsolatos útmutatásért lásd: [útmutató a szabályozott kérelmekhez](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Az adatkészlet eredményének mérete

Alapértelmezés szerint az erőforrás-gráf korlátozza a lekérdezéseket, hogy csak **100** rekordokat adjanak vissza. Ez a vezérlő védi a felhasználót és a szolgáltatást olyan véletlen lekérdezésektől, amelyek nagy adatkészleteket eredményezhetnek. Ez az esemény leggyakrabban akkor fordul elő, amikor az ügyfél a lekérdezésekkel kísérletezik, és az adott igényeknek megfelelő módon keresi és szűri az erőforrásokat. Ez a vezérlő más, mint a [felső](/azure/kusto/query/topoperator) vagy [Az Azure adatkezelő](/azure/kusto/query/limitoperator) nyelvi operátorok használatának korlátozása az eredmények korlátozásához.

> [!NOTE]
> **Első** használata esetén ajánlott az eredményeket legalább egy, a (z) vagy értékű oszlop szerint `asc` rendelni `desc` . Rendezés nélkül a visszaadott eredmények véletlenszerűek, és nem ismételhetők.

Az alapértelmezett korlát felülbírálható az erőforrás-Gráftal való interakció minden módszerén keresztül. Az alábbi példák bemutatják, hogyan módosíthatja az adathalmaz méretének korlátját a _200_ értékre:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

A [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)a vezérlő **$Top** , és a **QueryRequestOptions** része.

A _legszigorúbb_ vezérlő fog nyerni. Ha például a lekérdezés a **felső** vagy a **korlátot** használja, és az **elsőnél** több rekordot fog eredményezni, akkor a visszaadott maximális rekordok megegyeznek az **elsővel**. Hasonlóképpen, ha a **felső** vagy a **korlát** kisebb, mint az **első**, a visszaadott rekord a **felső** vagy a **korlát** által konfigurált kisebb érték lesz.

Az **első** jelenleg a maximálisan engedélyezett _5000_-as értékkel rendelkezik, amelyet a [lapozófájlok](#paging-results) az _1000_ -as számú rekordok elérésével érnek el egyszerre.

> [!IMPORTANT]
> Ha az **első** úgy van beállítva, hogy nagyobb legyen, mint _1000_ rekord, a lekérdezésnek az **azonosító** **mezőt kell megadnia** ahhoz, hogy a tördelés működjön. Ha hiányzik a lekérdezésből, a válasz nem fog [lapozni](#paging-results) , és az eredmények _1000_ rekordra korlátozódnak.

## <a name="skipping-records"></a>Rekordok kihagyása

A nagyméretű adatkészletek használatának következő lehetősége a **kihagyás** vezérlőelem. Ez a vezérlő lehetővé teszi, hogy a lekérdezés átugorjon vagy kihagyja a megadott számú rekordot, mielőtt visszaadná az eredményeket. A **skip (kihagyás** ) olyan lekérdezések esetében hasznos, amelyek értelmes módon jelenítik meg az eredményeket. Ha a szükséges eredmények a visszaadott adathalmaz végén találhatók, akkor hatékonyabb, ha más rendezési konfigurációt használ, és az eredményeket az adathalmaz elejéről kéri le.

> [!NOTE]
> Ha a **kihagyást** használja, azt javasoljuk, hogy az eredményeket legalább egy, a vagy a oszlop alapján rendelje `asc` `desc` . Rendezés nélkül a visszaadott eredmények véletlenszerűek, és nem ismételhetők. Ha `limit` vagy `take` használatban van a lekérdezésben, a **kihagyás** figyelmen kívül lesz hagyva.

Az alábbi példák azt mutatják be, hogyan lehet kihagyni az első _10_ rekordot, amely a lekérdezés eredményét eredményezi, ehelyett a visszaadott eredményhalmaz 11. rekorddal való megadásával:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

A [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)a vezérlő **$skip** , és a **QueryRequestOptions** része.

## <a name="paging-results"></a>Lapozás eredményei

Ha egy eredményhalmaz kisebb készletekre való bontására van szükség a feldolgozáshoz, vagy mert egy eredményhalmaz túllépi az engedélyezett maximális _1000_ -as értéket, használja a lapozást. A [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
 **QueryResponse** a következő értékeket adja meg: **resultTruncated** és **$skipToken**. a **resultTruncated** egy logikai érték, amely tájékoztatja a fogyasztót, ha a válaszban több rekord nem lett visszaadva. Ez az állapot akkor is azonosítható, ha a **Count** tulajdonság kisebb, mint a **totalRecords** tulajdonság. a **totalRecords** határozza meg, hogy hány rekord felel meg a lekérdezésnek.

 a **resultTruncated** akkor **igaz** , ha a lapozás le van tiltva vagy nem lehetséges, mert nincs `id` oszlop, vagy ha kevesebb erőforrás érhető el, mint a lekérdezés. Ha a **resultTruncated** értéke **igaz**, a **$skipToken** tulajdonság nincs beállítva.

Az alábbi példák bemutatják, hogyan **hagyhatja** ki az első 3000 rekordot, és visszaküldheti az **első** 1000-rekordokat, miután az Azure CLI-vel és Azure PowerShelltel kihagyta a bejegyzéseket

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A lekérdezésnek az **azonosító** mezőt kell megadnia ahhoz, **hogy a tördelés** működjön. Ha hiányzik a lekérdezésből, a válasz nem tartalmazza a **$skipToken**.

Példaként tekintse meg a [következő oldal lekérdezését](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) a REST API docs webhelyen.

## <a name="formatting-results"></a>Formázás eredményei

A Resource Graph-lekérdezések eredményei két formátumban, _tábla_ -és _ObjectArray_ érhetők el. A formátum a **resultFormat** paraméterrel van konfigurálva a kérési beállítások részeként. A **resultFormat** alapértelmezett értéke a _táblázat_ formátuma.

Az Azure CLI-ből származó eredmények alapértelmezés szerint a JSON-ben vannak megadva. Az Azure PowerShell alapértelmezés szerint **pscustomobject formájában kapja** , de a parancsmag használatával gyorsan átalakíthatók JSON-ra `ConvertTo-Json` . Más SDK-k esetén a lekérdezés eredményei konfigurálhatók úgy, hogy kiállítsák a _ObjectArray_ formátumot.

### <a name="format---table"></a>Táblázat formázása

Az alapértelmezett formátum, _tábla_, az eredményeket egy JSON-formátumban adja vissza, amely a lekérdezés által visszaadott tulajdonságok kiemelésére szolgál. Ez a formátum szorosan hasonlít egy strukturált táblázatban vagy táblázatban definiált oszlopokra az elsőként azonosított oszlopokkal, majd minden olyan sorral, amely az adott oszlopokhoz igazított adathalmazokat jelképezi.

Íme egy példa egy lekérdezési eredményre a _táblázat_ formázásával:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Formátum – ObjectArray

A _ObjectArray_ formátuma is JSON-formátumban adja vissza az eredményeket. Ez a kialakítás azonban a JSON-ban közös kulcs/érték párok kapcsolatához igazodik, ahol az oszlop és a sor adatsorai egyeznek a tömb csoportjaival.

Íme egy példa egy lekérdezési eredményre a _ObjectArray_ formázásával:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Íme néhány példa a **resultFormat** beállítására a _ObjectArray_ formátum használatára:

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- További információ az [erőforrások feltárásáról](explore-resources.md).
