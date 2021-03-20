---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for PostgreSQL
description: Megtudhatja, hogyan kezelheti Azure Database for PostgreSQL-kiszolgálókat az Azure CLI-vel.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 2ea07e2bc12e6fc0d62abd462b8537c6a93689f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935801"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL egyetlen kiszolgáló kezelése az Azure CLI használatával

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett önálló kiszolgálókat. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](/cli/azure/reference-index#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```

Ha még nem hozott létre kiszolgálót, tekintse [meg ezt a](quickstart-create-server-database-azure-cli.md) rövid útmutatót, és hozzon létre egyet.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A következő parancs használatával egyszerűen méretezheti a díjszabási szintet, a számítási és a tárolási kapacitást. Megtekintheti az összes olyan kiszolgálói műveletet, amelyet végrehajthat az [postgres Server áttekintése](/cli/azure/mysql/server) lehetőséggel

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

A fenti argumentumok részletei:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a Azure Database for PostgreSQL-kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|GP_Gen5_2|Adja meg az árképzési csomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További információkért tekintse meg a [díjszabási szintet](./concepts-pricing-tiers.md) .
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). A 5120 minimális és a 1024-os növekmények száma.

> [!Important]
> - A tárterület felskálázása lehetséges (azonban nem méretezheti le a tárterületet)
> - Az alapszintű az általános célra vagy a memória optimalizált díjszabási szintjére való skálázás nem támogatott. Manuálisan is felskálázást használhat  [bash-parancsfájllal](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) vagy a [PostgreSQL Workbench használatával](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>A PostgreSQL-adatbázisok kezelése egy kiszolgálón.
Ezen parancsok bármelyikével létrehozhat, törölhet, listázhat és megtekinthet adatbázis-tulajdonságokat a kiszolgálón

| Parancsmag | Használat| Description |
| --- | ---| --- |
|[az postgres db Create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Létrehoz egy adatbázist|
|[az postgres db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Törölje az adatbázist a kiszolgálóról. Ez a parancs nem törli a kiszolgálót. |
|[az postgres db List](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|a kiszolgálón található összes adatbázis listája|
|[az postgres db show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Az adatbázis további részleteit jeleníti meg|

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
A rendszergazdai szerepkör jelszava ezzel a paranccsal módosítható
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Ügyeljen arra, hogy a jelszó legalább 8 karakterből álljon, és legfeljebb 128 karakterből álljon.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs betűk, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése
Ha csak a PostgreSQL-kiszolgálót szeretné törölni, futtathatja az [az postgres Server delete](/cli/azure/mysql/server#az-mysql-server-delete) parancsot.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- [Kiszolgáló újraindítása](howto-restart-server-cli.md)
- [A kiszolgáló visszaállítása rossz állapotban](howto-restore-server-cli.md)
- [A kiszolgáló monitorozása és hangolása](concepts-monitoring.md)
