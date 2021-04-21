---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for PostgreSQL
description: Megtudhatja, hogyan kezelheti a Azure Database for PostgreSQL-kiszolgálót az Azure CLI-ről.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 36151a9afda0bb23ee244ee778a30f30a41243ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791462"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Egyetlen Azure Database for PostgreSQL kezelése az Azure CLI használatával

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett egyetlen kiszolgálót. A felügyeleti feladatok közé tartozik a számítás és a tárolás skálázása, a rendszergazdai jelszó visszaállítása és a kiszolgáló részleteinek megtekintése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az login paranccsal kell bejelentkeznie [a fiókjába.](/cli/azure/reference-index#az_login) Figyelje meg **az id** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** vonatkozik.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókjában [az az account set paranccsal.](/cli/azure/account) Jegyezze fel  az az **login** output parancs id értékét, amely a subscription argumentum értékeként lesz használva a parancsban.  Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

Ha még nem hozott létre kiszolgálót, a létrehozáshoz tekintse meg ezt a rövid útmutatót. [](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Számítás és tárolás skálázható

A tarifacsomagot, a számítást és a tárolást az alábbi paranccsal skálázhatja fel egyszerűen. Az az postgres server overview (Az postgres-kiszolgáló áttekintése) végrehajtásához [szükséges összes kiszolgálói műveletet láthatja](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

A fenti argumentumok részletei a következőek:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a Azure Database for PostgreSQL számára. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|GP_Gen5_2|Adja meg a tarifacsomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További [információért tekintse meg](./concepts-pricing-tiers.md) a tarifacsomagokat.
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). Minimum 5120, és 1024 növekményben növekszik.

> [!Important]
> - A tárterület skálázható felfelé (a tárterület azonban nem skálázható le)
> - Az Alapszintű szolgáltatási szintről általános célú vagy memóriaoptimalikus tarifacsomagra való felméretezés nem támogatott. Manuálisan skálázhat fel horizontálisan  [egy Bash-szkript](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) vagy a [PostgreSQL Workbench használatával](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-adatbázisok kezelése egy kiszolgálón.
Ezen parancsok bármelyikét használhatja egy adatbázis adatbázis-tulajdonságainak létrehozásához, törléséhez, listához és megtekintéséhez a kiszolgálón

| Parancsmag | Használat| Description |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az_mysql_db_create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Létrehoz egy adatbázist|
|[az postgres db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Törölje az adatbázist a kiszolgálóról. Ez a parancs nem törli a kiszolgálót. |
|[az postgres db list](/cli/azure/sql/db#az_mysql_db_list)|```az postgres db list -g myresourcegroup -s mydemoserver```|listázza a kiszolgálón található összes adatbázist|
|[az postgres db show](/cli/azure/sql/db#az_mysql_db_show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Az adatbázis további részleteit jeleníti meg|

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
Ezzel a paranccsal módosíthatja a rendszergazdai szerepkör jelszavát
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Győződjön meg arról, hogy a jelszó legalább 8 karakterből és legfeljebb 128 karakterből állhat.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése
Ha csak az egykiszolgálós PostgreSQL-kiszolgálót szeretné törölni, futtassa [az az postgres server delete](/cli/azure/mysql/server#az_mysql_server_delete) parancsot.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- [Kiszolgáló újraindítása](howto-restart-server-cli.md)
- [Rossz állapotban található kiszolgáló visszaállítása](howto-restore-server-cli.md)
- [A kiszolgáló monitorozása és hangolása](concepts-monitoring.md)
