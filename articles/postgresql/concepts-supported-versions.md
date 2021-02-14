---
title: Támogatott verziók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egyetlen kiszolgálón támogatott postgres fő-és alverziókat ismerteti.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 41662c5e4cc0ed9458f8b1b1279e2753daed789f
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518453"
---
# <a name="supported-postgresql-major-versions"></a>Támogatott PostgreSQL főverziók

A támogatási szabályzat részleteiért tekintse meg [Azure Database for PostgreSQL verziószámozási](concepts-version-policy.md) szabályzatot.

A Azure Database for PostgreSQL jelenleg a következő főbb verziókat támogatja:

## <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió
Az aktuális alverzió 11,6. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-6.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-10"></a>PostgreSQL 10-es verzió
Az aktuális alverzió 10,11. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/10/static/release-10-11.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-96"></a>PostgreSQL 9,6-es verzió
Az aktuális másodlagos kiadás a 9.6.16. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-95-retired"></a>PostgreSQL 9,5-es verzió (kivezetve)
A postgres-Közösség [verziószámozási szabályzatával](https://www.postgresql.org/support/versioning/)összehangolva a Azure Database for PostgreSQL postgres 9,5-es verziójának kivonása 2021. február 11. További részletekért és korlátozásért tekintse meg [Azure Database for PostgreSQL verziószámozási szabályzatát](concepts-version-policy.md) . Ha ezt a főverziót futtatja, frissítsen egy újabb verzióra, lehetőleg a PostgreSQL 11-et a lehető leghamarabb.

## <a name="managing-upgrades"></a>Frissítések kezelése
A PostgreSQL-projekt rendszeresen kiad kisebb kiadásokat a jelentett hibák kijavítása érdekében. Az Azure Database for PostgreSQL a szolgáltatás havi rendszerességű üzembehelyezései során automatikusan alverziókkal frissíti a kiszolgálókat. 

A főverziók automatikus helyi frissítése nem támogatott. Magasabb főverzióra való frissítéshez 
   * A főverzió frissítéseiben dokumentált módszerek egyikét használja a [dump és a Restore paranccsal](./how-to-upgrade-using-dump-and-restore.md).
   * [Pg_dump és pg_restore](./howto-migrate-using-dump-and-restore.md) használatával helyezzen át egy adatbázist az új motor verziójával létrehozott kiszolgálóra.
   * Az [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) használatával online frissítéseket hajthat végre.

### <a name="version-syntax"></a>Verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _kisebb_ kiadású frissítés. A 10 – _11 verzió a főverzió frissítése_ .

## <a name="next-steps"></a>Következő lépések
A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
