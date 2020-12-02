---
title: Naplók konfigurálása és elérése – rugalmas kiszolgáló – Azure Database for PostgreSQL
description: Adatbázis-naplók elérése Azure Database for PostgreSQL – rugalmas kiszolgáló számára
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 3b54fe1ffd49c12db82a038ad449190a0049133f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492352"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Naplók konfigurálása és elérése Azure Database for PostgreSQL-rugalmas kiszolgálón

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A PostgreSQL-naplók egy rugalmas kiszolgáló minden csomópontján elérhetők. A naplókat egy Storage-kiszolgálóra vagy egy elemzési szolgáltatásba is elküldheti. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt.

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

A postgres-kiszolgáló diagnosztikai beállításait a Azure Portal, a CLI, az REST API és a PowerShell használatával engedélyezheti. A kijelölni kívánt **PostgreSQLLogs**.

Az erőforrás-naplók engedélyezése a Azure Portal használatával:

1. A portálon lépjen a *diagnosztikai beállítások* elemre a postgres-kiszolgáló navigációs menüjében.
   
2. Válassza a *diagnosztikai beállítás hozzáadása* lehetőséget.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Diagnosztikai beállítások hozzáadása gomb":::

3. Nevezze el ezt a beállítást. 

4. Válassza ki az előnyben részesített végpontot (Storage-fiók, Event hub, log Analytics). 

5. Válassza ki a napló típusát **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="PostgreSQL-naplók kiválasztása":::

7. Mentse a beállítást.

Ha az erőforrás-naplókat a PowerShell, a CLI vagy a REST API használatával szeretné engedélyezni, keresse fel a [diagnosztikai beállítások](../../azure-monitor/platform/diagnostic-settings.md) című cikket.

### <a name="access-resource-logs"></a>Erőforrás-naplók elérése

A naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage szolgáltatással kapcsolatban lásd a [Storage-fiók naplózása](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) című cikket. Event Hubs esetében tekintse meg a [stream Azure-naplók](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) című cikket.

Azure Monitor naplók esetében a naplók a kiválasztott munkaterületre kerülnek. A postgres-naplók a **AzureDiagnostics** -gyűjtési módot használják, így a AzureDiagnostics táblából is lekérdezhető. A táblázatban szereplő mezők a következőkben olvashatók. További információ a lekérdezésekről és a riasztásokról: [Azure monitor naplók lekérdezése](../../azure-monitor/log-query/log-query-overview.md) – áttekintés.

Az alábbi lekérdezésekből megpróbálkozhat a kezdéshez. A riasztásokat lekérdezések alapján is konfigurálhatja.

Egy adott kiszolgáló összes postgres-naplójának keresése az elmúlt nap során

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Az összes nem localhost csatlakozási kísérlet keresése

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

A fenti lekérdezés a munkaterület összes postgres-kiszolgálójának utolsó 6 órájában megjeleníti az eredményeket.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a log Analytics-lekérdezésekkel](../../azure-monitor/log-query/log-analytics-tutorial.md)
- További tudnivalók az [Azure Event hub](../../event-hubs/event-hubs-about.md) -ról