---
title: Kiszolgáló újraindítása – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL rugalmas kiszolgálót a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: c44e1da46b969b2d359a225e9d310160ce2092ce
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681560"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Azure Database for MySQL rugalmas kiszolgáló újraindítása Azure Portal használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL rugalmas kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.

Az újraindítás befejezéséhez szükséges idő a MySQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL rugalmas kiszolgáló](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindítása

A következő lépések újraindítják a MySQL-kiszolgálót:

1. A Azure Portal válassza ki a Azure Database for MySQL rugalmas kiszolgálót.

2. A kiszolgáló **Áttekintés** lapjának eszköztárán kattintson az **Újraindítás** elemre.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – áttekintés – Újraindítás gomb":::

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – áttekintés – Újraindítás gomb" értékűre változik.

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – áttekintés – Újraindítás gomb":::

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – áttekintés – Újraindítás gomb":::

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: Azure Database for MySQL rugalmas kiszolgáló létrehozása Azure Portal használatával](quickstart-create-server-portal.md)
