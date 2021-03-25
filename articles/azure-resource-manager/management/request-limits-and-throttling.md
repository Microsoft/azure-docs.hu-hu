---
title: Kérelemkorlátozások és -szabályozás
description: Ismerteti, hogyan használható a szabályozás Azure Resource Manager kérelmekkel, ha elérte az előfizetési korlátokat.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024625"
---
# <a name="throttling-resource-manager-requests"></a>Erőforrás-kezelő kéréseinek szabályozása

Ez a cikk azt ismerteti, hogyan Azure Resource Manager szabályozza a kérelmeket. Bemutatja, hogyan követheti nyomon a korlátot megelőzően megmaradó kérelmek számát, és hogyan válaszolhat a korlát elérésekor.

A szabályozás két szinten történik. Azure Resource Manager szabályozza az előfizetés és a bérlő kérelmeit. Ha a kérelem az előfizetés és a bérlő szabályozási korlátja alá esik, a Resource Manager átirányítja a kérést az erőforrás-szolgáltatónak. Az erőforrás-szolgáltató a műveletekhez igazított szabályozási korlátozásokat alkalmaz. Az alábbi képen látható, hogyan történik a szabályozás alkalmazása a felhasználótól a Azure Resource Manager és az erőforrás-szolgáltatótól.

![Kérelmek szabályozása](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Előfizetés és bérlő korlátai

Minden előfizetési szintű és bérlői szintű művelethez szabályozási korlátok vonatkoznak. Az előfizetési kérelmek olyanok, amelyek az előfizetés-azonosító átadását foglalják magukban, például az erőforráscsoportok beolvasása az előfizetésben. A bérlői kérelmek nem tartalmazzák az előfizetés-azonosítót, például érvényes Azure-beli hely beolvasását.

Az alapértelmezett szabályozási korlát/óra az alábbi táblázatban látható.

| Hatókör | Üzemeltetés | Korlát |
| ----- | ---------- | ------- |
| Előfizetés | olvasás | 12000 |
| Előfizetés | törli | 15 000 |
| Előfizetés | írja | 1200 |
| Bérlő | olvasás | 12000 |
| Bérlő | írja | 1200 |

Ezeknek a korlátoknak a hatóköre a kéréseket intéző rendszerbiztonsági tag (felhasználó vagy alkalmazás) és az előfizetés azonosítója vagy a bérlő azonosítója. Ha a kérések több rendszerbiztonsági tagból érkeznek, akkor az előfizetésben vagy a bérlőben a korlát nagyobb, mint óránként 12 000 olvasás és 1200 írás.

Ezek a korlátozások minden Azure Resource Manager példányra érvényesek. Minden Azure-régióban több példány található, és Azure Resource Manager az összes Azure-régióban üzembe helyezhető.  Így a gyakorlatban a határértékek magasabbak, mint a határértékek. A felhasználóktól érkező kéréseket általában a Azure Resource Manager különböző példányai kezelik.

## <a name="resource-provider-limits"></a>Erőforrás-szolgáltatói korlátok

Az erőforrás-szolgáltatók saját szabályozási korlátokat alkalmaznak. Mivel az erőforrás-kezelő az elsődleges azonosító és a Resource Manager-példány alapján szabályozza a szabályozást, előfordulhat, hogy az erőforrás-szolgáltató több kérést kap, mint az előző szakasz alapértelmezett korlátai.

Ez a szakasz a széles körben használt erőforrás-szolgáltatók szabályozási korlátait tárgyalja.

### <a name="storage-throttling"></a>Tárterület-szabályozás

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Hálózati sávszélesség-szabályozás

A Microsoft. Network erőforrás-szolgáltató a következő szabályozási korlátokat alkalmazza:

| Művelet | Korlát |
| --------- | ----- |
| írás/törlés (PUT) | 1000/5 perc |
| olvasás (GET) | 10000/5 perc |

### <a name="compute-throttling"></a>Számítási szabályozás

További információ a számítási műveletek korlátozásáról: [API-szabályozási hibák elhárítása – számítás](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Virtuálisgép-méretezési csoporton belüli virtuálisgép-példányok ellenőrzéséhez használja a [Virtual Machine Scale sets műveleteket](/rest/api/compute/virtualmachinescalesetvms). Használja például a virtuálisgép [-méretezési csoport virtuális gépek-lista](/rest/api/compute/virtualmachinescalesetvms/list) paramétereit a virtuálisgép-példányok energiagazdálkodási állapotának vizsgálatához. Ez az API csökkenti a kérelmek számát.

### <a name="azure-resource-graph-throttling"></a>Az Azure Resource Graph szabályozása

Az [Azure Resource Graph](../../governance/resource-graph/overview.md) korlátozza a műveleteire irányuló kérések számát. A cikkben ismertetett lépések alapján megállapíthatja a fennmaradó kérelmeket, és hogyan reagálhat a korlátra, ha az erőforrás-gráfra is érvényes. Az erőforrás-gráf azonban beállítja a saját korlátját és az alaphelyzetbe állítási arányt. További információ: az [erőforrás-gráf szabályozására szolgáló fejlécek](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Egyéb erőforrás-szolgáltatók

A más erőforrás-szolgáltatók szabályozásával kapcsolatos információkért lásd:

* [Az Azure Key Vaultra vonatkozó szabályozási irányelvek](../../key-vault/general/overview-throttling.md)
* [AKS-hibaelhárítás](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Hibakód

Ha eléri a korlátot, a 429-as HTTP-állapotkód **túl sok kérést** kap. A válasz tartalmazza az **újrapróbálkozások** utáni értéket, amely meghatározza, hogy az alkalmazás hány másodpercig várjon (vagy alvó állapotba), mielőtt elküldené a következő kérést. Ha az újrapróbálkozási érték lejárta előtt küld egy kérést, a rendszer nem dolgozza fel a kérést, és új újrapróbálkozási értéket ad vissza.

A megadott időpontra való várakozás után lezárhatja és újra megnyithatja az Azure-hoz való kapcsolódást. A kapcsolat alaphelyzetbe állításával csatlakozhat a Azure Resource Manager egy másik példányához.

Ha Azure SDK-t használ, lehet, hogy az SDK automatikus újrapróbálkozási konfigurációval rendelkezik. További információ: [újrapróbálkozási útmutató az Azure-szolgáltatásokhoz](/azure/architecture/best-practices/retry-service-specific).

Egyes erőforrás-szolgáltatók a 429-et egy ideiglenes probléma jelentésére adják vissza. A probléma olyan túlterhelési feltétel lehet, amely közvetlenül nem a kérelem által okozott. Vagy ideiglenes hibát jelenthet a célként megadott erőforrás vagy a függő erőforrás állapotáról. A hálózati erőforrás-szolgáltató például a 429 értéket adja vissza a **RetryableErrorDueToAnotherOperation** hibakódtal, ha a célként megadott erőforrás zárolva van egy másik művelettel. Annak megállapításához, hogy a hiba a szabályozásból vagy egy ideiglenes állapotból származik-e, tekintse meg a válaszban a hiba részleteit.

## <a name="remaining-requests"></a>Fennmaradó kérelmek

A hátralévő kérések számát a válasz fejlécek vizsgálatával határozhatja meg. Az olvasási kérelmek egy értéket adnak vissza a fejlécben a hátralévő olvasási kérelmek számának megfelelően. Az írási kérelmek tartalmazzák a fennmaradó írási kérések számát. A következő táblázat ismerteti azokat a válasz-fejléceket, amelyeket megvizsgálhat az értékekhez:

| Válasz fejléce | Leírás |
| --- | --- |
| x-MS-ratelimit – hátralévő előfizetés – olvasás |Az előfizetés hatókörön belüli olvasása megmaradt. A rendszer ezt az értéket adja vissza olvasási műveletekben. |
| x-MS-ratelimit – hátralévő előfizetés – írások |Az előfizetés hatókörön belüli írása megmaradt. A rendszer ezt az értéket adja vissza az írási műveletekhez. |
| x-MS-ratelimit-fennmaradó-bérlő-olvasások |Bérlői hatókörű olvasások megmaradtak |
| x-MS-ratelimit-fennmaradó-bérlő-írások |A bérlő hatókörén belüli írások megmaradtak |
| x-MS-ratelimit-fennmaradó-előfizetés-erőforrás-kérelmek |Az előfizetés hatókörön belüli erőforrás-típusaira vonatkozó kérelmek száma megmarad.<br /><br />Ezt a fejléc-értéket csak akkor adja vissza a rendszer, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. A Resource Manager hozzáadja ezt az értéket az előfizetés vagy írás helyett. |
| x-MS-ratelimit-fennmaradó-előfizetés-erőforrás-entitások – olvasás |Az előfizetési hatókörrel rendelkező erőforrástípus-gyűjtési kérelmek megmaradnak.<br /><br />Ezt a fejléc-értéket csak akkor adja vissza a rendszer, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. Ez az érték megadja a fennmaradó gyűjtési kérelmek számát (erőforrások listázása). |
| x-MS-ratelimit-fennmaradó-bérlő-erőforrás-kérelmek |A bérlői hatókörön belüli erőforrás-kérelmek száma megmaradt.<br /><br />Ezt a fejlécet csak a bérlői szintű kérésekhez adja hozzá a rendszer, és csak akkor, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. A Resource Manager hozzáadja ezt az értéket a bérlői olvasások vagy írások helyett. |
| x-MS-ratelimit-fennmaradó-bérlő-erőforrás-entitások – olvasás |A bérlői hatókörrel rendelkező erőforrástípus-gyűjtési kérések megmaradnak.<br /><br />Ezt a fejlécet csak a bérlői szintű kérésekhez adja hozzá a rendszer, és csak akkor, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. |

Az erőforrás-szolgáltató a válasz fejléceit is visszaküldheti a fennmaradó kérelmekkel kapcsolatos információkkal. A számítási erőforrás-szolgáltató által visszaadott válasz fejlécekkel kapcsolatos információkért tekintse meg a [hívási sebességre vonatkozó tájékoztatási fejléceket](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>A fejléc értékeinek beolvasása

A fejléc értékeinek beolvasása a kódban vagy a parancsfájlban nem különbözik, mint a fejlécek értékének beolvasása. 

A **C# nyelvben** például lekéri a fejléc értékét egy **Válasz** nevű **HttpWebResponse** objektumból a következő kóddal:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

A **PowerShellben** egy Invoke-WebRequest műveletből kéri le a fejléc értékét.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Teljes PowerShell-példaként tekintse [meg az előfizetés Resource Manager-korlátainak ellenőrzését](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)ismertető témakört.

Ha szeretné megtekinteni a további hibakeresési kérelmeket, megadhatja a **-Debug** paramétert a **PowerShell** -parancsmagon.

```powershell
Get-AzResourceGroup -Debug
```

Amely sok értéket ad vissza, beleértve a következő választ:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Írási korlátok beszerzéséhez használjon írási műveletet: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Amely sok értéket ad vissza, beleértve a következő értékeket:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Az **Azure CLI**-ben a fejléc értékét a részletesebb lehetőség használatával kérdezheti le.

```azurecli
az group list --verbose --debug
```

Amely sok értéket ad vissza, beleértve a következő értékeket:

```output
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

Írási korlátok beszerzéséhez használjon írási műveletet: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Amely sok értéket ad vissza, beleértve a következő értékeket:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Következő lépések

* Teljes PowerShell-példaként tekintse [meg az előfizetés Resource Manager-korlátainak ellenőrzését](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)ismertető témakört.
* További információ a korlátozásokról és a kvótáról: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* További információ az aszinkron REST-kérelmek kezeléséről: az [aszinkron Azure-műveletek nyomon követése](async-operations.md).