---
title: Tűzfalszabályok kezelése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet Azure Database for MariaDB tűzfalszabályokat az Azure CLI parancssori felületének használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 87ff75a07bd1b91121d614e0f41c0ecf216e1b41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791714"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Tűzfalszabályok Azure Database for MariaDB és kezelése az Azure CLI használatával
A kiszolgálószintű tűzfalszabályok egy adott IP-címről Azure Database for MariaDB IP-címről vagy IP-címtartományból való hozzáférés kezelésére használhatók. A kényelmes Azure CLI-parancsokkal tűzfalszabályokat hozhat létre, frissíthet, törölhet, list használhat és mutathat be a kiszolgáló kezeléséhez. A tűzfalak áttekintését Azure Database for MariaDB kiszolgálói [tűzfalszabályok Azure Database for MariaDB talál.](./concepts-firewall-rules.md)

Virtual Network (VNet) szabályokkal is biztonságossá teheti a kiszolgálóhoz való hozzáférést. További információ a [szolgáltatásvégpontokkal és Virtual Network kezelésével](howto-manage-vnet-cli.md)kapcsolatban az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure CLI-t.](/cli/azure/install-azure-cli)
* Egy [Azure Database for MariaDB-kiszolgáló és -adatbázis.](quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="firewall-rule-commands"></a>Tűzfalszabály-parancsok:
Az **az mariadb server firewall-rule** paranccsal hozhat létre, törölhet, listálható, mutatható be és frissíthet tűzfalszabályokat az Azure CLI-n.

Parancsok:
- **create:** Hozzon létre egy Azure MariaDB-kiszolgálói tűzfalszabályt.
- **delete:** Azure MariaDB-kiszolgáló tűzfalszabályának törlése.
- **list:** List the Azure MariaDB server firewall rules .list the Azure MariaDB server firewall rules.
- **show**: Az Azure MariaDB-kiszolgáló tűzfalszabályának részleteinek megjelenítése.
- **update**: Azure MariaDB-kiszolgáló tűzfalszabályának frissítése.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Jelentkezzen be az Azure-ba, és listába Azure Database for MariaDB kiszolgálókat
Biztonságosan csatlakoztathatja az Azure CLI-t az Azure-fiókjához **az az login paranccsal.**

1. Futtassa a következő parancsot a parancssorból:
   ```azurecli
   az login
   ```
   Ez a parancs egy kódot ad ki, amely a következő lépésben lesz használva.

2. Egy webböngészővel nyissa meg a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) oldalt, majd írja be a kódot.

3. Amikor a rendszer kéri, jelentkezzen be Azure-beli hitelesítő adataival.

4. Miután engedélyezte a bejelentkezést, megjelenik az előfizetések listája a konzolon. Másolja ki a kívánt előfizetés azonosítóját az aktuális előfizetés használatára való beállításához. Használja az [az account set](/cli/azure/account#az_account_set) parancsot.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Ha nem biztos a neveiben, sorolja fel az előfizetéséhez és az erőforráscsoporthoz elérhető Azure Database for MariaDB-kiszolgálókat. Használja az [az mariadb server list parancsot.](/cli/azure/mariadb/server#az_mariadb_server_list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Jegyezze fel a lista névattribútumát, amelyen meg kell adnia a MariaDB-kiszolgálót, amelyen dolgozni fog. Ha szükséges, erősítse meg a kiszolgáló adatait, és használja a name attribútumot, hogy meggyőződjon a helyességről. Használja az [az mariadb server show](/cli/azure/mariadb/server#az_mariadb_server_show) parancsot.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Tűzfalszabályok felsorolása a Azure Database for MariaDB kiszolgálón 
A kiszolgáló és az erőforráscsoport nevének használatával listába kell sorolni a kiszolgálón meglévő kiszolgálói tűzfalszabályokat. Használja az [az mariadb server firewall list parancsot.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_list)  Figyelje meg, hogy a kiszolgálónév attribútum a **--server** kapcsolóban van megadva, nem pedig **a --name kapcsolóban.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet (alapértelmezés szerint) JSON formátumban listázza a szabályokat ( ha vannak). A **--output tábla kapcsolóval** olvashatóbb táblázatos formátumban kimenetet kaphat az eredményekhez.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály létrehozása a Azure Database for MariaDB kiszolgálón
Az Azure MariaDB-kiszolgáló és az erőforráscsoport nevének használatával hozzon létre egy új tűzfalszabályt a kiszolgálón. Használja az [az mariadb server firewall create parancsot.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) Adja meg a szabály nevét, valamint a szabály kezdő IP-címét és záró IP-címét (hogy hozzáférést biztosítson egy IP-címtartományhoz).
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Ha egyetlen IP-cím számára is engedélyeznie kell a hozzáférést, adja meg ugyanazt az IP-címet, mint a kezdő IP-cím és a záró IP-cím, ahogy ebben a példában is.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ha engedélyezni szeretne, hogy az Azure IP-címekről származó alkalmazások csatlakozzon a Azure Database for MariaDB-kiszolgálóhoz, adja meg a 0.0.0.0 IP-címet kezdő IP-címként és záró IP-címként, ahogy ebben a példában is.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Sikeres létrehozás esetén minden parancs kimenete JSON formátumban (alapértelmezés szerint) listázza a létrehozott tűzfalszabály részleteit. Hiba esetén a kimenetben ehelyett hibaüzenet jelenik meg.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály frissítése a Azure Database for MariaDB kiszolgálón 
Az Azure MariaDB-kiszolgáló és az erőforráscsoport nevének használatával frissítsen egy meglévő tűzfalszabályt a kiszolgálón. Használja az [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_update) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissíteni szükséges kezdő IP- és záró IP-attribútumokat.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Sikeresség esetén a parancs kimenete JSON formátumban (alapértelmezés szerint) listázza a frissített tűzfalszabály részleteit. Hiba esetén a kimenetben ehelyett hibaüzenet jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt az update paranccsal lehet létrehozni.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Tűzfalszabály részleteinek megjelenítése Azure Database for MariaDB kiszolgálón
Az Azure MariaDB-kiszolgáló és az erőforráscsoport neve használatával a kiszolgálóról származó meglévő tűzfalszabály-részleteket mutassa. Használja az [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_show) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Sikeresség esetén a parancs kimenete megjeleníti a megadott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Hiba esetén a kimenetben ehelyett hibaüzenet jelenik meg.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály törlése a Azure Database for MariaDB kiszolgálón
Az Azure MariaDB-kiszolgáló és az erőforráscsoport nevének használatával távolítson el egy meglévő tűzfalszabályt a kiszolgálóról. Használja az [az mariadb server firewall delete parancsot.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_delete) Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Sikeresség esetén nincs kimenet. Hiba esetén megjelenik a hibaüzenet szövege.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MariaDB tűzfalszabályainak konfigurálásról.](./concepts-firewall-rules.md)
- [Hozzon létre és kezeljen Azure Database for MariaDB tűzfalszabályokat a Azure Portal.](./howto-manage-firewall-portal.md)
- A kiszolgálóhoz való további biztonságos hozzáférés érdekében hozzon létre és Virtual Network azure CLI-t használó [szolgáltatásvégpontokat és szabályokat.](howto-manage-vnet-cli.md)