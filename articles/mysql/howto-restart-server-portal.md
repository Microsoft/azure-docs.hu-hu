---
title: Kiszolgáló újraindítása – Azure Portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL kiszolgálót a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 2d19c0bd4ef5c49b8be82ffa11115ff6a1c6b302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541793"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Az Azure Database for MySQL-kiszolgáló újraindítása az Azure Portalon
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.

Az újraindítás befejezéséhez szükséges idő a MySQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindítása

A következő lépések újraindítják a MySQL-kiszolgálót:

1. A Azure Portal válassza ki a Azure Database for MySQL kiszolgálót.

2. A kiszolgáló **Áttekintés** lapjának eszköztárán kattintson az **Újraindítás** elemre.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – áttekintés – Újraindítás gomb":::

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – újraindítás megerősítése":::

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – újraindítás állapota":::

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – sikeres újraindítás":::

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: Azure Database for MySQL kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
