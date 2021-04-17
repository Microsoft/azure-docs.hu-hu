---
title: Kiszolgálócsoport frissítése – rugalmas skálázás (Citus) – Azure Database for PostgreSQL
description: Ez a cikk a PostgreSQL és a Citus frissítését ismerteti a Azure Database for PostgreSQL – rugalmas skálázás (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518898"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Kiszolgálócsoport rugalmas skálázás (Citus) frissítése

Ezek az utasítások a PostgreSQL új főverzióra való frissítését ismertetik az összes kiszolgálócsoport-csomóponton.

## <a name="test-the-upgrade-first"></a>Először a frissítés tesztelése

A PostgreSQL frissítése több változást okoz, mint amilyennek elképzelhető, mert a rugalmas skálázás (Citus) az adatbázis-bővítményeket is [frissíti,](concepts-hyperscale-extensions.md)beleértve a Citus-bővítményt is.
Az éles környezet frissítése előtt határozottan javasoljuk, hogy tesztelje alkalmazását az új PostgreSQL- és Citus-verzióval.

A tesztelés kényelmes módja, ha időponthoz időben visszaállítja a [kiszolgálócsoportot.](concepts-hyperscale-backup.md#restore) Frissítse a másolatot, és tesztelje rajta az alkalmazást. Miután ellenőrizte, hogy minden megfelelően működik-e, frissítse az eredeti kiszolgálócsoportot.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Kiszolgálócsoport frissítése a Azure Portal

1. A **kiszolgálócsoport** Áttekintés rugalmas skálázás (Citus) kattintson a Frissítés **gombra.**
1. Megjelenik egy párbeszédpanel, amely a PostgreSQL és a Citus aktuális verzióját mutatja.
   Válasszon egy új PostgreSQL-verziót a **Frissítés a következőre** listában.
1. Ellenőrizze, hogy a **Citus verziószámában a frissítés után a** várt érték van-e megszabadulva.
   Ez az érték a kiválasztott PostgreSQL-verzió alapján változik.
1. A **folytatáshoz kattintson** a Frissítés gombra.

## <a name="next-steps"></a>Következő lépések

* További információ [a támogatott PostgreSQL-verziókról.](concepts-hyperscale-versions.md)
* Tekintse [meg, hogy mely bővítmények](concepts-hyperscale-extensions.md) vannak csomagolva az egyes PostgreSQL-verziókhoz egy rugalmas skálázás (Citus)-csoportban.
