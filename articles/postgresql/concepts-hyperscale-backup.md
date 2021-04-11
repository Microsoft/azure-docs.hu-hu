---
title: Biztonsági mentés és visszaállítás – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A véletlen sérülés vagy törlés adatainak védelme
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 90b2a39b9a5f3b4d011ff1a1ef3651dff75a1cf6
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968305"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL-nagy kapacitású (Citus)

Azure Database for PostgreSQL – a nagy kapacitású (Citus) automatikusan létrehozza az egyes csomópontok biztonsági másolatait, és helyileg redundáns tárolóban tárolja őket. A biztonsági mentések segítségével visszaállíthatja a nagy kapacitású-(Citus-) fürtöt egy adott időpontra. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

## <a name="backups"></a>Biztonsági másolatok

Naponta legalább egyszer Azure Database for PostgreSQL pillanatfelvételt készít az adatfájlokról és az adatbázis-tranzakciós naplóról. A biztonsági mentések lehetővé teszik a kiszolgálók bármely időpontra történő visszaállítását a megőrzési időtartamon belül. (A megőrzési időtartam jelenleg 35 nap az összes fürthöz.) Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik.

A rendelkezésre állási zónákat támogató Azure-régiókban a biztonsági mentési Pillanatképek három rendelkezésre állási zónában tárolódnak. Amíg legalább egy rendelkezésre állási zóna online állapotú, a nagy kapacitású (Citus) fürt visszaállítható.

Nem lehet exportálni a biztonságimásolat-fájlokat. Csak a Azure Database for PostgreSQL-ben történő visszaállítási műveletekhez használhatók.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

Az aktuális biztonsági mentési tár díjszabását a Azure Database for PostgreSQL-nagy kapacitású (Citus) [díjszabási oldalán](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)tekintheti meg.

## <a name="restore"></a>Visszaállítás

Azure Database for PostgreSQL egy nagy kapacitású-(Citus-) fürt visszaállítása új fürtöt hoz létre az eredeti csomópontok biztonsági másolatai közül. 

> [!IMPORTANT]
>A nagy kapacitású-(Citus-) fürtöt csak ugyanabban az előfizetésben és erőforráscsoporthoz, illetve egy másik fürt nevével állíthatja vissza.


> [!IMPORTANT]
> A törölt nagy kapacitású-(Citus-) fürtök nem állíthatók vissza. Ha törli a fürtöt, a fürthöz tartozó összes csomópont törölve lesz, és nem állítható helyre. A fürt erőforrásainak védelmét, az üzembe helyezést követően a véletlen törlés vagy a váratlan módosítások miatt a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Időponthoz tartozó visszaállítás (PITR)

Az elmúlt 35 napon belül bármikor visszaállíthat egy fürtöt bármely időpontra.
Az időponthoz való visszaállítás több esetben is hasznos lehet. Ez történik például ha egy felhasználó véletlenül adatokat töröl, eltávolít egy fontos táblát vagy adatbázist, vagy egy alkalmazás véletlenül rossz adatokkal írja felül a jó adatokat.

A visszaállítási folyamat egy új fürtöt hoz létre ugyanabban az Azure-régióban, előfizetésben és erőforráscsoporthoz, mint az eredeti. A fürt rendelkezik az eredeti konfigurációval: a csomópontok száma, a virtuális mag száma, a tárterület mérete, a felhasználói szerepkörök, a PostgreSQL-verzió és a Citus-bővítmény verziója.

A tűzfalbeállítások és a PostgreSQL-kiszolgáló paraméterei nem őrződnek meg az eredeti kiszolgáló csoportjából, alapértékre állnak vissza. A tűzfal megakadályozza az összes kapcsolatot. A visszaállítás után manuálisan kell módosítania ezeket a beállításokat.

> [!IMPORTANT]
> Meg kell nyitnia egy támogatási kérést a nagy kapacitású-(Citus-) fürt időponthoz való visszaállításának végrehajtásához.

### <a name="post-restore-tasks"></a>Post-restore tasks

A helyreállítási mechanizmusból való visszaállítás után a következőket kell tennie a felhasználók és alkalmazások biztonsági mentésének és futtatásának visszaszerzéséhez:

* Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra
* Győződjön meg arról, hogy a felhasználók csatlakozhatnak a megfelelő kiszolgálói szintű tűzfallal. Ezek a szabályok nem másolódnak át az eredeti kiszolgáló csoportjából.
* Szükség szerint módosítsa a PostgreSQL-kiszolgáló paramétereit. A paraméterek nem másolódnak át az eredeti kiszolgáló csoportjából.
* Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben
* Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [Azure rendelkezésre állási zónáit](../availability-zones/az-overview.md).
* A [javasolt riasztások](./howto-hyperscale-alert-on-metric.md#suggested-alerts) beállítása nagy kapacitású-(Citus-) kiszolgálócsoportok esetén.
