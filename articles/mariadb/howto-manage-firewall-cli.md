---
title: Tűzfalszabályok kezelése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan hozhatók létre és kezelhetők Azure Database for MariaDB tűzfalszabályok az Azure CLI parancssori felületének használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef04e2b4c820c14ea7df6c35ecb0189ef31ef7dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665038"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése az Azure CLI használatával
A kiszolgálói szintű tűzfalszabályok használatával felügyelhető egy adott IP-címről vagy IP-címről érkező Azure Database for MariaDB kiszolgálóhoz való hozzáférés. A kényelmes Azure CLI-parancsok használatával létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet tűzfalszabályok a kiszolgáló kezeléséhez. Azure Database for MariaDB tűzfalak áttekintését lásd: [Azure Database for MariaDB Server Firewall Rules](./concepts-firewall-rules.md).

A Virtual Network-(VNet-) szabályok a kiszolgálóhoz való hozzáférés biztonságossá tételére is alkalmasak. További információ [Virtual Network szolgáltatási végpontok és szabályok létrehozásáról és kezeléséről az Azure CLI használatával](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
* Egy [Azure Database for MariaDB-kiszolgáló és-adatbázis](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Tűzfalszabály parancsai:
Az az **MariaDB Server Firewall-Rule** parancs az Azure CLI-ből a tűzfalszabályok létrehozásához, törléséhez, listázásához, megjelenítéséhez és frissítéséhez használható.

Parancsok
- **Létrehozás**: Azure MariaDB kiszolgálói tűzfalszabály létrehozása.
- **delete**: az Azure MariaDB kiszolgálói tűzfalszabály törlése.
- **lista**: az Azure MariaDB-kiszolgáló tűzfalszabályok listázása.
- **Megjelenítés**: az Azure MariaDB Server tűzfalszabály részleteinek megjelenítése.
- **frissítés**: az Azure MariaDB Server-tűzfalszabály frissítése.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Jelentkezzen be az Azure-ba, és sorolja fel a Azure Database for MariaDB-kiszolgálókat
Az az **login** paranccsal biztonságosan összekapcsolhatja az Azure CLI-t az Azure-fiókjával.

1. Futtassa a következő parancsot a parancssorból:
   ```azurecli
   az login
   ```
   A parancs kimenete a következő lépésben használandó kódot eredményez.

2. Nyisson meg egy webböngészőt a lap megnyitásához [https://aka.ms/devicelogin](https://aka.ms/devicelogin) , majd írja be a kódot.

3. A parancssorban jelentkezzen be az Azure-beli hitelesítő adataival.

4. A bejelentkezés engedélyezése után a rendszer kinyomtatja az előfizetések listáját a konzolon. Másolja ki a kívánt előfizetés AZONOSÍTÓját, és állítsa be a jelenlegi előfizetést. Használja az az [Account set](/cli/azure/account#az-account-set) parancsot.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Az előfizetéshez és az erőforráscsoporthoz tartozó MariaDB-kiszolgálók Azure-adatbázisainak listázása, ha nem biztos a nevekben. Használja az az [MariaDB Server List](/cli/azure/mariadb/server#az-mariadb-server-list) parancsot.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Jegyezze fel a lista Name (név) attribútumát, amelyre vonatkozóan meg kell adnia a MariaDB-kiszolgálót, hogy működjön. Ha szükséges, erősítse meg a kiszolgáló adatait, és használja a Name (név) attribútumot, hogy az helyes legyen. Használja az az [MariaDB Server show](/cli/azure/mariadb/server#az-mariadb-server-show) parancsot.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Tűzfalszabályok listázása Azure Database for MariaDB kiszolgálón 
A kiszolgáló neve és az erőforráscsoport neve alapján sorolja fel a kiszolgáló meglévő tűzfalszabály-szabályait. Használja az az [MariaDB Server Firewall List](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) parancsot.  Figyelje meg, hogy a kiszolgálónév attribútum a- **-Server** kapcsolóban van megadva, nem pedig a **--Name** kapcsolóban. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet a (alapértelmezés szerint) JSON formátumban listázza a szabályokat. A **--output tábla** kapcsoló használatával az eredményeket olvashatóbb táblázatos formában jelenítheti meg.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály létrehozása Azure Database for MariaDB kiszolgálón
Az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport nevét használva hozzon létre egy új tűzfalszabály-szabályt a kiszolgálón. Használja az az [MariaDB Server Firewall Create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) parancsot. Adja meg a szabály nevét, valamint a kezdő IP-cím és a záró IP-címet (egy IP-címtartomány elérésének biztosításához) a szabályhoz.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Egyetlen IP-cím elérésének engedélyezéséhez adja meg ugyanazt az IP-címet, mint a kezdő IP-cím és a záró IP-cím, ahogy az ebben a példában is látható.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ha engedélyezni szeretné az Azure IP-címekről származó alkalmazások számára a Azure Database for MariaDB kiszolgálóhoz való kapcsolódást, adja meg a 0.0.0.0 IP-címet a kezdő IP-cím és a záró IP-cím alapján, ahogy az ebben a példában látható
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Ha a művelet sikeres, az egyes létrehozási parancsok kimenete a létrehozott tűzfalszabály részletes adatait jeleníti meg JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály frissítése Azure Database for MariaDB kiszolgálón 
Ha az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport nevét használja, frissítsen egy meglévő tűzfalszabály-szabályt a kiszolgálón. Használja az az [MariaDB Server Firewall Update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissíteni kívánt kezdő IP-cím és a záró IP-attribútum nevét.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
A művelet sikeressége után a parancs kimenete tartalmazza a módosított tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt a frissítési parancs hozza létre.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Tűzfalszabály részleteinek megjelenítése Azure Database for MariaDB kiszolgálón
Használja az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport nevét, és jelenítse meg a meglévő tűzfalszabály részleteit a kiszolgálóról. Használja az az [MariaDB Server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
A művelet sikeressége után a parancs kimenete tartalmazza a megadott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály törlése Azure Database for MariaDB kiszolgálón
Az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport nevét használva távolítson el egy meglévő tűzfalszabályet a-kiszolgálóról. Használja az az [MariaDB Server Firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
A művelet sikere esetén nincs kimenet. Hiba esetén a hibaüzenet szövege jelenik meg.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MariaDB kiszolgálói tűzfalszabályok](./concepts-firewall-rules.md)használatáról.
- [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-portal.md).
- A kiszolgálóhoz való további biztonságos hozzáférés [Virtual Network szolgáltatási végpontok és szabályok létrehozásával és kezelésével az Azure CLI használatával](howto-manage-vnet-cli.md).