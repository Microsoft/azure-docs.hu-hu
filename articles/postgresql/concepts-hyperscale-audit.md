---
title: Naplózás naplózása – Azure Database for PostgreSQL – nagy kapacitású (Citus)
description: A Azure Database for PostgreSQL-nagy kapacitású (Citus) naplózási pgAudit kapcsolatos fogalmak.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: d8a21a5583ec4655a2ee8593e50be5c7b5f702b7
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227592"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Naplózás naplózása Azure Database for PostgreSQL-nagy kapacitású (Citus)

A Azure Database for PostgreSQL-nagy kapacitású (Citus) adatbázis-tevékenységeinek naplózása a PostgreSQL-naplózási bővítményen keresztül érhető el: [pgAudit](https://www.pgaudit.org/). a pgAudit részletes munkamenet-és/vagy objektum-naplózási naplózást biztosít.

> [!IMPORTANT]
> a pgAudit előzetes verzióban érhető el Azure Database for PostgreSQL-nagy kapacitású (Citus)

Ha az Azure-beli erőforrás-szintű naplókat szeretné használni a számítási és tárolási skálázási műveletekhez, tekintse meg az [Azure-tevékenység naplóját](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Használati megfontolások
Alapértelmezés szerint a pgAudit naplóutasítások a normál naplóutasításokkal együtt vannak kibocsátva a Postgres standard naplózási eszközének használatával. A Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatásban konfigurálhatja az Azure Monitor log Store-ba küldendő naplókat az Log Analytics későbbi elemzéséhez. Ha engedélyezi Azure Monitor erőforrás-naplózást, a rendszer automatikusan elküldi a naplókat (JSON formátumban) az Azure Storage-ba, Event Hubsba és/vagy Azure Monitor naplókba, attól függően, hogy Ön milyen választ.

## <a name="enabling-pgaudit"></a>PgAudit engedélyezése

Az pgAudit bővítmény előre telepítve és engedélyezve van az összes nagy kapacitású (Citus) kiszolgálói csoport csomópontjain. Az engedélyezéséhez nincs szükség műveletre.

## <a name="pgaudit-settings"></a>pgAudit-beállítások

a pgAudit lehetővé teszi a munkamenet vagy az objektum naplózási naplózásának konfigurálását. A [munkamenet naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) a végrehajtott utasítások részletes naplóit bocsátja ki. Az [objektum naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) az adott kapcsolatokra terjed ki. Dönthet úgy is, hogy beállít egy vagy mindkét típusú naplózást. 

> [!NOTE]
> a pgAudit-beállítások globálisan vannak megadva, és nem adható meg adatbázis vagy szerepkör szintjén.
>
> Emellett a pgAudit beállítások egy adott kiszolgálócsoport számára is meg vannak adva. Ha módosítani szeretné az összes csomópontot, egyenként kell alkalmaznia az egyes csomópontokra.

A naplózás megkezdéséhez konfigurálnia kell a pgAudit paramétereket. Az [pgAudit dokumentációja](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) az egyes paraméterek definícióját tartalmazza. Először tesztelje a paramétereket, és győződjön meg róla, hogy a várt működést tapasztalja.

> [!NOTE]
> Ha a be értékre állítja, a `pgaudit.log_client` rendszer átirányítja a naplókat egy ügyfél-folyamatba (például psql) a fájlba való írás helyett. Ezt a beállítást általában javasolt letiltva hagyni. <br> <br>
> `pgaudit.log_level` csak akkor engedélyezett, ha `pgaudit.log_client` be van kapcsolva.

> [!NOTE]
> A Azure Database for PostgreSQL-nagy kapacitású (Citus) `pgaudit.log` nem állítható be `-` (mínusz) a pgAudit dokumentációjában leírtak szerint. Az összes kötelező utasítási osztályt (olvasás, írás stb.) egyedileg meg kell adni.

## <a name="audit-log-format"></a>Auditnapló formátuma
Az egyes naplózási bejegyzéseket a rendszer a `AUDIT:` naplósor elejéhez közel jelzi. A bejegyzés további részének formátuma a [pgAudit dokumentációjában](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)található.

## <a name="getting-started"></a>Első lépések
A gyors kezdéshez állítsa a következőt: `pgaudit.log` `WRITE` , majd nyissa meg a kiszolgálói naplókat a kimenet áttekintéséhez. 

## <a name="viewing-audit-logs"></a>Naplók megtekintése
A naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage szolgáltatással kapcsolatban lásd a [Storage-fiók naplózása](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) című cikket. Event Hubs esetében tekintse meg a [stream Azure-naplók](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) című cikket.

Azure Monitor naplók esetében a naplók a kiválasztott munkaterületre kerülnek. A postgres-naplók a **AzureDiagnostics** -gyűjtési módot használják, így a AzureDiagnostics táblából is lekérdezhető. A táblázatban szereplő mezők a következőkben olvashatók. További információ a lekérdezésekről és a riasztásokról: [Azure monitor naplók lekérdezése](../azure-monitor/log-query/log-query-overview.md) – áttekintés.

Ezt a lekérdezést használhatja a kezdéshez. A riasztásokat lekérdezések alapján is konfigurálhatja.

Az elmúlt nap egy adott kiszolgálójára vonatkozó összes pgAudit-bejegyzés keresése a postgres-naplókban
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan telepítheti a naplózást Azure Database for PostgreSQL-nagy kapacitású (Citus), és hogyan érheti el a naplókat](howto-hyperscale-logging.md)
