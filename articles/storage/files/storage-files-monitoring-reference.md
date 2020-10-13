---
title: Azure Files monitorozási adathivatkozás | Microsoft Docs
description: Naplózási és metrikai referenciák a Azure Filesból származó adatok figyeléséhez.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 11f89a0a42a42fd84d1dc0c8606128f5af9e751e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711341"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure Files monitorozási adathivatkozás

A Azure Files figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg a [figyelés Azure Files](storage-files-monitoring.md) .

## <a name="metrics"></a>Mérőszámok

A következő táblázatok a Azure Files összegyűjtött platform-metrikákat tartalmazzák. 

### <a name="capacity-metrics"></a>Kapacitásmetrikák

A kapacitás metrikáinak értékeit minden órában Azure Monitor elküldjük. Az értékek naponta frissülnek. Az időkeret határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes kapacitás metrikájának támogatott időszelete egy óra (PT1H).

A Azure Files a következő kapacitási metrikákat biztosítja a Azure Monitorban.

#### <a name="account-level"></a>Fiók szintje

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

Ez a táblázat [Azure Files mérőszámokat](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)jelenít meg.

| Metrika | Leírás |
| ------------------- | ----------------- |
| FileCapacity | A Storage-fiók által használt file Storage mennyisége. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| FileCount   | A Storage-fiókban található fájlok száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| FileShareCapacityQuota | A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024|
| FileShareCount | A Storage-fiókban lévő fájlmegosztás száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| FileShareProvisionedIOPS | A fájlmegosztás kiépített IOPS száma. Ez a mérőszám csak a Premium file Storage esetében alkalmazható. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag |
| FileShareSnapshotCount | A Storage-fiók Azure Files szolgáltatásában lévő megosztásban található Pillanatképek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag | 
|FileShareSnapshotSize|A pillanatképek által a Storage-fiók Azure Files szolgáltatásában használt tárterület mennyisége. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag|

### <a name="transaction-metrics"></a>Tranzakciómetrikák

A tranzakciós metrikák az Azure Storage-ból Azure Monitorba történő minden kérelemre kibocsátva. Abban az esetben, ha nincs tevékenység a Storage-fiókban, az adott időszakban nem lesz adat a tranzakciós metrikákban. Az összes tranzakciós metrika mind a fiók, mind a Azure Files szolgáltatási szinten elérhető. Az időgabona meghatározza a metrikai értékek bemutatásának időintervallumát. Az összes tranzakciós metrika támogatott időgabona-PT1H és PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikák méretei

A Azure Files a Azure Monitor metrikáinak következő dimenzióit támogatja.

> [!NOTE] 
> A fájlmegosztás dimenzió nem érhető el a standard fájlmegosztás esetében (csak a prémium szintű fájlmegosztás esetén). Normál fájlmegosztás használata esetén a megadott metrikák a Storage-fiókban található összes fájl megosztására vonatkoznak. A standard fájlmegosztás esetében a megosztási metrikák megszerzéséhez hozzon létre egy fájlmegosztást a Storage-fiókban.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Erőforrás-naplók (előzetes verzió)

> [!NOTE]
> Az Azure Storage-naplók Azure Monitor nyilvános előzetes verzióban érhető el, és az előzetes teszteléshez minden nyilvános felhőalapú régióban elérhető. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Ez az előzetes verzió lehetővé teszi a Blobok (többek között a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák és a Premium Storage-fiókok általános célú v1 és általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

A következő táblázat felsorolja az Azure Storage-beli erőforrás-naplók tulajdonságait Azure Monitor naplókba vagy az Azure Storage-ba való gyűjtéskor. A tulajdonságok leírják a műveletet, a szolgáltatást és a művelet végrehajtásához használt engedélyezési típust.

### <a name="fields-that-describe-the-operation"></a>A műveletet leíró mezők


[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>A művelet hitelesítésének módját leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>A szolgáltatást leíró mezők

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Lásd még

- Lásd: [figyelés Azure Files](storage-files-monitoring-reference.md) az Azure Storage figyelésének leírásához.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/insights/monitor-azure-resource.md) .