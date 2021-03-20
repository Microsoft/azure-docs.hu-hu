---
title: Tűzfalszabályok kezelése – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet tűzfalszabályok Azure Database for PostgreSQL – egyetlen kiszolgálón az Azure CLI parancssorral.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422739"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tűzfalszabályok létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgálón az Azure CLI használatával
A kiszolgálói szintű tűzfalszabályok használatával felügyelhető egy adott IP-cím vagy IP-címtartomány Azure Database for PostgreSQL-kiszolgálóhoz való hozzáférése. A kényelmes Azure CLI-parancsok használatával létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet tűzfalszabályok a kiszolgáló kezeléséhez. A Azure Database for PostgreSQL tűzfalszabályok áttekintését lásd: [Azure Database for PostgreSQL Server Firewall Rules](concepts-firewall-rules.md).

A Virtual Network-(VNet-) szabályok a kiszolgálóhoz való hozzáférés biztonságossá tételére is alkalmasak. További információ [Virtual Network szolgáltatási végpontok és szabályok létrehozásáról és kezeléséről az Azure CLI használatával](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Telepítse az [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogramot, vagy használja a Azure Cloud shellt a böngészőben.
- Egy [Azure Database for PostgreSQL-kiszolgáló és-adatbázis](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Tűzfalszabályok konfigurálása Azure Database for PostgreSQLhoz
Az az [postgres Server Firewall-Rule](/cli/azure/postgres/server/firewall-rule) parancs a tűzfalszabályok konfigurálására szolgál.

## <a name="list-firewall-rules"></a>Tűzfalszabályok listázása 
A meglévő kiszolgálói tűzfalszabályok listázásához futtassa az az [postgres Server Firewall-Rule List](/cli/azure/postgres/server/firewall-rule) parancsot.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet felsorolja a tűzfalszabályok (ha vannak) alapértelmezés szerint JSON formátumban. A kapcsolót a `--output table` kimenetként olvasható táblázatos formátumra használhatja.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tűzfalszabály létrehozása
Új tűzfalszabály létrehozásához a kiszolgálón futtassa az az [postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) parancsot. 


Az egyes IP-címekhez való hozzáférés engedélyezéséhez adja meg ugyanazt a címet a `--start-ip-address` és a esetében, ahogy az ebben a példában az `--end-ip-address` itt látható IP-címet helyettesíti.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Ha engedélyezni szeretné az Azure IP-címekről származó alkalmazások számára a Azure Database for PostgreSQL kiszolgálóhoz való kapcsolódást, adja meg a 0.0.0.0 IP-címet a kezdő IP-cím és a záró IP-cím alapján, ahogy az ebben a példában látható
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

A művelet sikeressége után a parancs kimenete a létrehozott tűzfalszabály részleteit sorolja fel, alapértelmezés szerint JSON formátumban. Hiba esetén a kimenet hibaüzenetet jelenít meg helyette.

## <a name="update-firewall-rule"></a>Tűzfalszabály frissítése 
Egy meglévő tűzfalszabály frissítése a kiszolgálón az [az postgres Server Firewall-Rule Update](/cli/azure/postgres/server/firewall-rule) parancs használatával. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint az IP-cím és a záró IP-attribútumokat a frissítéshez.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
A sikeres művelet után a parancs kimenete a módosított tűzfalszabály részleteit sorolja fel, alapértelmezés szerint JSON formátumban. Hiba esetén a kimenet hibaüzenetet jelenít meg helyette.
> [!NOTE]
> Ha a tűzfalszabály nem létezik, a frissítési parancs hozza létre.

## <a name="show-firewall-rule-details"></a>Tűzfalszabály részleteinek megjelenítése
A meglévő kiszolgálói szintű tűzfalszabály részleteit az [az postgres Server Firewall-Rule show](/cli/azure/postgres/server/firewall-rule) parancs futtatásával is megjelenítheti.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
A művelet sikeressége után a parancs kimenete a megadott tűzfalszabály részleteit sorolja fel, alapértelmezés szerint JSON formátumban. Hiba esetén a kimenet hibaüzenetet jelenít meg helyette.

## <a name="delete-firewall-rule"></a>Tűzfalszabály törlése
Ha egy IP-címtartomány hozzáférését vissza szeretné vonni a-kiszolgálóra, törölje a meglévő tűzfalszabály törlését az az [postgres Server Firewall-Rule delete](/cli/azure/postgres/server/firewall-rule) parancs végrehajtásával. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
A művelet sikere esetén nincs kimenet. Hiba esetén a rendszer a hibaüzenet szövegét adja vissza.

## <a name="next-steps"></a>Következő lépések
- Ehhez hasonlóan webböngészővel is [létrehozhat és kezelhet Azure Database for PostgreSQL tűzfalszabályok használatát a Azure Portal használatával](howto-manage-firewall-using-portal.md).
- További információ a [Azure Database for PostgreSQL kiszolgálói tűzfalszabályok](concepts-firewall-rules.md)használatáról.
- A kiszolgálóhoz való további biztonságos hozzáférés [Virtual Network szolgáltatási végpontok és szabályok létrehozásával és kezelésével az Azure CLI használatával](howto-manage-vnet-using-cli.md).
- Ha segítségre van a Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáshoz, tekintse meg a [Azure Database for PostgreSQL kapcsolódási kódtárait](concepts-connection-libraries.md).
