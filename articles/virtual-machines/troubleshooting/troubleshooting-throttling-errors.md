---
title: Hibaelhárítási szabályozási hibákat tapasztal, az Azure-ban |} A Microsoft Docs
description: Szabályozási hibák, az újrapróbálkozások és az Azure Compute leállítást.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: a9e0f2620bf6ff163207fc16ee24a327936ec4bf
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709198"
---
# <a name="troubleshooting-api-throttling-errors"></a>API szabályozási hibák elhárítása 

Azure számítási kérelmek szabályozva előfordulhat, hogy egy előfizetés és a szolgáltatás általános teljesítménye kiküszöbölni régiók szerinti alapon. Az Azure számítási erőforrás-szolgáltató (CRP), a Microsoft.Compute névtér az erőforrásokat felügyelő összes hívásainak nem haladhatja meg a maximális engedélyezett API-kérések mennyisége biztosítható. Ez a dokumentum ismerteti a szabályozás, sávszélesség-szabályozási hibák elhárítására vonatkozó részletek API és ajánlott eljárásokat szabályozva elkerülése érdekében.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Azure Resource Manager-és erőforrás-szolgáltatók által szabályozás  

Kezdettől fogva az Azure-ba, mint Azure Resource Manager hajtja végre a hitelesítési és elsőrendű érvényesítési és az összes bejövő API-kérelmek szabályozása. Az Azure Resource Manager-hívás sebességhatárok és a kapcsolódó diagnosztikai válasz HTTP-fejlécek [Itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits).
 
Amikor egy Azure API-ügyfél megkapja a sávszélesség-szabályozási hiba, a HTTP-állapot akkor 429 Too Many Requests. Szeretné megtudni, ha a kérelemszabályozás végzi el az Azure Resource Manager vagy az alapul szolgáló erőforrás-szolgáltató például a CRP, vizsgálja meg a `x-ms-ratelimit-remaining-subscription-reads` a GET-kérésekhez és `x-ms-ratelimit-remaining-subscription-writes` válaszfejlécek nem GET kérelmek esetén. A fennmaradó hívások számát közeledik 0, ha az előfizetés általános hívás Azure Resource Manager által meghatározott elérte. Az összes előfizetés ügyfelek tevékenységek együtt bájtjai számítanak. Ellenkező esetben a szabályozás származik a célként megadott erőforrás-szolgáltató (az egyik címzett által a `/providers/<RP>` szegmense, amely a kérelem URL-CÍMÉT). 

## <a name="call-rate-informational-response-headers"></a>Hívás arány tájékoztató válaszfejlécek 

| Fejléc                            | Érték formátuma                           | Példa                               | Leírás                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | A Teljesítményszabályozási szabályzat, amely többek között a kérelem a cél erőforráscsoport gyűjtőben vagy a művelet az API-hívások számát van hátra                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | A hívások száma counts "felszámított" a HTTP-kérelem a megfelelő csoportházirend-korlát felé. Ez leggyakrabban az 1. Kérések, például a méretezése egy virtuálisgép-méretezési csoportot, több counts díjat. |


Vegye figyelembe, hogy egy API-kérelem is kell alávetni, több Teljesítményszabályozási szabályzat. Egy külön lesz `x-ms-ratelimit-remaining-resource` minden fejléc. 

Itt látható a mintaválasz törölni a virtual machine scale set kérést.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Szabályozás a hiba részletei

Kérelem elutasítása, mert egy hívás sávszélesség-korlátjának elérése gyakran szolgál a 429 HTTP-állapot. A számítási erőforrás-szolgáltató egy tipikus szabályozási hibaüzenetet az alábbi példához hasonlóan fog kinézni (csak a megfelelő fejlécek látható):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

