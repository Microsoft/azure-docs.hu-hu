---
title: Infrastruktúra – kettős titkosítás – Azure Portal – Azure Database for PostgreSQL
description: Ismerje meg, hogyan állíthatja be és kezelheti az infrastruktúra kettős titkosítását a Azure Database for PostgreSQL számára.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: ea486b534ac3e703849ddb3922d7c3a428dd076b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242228"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Infrastruktúra kettős titkosítása Azure Database for PostgreSQL

Ismerje meg, hogyan állíthatja be és felügyelheti a Azure Database for PostgreSQL infrastruktúra kettős titkosítását.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Azure Database for PostgreSQL-kiszolgáló létrehozása infrastruktúra-kettős titkosítással – portál

Az alábbi lépéseket követve hozzon létre egy Azure Database for MySQL-kiszolgálót a Azure Portal infrastruktúra-dupla titkosítással:

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL** elemet. A PostgreSQL-t a keresőmezőbe is megadhatja a szolgáltatás megtalálásához. Az **egykiszolgálós** üzembe helyezési lehetőség engedélyezése.

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="A Azure Database for PostgreSQL a menüben":::

3. Adja meg a kiszolgáló alapszintű információit. Válassza a **További beállítások** lehetőséget, és engedélyezze az **infrastruktúra kettős titkosítás** jelölőnégyzetet a paraméter beállításához.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for PostgreSQL kijelölések":::

4. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kiszolgáló kiépítéséhez.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for PostgreSQL összegzése":::

5. A kiszolgáló létrehozása után ellenőrizheti az infrastruktúra kettős titkosítását, ha ellenőrzi az állapotot az **adattitkosítási** kiszolgáló paneljén.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL érvényesítése":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Azure Database for PostgreSQL-kiszolgáló létrehozása infrastruktúra-kettős titkosítással – parancssori felület

Az alábbi lépéseket követve hozzon létre egy Azure Database for MySQL-kiszolgálót az infrastruktúra kettős titkosításával a CLI-ből:

Ez a példa létrehoz egy nevű erőforráscsoportot `myresourcegroup` a `westus` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
A következő példa létrehoz egy PostgreSQL 11 kiszolgálót az USA nyugati régiójában, amely a `mydemoserver` `myresourcegroup` kiszolgálói rendszergazdai bejelentkezéssel van ellátva az erőforráscsoporthoz `myadmin` . Ez egy **Gen 4** **általános célú** -kiszolgáló **2 virtuális mag**. Ezzel a megoldással a kiszolgáló által létrehozott kettős titkosítás is engedélyezve lesz. A `<server_admin_password>` helyére írja be saját értékét.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Következő lépések

További tudnivalók az adattitkosításról: [Azure Database for PostgreSQL adatinfrastruktúra kettős titkosítása](concepts-Infrastructure-double-encryption.md).

