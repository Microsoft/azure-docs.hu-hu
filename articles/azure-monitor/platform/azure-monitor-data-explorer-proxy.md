---
title: Több erőforrás lekérdezése az Azure Adatkezelő Azure Monitor használatával
description: A Azure Monitor az Azure Adatkezelő, Log Analytics munkaterületek és a klasszikus Application Insights alkalmazások közötti, Azure Monitor-alapú lekérdezések végrehajtásához használható.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 1a35b80ceec12b378a01555f42b7a0500b8f6229
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060452"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Több erőforrás lekérdezése az Azure Adatkezelő Azure Monitor használatával
Azure Monitor támogatja az Azure Adatkezelő, a [Application Insights](/azure/azure-monitor/app/app-insights-overview)és a [log Analytics](/azure/azure-monitor/platform/data-platform-logs)közötti szolgáltatások közötti lekérdezéseket. Ezután lekérdezheti az Azure Adatkezelő-fürtöt Log Analytics-vagy Application Insights-eszközök használatával, és megtekintheti azt egy több szolgáltatásból álló lekérdezésben. A cikk bemutatja, hogyan végezheti el a szolgáltatások közötti lekérdezést.

Az alábbi ábrán a Azure Monitor szolgáltatások közötti folyamat látható:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="A felhasználó, a Azure Monitor, a proxy és az Azure Adatkezelő közötti lekérdezések folyamatát ábrázoló diagram.":::

>[!NOTE]
> Azure Monitor a szolgáltatások közötti lekérdezés nyilvános előzetes verzióban érhető el. Forduljon a [szolgáltatásért felelős csapathoz](mailto:ADXProxy@microsoft.com) bármilyen kérdéssel.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Log Analytics vagy Application Insights erőforrásainak és az Azure-nak a lekérdezése Adatkezelő

Az Kusto-lekérdezéseket támogató ügyféleszközök használatával több erőforrásra kiterjedő lekérdezéseket is futtathat. Ilyen eszközök például a Log Analytics webes felhasználói felület, a munkafüzetek, a PowerShell és a REST API.

Adja meg egy Azure Adatkezelő-fürt azonosítóját a mintában lévő lekérdezésben `adx` , majd az adatbázis neve és táblázata alapján.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Képernyőkép, amely egy több szolgáltatásból álló lekérdezésre mutat példát.":::

> [!NOTE]
>* Az adatbázisok nevei megkülönböztetik a kis-és nagybetűket.
>* A riasztások közötti erőforrás-lekérdezés nem támogatott.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Az Azure Adatkezelő-fürt tábláinak egyesítése Log Analytics-munkaterülettel

A `union` paranccsal egyesítheti log Analytics munkaterülettel a fürtözött táblákat.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Képernyőkép, amely a Union paranccsal egy több szolgáltatásra kiterjedő lekérdezési példát mutat be.":::

> [!Tip]
> A Gyorsírás formátuma engedélyezett: *ClusterName* / *InitialCatalog*. Például a `adx('help/Samples')` fordítása: `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Adatok csatlakoztatása Azure Adatkezelő-fürtből egyik bérlőn egy másik Azure Monitor erőforrással

A szolgáltatások közötti több-bérlős lekérdezések nem támogatottak. Be van jelentkezve egyetlen bérlőbe a mindkét erőforrást átívelő lekérdezés futtatásához.

Ha az Azure Adatkezelő erőforrás az A bérlőn található, és a Log Analytics munkaterület a B bérlőn található, használja az alábbi módszerek egyikét:

*  Az Azure Adatkezelő lehetővé teszi szerepkörök hozzáadását a különböző bérlők résztvevői számára. Adja hozzá a saját felhasználói AZONOSÍTÓját a B bérlőhöz egy meghatalmazott felhasználóként az Azure Adatkezelő-fürtön. Ellenőrizze, hogy az Azure Adatkezelő-fürt [TrustedExternalTenant](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster) tulajdonsága tartalmazza-e a B bérlőt. a teljes lekérdezést futtassa a b bérlőn.
*  A [világítótorony](https://docs.microsoft.com/azure/lighthouse/) használatával a Azure monitor-erőforrást az a bérlőhöz tervezheti.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Kapcsolódás Azure Adatkezelő-fürtökhöz különböző bérlők között

A Kusto Explorer automatikusan bejelentkezik arra a bérlőre, amelyhez a felhasználói fiók eredetileg tartozik. Ha más bérlők erőforrásait ugyanazzal a felhasználói fiókkal szeretné elérni, explicit módon meg kell adnia `TenantId` a kapcsolati karakterláncot:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>További lépések
* [Lekérdezések írása](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Azure Monitor lekérdezése az Azure Adatkezelő használatával](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Erőforrás-naplózási lekérdezések végrehajtása Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
