---
title: Azure Queue Storage adatok referenciája
description: Napló- és metrika-referencia az adatok Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 506f5a46688f597b8ac5db341c5bbe5eb5fb67c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763144"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Azure Queue Storage adatok referenciája

Az [Azure Storage monitorozási](monitor-queue-storage.md) adatainak gyűjtésével és elemzésével kapcsolatos részletekért lásd: Az Azure Storage monitorozása.

## <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok az Azure Storage-hoz gyűjtött platformmetrikákat sorolják fel.

### <a name="capacity-metrics"></a>Kapacitásmetrikák

A kapacitásmetrikák értékei naponta frissülnek (legfeljebb 24 óra). Az időintervallum határozza meg a metrikaértékek bemutatási időközét. Az összes kapacitásmetrika támogatott időmetrika egy óra (PT1H).

Az Azure Storage a következő kapacitásmetrikákat biztosítja Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Fiókszintű kapacitásmetrikák

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage metrikák

Ez a táblázat Queue Storage [metrikákat mutat be.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)

| Metric | Leírás |
| ------------------- | ----------------- |
| **QueueCapacity (Várólistakapacitás)** | A tárfiók Queue Storage használt mennyiség. <br><br> Egység: `Bytes` <br> Összesítés típusa: `Average` <br> Példa értékre: `1024` |
| **QueueCount** | A tárfiókban lévő üzenetsorok száma. <br><br> Egység: `Count` <br> Összesítés típusa: `Average` <br> Példa értékre: `1024` |
| **QueueMessageCount** | A tárfiókban lévő le nemért üzenetsor-üzenetek száma. <br><br> Egység: `Count` <br> Összesítés típusa: `Average` <br> Példa értékre: `1024` |

### <a name="transaction-metrics"></a>Tranzakciómetrikák

A tranzakciómetrikák az Azure Storage-tárfiókhoz küldött minden kéréskor ki vannak bocsátva a Azure Monitor. Ha nem történik tevékenység a tárfiókban, az időszak tranzakciómetrikákra vonatkozó adatai nem lesznek. Az összes tranzakciómetrika fiók- és szolgáltatásszinten Queue Storage érhető el. Az időintervallum határozza meg a metrikaértékek időintervallumát. Az összes tranzakciós metrika támogatott időmetrikák a PT1H és a PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikadimenziók

Az Azure Storage a következő dimenziókat támogatja a Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Erőforrásnaplók (előzetes verzió)

> [!NOTE]
> Az Azure Storage Azure Monitor naplók nyilvános előzetes verzióban érhetők el, és előzetes verzióban tesztelhetőek minden nyilvános felhőrégióban. Ez az előzetes verzió lehetővé teszi a blobok (például Azure Data Lake Storage Gen2), fájlok, üzenetsorok, táblák, prémium szintű tárfiókok naplóit az általános célú v1 és általános célú v2 tárfiókok esetében. A klasszikus tárfiókok nem támogatottak.

Az alábbi táblázat az Azure Storage-erőforrásnaplók tulajdonságait sorolja fel, amikor azok a naplókban vagy Azure Monitor Azure Storage-ban vannak összegyűjtve. A tulajdonságok a műveletet, a szolgáltatást és a művelet végrehajtásához használt engedélyezés típusát írják le.

### <a name="fields-that-describe-the-operation"></a>A műveletet leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>A művelet hitelesítését leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>A szolgáltatást leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Lásd még

- A [monitorozási Azure Queue Storage](monitor-queue-storage.md) a Figyelés és Azure Queue Storage.
- Az [Azure-erőforrások monitorozásával](../../azure-monitor/essentials/monitor-azure-resource.md) kapcsolatos Azure Monitor Azure-erőforrások monitorozásával kapcsolatos további információkért lásd: Azure-erőforrások monitorozása az erőforrásokkal.
