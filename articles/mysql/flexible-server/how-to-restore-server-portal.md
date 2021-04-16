---
title: Rugalmas Azure Database for MySQL visszaállítása a Azure Portal.
description: Ez a cikk azt ismerteti, hogyan végezhet visszaállítási műveleteket Azure Database for MySQL rugalmas kiszolgálón a Azure Portal
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502045"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Rugalmas kiszolgáló (előzetes verzió) időponthoz Azure Database for MySQL visszaállítása a Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk lépésenként beható eljárásokat tartalmaz a rugalmas kiszolgálókon biztonsági másolatok használatával, időponthoz időben való helyreállítások végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez a következőre lesz szüksége:

-   Rugalmas kiszolgálóval Azure Database for MySQL kell lennie.

## <a name="restore-to-the-latest-restore-point"></a>Visszaállítás a legutóbbi visszaállítási pontra

Kövesse az alábbi lépéseket a rugalmas kiszolgáló visszaállításához egy legkorábbi meglévő biztonsági másolat használatával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki azt a rugalmas kiszolgálót, amelyről vissza szeretné állítani a biztonsági másolatot.

2.  Kattintson **az Áttekintés** elemre a bal oldali panelen.

3.  Az áttekintési lapon kattintson a Visszaállítás **elemre.**

4.  Megjelenik a Visszaállítás lap, amely lehetőséget ad a Legutóbbi visszaállítási pont és az **Egyéni** visszaállítási pont közötti választásra.

5.  Válassza **a Legutóbbi visszaállítási pont lehetőséget.**

6.  Adjon meg egy új kiszolgálónevet a **Visszaállítás új kiszolgálóra mezőben.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Legkorábbi visszaállítási idő":::

8.  Kattintson az **OK** gombra.

9.  Megjelenik egy értesítés arról, hogy a visszaállítási művelet el lett indítva.

## <a name="restoring-to-a-custom-restore-point"></a>Visszaállítás egyéni visszaállítási pontra

Kövesse az alábbi lépéseket a rugalmas kiszolgáló visszaállításához egy legkorábbi meglévő biztonsági másolat használatával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki azt a rugalmas kiszolgálót, amelyről vissza szeretné állítani a biztonsági másolatot.

2.  Az áttekintési lapon kattintson a Visszaállítás **elemre.**

3.  Megjelenik a Visszaállítás oldal, amely a Legkorábbi visszaállítási pont és az Egyéni visszaállítási pont lehetőségek közül választhat.

4.  Válassza **az Egyéni visszaállítási pont lehetőséget.**

5.  Válassza ki a dátumot és az időt.

6.  Adjon meg egy új kiszolgálónevet a **Visszaállítás új kiszolgálóra mezőben.**

6.  Adjon meg egy új kiszolgálónevet a **Visszaállítás új kiszolgálóra mezőben.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="áttekintés megtekintése":::

7.  Kattintson az **OK** gombra.

8.  Megjelenik egy értesítés arról, hogy a visszaállítási művelet el lett indítva.


## <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása
A visszaállítás befejezése után a következő feladatokat kell végrehajtania, hogy a felhasználók és az alkalmazások ismét el tudjanak indulni:

- Ha az új kiszolgálót az eredeti kiszolgáló cseréjére szánta, irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra.
- Győződjön meg arról, hogy a megfelelő VNet-szabályok vannak bevetve a felhasználók számára a csatlakozáshoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy a megfelelő bejelentkezési adatok és adatbázisszintű engedélyek meg vannak biztosítva.
- Konfigurálja az újonnan visszaállított kiszolgálóhoz megfelelő riasztásokat.


## <a name="next-steps"></a>Következő lépések
További információ az [üzletmenet-folytonosságról](concepts-business-continuity.md)
