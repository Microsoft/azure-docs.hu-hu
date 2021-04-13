---
title: Automatikus tárterület-nő – Azure Portal - Azure Database for PostgreSQL – Egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan konfigurálhatja a tárterület automatikus növekedését a Azure Portal az Azure Database for PostgreSQL – Egyetlen kiszolgáló használatával
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366130"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Tárterület automatikus méretezése a Azure Portal használatával Azure Database for PostgreSQL – Egyetlen kiszolgáló
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL kiszolgáló tárolóját úgy, hogy az ne befolyásolja a számítási feladatokat.

Amikor egy kiszolgáló eléri a lefoglalt tárhelykorlátot, a kiszolgáló csak olvashatóként lesz megjelölve. Ha azonban engedélyezi a tárterület automatikus növekedését, a kiszolgáló tárterülete nő a növekvő adatoknak való igazodás érdekében. A 100 GB-nál kisebb kiépített tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület mérete 5 GB-kal nő, amint a szabad tárterület a kiépített tárterület 1 GB-nál nagyobb vagy 10%-a alá csökken. A 100 GB-nál nagyobb kiépítésű tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület 5%-kal nő, ha a szabad tárterület a kiépített tárterület 10 GB-nál kisebb. Az itt megadott maximális tárolási [korlátok érvényesek.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Tárterület automatikus növekedésének engedélyezése 

Kövesse az alábbi lépéseket a PostgreSQL-kiszolgáló tárterületének automatikus növekedéséhez:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL kiszolgálót.

2. A PostgreSQL-kiszolgáló lapon, a Beállítások alatt kattintson a **Tarifacsomag** elemre a tarifacsomag lap megnyitásához. 

3. Az Automatikus **növekedés szakaszban** válassza az **Igen lehetőséget** a tárterület automatikus növekedésének engedélyezéséhez.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL – Settings_Pricing_tier – Automatikus növekedés":::

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve van.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL – sikeres automatikus növekedés":::

## <a name="next-steps"></a>Következő lépések

Ismerje [meg, hogyan hozhat létre metrikákra vonatkozó riasztásokat.](howto-alert-on-metric.md)
