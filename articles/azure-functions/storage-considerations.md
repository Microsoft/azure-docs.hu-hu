---
title: Tárolási szempontok a Azure Functions
description: Ismerje meg az adatok tárolási követelményeit Azure Functions és a tárolt adatok titkosítását.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 5faa85a4fac9fc0b8639f33c475283f4f043c627
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779254"
---
# <a name="storage-considerations-for-azure-functions"></a>Tárolási szempontok a Azure Functions

Azure Functions azure storage-fiókra van szükség a függvényalkalmazás-példány létrehozásakor. A függvényalkalmazás a következő tárolási szolgáltatásokat hatja igénybe:


|Storage szolgáltatás  | Függvények használata  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Kötési állapot és függvénykulcsok karbantartása.  <br/>A [feladatközpontok is használják a Durable Functions.](durable/durable-functions-task-hubs.md) |
| [Azure Files](../storage/files/storage-files-introduction.md)  | A függvényalkalmazás kódját egy használaton belüli és [prémium](consumption-plan.md) szintű csomagban tárolja és [futtatja.](functions-premium-plan.md) |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | A [tevékenységközpontok használják](durable/durable-functions-task-hubs.md)a Durable Functions.   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  A [tevékenységközpontok használják](durable/durable-functions-task-hubs.md)a Durable Functions.       |

> [!IMPORTANT]
> A használat/prémium szintű üzemeltetési csomag használata esetén a függvénykód és a kötéskonfigurációs fájlok az Azure File Storage-ban vannak tárolva a fő tárfiókban. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Függvényalkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, vagy hivatkozni kell rá, amely támogatja a Blob, Queue és Table Storage tárolókat. Ennek az az oka, hogy a Functions az Azure Storage-ban kezeli az olyan műveleteket, mint az eseményindítók kezelése és a függvények végrehajtásának naplózása. Egyes tárfiókok nem támogatják az üzenetsorokat és táblákat. Ezek közé a fiókok közé tartoznak a csak blobok számára elérhető tárfiókok és az Azure Premium Storage.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#core-storage-services). 

Bár használhat egy meglévő tárfiókot a függvényalkalmazással, meg kell győződnie arról, hogy az megfelel ezeknek a követelményeknek. A függvényalkalmazás létrehozási folyamatának részeként létrehozott tárfiókok garantáltan megfelelnek Azure Portal tárfiókra vonatkozó követelményeknek. A portálon a rendszer kiszűri a nem támogatott fiókokat, amikor kiválaszt egy meglévő tárfiókot a függvényalkalmazás létrehozása során. Ebben a folyamatban csak olyan meglévő tárfiókokat választhat ki, amelyek ugyanabban a régióban vannak, mint a létrehozott függvényalkalmazás. További információ: [Tárfiók helye.](#storage-account-location)

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Útmutató a tárfiókhoz

Minden függvényalkalmazás működéséhez tárfiók szükséges. Ha törli a fiókot, a függvényalkalmazás nem fog futni. A tárolással kapcsolatos hibák elhárításához [lásd: Tárolással kapcsolatos problémák elhárítása.](functions-recover-storage-account.md) A függvényalkalmazások által használt Storage-fiókra az alábbi további szempontok vonatkoznak.

### <a name="storage-account-location"></a>Tárfiók helye

A legjobb teljesítmény érdekében a függvényalkalmazásnak ugyanabban a régióban kell használnia egy tárfiókot, ami csökkenti a késést. A Azure Portal betartatja ezt az ajánlott gyakorlatot. Ha valamilyen okból a függvényalkalmazástól eltérő régióban kell használnia egy tárfiókot, a függvényalkalmazást a portálon kívül kell létrehoznia. 

### <a name="storage-account-connection-setting"></a>Tárfiók kapcsolati beállítása

A tárfiók kapcsolata az [AzureWebJobsStorage alkalmazásbeállításban tartható fenn.](./functions-app-settings.md#azurewebjobsstorage) 

A tárfiók kapcsolati sztringet frissíteni kell, amikor újra létrehozza a tárkulcsokat. [A tárkulcsok kezelésével kapcsolatban itt olvashat bővebben.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>Megosztott tárfiókok

Több függvényalkalmazás is probléma nélkül megoszthatja ugyanazt a tárfiókot. Ebben a példában Visual Studio Azure Storage Emulator használatával több alkalmazást is fejleszthet. Ebben az esetben az emulátor egyetlen tárfiókként működik. A függvényalkalmazás által használt tárfiók az alkalmazásadatok tárolására is használható. Ez a megközelítés azonban éles környezetben nem mindig jó ötlet.

### <a name="optimize-storage-performance"></a>A tárolási teljesítmény optimalizálása

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Tárolási adatok titkosítása

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Régión belül található adatok helye

Ha minden ügyféladatnak egyetlen régióban kell maradnia, a függvényalkalmazáshoz társított tárfióknak régión belüli redundanciával kell [lennie.](../storage/common/storage-redundancy.md) A régión belül redundáns tárfiókot is használni kell az [Azure Durable Functions.](./durable/durable-functions-perf-and-scale.md#storage-account-selection)

A platform által felügyelt egyéb ügyféladatok csak akkor vannak a régióban tárolva, ha belsőleg elosztott terhelésű környezetben (ASE App Service Environment tárolják. További információ: [ASE-zónaredundania.](../app-service/environment/zone-redundancy.md#in-region-data-residency)

## <a name="mount-file-shares"></a>Fájlmegosztások csatlakoztatása

_Ez a funkció csak Linuxon való futtatáskor érhető el._ 

Csatlakoztathat meglévő Azure Files megosztásokat a Linux-függvényalkalmazáshoz. Ha egy megosztást a Linux-függvényalkalmazáshoz rögzít, kihasználhatja a meglévő gépi tanulási modelleket vagy a függvények egyéb adatait. A paranccsal [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) csatlakoztathat egy meglévő megosztást a Linux-függvényalkalmazáshoz. 

Ebben a parancsban a a meglévő Azure Files-megosztás neve, és bármilyen sztring lehet, amely egyedileg meghatározza a megosztást a `share-name` `custom-id` függvényalkalmazáshoz csatlakoztatva. Emellett `mount-path` az az elérési út is, amelyről a megosztás elérhető a függvényalkalmazásban. `mount-path` formátumban kell `/dir-name` lennie, és nem kezdődhet a következővel: `/home` .

A teljes példát a Python-függvényalkalmazás létrehozása és egy Azure Files szkriptek között [láthatja.](scripts/functions-cli-mount-files-storage-linux.md) 

Jelenleg csak a `storage-type` `AzureFiles` támogatott. Egy adott függvényalkalmazáshoz csak öt megosztást csatlakoztathat. A fájlmegosztás csatlakoztatása akár 200–300 ezredm-sel is megnövelheti a hideg kezdési időt, vagy még több időt, ha a tárfiók másik régióban van.

A csatlakoztatott megosztás a függvénykód számára a megadott időpontban `mount-path` érhető el. Ha például a , fájlrendszer API-k segítségével férhet hozzá a célkönyvtárhoz, ahogyan az a `mount-path` `/path/to/mount` következő Python-példában is található:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions lehetőségekről.

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetése](functions-scale.md)
