---
title: Naplók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerteti a naplózás konfigurációját, tárolását és elemzését Azure Database for PostgreSQL – egyetlen kiszolgálón
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 621d5a6a91a8c22c52e6febc7c2638571f5bf113
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595798"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Naplók Azure Database for PostgreSQL – egyetlen kiszolgáló

Azure Database for PostgreSQL lehetővé teszi a postgres szabványos naplófájljainak konfigurálását és elérését. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt. A konfigurálható és elérhető naplózási információk közé tartoznak a hibák, a lekérdezési információk, az autoporszívós rekordok, a kapcsolatok és az ellenőrzőpontok. (A tranzakciós naplókhoz való hozzáférés nem érhető el).

A naplózás a pgaudit postgres-bővítménnyel érhető el. További tudnivalókért tekintse meg a [naplózási fogalmakat](concepts-audit.md) ismertető cikket.


## <a name="configure-logging"></a>Naplózás konfigurálása 
A postgres standard naplózást a kiszolgálón a naplózási kiszolgáló paramétereinek használatával konfigurálhatja. Minden Azure Database for PostgreSQL kiszolgálón, `log_checkpoints` és `log_connections` alapértelmezés szerint be van kapcsolva. További paraméterek is megadhatók a naplózási igényeknek megfelelően: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Azure Database for PostgreSQL – naplózási paraméterek":::

Ha többet szeretne megtudni a postgres, tekintse meg a [Mikor jelentkezzen be](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) , és [Mi a naplózási](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) szakaszt a postgres dokumentációjában. A postgres naplózási paramétereinek többsége a legtöbb esetben elérhető a Azure Database for PostgreSQLban való konfiguráláshoz.

