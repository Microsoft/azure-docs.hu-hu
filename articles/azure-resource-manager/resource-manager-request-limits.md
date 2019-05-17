---
title: Kérelmekre vonatkozó korlátok és sávszélesség-szabályozási – Azure Resource Manager
description: Ismerteti, hogyan használható az Azure Resource Manager által szabályozás előfizetési korlátok elérésekor.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: fc731b1abec9c101356a0fa57eef498b58612ab9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791361"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager-kérelmek szabályozása

Minden Azure-előfizetés és bérlő erőforrás-kezelő lehetővé teszi, hogy legfeljebb 12 000 olvasási kérelmek száma óránként és 1200 írási kérelmek száma óránként. Ezek a korlátok a kérelmet benyújtó résztvevő-Azonosítót és az előfizetés-azonosító hatóköre, vagy a bérlői azonosító. Ha a kérések érkeznek, az egynél több résztvevő-azonosító, a korlátot, az előfizetés vagy a bérlő között nagyobb, mint 12 000 és 1200 óránként.

Az előfizetés vagy a bérlő kérelmek lépnek. Feliratkozási kérelmeket azok az előfizetés átadása a involve ID, például az erőforráscsoportok az előfizetésében beolvasása. Bérlő kérelmek nem tartalmaznak, például lekér érvényes Azure-helyen az előfizetés-Azonosítóját.

Ezek a korlátok vonatkoznak minden Azure Resource Manager-példány. Több példány minden Azure-régióban, és Azure Resource Manager az összes Azure-régióban üzemel.  Így a gyakorlatban korlátai hatékonyan sokkal nagyobb ezeket a korlátokat, felhasználói kérések általában által kiszolgált számos különböző példányait.

Ha az alkalmazást vagy parancsfájlt eléri az ezeket a korlátokat, akkor a kérelmek szabályozása. Ez a cikk bemutatja, miként állapítható meg, a fennmaradó kérelmek korlátjának elérése előtt, és hogyan reagáljon, amikor eléri a korlátot.

Ha eléri a korlátot, kap-e a HTTP-állapotkódot **429 túl sok kérelem**.

Az Azure Erőforrás-grafikon korlátozza a műveleteire kérések száma. A cikkben található lépések meghatározásához, a fennmaradó kérelmeket, és hogyan reagáljon a korlát elérésekor Erőforrás-grafikon is vonatkozik. Erőforrás-grafikon azonban a saját korlátozása és a visszaállítási arány állítja be. További információkért lásd: [szabályozása az Azure-erőforrás Graph](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Fennmaradó kérelmek
Megadhatja, hogy a fennmaradó kérések száma válaszfejlécek megvizsgálásával. Olvasási kérelmek ad vissza értéket, a fennmaradó olvasási kérelmek száma a fejlécében. Az írási kérések tartalmazzák a fennmaradó írási kérelmek száma értékét. A következő táblázat ismerteti a válaszfejlécek ezekhez az értékekhez ellenőrizheti:

| Válaszfejléc | Leírás |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Hatókörrel rendelkező előfizetés beolvasása van hátra. Ezt az értéket adja vissza az olvasási műveletekre. |
| x-ms-ratelimit-remaining-subscription-writes |Hatókörrel rendelkező előfizetés ír van hátra. Ez az érték az írási művelet adja vissza. |
| x-ms-ratelimit-remaining-tenant-reads |Hatókörrel rendelkező bérlő beolvasása van hátra |
| x-ms-ratelimit-remaining-tenant-writes |Hatókörrel rendelkező bérlő ír van hátra |
| x-ms-ratelimit-remaining-subscription-resource-requests |Előfizetés a fennmaradó erőforrást típusú kérések hatókörét.<br /><br />A fejléc értéke csak akkor ad vissza, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. Erőforrás-kezelő hozzáadja ezt az értéket az előfizetés olvasási vagy írási műveletek helyett. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Előfizetés erőforrás típusa adatgyűjtési kérelmek fennmaradó hatókörét.<br /><br />A fejléc értéke csak akkor ad vissza, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. Ezt az értéket megadja a fennmaradó adatgyűjtési kérelmek (lista erőforrások). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Bérlői erőforrás-típusú kérések fennmaradó hatókörét.<br /><br />Ez a fejléc csak bérlői szintű kérelmek ad hozzá, és csak akkor, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. Erőforrás-kezelő hozzáadja ezt az értéket a bérlő olvasások és írások helyett. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Bérlői erőforrás típusa adatgyűjtési kérelmek fennmaradó hatókörét.<br /><br />Ez a fejléc csak bérlői szintű kérelmek ad hozzá, és csak akkor, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. |

## <a name="retrieving-the-header-values"></a>A fejléc értékek beolvasása
Ezek a kódot vagy szkriptet fejléc az értékek beolvasása semmiben nem különbözik minden fejléc értékének beolvasása. 

Például a **C#**, kérheti le a Fejlécérték egy **HttpWebResponse** nevű objektum **válasz** a következő kóddal:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

A **PowerShell**, Invoke-WebRequest művelet lekérése a fejléc értéke.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Teljes PowerShell-példa: [Resource Manager korlátok ellenőrzése egy előfizetés](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Ha meg szeretné tekinteni a hibakereséshez a fennmaradó kérelmeket, megadhatja a **-Debug** paraméterrel a **PowerShell** parancsmagot.

```powershell
Get-AzResourceGroup -Debug
```

Több értékhez, többek között a következő választ értéket ad vissza:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Írási korlátok lekéréséhez használja az írási művelet: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Több értékhez, többek között a következő értékeket ad vissza:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

A **Azure CLI-vel**, a részletesebb lehetőség használatával beolvashatja a fejléc értéke.

```azurecli
az group list --verbose --debug
```

Több értékhez, többek között a következő értékeket ad vissza:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Írási korlátok lekéréséhez használja az írási művelet: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Több értékhez, többek között a következő értékeket ad vissza:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Várakozás a következő kérés elküldése előtt
Ha egyenlege eléri a kérelmi korlátjának, erőforrás-kezelő adja vissza a **429-es** HTTP-állapotkódot és a egy **Retry-After** a fejléc értéke. A **Retry-After** érték másodperc az alkalmazásnak várnia kell (vagy alvó) számát adja meg a kérések elküldése előtt. Az újrapróbálkozások letelte előtt egy kérelmet küld, ha a kérelem feldolgozása nem, és a egy új újrapróbálkozási értéket adja vissza.

## <a name="next-steps"></a>További lépések

* Teljes PowerShell-példa: [Resource Manager korlátok ellenőrzése egy előfizetés](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Korlátok és kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
* Aszinkron REST-kérések kezelésével kapcsolatos további információkért lásd: [Azure aszinkron műveletek követése](resource-manager-async-operations.md).
