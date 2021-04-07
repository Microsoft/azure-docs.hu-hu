---
title: Naplózás – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: A pgAudit-naplózási naplózással kapcsolatos fogalmak Azure Database for PostgreSQL – rugalmas kiszolgáló.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588251"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Naplózás Azure Database for PostgreSQL – rugalmas kiszolgáló

Az adatbázis-tevékenységek naplózása Azure Database for PostgreSQL-rugalmas kiszolgálón a PostgreSQL-naplózási bővítményen keresztül érhető el: [pgAudit](https://www.pgaudit.org/). a pgAudit részletes munkamenet-és/vagy objektum-naplózási naplózást biztosít.

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ha az Azure-beli erőforrás-szintű naplókat szeretné használni a számítási és tárolási skálázási műveletekhez, tekintse meg az [Azure-tevékenység naplóját](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Használati megfontolások
Alapértelmezés szerint a pgAudit naplóutasítások a normál naplóutasításokkal együtt vannak kibocsátva a Postgres standard naplózási eszközének használatával. Azure Database for PostgreSQL-rugalmas kiszolgálókon beállíthatja, hogy a rendszer az összes naplót a Azure Monitor log Store-ba küldje a Log Analytics későbbi elemzéséhez. Ha engedélyezi Azure Monitor erőforrás-naplózást, a rendszer automatikusan elküldi a naplókat (JSON formátumban) az Azure Storage-ba, Event Hubsba és/vagy Azure Monitor naplókba, attól függően, hogy Ön milyen választ.

Ha meg szeretné tudni, hogyan állíthatja be az Azure Storage-ba való naplózást, Event Hubs vagy Azure Monitor naplókat, tekintse meg a [kiszolgáló naplói című cikk](concepts-logging.md)erőforrás-naplók című szakaszát.

## <a name="enabling-pgaudit"></a>PgAudit engedélyezése

A pgAudit engedélyezéséhez egy ügyfél (például a psql) használatával kell csatlakoznia a kiszolgálóhoz, és engedélyeznie kell a pgAudit bővítményt a következő parancs futtatásával:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>pgAudit-beállítások

a pgAudit lehetővé teszi a munkamenet vagy az objektum naplózási naplózásának konfigurálását. A [munkamenet naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) a végrehajtott utasítások részletes naplóit bocsátja ki. Az [objektum naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) az adott kapcsolatokra terjed ki. Dönthet úgy is, hogy beállít egy vagy mindkét típusú naplózást. 

> [!NOTE]
> a pgAudit-beállítások globálisan vannak megadva, és nem adható meg adatbázis vagy szerepkör szintjén.

Miután [engedélyezte a pgAudit](#enabling-pgaudit), beállíthatja a paramétereket a naplózás megkezdéséhez. Az [pgAudit dokumentációja](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) az egyes paraméterek definícióját tartalmazza. Először tesztelje a paramétereket, és győződjön meg róla, hogy a várt működést tapasztalja.

> [!NOTE]
> Ha a be értékre állítja, a `pgaudit.log_client` rendszer átirányítja a naplókat egy ügyfél-folyamatba (például psql) a fájlba való írás helyett. Ezt a beállítást általában javasolt letiltva hagyni. <br> <br>
> A `pgaudit.log_level` csak akkor engedélyezett, ha a `pgaudit.log_client` be van kapcsolva.

> [!NOTE]
> Azure Database for PostgreSQL – rugalmas kiszolgáló `pgaudit.log` nem állítható be a `-` pgAudit dokumentációjában leírtak szerint (mínusz). Az összes kötelező utasítási osztályt (olvasás, írás stb.) egyedileg meg kell adni.

## <a name="audit-log-format"></a>Auditnapló formátuma
Az egyes naplózási bejegyzéseket a rendszer a `AUDIT:` naplósor elejéhez közel jelzi. A bejegyzés további részének formátuma a [pgAudit dokumentációjában](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)található.

## <a name="getting-started"></a>Első lépések
A gyors kezdéshez állítsa a következőt: `pgaudit.log` `WRITE` , majd nyissa meg a kiszolgálói naplókat a kimenet áttekintéséhez. 

## <a name="viewing-audit-logs"></a>Naplók megtekintése
A naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage szolgáltatással kapcsolatban lásd a [Storage-fiók naplózása](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) című cikket. Event Hubs esetében tekintse meg a [stream Azure-naplók](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) című cikket.

Azure Monitor naplók esetében a naplók a kiválasztott munkaterületre kerülnek. A postgres-naplók a **AzureDiagnostics** -gyűjtési módot használják, így a AzureDiagnostics táblából is lekérdezhető. A táblázatban szereplő mezők a következőkben olvashatók. További információ a lekérdezésekről és a riasztásokról: [Azure monitor naplók lekérdezése](../../azure-monitor/logs/log-query-overview.md) – áttekintés.

Ezt a lekérdezést használhatja a kezdéshez. A riasztásokat lekérdezések alapján is konfigurálhatja.

Az elmúlt nap egy adott kiszolgálójára vonatkozó összes pgAudit-bejegyzés keresése a postgres-naplókban
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a Azure Database for PostgreSQL-rugalmas kiszolgálókon való bejelentkezésről](concepts-logging.md)
- [Megtudhatja, hogyan telepítheti a naplózást Azure Database for PostgreSQL rugalmas kiszolgálón, és hogyan érheti el a naplókat](howto-configure-and-access-logs.md)