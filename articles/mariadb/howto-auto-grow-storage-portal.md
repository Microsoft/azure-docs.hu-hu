---
title: Tárterület automatikus nőjön – Azure Portal – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az automatikus tárterület-nőt Azure Database for MariaDB a Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366113"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Tárterület automatikus Azure Database for MariaDB a Azure Portal
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgáló tárolóját úgy, hogy az ne befolyásolja a számítási feladatokat.

Amikor egy kiszolgáló eléri a lefoglalt tárhelykorlátot, a kiszolgáló csak olvashatóként lesz megjelölve. Ha azonban engedélyezi a tárterület automatikus növekedését, a kiszolgáló tárhelye nő a növekvő adatoknak való igazodás érdekében. A 100 GB-nál kisebb kiépítésű tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület mérete 5 GB-kal nő, amint a szabad tárterület a kiépített tárterület 1 GB-nál nagyobb vagy 10%-a alá csökken. A 100 GB-nál nagyobb kiépített tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület 5%-kal nő, ha a szabad tárterület a kiépített tárterület 10 GB-nál kisebb. Az itt megadott maximális tárolási [korlátok érvényesek.](concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőre lesz szüksége:
- Egy [Azure Database for MariaDB kiszolgáló](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Tárterület automatikus növekedésének engedélyezése 

Kövesse az alábbi lépéseket a MariaDB-kiszolgáló tárterületének automatikus növekedéséhez:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MariaDB kiszolgálót.

2. A MariaDB-kiszolgáló oldal Beállítások **fejléce** alatt kattintson a Tarifacsomag elemre a tarifacsomag lap megnyitásához. 

3. Az Automatikus növekedés szakaszban válassza az **Igen** lehetőséget a tárterület automatikus növekedésének engedélyezéséhez.

    ![Azure Database for MariaDB – Settings_Pricing_tier – Automatikus növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve van.

    ![Azure Database for MariaDB – sikeres automatikus növekedés](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Következő lépések

Ismerje [meg, hogyan hozhat létre metrikákra vonatkozó riasztásokat.](howto-alert-metric.md)