Ha meg szeretné tudni, hogyan konfigurálhatja a paramétereket a Azure Database for PostgreSQLban, tekintse meg a [portál dokumentációját](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációját](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> A nagy mennyiségű napló, például az utasítások naplózásának konfigurálása jelentős teljesítménybeli terhelést vehet igénybe. 

## <a name="access-log-files"></a>Access. log fájlok
Az alapértelmezett naplózási formátum a Azure Database for PostgreSQL. log. A naplóból a következőhöz hasonló minta sor jelenik meg:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL rövid távú tárolási helyet biztosít a. log fájlok számára. Egy új fájl 1 óránként vagy 100 MB-ban kezdődik, attól függően, hogy melyik érkezik először. A rendszer a naplófájlokat a postgres-ből kibocsátva az aktuális fájlhoz fűzi.  

A rövid távú naplófájl megőrzési időtartamát a paraméter használatával állíthatja be `log_retention_period` . Az alapértelmezett érték 3 nap, a megadható legnagyobb érték a 7 nap. A rövid távú tárolási hely legfeljebb 1 GB naplófájlt tud tárolni. 1 GB után a rendszer törli a legrégebbi fájlokat a megőrzési időtartamtól függetlenül, hogy helyet szabadítson fel az új naplók számára. 

A naplók és a napló-elemzések hosszú távú megőrzése érdekében letöltheti a. log fájlokat, és áthelyezheti azokat egy külső szolgáltatásba. A fájlokat a [Azure Portal](howto-configure-server-logs-in-portal.md), az [Azure CLI](howto-configure-server-logs-using-cli.md)használatával töltheti le. Azt is megteheti, hogy Azure Monitor diagnosztikai beállításokat konfigurál, amelyek automatikusan kibocsátják a naplókat (JSON formátumban) a hosszú távú helyekre. Erről a lehetőségről az alábbi részben olvashat bővebben. 

A. log fájlok létrehozásához állítsa a paramétert `logging_collector` KIkapcsolva értékre. A kikapcsolás. a naplófájl létrehozása ajánlott, ha Azure Monitor diagnosztikai beállításokat használ. Ez a konfiguráció csökkenti a további naplózás teljesítményére gyakorolt hatást.

## <a name="resource-logs"></a>Erőforrásnaplók

A Azure Database for PostgreSQL Azure Monitor diagnosztikai beállításokkal van integrálva. A diagnosztikai beállítások lehetővé teszik, hogy JSON formátumban küldje el a postgres-naplókat, hogy Azure Monitor naplókat az elemzéshez és a riasztásokhoz, Event Hubs a folyamatos átvitelhez és az Azure Storage-hoz az archiváláshoz. 

> [!IMPORTANT]
> A kiszolgálói naplókhoz tartozó diagnosztikai funkció csak a általános célú és a memória optimalizált [díjszabási szintjein](concepts-pricing-tiers.md)érhető el.


### <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

A postgres-kiszolgáló diagnosztikai beállításait a Azure Portal, a CLI, az REST API és a PowerShell használatával engedélyezheti. A kijelölni kívánt **PostgreSQLLogs**. (Más naplók is konfigurálhatók, ha a [query Store](concepts-query-store.md)-t használja.)

Az erőforrás-naplók engedélyezése a Azure Portal használatával:

   1. A portálon lépjen a *diagnosztikai beállítások* elemre a postgres-kiszolgáló navigációs menüjében.
   2. Válassza a *diagnosztikai beállítás hozzáadása* lehetőséget.
   3. Nevezze el ezt a beállítást. 
   4. Válassza ki az előnyben részesített végpontot (Storage-fiók, Event hub, log Analytics). 
   5. Válassza ki a napló típusát **PostgreSQLLogs**.
   7. Mentse a beállítást.

Ha az erőforrás-naplókat a PowerShell, a CLI vagy a REST API használatával szeretné engedélyezni, keresse fel a [diagnosztikai beállítások](../azure-monitor/essentials/diagnostic-settings.md) című cikket.

### <a name="access-resource-logs"></a>Erőforrás-naplók elérése

A naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage szolgáltatással kapcsolatban lásd a [Storage-fiók naplózása](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) című cikket. Event Hubs esetében tekintse meg a [stream Azure-naplók](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) című cikket.

Azure Monitor naplók esetében a naplók a kiválasztott munkaterületre kerülnek. A postgres-naplók a **AzureDiagnostics** -gyűjtési módot használják, így a AzureDiagnostics táblából is lekérdezhető. A táblázatban szereplő mezők a következőkben olvashatók. További információ a lekérdezésekről és a riasztásokról: [Azure monitor naplók lekérdezése](../azure-monitor/logs/log-query-overview.md) – áttekintés.

Az alábbi lekérdezésekből megpróbálkozhat a kezdéshez. A riasztásokat lekérdezések alapján is konfigurálhatja.

Egy adott kiszolgáló összes postgres-naplójának keresése az elmúlt nap során
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Az összes nem localhost csatlakozási kísérlet keresése
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A fenti lekérdezés a munkaterület összes postgres-kiszolgálójának utolsó 6 órájában megjeleníti az eredményeket.

### <a name="log-format"></a>Napló formátuma

A következő táblázat a **PostgreSQLLogs** típusának mezőit ismerteti. A kiválasztott kimeneti végponttól függően a befoglalt mezők és a megjelenő sorrend eltérő lehet. 

|**Mező** | **Leírás** |
|---|---|
| TenantId | A bérlő azonosítója |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | A napló UTC-ben való rögzítésének időbélyegzője |
| Típus | A napló típusa. Mindig `AzureDiagnostics` |
| SubscriptionId | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| ResourceGroup | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| ResourceProvider | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Erőforrás URI-ja |
| Erőforrás | A kiszolgáló neve |
| Kategória | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Naplózási szint, példa: napló, hiba, figyelmeztetés |
| Üzenet | Elsődleges napló üzenete | 
| Tartomány | Kiszolgáló verziója, példa: postgres-10 |
| Részletek | Másodlagos napló üzenete (ha van ilyen) |
| ColumnName | Az oszlop neve (ha van ilyen) |
| SchemaName (Sémanév) | A séma neve (ha van ilyen) |
| DatatypeName | Az adattípus neve (ha van ilyen) |
| LogicalServerName | A kiszolgáló neve | 
| _ResourceId | Erőforrás URI-ja |
| Előtag | A naplózási sor előtagja |


## <a name="next-steps"></a>Következő lépések
- További információ a naplók [Azure Portal](howto-configure-server-logs-in-portal.md) vagy az [Azure CLI](howto-configure-server-logs-using-cli.md)-ből való eléréséről.
- További információ a [Azure monitor díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).
- További információ a [naplókról](concepts-audit.md)