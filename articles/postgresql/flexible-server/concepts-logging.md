---
title: Naplók – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: A Azure Database for PostgreSQL-rugalmas kiszolgáló konfigurációjának, tárolásának és elemzésének naplózását ismerteti
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90940392"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Naplók Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL lehetővé teszi a postgres standard szintű naplók konfigurálását és elérését. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt. A konfigurálható és elérhető naplózási információk közé tartoznak a hibák, a lekérdezési információk, az autoporszívós rekordok, a kapcsolatok és az ellenőrzőpontok. (A tranzakciós naplókhoz való hozzáférés nem érhető el).

A naplózás a postgres-bővítményen keresztül érhető el `pgaudit` . További tudnivalókért tekintse meg a [naplózási fogalmakat](concepts-audit.md) ismertető cikket.

## <a name="configure-logging"></a>Naplózás konfigurálása

A postgres standard naplózást a kiszolgálón a naplózási kiszolgáló paramétereinek használatával konfigurálhatja. Ha többet szeretne megtudni a postgres, tekintse meg a [Mikor jelentkezzen be](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) , és [Mi a naplózási](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) szakaszt a postgres dokumentációjában. A postgres naplózási paramétereinek többsége a legtöbb esetben elérhető a Azure Database for PostgreSQLban való konfiguráláshoz.

Ha meg szeretné tudni, hogyan konfigurálhatja a paramétereket a Azure Database for PostgreSQLban, tekintse meg a [portál dokumentációját](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációját](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> A nagy mennyiségű napló, például az utasítások naplózásának konfigurálása jelentős teljesítménybeli terhelést vehet igénybe. 

## <a name="accessing-logs"></a>Naplók elérése

A Azure Database for PostgreSQL Azure Monitor diagnosztikai beállításokkal van integrálva. A diagnosztikai beállítások lehetővé teszik, hogy JSON formátumban küldje el a postgres-naplókat, hogy Azure Monitor naplókat az elemzéshez és a riasztásokhoz, Event Hubs a folyamatos átvitelhez és az Azure Storage-hoz az archiváláshoz. 

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

- További információ a [naplók konfigurálásáról és eléréséről](howto-configure-and-access-logs.md).
- További információ a [Azure monitor díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).
- További információ a [naplókról](concepts-audit.md)
