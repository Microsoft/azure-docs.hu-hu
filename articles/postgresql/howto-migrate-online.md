---
title: Minimális – állásidő áttelepítése Azure Database for PostgreSQL – egyetlen kiszolgálóra
description: Ez a cikk azt ismerteti, hogyan hajtható végre a PostgreSQL-adatbázisok minimális leállás utáni áttelepítése a Azure Database Migration Service használatával Azure Database for PostgreSQL egyetlen kiszolgálóra.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489795"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimális – állásidő áttelepítése Azure Database for PostgreSQL – egyetlen kiszolgálóra
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

A PostgreSQL áttelepítését minimális állásidővel Azure Database for PostgreSQLhatja a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) újonnan bevezetett **folyamatos szinkronizálási funkciójának** használatával. Ez a funkció korlátozza az alkalmazás által felmerült állásidő mennyiségét.

## <a name="overview"></a>Áttekintés
Az Azure DMS egy kezdeti terhelést hajt végre a helyszíni Azure Database for PostgreSQLon, majd folyamatosan szinkronizál minden új tranzakciót az Azure-ba, amíg az alkalmazás fut. Miután az adatok bekerültek a cél Azure-ra, egy rövid pillanatra (a minimális állásidőig) megvárhatják az utolsó adatköteget (az alkalmazás leállításának időpontjától egészen addig, amíg az alkalmazás nem áll készen az új forgalom elvégzésére), majd frissítenie kell a kapcsolódási karakterláncot, hogy az Azure-ra mutasson. Ha elkészült, az alkalmazás az Azure-ban fog élni.

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Folyamatos szinkronizálás a Azure Database Migration Service":::

## <a name="next-steps"></a>Következő lépések
- Tekintse meg a videós [alkalmazások modernizációját Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)segítségével, amely egy bemutatót tartalmaz, amely bemutatja, hogyan telepítse át a PostgreSQL-alkalmazásokat a Azure Database for PostgreSQLba.
- Tekintse [meg a PostgreSQL Áttelepítését Azure Database for PostgreSQL online-ba a DMS használatával](../dms/tutorial-postgresql-azure-postgresql-online.md)című oktatóanyagot.