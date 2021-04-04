---
title: Tárterület automatikus növekedése – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálható a tárterület automatikus növekedése a Azure Database for PostgreSQL-Single Server Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: d49047e9b3a65da90f4a5ac9e4b3cb1e32bd59a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489982"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Tárterület automatikus növekedése a Azure Database for PostgreSQL egyetlen kiszolgáló Azure Portal használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

Ha egy kiszolgáló eléri a lefoglalt tárterület korlátját, a kiszolgáló csak olvashatóként van megjelölve. Ha azonban engedélyezi a tárterület automatikus növekedését, a kiszolgáló tárterülete megnöveli a növekvő adatmennyiséget. A 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiépített tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](./concepts-pricing-tiers.md#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Tárterület automatikus növekedésének engedélyezése 

A PostgreSQL-kiszolgáló tárterület automatikus növekedésének beállításához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálót.

2. A PostgreSQL-kiszolgáló lap **Beállítások** területén kattintson az **árképzési** csomag elemre a díjszabási csomag megnyitásához.

3. Az **automatikus növekedés** szakaszban válassza az **Igen** lehetőséget a tárterület automatikus növekedésének engedélyezéséhez.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL-Settings_Pricing_tier – automatikus növekedés":::

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve lett.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL – az automatikus növekedés sikere":::

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).