---
title: VNet-végpontok kezelése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan hozhatók létre és kezelhetők Azure Database for MariaDB VNet szolgáltatásbeli végpontok és szabályok az Azure CLI parancssorral.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43d1b7700395bd06960737eae4f318d61aa03717
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665089"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure Database for MariaDB VNet-szolgáltatásbeli végpontok létrehozása és kezelése az Azure CLI-vel

A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MariaDB-kiszolgálóra. A kényelmes Azure parancssori felületi (CLI) parancsok használatával létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet VNet szolgáltatási végpontokat és szabályokat a kiszolgáló kezeléséhez. Az Azure Database for MariaDB VNet szolgáltatásbeli végpontok, például a korlátozások áttekintését lásd: [Azure Database for MariaDB Server VNet Service-végpontok](concepts-data-access-security-vnet.md). A VNet szolgáltatási végpontok a Azure Database for MariaDB összes támogatott régiójában elérhetők.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Szüksége van egy [Azure Database for MariaDB kiszolgálóra és adatbázisra](quickstart-create-mariadb-server-database-using-azure-cli.md).

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!NOTE]
> A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

## <a name="configure-vnet-service-endpoints"></a>VNet-szolgáltatási végpontok konfigurálása

Az az [Network vnet](/cli/azure/network/vnet) parancs a virtuális hálózatok konfigurálására szolgál.

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az-account-set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

A szolgáltatási végpontok egymástól függetlenül konfigurálhatók virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználóktól.

Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" jogosultsággal a hozzáadott alhálózatokhoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a VNet és az Azure-szolgáltatási erőforrások különböző előfizetésekben találhatók, akkor az erőforrásoknak ugyanahhoz a Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. SQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

> [!IMPORTANT]
> Javasoljuk, hogy a szolgáltatási végpontok konfigurálása előtt olvassa el ezt a cikket a szolgáltatás-végponti konfigurációkkal és szempontokkal kapcsolatban. **Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. A VNet Services-végpontok a **Microsoft. SQL** nevű szolgáltatástípus-nevet használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatási címke a Azure SQL Databasera, a Azure Database for MariaDBra, a PostgreSQL-re és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft. SQL** szolgáltatás címkéjét egy VNet szolgáltatási végpontra alkalmazza, a szolgáltatás végponti forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve az alhálózaton Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB és Azure Database for MySQL kiszolgálókat.

### <a name="sample-script"></a>Példaszkript

Ez a minta parancsfájl egy Azure Database for MariaDB kiszolgáló létrehozásához, egy VNet, egy VNet szolgáltatás végpontjának létrehozásához és a kiszolgáló VNet-szabállyal való biztonságossá tételéhez használható. A minta parancsfájlban módosítsa a rendszergazdai felhasználónevet és jelszót. Cserélje le a parancsban használt SubscriptionID a `az account set --subscription` saját előfizetés-azonosítójával.

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