---
title: Automatikus tárterület-nő – Azure Portal – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan engedélyezheti a tárterület automatikus növekedését a Azure Database for MySQL a Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: afa62d6e3ec93c18596ee5870361bf3b657619d1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365025"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>A tárterület automatikus Azure Database for MySQL a Azure Portal
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL kiszolgáló tárolóját úgy, hogy az ne befolyásolja a számítási feladatokat.

Amikor egy kiszolgáló eléri a lefoglalt tárhelykorlátot, a kiszolgáló csak olvashatóként lesz megjelölve. Ha azonban engedélyezi a tárterület automatikus növekedését, a kiszolgáló tárterülete nő a növekvő adatoknak való igazodás érdekében. A 100 GB-nál kisebb kiépített tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület mérete 5 GB-kal nő, amint a szabad tárterület a kiépített tárterület 1 GB-nál nagyobb vagy 10%-a alá csökken. A 100 GB-nál nagyobb kiépítésű tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület 5%-kal nő, ha a szabad tárterület a kiépített tárterület 10 GB-nál kisebb. Az itt megadott maximális tárolási [korlátok érvényesek.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Tárterület automatikus növekedésének engedélyezése 

Kövesse az alábbi lépéseket a MySQL-kiszolgáló tárterületének automatikus növekedéséhez:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL kiszolgálót.

2. A MySQL-kiszolgáló oldal Beállítások **fejlécében** kattintson a Tarifacsomag elemre a Tarifacsomag lap megnyitásához. 

3. Az Automatikus növekedés szakaszban válassza az **Igen lehetőséget** a tárterület automatikus növekedésének engedélyezéséhez.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL – Settings_Pricing_tier – Automatikus növekedés":::

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve van.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL – sikeres automatikus növekedés":::

## <a name="next-steps"></a>Következő lépések

Ismerje [meg, hogyan hozhat létre metrikákra vonatkozó riasztásokat.](howto-alert-on-metric.md)
