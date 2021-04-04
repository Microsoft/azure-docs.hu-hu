---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for MySQL rugalmas kiszolgáló
description: Megtudhatja, hogyan kezelheti Azure Database for MySQL rugalmas kiszolgálóját az Azure CLI-vel.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545055"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Azure Database for MySQL rugalmas kiszolgáló (előzetes verzió) kezelése az Azure CLI-vel

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett rugalmas kiszolgálókat (előzetes verzió). A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

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

> [!Important]
> Ha még nem hozott létre rugalmas kiszolgálót, hozzon létre egyet, hogy megismerkedjen az útmutatóval.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A számítási szintet, a virtuális mag és a tárterületet könnyedén méretezheti a következő parancs használatával. Megtekintheti az összes olyan kiszolgálói műveletet, amelyet végrehajthat az [az MySQL rugalmas kiszolgáló frissítése](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) lehetőséggel

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

A fenti argumentumok részletei:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a Azure Database for MySQL-kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|Standard_D4ds_v4|Adja meg a számítási rétegek és a méret nevét. A következő konvenciót követi: Standard_ {VM size} a gyorsírásban. További információkért tekintse meg a [díjszabási szintet](../concepts-pricing-tiers.md) .
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). A 5120 minimális és a 1024-os növekmények száma.

> [!Important]
> - A tárterület felskálázása lehetséges (azonban nem méretezheti le a tárterületet)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-adatbázisok kezelése kiszolgálón.
Ezen parancsok bármelyikével létrehozhat, törölhet, listázhat és megtekinthet adatbázis-tulajdonságokat a kiszolgálón

| Parancsmag | Használat| Leírás |
| --- | ---| --- |
|[az MySQL flexibilis-Server db Create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Létrehoz egy adatbázist|
|[az MySQL flexibilis-Server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Törölje az adatbázist a kiszolgálóról. Ez a parancs nem törli a kiszolgálót. |
|[az MySQL flexibilis-Server db List](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|a kiszolgálón található összes adatbázis listája|
|[az MySQL rugalmas-Server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Az adatbázis további részleteit jeleníti meg|

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
A rendszergazdai szerepkör jelszava ezzel a paranccsal módosítható
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Ügyeljen arra, hogy a jelszó legalább 8 karakterből álljon, és legfeljebb 128 karakterből álljon.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs betűk, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése
Ha a MySQL rugalmas kiszolgálót szeretné törölni, futtathatja az [az MySQL flexibilis-Server Server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- [Útmutató a kiszolgálók indításához és leállításához](how-to-stop-start-server-portal.md)
- [Ismerje meg, hogyan kezelheti a virtuális hálózatokat](how-to-manage-virtual-network-cli.md)
- [Kapcsolati problémák hibaelhárítása](how-to-troubleshoot-common-connection-issues.md)
- [Tűzfal létrehozása és kezelése](how-to-manage-firewall-cli.md)