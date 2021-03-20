---
title: Infrastruktúra – kettős titkosítás – Azure Portal – Azure Database for MySQL
description: Ismerje meg, hogyan állíthatja be és kezelheti az infrastruktúra kettős titkosítását a Azure Database for MySQL számára.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241752"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Infrastruktúra kettős titkosítása Azure Database for MySQL

Ismerje meg, hogyan állíthatja be és felügyelheti a Azure Database for MySQL infrastruktúra kettős titkosítását.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Azure Database for MySQL-kiszolgáló létrehozása infrastruktúra-kettős titkosítással – portál

Az alábbi lépéseket követve hozzon létre egy Azure Database for MySQL-kiszolgálót a Azure Portal infrastruktúra-dupla titkosítással:

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for MySQL** elemet. Megadhatja a **MySQL** kifejezést is a keresőmezőbe a szolgáltatás megtalálásához.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Az Azure Database for MySQL menüpont":::

3. Adja meg a kiszolgáló alapszintű információit. Válassza a **További beállítások** lehetőséget, és engedélyezze az **infrastruktúra kettős titkosítás** jelölőnégyzetet a paraméter beállításához.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for MySQL kijelölések":::

4. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kiszolgáló kiépítéséhez.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for MySQL összegzése":::

5. A kiszolgáló létrehozása után ellenőrizheti az infrastruktúra kettős titkosítását, ha ellenőrzi az állapotot az **adattitkosítási** kiszolgáló paneljén.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL érvényesítése":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Azure Database for MySQL-kiszolgáló létrehozása infrastruktúra-kettős titkosítással – parancssori felület

Az alábbi lépéseket követve hozzon létre egy Azure Database for MySQL-kiszolgálót az infrastruktúra kettős titkosításával a CLI-ből:

Ez a példa létrehoz egy nevű erőforráscsoportot `myresourcegroup` a `westus` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
A következő példában az USA nyugati régiójában létrehozunk egy `mydemoserver` nevű MySQL 5.7-kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. Ez egy **Gen 4** **általános célú** -kiszolgáló **2 virtuális mag**. Ezzel a megoldással a kiszolgáló által létrehozott kettős titkosítás is engedélyezve lesz. A `<server_admin_password>` helyére írja be saját értékét.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Következő lépések

 További tudnivalók az adattitkosításról: [Azure Database for MySQL adatinfrastruktúra kettős titkosítása](concepts-Infrastructure-double-encryption.md).
