---
title: Naplózás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A pgAudit naplózási naplózásának fogalmai Azure Database for PostgreSQL – egyetlen kiszolgálón.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 33fa6420f52cae9c869cc75a04ea82de0ec48262
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596294"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Naplózás naplózása Azure Database for PostgreSQL – egyetlen kiszolgáló

Az adatbázis-tevékenységek naplózása Azure Database for PostgreSQL – az egyes kiszolgálók a PostgreSQL-naplózási bővítményen keresztül érhetők el: [pgAudit](https://www.pgaudit.org/). a pgAudit részletes munkamenet-és/vagy objektum-naplózási naplózást biztosít.

> [!NOTE]
> a pgAudit előzetes verzióban érhető el Azure Database for PostgreSQL.
> A bővítmény csak általános célú és a memóriára optimalizált kiszolgálókon engedélyezhető.

Ha az Azure-beli erőforrás-szintű naplókat szeretné használni a számítási és tárolási skálázási műveletekhez, tekintse meg az [Azure-tevékenység naplóját](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Használati megfontolások
Alapértelmezés szerint a pgAudit naplóutasítások a normál naplóutasításokkal együtt vannak kibocsátva a Postgres standard naplózási eszközének használatával. Az Azure Database for PostgreSQL-ben ezek a .log-fájlok az Azure Portalról vagy a parancssori felületről tölthetők le. A fájlok gyűjteményének maximális tárterülete 1 GB, és minden fájl legfeljebb hét napig érhető el (az alapértelmezett érték három nap). Ez a szolgáltatás egy rövid távú tárolási lehetőség.

Azt is megteheti, hogy az összes naplót be szeretné állítani Azure Monitor log Store-ba a későbbi elemzésekhez Log Analyticsban. Ha engedélyezi Azure Monitor erőforrás-naplózást, a rendszer automatikusan elküldi a naplókat (JSON formátumban) az Azure Storage-ba, Event Hubsba és/vagy Azure Monitor naplókba, attól függően, hogy Ön milyen választ.

A pgAudit engedélyezése nagy mennyiségű naplót eredményez a kiszolgálón, ami hatással van a teljesítményre és a naplózási tárkapacitásra. Javasoljuk, hogy használjon Azure Monitor naplókat, amelyek hosszú távú tárolási lehetőségeket, valamint elemzési és riasztási funkciókat biztosítanak. Javasoljuk a standard naplózás kikapcsolását, hogy a további naplózás ne rontsa a teljesítményt:

   1. Állítsa a paramétert `logging_collector` KIkapcsolt értékre. 
   2. A módosítás alkalmazásához indítsa újra a kiszolgálót.

Ha meg szeretné tudni, hogyan állíthatja be az Azure Storage-ba való naplózást, Event Hubs vagy Azure Monitor naplókat, tekintse meg a [kiszolgáló naplói című cikk](concepts-server-logs.md)erőforrás-naplók című szakaszát.

## <a name="installing-pgaudit"></a>A pgAudit telepítése

A pgAudit telepítéséhez fel kell vennie azt a kiszolgáló megosztott előtelepítő könyvtáraiba. A postgres `shared_preload_libraries` paraméterének módosítása a kiszolgáló újraindítását igényli. A paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával módosíthatja.

A [Azure Portal](https://portal.azure.com)használata:

   1. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.
   2. Az oldalsávon válassza a **kiszolgálói paraméterek** lehetőséget.
   3. Keresse meg a `shared_preload_libraries` paramétert.
   4. Válassza a **pgaudit** lehetőséget.
   5. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   6. Kapcsolódjon a kiszolgálóhoz egy ügyfél használatával (például psql), és engedélyezze az pgAudit-bővítményt
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Ha hibaüzenet jelenik meg, ellenőrizze, hogy a mentés után újraindította-e a kiszolgálót `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>pgAudit-beállítások

a pgAudit lehetővé teszi a munkamenet vagy az objektum naplózási naplózásának konfigurálását. A [munkamenet naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) a végrehajtott utasítások részletes naplóit bocsátja ki. Az [objektum naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) az adott kapcsolatokra terjed ki. Dönthet úgy is, hogy beállít egy vagy mindkét típusú naplózást. 

> [!NOTE]
> a pgAudit-beállítások megadott gloabally, és nem adhatók meg adatbázis vagy szerepkör szintjén.

A [pgAudit telepítése](#installing-pgaudit)után a paramétereket a naplózás megkezdéséhez is konfigurálhatja. Az [pgAudit dokumentációja](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) az egyes paraméterek definícióját tartalmazza. Először tesztelje a paramétereket, és győződjön meg róla, hogy a várt működést tapasztalja.

> [!NOTE]
> Ha a be értékre állítja, a `pgaudit.log_client` rendszer átirányítja a naplókat egy ügyfél-folyamatba (például psql) a fájlba való írás helyett. Ezt a beállítást általában javasolt letiltva hagyni. <br> <br>
> A `pgaudit.log_level` csak akkor engedélyezett, ha a `pgaudit.log_client` be van kapcsolva.

> [!NOTE]
> A Azure Database for PostgreSQLban `pgaudit.log` nem állítható be `-` (mínusz) az pgAudit dokumentációjában leírtak szerint. Az összes kötelező utasítási osztályt (olvasás, írás stb.) külön-külön kell megadni.

### <a name="audit-log-format"></a>Auditnapló formátuma
Az egyes naplózási bejegyzéseket a rendszer a `AUDIT:` naplósor elejéhez közel jelzi. A bejegyzés további részének formátuma a [pgAudit dokumentációjában](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)található.

Ha más mezőkre van szüksége a naplózási követelmények kielégítéséhez, használja a postgres paramétert `log_line_prefix` . `log_line_prefix` egy olyan karakterlánc, amely minden postgres elején kimenetként szerepel. A következő beállítás például az `log_line_prefix` időbélyeget, a felhasználónevet, az adatbázis nevét és a folyamat azonosítóját tartalmazza:

```
t=%m u=%u db=%d pid=[%p]:
```

További információ `log_line_prefix` a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)található.

### <a name="getting-started"></a>Első lépések
A gyors kezdéshez állítsa a következőt: `pgaudit.log` `WRITE` , majd nyissa meg a naplókat a kimenet áttekintéséhez. 

## <a name="viewing-audit-logs"></a>Naplók megtekintése
Ha. log fájlokat használ, a rendszer a naplókat a PostgreSQL-hibákkal megegyező fájlban fogja tartalmazni. A naplófájlokat az Azure [Portalról](howto-configure-server-logs-in-portal.md) vagy a [parancssori](howto-configure-server-logs-using-cli.md)felületről töltheti le. 

Ha Azure erőforrás-naplózást használ, a naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage szolgáltatással kapcsolatban lásd a [Storage-fiók naplózása](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) című cikket. Event Hubs esetében tekintse meg a [stream Azure-naplók](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) című cikket.

Azure Monitor naplók esetében a naplók a kiválasztott munkaterületre kerülnek. A postgres-naplók a **AzureDiagnostics** -gyűjtési módot használják, így a AzureDiagnostics táblából is lekérdezhető. A táblázatban szereplő mezők a következőkben olvashatók. További információ a lekérdezésekről és a riasztásokról: [Azure monitor naplók lekérdezése](../azure-monitor/logs/log-query-overview.md) – áttekintés.

Ezt a lekérdezést használhatja a kezdéshez. A riasztásokat lekérdezések alapján is konfigurálhatja.

Egy adott kiszolgáló összes postgres-naplójának keresése az elmúlt nap során
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a Azure Database for PostgreSQL való bejelentkezésről](concepts-server-logs.md)
- Megtudhatja, hogyan állíthatja be a paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával.