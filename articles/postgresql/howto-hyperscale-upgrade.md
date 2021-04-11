---
title: Frissítési kiszolgáló csoport – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ez a cikk azt ismerteti, hogyan frissítheti a PostgreSQL-t és a Citus-t a Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatásban.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023979"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport frissítése

Ezek az utasítások azt írják le, hogyan lehet a PostgreSQL új főverziójára frissíteni az összes kiszolgálói csoport csomópontjain.

## <a name="test-the-upgrade-first"></a>Először a frissítés tesztelése

A PostgreSQL frissítése több változást okoz, mint amennyit valószínűleg elképzel, mert a nagy kapacitású (Citus) frissíti az [adatbázis-bővítményeket](concepts-hyperscale-extensions.md)is, beleértve a Citus-bővítményt is.
Javasoljuk, hogy az éles környezet frissítése előtt tesztelje az alkalmazást az új PostgreSQL-sel és a Citus-verzióval.

A tesztelésének kényelmes módja, ha az [időponthoz tartozó visszaállítással](concepts-hyperscale-backup.md#point-in-time-restore-pitr)készíti el a kiszolgálócsoport másolatát. Frissítse a példányt, és tesztelje az alkalmazást. Miután meggyőződött róla, hogy minden megfelelően működik, frissítse az eredeti kiszolgálói csoportot.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Kiszolgálócsoport frissítése a Azure Portal

1. A nagy kapacitású (Citus) kiszolgálócsoport **Áttekintés** szakaszában kattintson a **frissítés** gombra.
1. Megjelenik egy párbeszédpanel, amely a PostgreSQL és a Citus aktuális verzióját jeleníti meg.
   Válasszon egy új PostgreSQL-verziót a **frissítés** listán.
1. A **frissítés után ellenőrizze a Citus verziójának** értékét.
   Ez az érték a kiválasztott PostgreSQL-verzió alapján változik.
1. A folytatáshoz kattintson a **frissítés** gombra.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg a [PostgreSQL támogatott verzióit](concepts-hyperscale-versions.md).
* Tekintse meg, hogy [mely bővítmények](concepts-hyperscale-extensions.md) vannak becsomagolva a PostgreSQL egyes verzióihoz egy nagy kapacitású (Citus).
