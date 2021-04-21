---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for MySQL
description: Megtudhatja, hogyan kezelheti a Azure Database for MySQL kiszolgálót az Azure CLI-ről.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 03227f121f58e52d2e9d34613917fda864666ce1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769966"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Egyetlen Azure Database for MySQL kezelése az Azure CLI használatával

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett egyetlen kiszolgálót. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó-visszaállítás és a kiszolgáló részleteinek megtekintése.

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az login paranccsal jelentkezzen be [a fiókjába.](/cli/azure/reference-index#az_login) Jegyezze fel **az id** tulajdonságot, amely **az** Azure-fiók előfizetés-azonosítójára vonatkozik.

```azurecli-interactive
az login
```

Válassza ki a fiókjában az adott előfizetést [az az account set paranccsal.](/cli/azure/account) Jegyezze fel  az az **login** kimenet azonosítóértékét,  amely a subscription argumentum értékeként lesz használva a parancsban. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

Ha még nem hozott létre egy -t, tekintse meg ezt a rövid [útmutatót](quickstart-create-mysql-server-database-using-azure-cli.md) egy ilyen létrehozásához.

## <a name="scale-compute-and-storage"></a>Számítás és tárolás skálázható
A tarifacsomagot, a számítást és a tárolást az alábbi paranccsal skálázhatja fel egyszerűen. Az az mysql server overview (az az mysql server áttekintése) művelettel végrehajthatja az [összes kiszolgálóműveletet](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

A fenti argumentumok részletei a következőek:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon egyedi nevet a Azure Database for MySQL kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|GP_Gen5_2|Adja meg a tarifacsomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További [információért tekintse meg a](./concepts-pricing-tiers.md) tarifacsomagokat.
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). Legalább 5120, és 1024-es növekményekben növekszik.

> [!Important]
> - A tárterület skálázható felfelé (a tárterület azonban nem skálázható le)
> - Az Alapszintű szolgáltatási szintről Általános célú vagy Memóriaoptimalált tarifacsomagra való felméretezés nem támogatott. Manuálisan skálázhat fel horizontálisan  [bash szkript használatával](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) vagy a [MySQL Workbench használatával](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-adatbázisok kezelése kiszolgálón
A következő parancsok bármelyikét használhatja egy adatbázis adatbázis-tulajdonságainak létrehozásához, törléséhez, listához és megtekintéséhez a kiszolgálón

| Parancsmag | Használat| Description |
| --- | ---| --- |
|[az mysql db create](/cli/azure/sql/db#az_mysql_db_create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Létrehoz egy adatbázist|
|[az mysql db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Törölje az adatbázist a kiszolgálóról. Ez a parancs nem törli a kiszolgálót. |
|[az mysql db list](/cli/azure/sql/db#az_mysql_db_list)|```az mysql db list -g myresourcegroup -s mydemoserver```|listázza a kiszolgálón található összes adatbázist|
|[az mysql db show](/cli/azure/sql/db#az_mysql_db_show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Az adatbázis további részleteit jeleníti meg|

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
Ezzel a paranccsal módosíthatja a rendszergazdai szerepkör jelszavát
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Győződjön meg arról, hogy a jelszó legalább 8 karakterből és legfeljebb 128 karakterből állhat.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése
Ha csak az egykiszolgálós MySQL-kiszolgálót szeretné törölni, futtassa [az az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- [Kiszolgáló újraindítása](howto-restart-server-cli.md)
- [Rossz állapotban található kiszolgáló visszaállítása](howto-restore-server-cli.md)
- [A kiszolgáló monitorozása és hangolása](concepts-monitoring.md)