A fennmaradó hívások számát, a 0-házirend miatt, amely a sávszélesség-szabályozási hibát adott vissza lesz. Ebben az esetben ez `HighCostGet30Min`. A válasz törzse általános formátuma az általános Azure Resource Manager API-hiba formátum (OData megfelelő). A fő hibakód `OperationNotAllowed`, egy számítási erőforrás-szolgáltató jelentheti a szabályozási hibák (többek között a más típusú ügyfél hibák) használja. A `message` a belső hiba (hibák) tulajdonságát a sávszélesség-szabályozási megsértése részleteit tartalmazó szerializált JSON struktúrát tartalmaz.

Ahogy fent ábrázolt minden szabályozási hiba magában foglalja a `Retry-After` fejléc, így másodpercek minimális száma az ügyfél várnia kell a kérés újbóli megkísérlése előtt. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API hívása arányát és a sávszélesség-szabályozási hiba elemző
A számítási erőforrás-szolgáltató API-hoz a hibaelhárítási funkcióval előzetes verziója érhető el. PowerShell-parancsmagokat API kérés egységár időintervallum művelet és a sávszélesség-szabályozási szabálysértések (házirend) művelet csoportonként statisztikáit meg:
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Az API-hívás statisztikái is nagyszerű cybercrime egy előfizetésben ügyféltől/ügyfelektől érkezők viselkedését, és engedélyezze a szabályozás okozó hívás minták egyszerű azonosítása.

Az elemző jelenleg korlátozva, hogy a számláló nem kérelmek erőforrástípusok lemez és a pillanatkép (támogatásához a felügyelt lemezeket). A CRP a telemetriai adatokat gyűjt, mivel azt is nem segít az ARM szabályozási hibák azonosítására. De azok azonosíthatók egyszerűen alapján ARM megkülönböztető válaszfejlécek taglaltak szerint.

A PowerShell-parancsmagokat használ a szolgáltatás REST API, amely egyszerűen közvetlenül az ügyfelek (bár a még nem hivatalosan támogatott). A HTTP-kérés formátuma megtekintéséhez futtassa a parancsmagokat a - Debug paraméter vagy Bepillantás azok végrehajtását a Fiddler.


## <a name="best-practices"></a>Ajánlott eljárások 

- Ne próbálkozzon újra az Azure-szolgáltatás API-hibák feltétel nélkül, illetve azonnal. Gyakran előfordul olyan ügyfél gyors újrapróbálkozási hurokba került kaphat, amikor egy hiba, amely nem tud újra kódot. Újrapróbálkozások végül felhasználta a rendelkezésére a cél művelet csoport engedélyezett hívások maximális, és hatással van az előfizetés ügyfelek számára. 
- Nagy mennyiségű API automation esetben fontolja meg az előjelzéses ügyféloldali önálló szabályozás implementálása során néhány alsó küszöbérték alá esik a rendelkezésre álló hívások számát egy célcsoport művelet. 
- Amikor az aszinkron műveletek nyomon követésére, tiszteletben a Retry-After fejléccel mutatók. 
- Ha az Ügyfélkód van szüksége egy adott virtuális gép adatait, a lekérdezés a virtuális gép közvetlenül az összes virtuális gépet tartalmazó erőforráscsoport vagy a teljes előfizetés listázása és majd kiválasztotta a szükséges virtuális Gépet az ügyféloldalon helyett. 
- Ügyfélkód van szüksége a virtuális gépek, a lemezek és a egy adott Azure-beli hely pillanatképeinek, ha a lekérdezés-helyalapú űrlap használata helyett az összes előfizetés virtuális gépek lekérdezésére, és az ügyféloldalon hely szerint, majd szűrést futtatna: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` számítási erőforrás-szolgáltató területi lekérdezés végpontok. 
-   Amikor létrehozása vagy frissítése különösen az API-erőforrásokhoz, virtuális gépek és a virtual machine scale sets, sokkal hatékonyabb, ha nyomon követheti a visszaadott aszinkron művelet befejezését, mint maga erőforrás URL-cím lekérdezési (alapján a `provisioningState`).

## <a name="next-steps"></a>További lépések

Újrapróbálkozási útmutatás más Azure-szolgáltatások kapcsolatos további információkért lásd: [újrapróbálkozási útmutatás adott szolgáltatásoknál](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
