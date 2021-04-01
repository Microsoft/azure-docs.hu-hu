---
title: Tűzfalszabályok kezelése – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan hozhatók létre és kezelhetők Azure Database for MySQL tűzfalszabályok az Azure CLI parancssori felületének használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaddd8b2979b30251301ad041ea4b872c23d680b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541334"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI használatával
A kiszolgálói szintű tűzfalszabályok használatával felügyelhető egy adott IP-címről vagy IP-címről érkező Azure Database for MySQL kiszolgálóhoz való hozzáférés. A kényelmes Azure CLI-parancsok használatával létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet tűzfalszabályok a kiszolgáló kezeléséhez. Azure Database for MySQL tűzfalak áttekintését lásd: [Azure Database for MySQL Server Firewall Rules](./concepts-firewall-rules.md).

A Virtual Network-(VNet-) szabályok a kiszolgálóhoz való hozzáférés biztonságossá tételére is alkalmasak. További információ [Virtual Network szolgáltatási végpontok és szabályok létrehozásáról és kezeléséről az Azure CLI használatával](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
* Egy [Azure Database for MySQL-kiszolgáló és-adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Tűzfalszabály parancsai:
Az az **MySQL Server Firewall-Rule** parancs az Azure CLI-ből a tűzfalszabályok létrehozásához, törléséhez, listázásához, megjelenítéséhez és frissítéséhez használható.

Parancsok
- **Létrehozás**: hozzon létre egy Azure MySQL-kiszolgálói tűzfalszabály-szabályt.
- **Törlés**: az Azure MySQL-kiszolgáló tűzfalszabály törlése.
- **lista**: az Azure MySQL-kiszolgáló tűzfalszabályok listázása.
- **Megjelenítés**: az Azure MySQL-kiszolgáló tűzfalszabály részleteinek megjelenítése.
- **frissítés**: Azure MySQL-kiszolgáló tűzfalszabály frissítése.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Jelentkezzen be az Azure-ba, és sorolja fel a Azure Database for MySQL-kiszolgálókat
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

5. Az előfizetéshez és az erőforráscsoporthoz tartozó MySQL-kiszolgálók Azure-adatbázisainak listázása, ha nem biztos a nevekben. Használja az az [MySQL Server List](/cli/azure/mysql/server#az-mysql-server-list) parancsot.

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Jegyezze fel a lista Name (név) attribútumát, amelyre a MySQL-kiszolgálót kell megadnia a működéshez. Ha szükséges, erősítse meg a kiszolgáló adatait, és használja a Name (név) attribútumot, hogy az helyes legyen. Használja az az [MySQL Server show](/cli/azure/mysql/server#az-mysql-server-show) parancsot.

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Tűzfalszabályok listázása Azure Database for MySQL kiszolgálón 
A kiszolgáló neve és az erőforráscsoport neve alapján sorolja fel a kiszolgáló meglévő tűzfalszabály-szabályait. Használja az az [MySQL Server Firewall List](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) parancsot.  Figyelje meg, hogy a kiszolgálónév attribútum a- **-Server** kapcsolóban van megadva, nem pedig a **--Name** kapcsolóban. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet a (alapértelmezés szerint) JSON formátumban listázza a szabályokat. A **--output tábla** kapcsoló használatával az eredményeket olvashatóbb táblázatos formában jelenítheti meg.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tűzfalszabály létrehozása Azure Database for MySQL kiszolgálón
Az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét használva hozzon létre egy új tűzfalszabály-szabályt a kiszolgálón. Használja az az [MySQL Server Firewall Create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) parancsot. Adja meg a szabály nevét, valamint a kezdő IP-cím és a záró IP-címet (egy IP-címtartomány elérésének biztosításához) a szabályhoz.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Egyetlen IP-cím elérésének engedélyezéséhez adja meg ugyanazt az IP-címet, mint a kezdő IP-cím és a záró IP-cím, ahogy az ebben a példában is látható.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ha engedélyezni szeretné az Azure IP-címekről származó alkalmazások számára a Azure Database for MySQL kiszolgálóhoz való kapcsolódást, adja meg a 0.0.0.0 IP-címet a kezdő IP-cím és a záró IP-cím alapján, ahogy az ebben a példában látható
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Ha a művelet sikeres, az egyes létrehozási parancsok kimenete a létrehozott tűzfalszabály részletes adatait jeleníti meg JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tűzfalszabály frissítése Azure Database for MySQL kiszolgálón 
Ha az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét használja, frissítsen egy meglévő tűzfalszabály-szabályt a kiszolgálón. Használja az az [MySQL Server Firewall Update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissíteni kívánt kezdő IP-cím és a záró IP-attribútum nevét.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
A művelet sikeressége után a parancs kimenete tartalmazza a módosított tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt a frissítési parancs hozza létre.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Tűzfalszabály részleteinek megjelenítése Azure Database for MySQL kiszolgálón
Használja az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét, és jelenítse meg a kiszolgáló meglévő tűzfalszabály-részleteit. Használja az az [MySQL Server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
A művelet sikeressége után a parancs kimenete tartalmazza a megadott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tűzfalszabály törlése Azure Database for MySQL kiszolgálón
Az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét használva távolítson el egy meglévő tűzfalszabályet a-kiszolgálóról. Használja az az [MySQL Server Firewall delete](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
A művelet sikere esetén nincs kimenet. Hiba esetén a hibaüzenet szövege jelenik meg.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MySQL kiszolgálói tűzfalszabályok](./concepts-firewall-rules.md)használatáról.
- [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-using-portal.md).
- A kiszolgálóhoz való további biztonságos hozzáférés [Virtual Network szolgáltatási végpontok és szabályok létrehozásával és kezelésével az Azure CLI használatával](howto-manage-vnet-using-cli.md).