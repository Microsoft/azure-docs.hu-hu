---
title: Biztonsági mentés és visszaállítás – rugalmas skálázás (Citus) – Azure Database for PostgreSQL
description: Adatok véletlen sérüléssel vagy törléssel szembeni védelme
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8dfc82ce79f33553be5220b52a1e415d99c26518
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483911"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL – rugalmas skálázás (Citus)

Azure Database for PostgreSQL – rugalmas skálázás (Citus) biztonsági másolatokat hoz létre az egyes csomópontokról, és helyileg redundáns tárolóban tárolja azokat. A biztonsági másolatok segítségével visszaállíthatja rugalmas skálázás (Citus) fürtöt egy adott időpontra. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

## <a name="backups"></a>Biztonsági másolatok

Naponta legalább egyszer Azure Database for PostgreSQL pillanatképet készít az adatfájlokról és az adatbázis-tranzakciós naplóról. A biztonsági másolatokkal a megőrzési időtartamon belül bármely időpontra visszaállíthatja a kiszolgálót. (A megőrzési időtartam jelenleg 35 nap az összes fürtön.) Minden biztonsági másolat AES 256 bites titkosítással van titkosítva.

A rendelkezésre állási zónákat támogató Azure-régiókban a biztonsági mentési pillanatképek három rendelkezésre állási zónában vannak tárolva. Amíg legalább egy rendelkezésre állási zóna elérhető, a rugalmas skálázás (Citus) visszaállítható.

A biztonsági mentési fájlok nem exportálhatók. Ezek csak visszaállítási műveletekhez használhatók a Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>A biztonsági mentés tárolási költsége

A Backup Storage aktuális díjszabását a Azure Database for PostgreSQL – rugalmas skálázás (Citus) [oldalon láthatja.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Visszaállítás

Ebben Azure Database for PostgreSQL fürt visszaállítása egy rugalmas skálázás (Citus) új fürtöt hoz létre az eredeti csomópontok biztonsági másolataiból. 

> [!IMPORTANT]
>A fürtöt csak rugalmas skálázás (Citus) előfizetésen és erőforráscsoporton belül, más fürtnévvel lehet visszaállítani.


> [!IMPORTANT]
> Törölt rugalmas skálázás (Citus) fürtök nem állíthatók vissza. Ha törli a fürtöt, a fürthöz tartozó összes csomópont törlődik, és nem állítható helyre. A fürterőforrások, az üzembe helyezés után, a véletlen törléstől vagy váratlan változásoktól való védelme érdekében a rendszergazdák kihasználhatják a [felügyeleti zárolásokat.](../azure-resource-manager/management/lock-resources.md)

### <a name="point-in-time-restore-pitr"></a>Időponthoz időben való visszaállítás (PITR)

A fürtök az elmúlt 35 napban bármikor visszaállíthatóak.
Az időponthoz időben történő visszaállítás több helyzetben is hasznos. Ez történik például ha egy felhasználó véletlenül adatokat töröl, eltávolít egy fontos táblát vagy adatbázist, vagy egy alkalmazás véletlenül rossz adatokkal írja felül a jó adatokat.

A visszaállítási folyamat létrehoz egy új fürtöt ugyanabban az Azure-régióban, előfizetésben és erőforráscsoportban, mint az eredeti. A fürt az eredeti konfigurációval rendelkezik: azonos számú csomópont, virtuális magok száma, tárterületméret, felhasználói szerepkörök, PostgreSQL-verzió és a Citus-bővítmény verziója.

A tűzfalbeállítások és a PostgreSQL-kiszolgálóparaméterek nem maradnak meg az eredeti kiszolgálócsoportból, a rendszer visszaállítja őket az alapértelmezett értékekre. A tűzfal minden kapcsolatot letilt. Ezeket a beállításokat manuálisan kell módosítania a visszaállítás után. Általánosságban lásd a javasolt visszaállítás utáni [feladatok listáját.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a kiszolgálócsoport [visszaállításának lépéseit a](howto-hyperscale-restore-portal.md) Azure Portal.
* További tudnivalók az [Azure rendelkezésre állási zónákról.](../availability-zones/az-overview.md)
