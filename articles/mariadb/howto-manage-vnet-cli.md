---
title: VNet-végpontok kezelése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet virtuális Azure Database for MariaDB szolgáltatásvégpontokat és szabályokat az Azure CLI parancssori felületének használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf87865fb2fc70251e1e417361333cfd750d6e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783664"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Virtuális hálózati Azure Database for MariaDB létrehozása és kezelése az Azure CLI használatával

A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MariaDB-kiszolgálóra. A kényelmes Azure parancssori felületi (CLI-) parancsokkal létrehozhat, frissíthet, törölhet, list készíthet és felsorolhat VNet-szolgáltatásvégpontokat és szabályokat a kiszolgáló kezeléséhez. A virtuális hálózatok Azure Database for MariaDB áttekintését, beleértve a korlátozásokat: Azure Database for MariaDB [server VNet szolgáltatásvégpontokat.](concepts-data-access-security-vnet.md) A VNet-szolgáltatásvégpontokat az összes támogatott régióban el lehet Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Szüksége lesz egy [Azure Database for MariaDB-kiszolgálóra és -adatbázisra.](quickstart-create-mariadb-server-database-using-azure-cli.md)

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!NOTE]
> A VNet-szolgáltatásvégpontokat csak a virtuális hálózati általános célú memóriaoptimal optimalizált kiszolgálók támogatják.

## <a name="configure-vnet-service-endpoints"></a>VNet-szolgáltatásvégpont konfigurálása

Az [az network vnet](/cli/azure/network/vnet) parancsokkal konfigurálhatja a virtuális hálózatokat.

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

A szolgáltatásvégpontokat a virtuális hálózatokon egymástól függetlenül konfigurálhatja egy olyan felhasználó, aki írási hozzáféréssel rendelkezik a virtuális hálózathoz.

Ahhoz, hogy azure-szolgáltatási erőforrásokat biztosítsunk egy virtuális hálózat számára, a felhasználónak rendelkeznie kell a hozzáadott alhálózatok "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedélyével. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások eltérő előfizetésben vannak, az erőforrásoknak ugyanabban a Active Directory (AD) bérlőben kell lennie. Győződjön meg arról, hogy mindkét előfizetésben regisztrálva van a **Microsoft.Sql** erőforrás-szolgáltató. További információ: [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> A szolgáltatásvégpont konfigurálása előtt erősen ajánlott elolvasni ezt a cikket a szolgáltatásvégpont-konfigurációkról és szempontokról. **Virtual Network szolgáltatásvégpont:** A [Virtual Network szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékei egy vagy több formális Azure-szolgáltatástípusnevet tartalmaznak. A VNet-szolgáltatásvégpont a **Microsoft.Sql** szolgáltatástípusnevet használja, amely az SQL Database. Ez a szolgáltatáscímke a Azure SQL Database, Azure Database for MariaDB, a PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft.Sql** szolgáltatáscímkét egy VNet-szolgáltatásvégpontra alkalmazza, a szolgáltatásvégpont forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve az Azure SQL Database-, Azure Database for PostgreSQL-, Azure Database for MariaDB- és Azure Database for MySQL-kiszolgálókat az alhálózaton.

### <a name="sample-script"></a>Példaszkript

Ez a példaszkprogram egy Azure Database for MariaDB-kiszolgáló létrehozására, egy VNet- és egy VNet-szolgáltatásvégpont létrehozására, valamint a kiszolgáló alhálózattal való biztonságossá létrehozására szolgál egy VNet-s szabályt használva. Ebben a példaszk szkriptben módosítsa a rendszergazda felhasználónevét és jelszavát. Cserélje le a parancsban használt SubscriptionID `az account set --subscription` azonosítót a saját előfizetés-azonosítóra.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md