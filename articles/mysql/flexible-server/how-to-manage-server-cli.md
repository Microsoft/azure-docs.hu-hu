---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for MySQL kiszolgáló kezelése
description: Megtudhatja, hogyan kezelheti a rugalmas Azure Database for MySQL kiszolgálót az Azure CLI-ről.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4ef1408d5f7afc3b78ab021cdd25eedd75110849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776931"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Rugalmas Azure Database for MySQL (előzetes verzió) kezelése az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett rugalmas kiszolgálót (előzetes verzió). A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó-visszaállítás és a kiszolgáló részleteinek megtekintése.

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

> [!Important]
> Ha még nem hozott létre rugalmas kiszolgálót, hozzon létre egyet az útmutató első lépésekhez.

## <a name="scale-compute-and-storage"></a>Számítás és tárolás skálázható

A számítási réteget, a virtuális magokat és a tárterületet az alábbi paranccsal skálázhatja fel egyszerűen. Láthatja az összes olyan kiszolgálóműveletet, amely [az az mysql flexible-server update végrehajtásához szükséges](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

A fenti argumentumok részletei a következőek:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon egyedi nevet a Azure Database for MySQL kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|Standard_D4ds_v4|Adja meg a számítási szint nevét és méretét. Röviden követi a Standard_{VM size} (Virtuális gép mérete} méret) konvenciót. További [információért tekintse meg](../concepts-pricing-tiers.md) a tarifacsomagokat.
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). Minimum 5120, és 1024 növekményben növekszik.

> [!Important]
> - A tárterület skálázható felfelé (a tárterület azonban nem skálázható le)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-adatbázisok kezelése egy kiszolgálón.
A következő parancsok bármelyikét használhatja egy adatbázis adatbázis-tulajdonságainak létrehozásához, törléséhez, listához és megtekintéséhez a kiszolgálón

| Parancsmag | Használat| Description |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Létrehoz egy adatbázist|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Törölje az adatbázist a kiszolgálóról. Ez a parancs nem törli a kiszolgálót. |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|listázza a kiszolgálón található összes adatbázist|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Az adatbázis további részleteit jeleníti meg|

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
Ezzel a paranccsal módosíthatja a rendszergazdai szerepkör jelszavát
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Győződjön meg arról, hogy a jelszó legalább 8 karakterből és legfeljebb 128 karakterből állhat.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése
Ha csak a rugalmas MySQL-kiszolgálót szeretné törölni, futtathatja [az az mysql flexible-server server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a kiszolgálók indításról vagy leállításról](how-to-stop-start-server-portal.md)
- [Tudnivalók a virtuális hálózatok kezelésével](how-to-manage-virtual-network-cli.md)
- [Kapcsolati problémák hibaelhárítása](how-to-troubleshoot-common-connection-issues.md)
- [Tűzfal létrehozása és kezelése](how-to-manage-firewall-cli.md)