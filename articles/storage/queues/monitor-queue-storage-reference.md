---
title: Az Azure Queue Storage monitorozási dokumentációja
description: Naplózási és metrikai referenciák az Azure Queue Storage figyelési adataihoz.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 95f20737b044140fe12ea939e71cd2397cb4826d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576688"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Az Azure Queue Storage monitorozási dokumentációja

Az Azure Storage figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért lásd: az [Azure Storage figyelése](monitor-queue-storage.md) .

## <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok felsorolják az Azure Storage-hoz összegyűjtött platform metrikákat.

### <a name="capacity-metrics"></a>Kapacitásmetrikák

A kapacitás metrikáinak értékei naponta frissülnek (akár 24 óráig). Az időkeret határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes kapacitás metrikájának támogatott időszelete egy óra (PT1H).

Az Azure Storage a következő kapacitási mérőszámokat biztosítja Azure Monitorban.

#### <a name="account-level-capacity-metrics"></a>Fiók szintű kapacitás metrikái

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage metrikák

Ez a táblázat [Queue Storage mérőszámokat](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)jelenít meg.

| Metric | Leírás |
| ------------------- | ----------------- |
| **QueueCapacity** | A Storage-fiók által használt Queue Storage mennyisége. <br><br> Egység `Bytes` <br> Összesítés típusa: `Average` <br> Érték példa: `1024` |
| **QueueCount** | A Storage-fiókban lévő várólisták száma. <br><br> Egység `Count` <br> Összesítés típusa: `Average` <br> Érték példa: `1024` |
| **QueueMessageCount** | A tárolási fiók üzenetsor-üzeneteinek hozzávetőleges száma. <br><br> Egység `Count` <br> Összesítés típusa: `Average` <br> Érték példa: `1024` |

### <a name="transaction-metrics"></a>Tranzakciómetrikák

A tranzakciós metrikák az Azure Storage-ból Azure Monitorba történő minden kérelemre kibocsátva. Abban az esetben, ha nincs tevékenység a Storage-fiókban, az adott időszakban nem lesz adat a tranzakciós metrikákban. Az összes tranzakciós metrika mind a fiók, mind a Queue Storage szolgáltatási szinten elérhető. Az időgabona meghatározza a metrikai értékek bemutatásának időintervallumát. Az összes tranzakciós metrika támogatott időgabona-PT1H és PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikák méretei

Az Azure Storage a Azure Monitor metrikáinak következő dimenzióit támogatja.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Erőforrás-naplók (előzetes verzió)

> [!NOTE]
> Az Azure Storage-naplók Azure Monitor nyilvános előzetes verzióban érhető el, és az előzetes teszteléshez minden nyilvános felhőalapú régióban elérhető. Ez az előzetes verzió lehetővé teszi a Blobok (többek között a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák és a Premium Storage-fiókok általános célú v1 és általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

A következő táblázat felsorolja az Azure Storage-beli erőforrás-naplók tulajdonságait Azure Monitor naplókba vagy az Azure Storage-ba való gyűjtéskor. A tulajdonságok leírják a műveletet, a szolgáltatást és a művelet végrehajtásához használt engedélyezési típust.

### <a name="fields-that-describe-the-operation"></a>A műveletet leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>A művelet hitelesítésének módját leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>A szolgáltatást leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Lásd még

- Lásd: az Azure [Queue Storage figyelése](monitor-queue-storage.md) az azure-Queue Storage figyelésének leírásához.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/essentials/monitor-azure-resource.md) .
