---
title: Kiszolgáló újraindítása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-egy kiszolgálót a Azure Portal használatával.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: faa61ff477f44347755890dc59ebf4b917afda6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97882942"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure Database for PostgreSQL újraindítása – egyetlen kiszolgáló a Azure Portal használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.
 
> [!NOTE] 
> Az újraindítás befejezéséhez szükséges idő a PostgreSQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt. Az ellenőrzőpontok gyakoriságát is érdemes lehet emelni. Az ellenőrzőpontokkal kapcsolatos paraméterek értékeit is beállíthatja, beleértve a következőket: `max_wal_size` . Azt is javasoljuk, hogy `CHECKPOINT` a-kiszolgáló újraindítása előtt futtassa a parancsot.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindítása

A következő lépések újraindítják a PostgreSQL-kiszolgálót:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Azure Database for PostgreSQL kiszolgálót.

2. A kiszolgáló **Áttekintés** lapjának eszköztárán kattintson az **Újraindítás** elemre.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL – áttekintés – Újraindítás gomb":::

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL – újraindítás megerősítése":::

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL – újraindítás állapota":::

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL – sikeres újraindítás":::

## <a name="next-steps"></a>Következő lépések

Útmutató [Paraméterek beállításához a Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)
