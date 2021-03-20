---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for MySQL
description: Megtudhatja, hogyan kezelheti Azure Database for MySQL-kiszolgálókat az Azure CLI-vel.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: bd068f0cf76a8edefca854d72d5240c0becaf8fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542065"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Azure Database for MySQL egyetlen kiszolgáló kezelése az Azure CLI használatával

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

Ha még nem hozott létre egy kiszolgálót [, a rövid](quickstart-create-mysql-server-database-using-azure-cli.md) útmutatóban hozhat létre egyet.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés
Az árképzési szintet, a számítási és a tárolási kapacitást egyszerűen méretezheti a következő parancs használatával. Láthatja az összes olyan kiszolgálói műveletet, amelyet végrehajthat az [az MySQL Server Overview](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

A fenti argumentumok részletei:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a Azure Database for MySQL-kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|GP_Gen5_2|Adja meg az árképzési csomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További információkért tekintse meg a [díjszabási szintet](./concepts-pricing-tiers.md) .
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). A 5120 minimális és a 1024-os növekmények száma.

> [!Important]
> - A tárterület felskálázása lehetséges (azonban nem méretezheti le a tárterületet)
> - Az alapszintű az általános célra vagy a memória optimalizált díjszabási szintjére való skálázás nem támogatott. Manuálisan is felskálázást használhat  [bash-parancsfájllal](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) vagy a [MySQL Workbench használatával](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-adatbázisok kezelése kiszolgálón
Ezen parancsok bármelyikével létrehozhat, törölhet, listázhat és megtekinthet adatbázis-tulajdonságokat a kiszolgálón

| Parancsmag | Használat| Description |
| --- | ---| --- |
|[az MySQL db Create](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Létrehoz egy adatbázist|
|[az MySQL db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Törölje az adatbázist a kiszolgálóról. Ez a parancs nem törli a kiszolgálót. |
|[az MySQL db List](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|a kiszolgálón található összes adatbázis listája|
|[az MySQL db show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Az adatbázis további részleteit jeleníti meg|

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
A rendszergazdai szerepkör jelszava ezzel a paranccsal módosítható
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Ügyeljen arra, hogy a jelszó legalább 8 karakterből álljon, és legfeljebb 128 karakterből álljon.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs betűk, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése
Ha csak a MySQL-kiszolgálót szeretné törölni, futtathatja az [az MySQL Server delete](/cli/azure/mysql/server#az-mysql-server-delete) parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- [Kiszolgáló újraindítása](howto-restart-server-cli.md)
- [A kiszolgáló visszaállítása rossz állapotban](howto-restore-server-cli.md)
- [A kiszolgáló monitorozása és hangolása](concepts-monitoring.md)