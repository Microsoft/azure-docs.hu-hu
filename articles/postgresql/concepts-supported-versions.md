---
title: Támogatott verziók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egyetlen kiszolgálón támogatott postgres fő-és alverziókat ismerteti.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658519"
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

## <a name="postgresql-version-95"></a>PostgreSQL 9,5-es verzió
Az aktuális másodlagos kiadás a 9.5.20. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) , amely az ebben a másodlagos kiadásban található javításokat és javításokat ismerteti.

> [!NOTE]
> A postgres közösségi [verziószámozási szabályzattal](https://www.postgresql.org/support/versioning/)való összehangolása Azure Database for PostgreSQL 9,5-es verziójának kivonásával történik a 2021. február 11-én a postgres. További részletekért és korlátozásért tekintse meg [Azure Database for PostgreSQL verziószámozási szabályzatát](concepts-version-policy.md) .

## <a name="managing-upgrades"></a>Frissítések kezelése
A PostgreSQL-projekt rendszeresen kiad kisebb kiadásokat a jelentett hibák kijavítása érdekében. Azure Database for PostgreSQL a szolgáltatás havi üzembe helyezése során a rendszer a kisebb kiadású kiszolgálók automatikus javítását végzi. 

A főverziók automatikus helyi frissítése nem támogatott. A következő főverzióra való frissítéshez 
   * A főbb verziófrissítések elvégzésének különböző módszerei a [dump és a Restore használatával](./how-to-upgrade-using-dump-and-restore.md)
   * A [pg_dump és a pg_restore](./howto-migrate-using-dump-and-restore.md) használatával helyezzen át egy adatbázist az új motor verziójával létrehozott kiszolgálóra
   * Azt is megteheti, hogy a PostgreSQL 10 és 11 közötti verziójára frissít az [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) használatával

### <a name="version-syntax"></a>Verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _kisebb_ kiadású frissítés. A 10 – _11 verzió a főverzió frissítése_ .

## <a name="next-steps"></a>Következő lépések
A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
