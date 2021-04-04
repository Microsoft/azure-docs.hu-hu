---
title: Virtuális hálózati szabályok használata – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan hozhatók létre és kezelhetők VNet-szolgáltatási végpontok és szabályok az Azure CLI parancssor használatával történő Azure Database for PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660882"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>VNet-szolgáltatási végpontok létrehozása és kezelése az Azure CLI-vel való Azure Database for PostgreSQL – egyetlen kiszolgáló esetén
Virtual Network (VNet) szolgáltatási végpontok és szabályok kiterjesztik a Virtual Network privát címterület-területét a Azure Database for PostgreSQL-kiszolgálóra. A kényelmes Azure parancssori felületi (CLI) parancsok használatával létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet VNet szolgáltatási végpontokat és szabályokat a kiszolgáló kezeléséhez. Az Azure Database for PostgreSQL VNet szolgáltatásbeli végpontok, például a korlátozások áttekintését lásd: [Azure Database for PostgreSQL Server VNet Service-végpontok](concepts-data-access-and-security-vnet.md). A VNet szolgáltatási végpontok a Azure Database for PostgreSQL összes támogatott régiójában elérhetők.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépései:
- Telepítse [Az Azure CLI](/cli/azure/install-azure-cli) -t, vagy használja a Azure Cloud shellt a böngészőben.
- Hozzon létre egy [Azure Database for PostgreSQL-kiszolgálót és-adatbázist](quickstart-create-server-database-azure-cli.md).

    > [!NOTE]
    > A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.
    > VNet esetén, ha a forgalom egy közös VNet-átjárón keresztül áramlik a szolgáltatási végpontokkal, és a partnernek kell lennie, hozzon létre egy ACL/VNet szabályt, amely lehetővé teszi, hogy az Azure Virtual Machines az átjáró VNet hozzáférjenek a Azure Database for PostgreSQL-kiszolgálóhoz.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Vnet-szolgáltatási végpontok konfigurálása Azure Database for PostgreSQLhoz
Az az [Network vnet](/cli/azure/network/vnet) parancs a virtuális hálózatok konfigurálására szolgál.

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az-account-set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

A szolgáltatási végpontok egymástól függetlenül konfigurálhatók virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználóktól.

Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" jogosultsággal a hozzáadott alhálózatokhoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a VNet és az Azure-szolgáltatási erőforrások különböző előfizetésekben találhatók, akkor az erőforrásoknak ugyanahhoz a Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. SQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal].

> [!IMPORTANT]
> Javasoljuk, hogy olvassa el ezt a cikket a szolgáltatás-végponti konfigurációkról és a szempontokról, mielőtt futtatná az alábbi parancsfájlt, vagy konfigurálja a szolgáltatási végpontokat. **Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. A VNet Services-végpontok a **Microsoft. SQL** nevű szolgáltatástípus-nevet használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatási címke a Azure SQL Databasere, a Azure Database for PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft. SQL** szolgáltatás címkéjét egy VNet szolgáltatási végpontra alkalmazza, a szolgáltatás végponti forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve az alhálózaton lévő Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL kiszolgálókat is. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Parancsfájl Azure Database for PostgreSQL adatbázis létrehozásához, VNet létrehozásához, VNet szolgáltatás végpontjának biztonságossá tételéhez, valamint a kiszolgáló VNet-szabállyal való biztonságossá tétele az alhálózathoz
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a parancsban használt SubscriptionID a `az account set --subscription` saját előfizetés-azonosítójával.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
