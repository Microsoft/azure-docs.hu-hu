---
title: Biztonsági mentés és visszaállítás – rugalmas skálázás (Citus) – Azure Database for PostgreSQL
description: Adatok véletlen sérüléssel vagy törléssel szembeni védelme
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520173"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL – rugalmas skálázás (Citus)

Azure Database for PostgreSQL – rugalmas skálázás (Citus) biztonsági másolatokat hoz létre az egyes csomópontokról, és helyileg redundáns tárolóban tárolja azokat. A biztonsági másolatok segítségével visszaállíthatja a rugalmas skálázás (Citus)-kiszolgálócsoportot egy adott időpontra.
A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

## <a name="backups"></a>Biztonsági másolatok

Naponta legalább egyszer Azure Database for PostgreSQL pillanatképet készít az adatfájlokról és az adatbázis-tranzakciós naplóról. A biztonsági másolatokkal a megőrzési időtartamon belül bármely időpontra visszaállíthatja a kiszolgálót. (A megőrzési időtartam jelenleg 35 nap az összes kiszolgálócsoport esetében.) Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

A rendelkezésre állási zónákat támogató Azure-régiókban a biztonsági mentési pillanatképek három rendelkezésre állási zónában vannak tárolva. Amíg legalább egy rendelkezésre állási zóna elérhető, a rugalmas skálázás (Citus) visszaállítható.

A biztonsági mentési fájlok nem exportálhatók. Ezek csak visszaállítási műveletekhez használhatók a Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>A biztonsági mentés tárolási költsége

A Backup Storage aktuális díjszabását a Azure Database for PostgreSQL – rugalmas skálázás (Citus) [oldalon láthatja.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Visszaállítás

A kiszolgálócsoportokat rugalmas skálázás (Citus) az elmúlt 35 nap bármely időpontában visszaállíthatja.  Az időponthoz időben történő visszaállítás több helyzetben is hasznos. Ez történik például ha egy felhasználó véletlenül adatokat töröl, eltávolít egy fontos táblát vagy adatbázist, vagy egy alkalmazás véletlenül rossz adatokkal írja felül a jó adatokat.

> [!IMPORTANT]
> Törölt rugalmas skálázás (Citus) kiszolgálócsoportok nem állíthatók vissza. Ha törli a kiszolgálócsoportot, a kiszolgálócsoporthoz tartozó összes csomópont törlődik, és nem állítható helyre. A kiszolgálócsoport erőforrásainak védelme érdekében az üzembe helyezés után, a véletlen törléstől vagy váratlan változásoktól a rendszergazdák kihasználhatják a [felügyeleti zárolásokat.](../azure-resource-manager/management/lock-resources.md)

A visszaállítási folyamat létrehoz egy új kiszolgálócsoportot ugyanabban az Azure-régióban, előfizetésben és erőforráscsoportban, mint az eredeti. A kiszolgálócsoport az eredeti konfigurációval rendelkezik: azonos számú csomópont, virtuális magok száma, tárterületméret, felhasználói szerepkörök, PostgreSQL-verzió és a Citus-bővítmény verziója.

A tűzfalbeállítások és a PostgreSQL-kiszolgálóparaméterek nem maradnak meg az eredeti kiszolgálócsoportból, a rendszer visszaállítja őket az alapértelmezett értékekre. A tűzfal minden kapcsolatot letilt. Ezeket a beállításokat manuálisan kell módosítania a visszaállítás után. Általánosságban lásd a javasolt visszaállítás utáni [feladatok listáját.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a kiszolgálócsoport [visszaállításának lépéseit a](howto-hyperscale-restore-portal.md) Azure Portal.
* További tudnivalók az [Azure rendelkezésre állási zónákról.](../availability-zones/az-overview.md)
