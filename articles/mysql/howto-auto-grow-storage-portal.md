---
title: Tárterület automatikus növekedése – Azure Portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az automatikus növekedés tárolását Azure Database for MySQL használatával Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5242ef7d5d2eb9fb3aca2138ad525199c8a6afee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109947"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>A tároló automatikus növekedése Azure Database for MySQL a Azure Portal használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

Ha egy kiszolgáló eléri a lefoglalt tárterület korlátját, a kiszolgáló csak olvashatóként van megjelölve. Ha azonban engedélyezi a tárterület automatikus növekedését, a kiszolgáló tárterülete megnöveli a növekvő adatmennyiséget. A 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiépített tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](./concepts-pricing-tiers.md#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Tárterület automatikus növekedésének engedélyezése 

A MySQL Server Storage automatikus növekedésének beállításához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL-kiszolgálót.

2. A MySQL-kiszolgáló lapon, a **Beállítások** fejléc alatt kattintson a **díjszabási** csomag elemre a díjszabási szintek lap megnyitásához.

3. Az automatikus növekedés szakaszban válassza az **Igen** lehetőséget a tárterület automatikus növekedésének engedélyezéséhez.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL-Settings_Pricing_tier – automatikus növekedés":::

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve lett.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL – az automatikus növekedés sikere":::

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).