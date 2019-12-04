---
title: Kiszolgáló kezelése – Azure Portal – Azure Database for MariaDB
description: Megtudhatja, hogyan kezelheti Azure Database for MariaDB-kiszolgálókat a Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b70b4810f32941938b6afe217f94e18a0f2f3523
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767126"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Azure Database for MariaDB-kiszolgáló kezelése a Azure Portal használatával
Ez a cikk bemutatja, hogyan kezelheti a Azure Database for MariaDB-kiszolgálókat. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

## <a name="sign-in"></a>Bejelentkezés
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-server"></a>Kiszolgáló létrehozása
A rövid útmutatóból megtudhatja [, hogyan](quickstart-create-mariadb-server-database-using-azure-portal.md) hozhat létre és kezdjen el egy Azure Database for MariaDB-kiszolgálót.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A kiszolgáló létrehozása után a általános célú és a memória optimalizált szintjei között méretezheti az igények változását. A számítási és a memória méretezése a virtuális mag növelésével vagy csökkentésével is elvégezhető. A tárterület méretezhető (azonban nem méretezheti le a tárterületet).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Méretezés általános célú és a memória optimalizált szintjei között

Általános célúról a memóriára optimalizált és fordítva is méretezhető. A kiszolgáló létrehozása után az alapszintű csomagra való váltás nem támogatott. 

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Válassza ki az **általános célú** vagy a **memória optimalizált**elemet attól függően, hogy mit kíván méretezni. 

    ![változás – díjszabás – réteg](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > A rétegek módosítása a kiszolgáló újraindítását eredményezi.

4. A módosítások mentéséhez kattintson **az OK gombra** .


### <a name="scale-vcores-up-or-down"></a>Méretezési virtuális mag felfelé vagy lefelé

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Módosítsa a **virtuális mag** beállítást úgy, hogy áthelyezi a csúszkát a kívánt értékre.

    ![méretezés – számítás](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > A skálázás virtuális mag a kiszolgáló újraindítását okozza.

3. A módosítások mentéséhez kattintson **az OK gombra** .


### <a name="scale-storage-up"></a>A tárterület felskálázása

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Módosítsa a **tárolási** beállításokat úgy, hogy a csúszkát felfelé helyezi a kívánt értékre.

    ![méretezés – tárterület](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > A tárterület nem méretezhető le.

3. A módosítások mentéséhez kattintson **az OK gombra** .


## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
A rendszergazdai szerepkör jelszava a Azure Portal használatával módosítható.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **jelszó alaphelyzetbe állítása**lehetőséget.

   ![Áttekintés](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Adjon meg egy új jelszót, és erősítse meg a jelszót. A szövegmező a jelszó bonyolultságára vonatkozó követelményeket fogja kérni.

   ![jelszó alaphelyzetbe állítása](./media/howto-create-manage-server-portal/reset-password.png)

3. Az új jelszó mentéséhez kattintson **az OK gombra** .


## <a name="delete-a-server"></a>Kiszolgáló törlése

Ha már nincs szüksége rá, törölheti a kiszolgálót. 

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **Törlés**lehetőséget.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Írja be a kiszolgáló nevét a beviteli mezőbe annak megerősítéséhez, hogy ez a kiszolgáló, amelyet törölni szeretne.

    ![Törlés megerősítése](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > A kiszolgáló törlése visszafordíthatatlan.

3. Válassza a **Törlés** elemet.


## <a name="next-steps"></a>Következő lépések
- A [biztonsági másolatok és a kiszolgáló-visszaállítás](howto-restore-server-portal.md) ismertetése
- Ismerkedjen meg [a hangolási és figyelési lehetőségekkel Azure Database for MariaDB](concepts-monitoring.md)
