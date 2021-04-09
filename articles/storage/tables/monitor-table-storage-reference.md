---
title: Az Azure Table Storage figyelési adatreferenciája | Microsoft Docs
description: Naplózási és metrikai referenciák az Azure Table Storage-ból származó adatok figyeléséhez.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: ad56b6af9a9071812ad6fa581954010df3b6b5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574844"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Az Azure Table Storage figyelési adatreferenciája

Az Azure Storage figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért lásd: az [Azure Storage figyelése](monitor-table-storage.md) .

## <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok felsorolják az Azure Storage-hoz összegyűjtött platform metrikákat. 

### <a name="capacity-metrics"></a>Kapacitásmetrikák

A kapacitás metrikáinak értékeit minden órában Azure Monitor elküldjük. Az értékek naponta frissülnek. Az időkeret határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes kapacitás metrikájának támogatott időszelete egy óra (PT1H).

Az Azure Storage a következő kapacitási mérőszámokat biztosítja Azure Monitorban.

#### <a name="account-level"></a>Fiók szintje

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>Table Storage

Ez a táblázat a [táblázatos tároló metrikáit](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices)jeleníti meg.

| Metric | Leírás |
| ------------------- | ----------------- |
| TableCapacity | A Storage-fiók által használt Table Storage mennyisége. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| TableCount   | A Storage-fiókban lévő táblák száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| TableEntityCount | A Storage-fiókban lévő tábla entitások száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |

### <a name="transaction-metrics"></a>Tranzakciómetrikák

A tranzakciós metrikák az Azure Storage-ból Azure Monitorba történő minden kérelemre kibocsátva. Abban az esetben, ha nincs tevékenység a Storage-fiókban, az adott időszakban nem lesz adat a tranzakciós metrikákban. Az összes tranzakciós metrika mind a fiók, mind a Table Storage szolgáltatási szinten elérhető. Az időgabona meghatározza a metrikai értékek bemutatásának időintervallumát. Az összes tranzakciós metrika támogatott időgabona-PT1H és PT1M.

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

- Az Azure Storage figyelésének leírását lásd: az [Azure Table Storage figyelése](monitor-table-storage.md) .
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/essentials/monitor-azure-resource.md) .