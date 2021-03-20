---
title: A Azure Functions tárolási szempontjai
description: Ismerje meg a Azure Functions tárolási követelményeit és a tárolt adat titkosítását.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: c4ffb622482585e35337caf8e43b69e0f3b0385c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517263"
---
# <a name="storage-considerations-for-azure-functions"></a>A Azure Functions tárolási szempontjai

A Azure Functions egy Azure Storage-fiókot igényel a Function App-példány létrehozásakor. A Function app a következő tárolási szolgáltatásokat használhatja:


|Tárolási szolgáltatás  | Függvények használata  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Kötések állapotának és funkcióbillentyűk megtartása.  <br/>[A Durable Functionsban a feladatok hubok](durable/durable-functions-task-hubs.md)is használják. |
| [Azure Files](../storage/files/storage-files-introduction.md)  | A Function app-kód tárolásához és futtatásához használt fájlmegosztás a használati [tervben](consumption-plan.md) és a [prémium csomagban](functions-premium-plan.md). |
| [Azure üzenetsor-tároló](../storage/queues/storage-queues-introduction.md)     | [A feladatok hubok használják Durable Functionsban](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  [A feladatok hubok használják Durable Functionsban](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> A fogyasztási/prémium szintű üzemeltetési csomag használatakor a rendszer a fő Storage-fiókban tárolja a függvény kódját és a kötési konfigurációs fájlokat az Azure file Storage-ban. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. Ennek az az oka, hogy a függvények az Azure Storage-on alapulnak olyan műveletekre, mint például az eseményindítók és a naplózási függvények végrehajtásának kezelése. Egyes Storage-fiókok nem támogatják a várólistákat és a táblákat. Ezek a fiókok csak blob Storage-fiókokat és Azure-Premium Storage tartalmaznak.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#core-storage-services). 

Habár meglévő Storage-fiókot is használhat a Function alkalmazással, meg kell győződnie arról, hogy megfelel a követelményeknek. Azok a Storage-fiókok, amelyek az alkalmazás-létrehozási folyamat részeként jöttek létre a Azure Portalban, garantáltan megfelelnek a Storage-fiókra vonatkozó követelményeknek. A portálon a rendszer kiszűri a nem támogatott fiókokat, amikor egy meglévő Storage-fiókot választ ki egy Function-alkalmazás létrehozásakor. Ebben a folyamatban csak olyan meglévő Storage-fiókokat választhat, amelyek ugyanabban a régióban találhatók, mint a létrehozandó Function alkalmazás. További információ: Storage- [fiók helye](#storage-account-location).

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>A Storage-fiók útmutatója

Minden Function alkalmazás működéséhez szükség van egy Storage-fiókra. Ha a fiók törlődik, a Function alkalmazás nem fog futni. A tárolással kapcsolatos problémák elhárításával kapcsolatban lásd: [a tárolással kapcsolatos problémák elhárítása](functions-recover-storage-account.md). A következő további szempontok vonatkoznak a Function apps által használt Storage-fiókra.

### <a name="storage-account-location"></a>Tárfiók helye

A legjobb teljesítmény érdekében a Function alkalmazásnak egy azonos régióban lévő Storage-fiókot kell használnia, amely csökkenti a késést. A Azure Portal érvényesíti ezt az ajánlott gyakorlatot. Ha valamilyen okból kifolyólag egy Storage-fiókot kell használnia a Function alkalmazástól eltérő régióban, a Function alkalmazást a portálon kívül kell létrehoznia. 

### <a name="storage-account-connection-setting"></a>Storage-fiók kapcsolatainak beállítása

A Storage-fiók kapcsolatai a [AzureWebJobsStorage alkalmazás-beállításban](./functions-app-settings.md#azurewebjobsstorage)maradnak. 

A Storage-fiók kapcsolódási karakterláncát frissíteni kell a tárolási kulcsok újragenerálása során. [További információ a Storage Key managementről](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Megosztott Storage-fiókok

Több Function-alkalmazás is lehetséges, hogy problémák nélkül megoszthatja ugyanazt a Storage-fiókot. A Visual Studióban például több alkalmazás is fejleszthető az Azure Storage Emulator használatával. Ebben az esetben az emulátor egyetlen Storage-fiókhoz hasonlóan működik. A Function alkalmazás által használt Storage-fiók is használható az alkalmazásadatok tárolására. Azonban ez a megközelítés nem mindig jó ötlet az éles környezetben.

### <a name="optimize-storage-performance"></a>A tárolási teljesítmény optimalizálása

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Storage-adattitkosítás

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Régión belüli adattárolás

Ha az összes ügyfél-adatnak egyetlen régióban kell maradnia, a Function alkalmazáshoz társított Storage-fióknak a [régión belüli redundanciával](../storage/common/storage-redundancy.md)kell rendelkeznie. A régión belüli redundáns Storage-fiókot az [Azure Durable functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection)is használni kell.

A platform által felügyelt ügyféladatokat csak a régión belül tárolják, ha belsőleg elosztott terhelésű App Service Environment (bevezetéses) környezetben üzemeltetik azokat. További információ: a [beépítési zóna redundancia](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="mount-file-shares"></a>Csatlakoztatási fájlmegosztás

_Ez a funkció jelenleg csak Linux rendszeren érhető el._ 

Meglévő Azure Files-megosztásokat csatlakoztathat a Linux Function-alkalmazásaihoz. Ha egy megosztást csatlakoztat a linuxos Function-alkalmazáshoz, használhatja a meglévő gépi tanulási modelleket és a függvények más adatait. A [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) parancs használatával meglévő megosztásokat csatlakoztathat a Linux-függvény alkalmazásához. 

Ebben a parancsban a `share-name` létező Azure Files megosztás neve, és `custom-id` bármely olyan karakterlánc lehet, amely egyedileg definiálja a megosztást a Function alkalmazáshoz való csatlakoztatáskor. Emellett az az `mount-path` elérési út, amelyből a megosztás elérhető a Function alkalmazásban. `mount-path` formátumúnak kell lennie `/dir-name` , és nem kezdődhet a-vel `/home` .

Teljes példaként tekintse meg a Python- [függvény létrehozása és a Azure Files megosztás csatlakoztatása](scripts/functions-cli-mount-files-storage-linux.md)című témakörben található parancsfájlokat. 

Jelenleg csak a a `storage-type` `AzureFiles` támogatott. Csak öt megosztást lehet csatlakoztatni egy adott Function alkalmazáshoz. A fájlmegosztás csatlakoztatása növelheti a hideg kezdési időt legalább 200 300ms, vagy akár több is, ha a Storage-fiók egy másik régióban található.

A csatlakoztatott megosztás a megadott függvény kódjában érhető el `mount-path` . Ha például a (z `mount-path` ) `/path/to/mount` , a cél könyvtárat a fájlrendszer API-jai segítségével érheti el, ahogy az a következő Python-példában látható:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions üzemeltetési lehetőségeiről.

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetése](functions-scale.md)
