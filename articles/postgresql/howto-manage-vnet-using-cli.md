---
title: Virtuális hálózati szabályok használata – Azure CLI – Azure Database for PostgreSQL – Egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet virtuális hálózati szolgáltatásvégpontokat és szabályokat a Azure Database for PostgreSQL Azure CLI parancssori felületének használatával.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0de1f00823bd34e18a727b8e2929f64a8769c93a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789896"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Virtuális hálózati szolgáltatásvégpont létrehozása és kezelése Azure Database for PostgreSQL – Egyetlen kiszolgáló az Azure CLI használatával
Virtual Network (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik egy Virtual Network privát címterét a Azure Database for PostgreSQL kiszolgálóra. Az Azure parancssori felület (CLI) kényelmesen használható parancsai segítségével létrehozhatja, frissítheti, törölheti, listába sorolhatja és meg is mutathatja a kiszolgáló kezeléséhez szükséges VNet-szolgáltatásvégpontokat és szabályokat. A virtuális hálózatok szolgáltatásvégpontjainak Azure Database for PostgreSQL, beleértve a korlátozásokat: Azure Database for PostgreSQL [VNet-szolgáltatásvégpontokat](concepts-data-access-and-security-vnet.md)ismertető témakör. A virtuális hálózatok szolgáltatásvégpontjai az összes támogatott régióban elérhetők Azure Database for PostgreSQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek
Az útmutató lépésenként:
- Telepítse [az Azure CLI-t,](/cli/azure/install-azure-cli) vagy használja Azure Cloud Shell a böngészőben.
- Hozzon létre [egy Azure Database for PostgreSQL-kiszolgálót és -adatbázist.](quickstart-create-server-database-azure-cli.md)

    > [!NOTE]
    > A VNet-szolgáltatásvégpontokat csak a virtuális hálózatok általános célú memóriaoptimalitált kiszolgálók támogatják.
    > Virtuális hálózatok közötti társviszony esetén, ha a forgalom egy szolgáltatásvégpontokkal közös VNet Gateway-kapcsolaton keresztül áramlik, és a társhoz kell áramlik, hozzon létre egy ACL/VNet-szabályt, amely engedélyezi az átjáró virtuális hálózatában található Azure Virtual Machines számára az Azure Database for PostgreSQL-kiszolgálóhoz való hozzáférést.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Virtuális hálózati szolgáltatásvégpont konfigurálása Azure Database for PostgreSQL
Az [az network vnet](/cli/azure/network/vnet) parancsokkal konfigurálhatja a virtuális hálózatokat.

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

A szolgáltatásvégpontokat a virtuális hálózatokon egymástól függetlenül konfigurálhatja egy olyan felhasználó, aki írási hozzáféréssel rendelkezik a virtuális hálózathoz.

Ahhoz, hogy azure-szolgáltatási erőforrásokat biztosítsunk egy virtuális hálózat számára, a felhasználónak rendelkeznie kell a hozzáadott alhálózatok "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedélyével. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások eltérő előfizetésben vannak, az erőforrásoknak ugyanabban a Active Directory (AD) bérlőben kell lennie. Győződjön meg arról, hogy mindkét előfizetésben regisztrálva van a **Microsoft.Sql** erőforrás-szolgáltató. További információ: [resource-manager-registration.][resource-manager-portal]

> [!IMPORTANT]
> Javasoljuk, hogy olvassa el ezt a cikket a szolgáltatásvégpont konfigurációiról és megfontolandó szempontjairól, mielőtt futtatja az alábbi parancsfájlt, vagy konfigurálja a szolgáltatásvégpontokat. **Virtual Network szolgáltatásvégpont:** A [Virtual Network szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékei egy vagy több formális Azure-szolgáltatástípusnevet tartalmaznak. A VNet-szolgáltatásvégpont a **Microsoft.Sql** szolgáltatástípusnevet használja, amely az SQL Database. Ez a szolgáltatáscímke a Azure SQL Database, Azure Database for PostgreSQL MySQL-szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft.Sql** szolgáltatáscímkét egy VNet-szolgáltatásvégpontra alkalmazza, a szolgáltatásvégpont forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve az Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL-kiszolgálók forgalmát az alhálózaton. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Példaszkprogram egy Azure Database for PostgreSQL-adatbázis létrehozásához, egy VNet- és egy VNet-szolgáltatásvégpont létrehozásához, valamint a kiszolgáló alhálózathoz való biztonságossá létrehozásához egy VNet-s s szabály használatával
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a parancsban használt SubscriptionID `az account set --subscription` azonosítót a saját előfizetés-azonosítóra.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
