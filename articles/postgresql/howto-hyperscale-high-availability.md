---
title: Magas rendelkezésre állás konfigurálása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Magas rendelkezésre állás engedélyezése vagy letiltása
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907397"
---
# <a name="configure-hyperscale-citus-high-availability"></a>A nagy kapacitású (Citus) magas rendelkezésre állásának konfigurálása

A Azure Database for PostgreSQL-nagy kapacitású (Citus) magas rendelkezésre állást biztosít az adatbázis leállásának elkerülése érdekében. HA engedélyezve van, a kiszolgálócsoport minden csomópontja készenléti állapotba kerül. Ha az eredeti csomópont nem Kifogástalan állapotba kerül, a rendszer lecseréli a készenléti állapotot.

> [!IMPORTANT]
> Mivel a HA megduplázza a csoportba tartozó kiszolgálók számát, a rendszer a költségeket is megduplázza.

A beállítás engedélyezése a kiszolgálócsoport létrehozása során lehetséges, vagy később a Azure Portal lévő kiszolgálói csoport **számítási és tárolási** lapján. A felhasználói felület mindkét esetben hasonló. Húzza a csúszkát a **magas rendelkezésre álláshoz** a nem értéket Igen értékre:

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="Ha csúszka":::

Kattintson a **Save (Mentés** ) gombra a kijelölés alkalmazásához. HA engedélyezi, hogy a kiszolgálócsoport kiépítse a készenléti és adatstream-adatforgalmat, eltarthat egy ideig.

A kiszolgálócsoport **Áttekintés** lapja felsorolja az összes csomópontot és a készenléti állapotot, valamint egy **magas rendelkezésre állású** oszlopot, amely azt jelzi, hogy az összes csomóponton sikeresen engedélyezve van-e a ha.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="a kiszolgáló csoportjában található ha oszlop – áttekintés":::

### <a name="next-steps"></a>Következő lépések

További információ a [magas rendelkezésre állásról](concepts-hyperscale-high-availability.md).
