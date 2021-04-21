---
title: VNet-végpontok kezelése – Azure CLI – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet virtuális Azure Database for MySQL szolgáltatásvégpontokat és szabályokat az Azure CLI parancssori felületének használatával.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bc686efbd07cf39d7932b175b6f9800b1ff185f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774664"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Virtuális hálózati Azure Database for MySQL létrehozása és kezelése az Azure CLI használatával
A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MySQL-kiszolgálóra. A kényelmes Azure parancssori felületi (CLI-) parancsokkal létrehozhat, frissíthet, törölhet, list készíthet és felsorolhat VNet-szolgáltatásvégpontokat és szabályokat a kiszolgáló kezeléséhez. A virtuális hálózatok Azure Database for MySQL áttekintését, beleértve a korlátozásokat: Azure Database for MySQL [VNet-szolgáltatásvégpontokat.](concepts-data-access-and-security-vnet.md) A VNet-szolgáltatásvégpontokat az összes támogatott régióban el lehet Azure Database for MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Szüksége lesz egy [Azure Database for MySQL-kiszolgálóra és -adatbázisra.](quickstart-create-mysql-server-database-using-azure-cli.md)
 
- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!NOTE]
> A VNet-szolgáltatásvégpontokat csak a virtuális hálózati általános célú memóriaoptimal optimalizált kiszolgálók támogatják.
> Virtuális hálózatok közötti társviszony esetén, ha a forgalom egy szolgáltatásvégpontokkal közös VNet Gateway-kapcsolaton keresztül áramlik, és a társhoz kell áramlónak lennie, hozzon létre egy ACL/VNet-szabályt, amely engedélyezi az Azure Virtual Machines számára az átjáró virtuális hálózatában az Azure Database for MySQL-kiszolgálóhoz való hozzáférést.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Vnet-szolgáltatásvégpont konfigurálása Azure Database for MySQL
Az [az network vnet](/cli/azure/network/vnet) parancsokkal konfigurálhatja a virtuális hálózatokat.

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) paranccsal. Helyettesítse **be** az **előfizetése az login kimenetéből** származó id tulajdonságot az előfizetés-azonosító helyőrzőjére.

- A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

A szolgáltatásvégpontokat a virtuális hálózatokon egymástól függetlenül konfigurálhatja egy olyan felhasználó, aki írási hozzáféréssel rendelkezik a virtuális hálózathoz.

Ahhoz, hogy azure-szolgáltatási erőforrásokat biztosítsunk egy virtuális hálózat számára, a felhasználónak rendelkeznie kell a hozzáadott alhálózatok "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedélyével. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások eltérő előfizetésben vannak, az erőforrásoknak ugyanabban a Active Directory (AD) bérlőben kell lennie. Győződjön meg arról, hogy mindkét előfizetésben regisztrálva van **a Microsoft.Sql** erőforrás-szolgáltató. További információ: [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> Javasoljuk, hogy olvassa el ezt a cikket a szolgáltatásvégpont-konfigurációkról és a megfontolandó szempontokról, mielőtt futtatja az alábbi parancsfájlmintát, vagy konfigurálja a szolgáltatásvégpontokat. **Virtual Network szolgáltatásvégpont:** A [Virtual Network szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékei egy vagy több formális Azure-szolgáltatástípusnevet tartalmaznak. A VNet-szolgáltatásvégpont a **Microsoft.Sql** szolgáltatástípusnevet használja, amely az SQL Database. Ez a szolgáltatáscímke a Azure SQL Database, Azure Database for PostgreSQL MySQL-szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft.Sql** szolgáltatáscímkét egy VNet-szolgáltatásvégpontra alkalmazza, a szolgáltatásvégpont forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve az alhálózat Azure SQL Database-, Azure Database for PostgreSQL- és Azure Database for MySQL-kiszolgálóit is. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Mintaszkenn, amely létrehoz egy Azure Database for MySQL-adatbázist, létrehoz egy VNetet és egy VNet-szolgáltatásvégpontot, és egy VNet-s s szabály segítségével biztosítja a kiszolgáló és az alhálózat biztonságát
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a parancsban használt SubscriptionID `az account set --subscription` azonosítót a saját előfizetés-azonosítóra.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
