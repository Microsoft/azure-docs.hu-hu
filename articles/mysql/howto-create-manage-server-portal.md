---
title: Kiszolgáló kezelése – Azure Portal – Azure Database for MySQL
description: Megtudhatja, hogyan kezelheti Azure Database for MySQL-kiszolgálókat a Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897985"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure Database for MySQL-kiszolgáló kezelése a Azure Portal használatával

Ez a cikk bemutatja, hogyan kezelheti a Azure Database for MySQL-kiszolgálókat. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
>

## <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-server"></a>A kiszolgáló létrehozása

A rövid útmutatóból megtudhatja [, hogyan](quickstart-create-mysql-server-database-using-azure-portal.md) hozhat létre és kezdjen el egy Azure Database for MySQL-kiszolgálót.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A kiszolgáló létrehozása után a általános célú és a memória optimalizált szintjei között méretezheti az igények változását. A számítási és a memória méretezése a virtuális mag növelésével vagy csökkentésével is elvégezhető. A tárterület méretezhető (azonban nem méretezheti le a tárterületet).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Méretezés általános célú és a memória optimalizált szintjei között

Általános célúról a memóriára optimalizált és fordítva is méretezhető. A kiszolgáló létrehozása után az alapszintű csomagra való váltás nem támogatott.

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Válassza ki az **általános célú** vagy a **memória optimalizált** elemet attól függően, hogy mit kíván méretezni.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="A Azure Portal képernyőképe az alapszintű, általános célú vagy a memória optimalizált szintjeinek kiválasztásához Azure Database for MySQL":::

   > [!NOTE]
   > A rétegek módosítása a kiszolgáló újraindítását eredményezi.

3. A módosítások mentéséhez kattintson **az OK gombra** .

### <a name="scale-vcores-up-or-down"></a>Méretezési virtuális mag felfelé vagy lefelé

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Módosítsa a **virtuális mag** beállítást úgy, hogy áthelyezi a csúszkát a kívánt értékre.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Képernyőkép a Azure Portalról a virtuális mag lehetőség kiválasztásához Azure Database for MySQL":::

    > [!NOTE]
    > A skálázás virtuális mag a kiszolgáló újraindítását okozza.

3. A módosítások mentéséhez kattintson **az OK gombra** .

### <a name="scale-storage-up"></a>A tárterület felskálázása

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Módosítsa a **tárolási** beállításokat úgy, hogy a csúszkát felfelé helyezi a kívánt értékre.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Képernyőfelvétel a Azure Portal a tárolási skála kiválasztásához Azure Database for MySQL":::

   > [!NOTE]
   > A tárterület nem méretezhető le.

3. A módosítások mentéséhez kattintson **az OK gombra** .

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése

A rendszergazdai szerepkör jelszava a Azure Portal használatával módosítható.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **jelszó alaphelyzetbe állítása** lehetőséget.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Képernyőkép a Azure Portalről a jelszó alaphelyzetbe állításához Azure Database for MySQL":::

2. Adjon meg egy új jelszót, és erősítse meg a jelszót. A szövegmező a jelszó bonyolultságára vonatkozó követelményeket fogja kérni.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Képernyőkép a Azure Portalről a jelszó alaphelyzetbe állításához és a mentéshez Azure Database for MySQL":::

3. Az új jelszó mentéséhez kattintson **az OK gombra** .
 

> [!IMPORTANT]
> A kiszolgáló rendszergazdai jelszavának alaphelyzetbe állítása automatikusan visszaállítja a kiszolgáló-rendszergazdai jogosultságokat az alapértelmezett értékre. Ha véletlenül visszavonta egy vagy több kiszolgáló-rendszergazdai jogosultságot, érdemes lehet alaphelyzetbe állítani a kiszolgáló-rendszergazdai jelszót.
   
> [!NOTE]
> A kiszolgáló-rendszergazda felhasználó alapértelmezés szerint a következő jogosultságokkal rendelkezik: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCIÁk, INDEX, ALTER, adatbázisok megjelenítése, ideiglenes táblák létrehozása, táblák ZÁROLÁSa, végrehajtás, REPLIKÁLÁSi SLAVE, replikációs ügyfél, a nézet létrehozása, megjelenítés, létrehozás, RUTIN, ALTER RUTIN, felhasználó létrehozása, esemény, ESEMÉNYINDÍTÓ

## <a name="delete-a-server"></a>Kiszolgáló törlése

Ha már nincs szüksége rá, törölheti a kiszolgálót.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Képernyőkép a Azure Portalről a kiszolgáló törléséhez Azure Database for MySQL":::

2. Írja be a kiszolgáló nevét a beviteli mezőbe annak megerősítéséhez, hogy ez a kiszolgáló, amelyet törölni szeretne.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Képernyőkép a Azure Portalről a kiszolgáló törlésének megerősítéséhez Azure Database for MySQL":::

   > [!NOTE]
   > A kiszolgáló törlése visszafordíthatatlan.

3. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

- A [biztonsági másolatok és a kiszolgáló-visszaállítás](howto-restore-server-portal.md) ismertetése
- Ismerkedjen meg [a hangolási és figyelési lehetőségekkel Azure Database for MySQL](concepts-monitoring.md)
