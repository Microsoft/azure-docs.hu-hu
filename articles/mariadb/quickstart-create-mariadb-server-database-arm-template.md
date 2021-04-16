---
title: 'Rövid útmutató: Azure DB for MariaDB létrehozása – ARM-sablon'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Database for MariaDB-kiszolgálót egy Azure Resource Manager sablon használatával.
author: savjani
ms.author: pariks
ms.date: 05/14/2020
ms.topic: quickstart
ms.service: mariadb
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6bf7f4d30f2ad4f9e0181aed332e3f6cb9265ca0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531335"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Rövid útmutató: ARM-sablon használata új Azure Database for MariaDB létrehozásához

Azure Database for MariaDB egy felügyelt szolgáltatás, amely segítségével magas rendelkezésre álló MariaDB-adatbázisokat futtathat, kezelhet és skálázhat a felhőben. Ebben a rövid útmutatóban egy Azure Resource Manager-sablont (ARM-sablont) fog használni egy Azure Database for MariaDB-kiszolgáló létrehozásához a Azure Portal, a PowerShell vagy az Azure CLI használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/)
* Ha helyileg szeretné futtatni [](/powershell/azure/)a kódot, Azure PowerShell.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/)
* Ha helyileg szeretné futtatni a kódot, használja [az Azure CLI-t.](/cli/azure/)

---

## <a name="review-the-template"></a>A sablon áttekintése

Az Azure Database for MariaDB-kiszolgálót számítási és tárolási erőforrások egy meghatározott készletével együtt fogja létrehozni. További információ: Azure Database for MariaDB [tarifacsomagok.](concepts-pricing-tiers.md) A kiszolgálót egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül hozza létre.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/) közül származik.

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

A sablon öt Azure-erőforrást határoz meg:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/alhálózatok**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMariaDB/servers**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforMariaDB/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforMariaDB/servers/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

További Azure Database for MariaDB mintákat az Azure gyorsindítási [sablonok között talál.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő hivatkozásra kattintva telepítse a Azure Database for MariaDB-kiszolgálósablont a Azure Portal:

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

A Deploy Azure Database for MariaDB with VNet (Virtuális hálózat üzembe helyezése **virtuális hálózatokkal) lapon:**

1. Az **Erőforráscsoport mezőben** válassza az **Új létrehozása** lehetőséget, adjon nevet az új erőforráscsoportnak, majd kattintson az OK **gombra.**

2. Ha új erőforráscsoportot hozott létre, válassza **ki** az erőforráscsoport és az új kiszolgáló helyét.

3. Adjon meg egy **kiszolgálónevet,** **egy rendszergazdai bejelentkezési nevet** és egy **rendszergazdai bejelentkezési jelszót.**

    ![Üzembe Azure Database for MariaDB virtuális hálózat ablakával, Azure gyorsindítási sablonnal és Azure Portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Ha szeretné, módosítsa a többi alapértelmezett beállítást:

    * **Előfizetés:** a kiszolgálóhoz használni kívánt Azure-előfizetés.
    * **Termékváltozat-kapacitás:** a virtuális mag kapacitása, amely *2* (alapértelmezett), *4,* *8,* *16,* *32* vagy *64 lehet.*
    * **Termékváltozat neve:** a termékváltozat-szint előtagja, a termékváltozat-család és a termékváltozat-kapacitás, aláhúzásjelekkel (például *B_Gen5_1,* *GP_Gen5_2* (alapértelmezett) vagy *MO_Gen5_32.*
    * **Sku Size MB (Termékváltozat** mérete MB) : a tárterület mérete megabájtban a Azure Database for MariaDB (alapértelmezett *érték: 51200).*
    * **Termékváltozat rétege:** az üzembe helyezési szint, például *Alapszintű,* *ÁltalánosCélú* (alapértelmezett) vagy *MemoryOptimized.*
    * **Termékváltozat-család:** *Gen4* vagy *Gen5* (alapértelmezett), amely a kiszolgálótelepítés hardvergenerációját jelzi.
    * **Mariadb-verzió:** az üzembe helyezendő MariaDB-kiszolgáló verziója, például *10.2* vagy *10.3* (alapértelmezés).
    * **Biztonsági másolatok megőrzése (nap):** a georedundáns biztonsági másolatok megőrzésének kívánt időtartama napokban *(alapértelmezés: 7).*
    * **Georedundáns biztonsági mentés:** *Engedélyezve* vagy *Letiltva* (ez az alapértelmezett beállítás) a georedundáns helyreállítás (Geo-DR) követelményeitől függően.
    * **Virtual Network Name**: a virtuális hálózat neve (alapértelmezett *azure_mariadb_vnet*).
    * **Alhálózat neve:** az alhálózat neve (alapértelmezett *azure_mariadb_subnet*).
    * **Virtual Network szabály neve:** az alhálózatot engedélyező virtuális hálózati szabály neve (alapértelmezett *AllowSubnet).*
    * **Virtuális hálózat címelőtagja:** a virtuális hálózat címelőtagja *(alapértelmezett: 10.0.0.0/16).*
    * **Alhálózati előtag:** az alhálózat címelőtagja *(alapértelmezett: 10.0.0.0/16).*

5. Olvassa el a használati feltételeket, majd válassza az **Elfogadom a** fenti feltételeket lehetőséget.

6. Válassza a **Beszerzés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Az alábbi interaktív kóddal hozzon létre egy új Azure Database for MariaDB a sablon használatával. A kód kérni fogja az új kiszolgáló nevét, az új erőforráscsoport nevét és helyét, valamint a rendszergazdai fiók nevét és jelszavát.

A kód futtatásához Azure Cloud Shell bármelyik  kódblokk felső sarkában válassza a Kipróbálom lehetőséget.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Az alábbi interaktív kóddal hozzon létre egy új Azure Database for MariaDB a sablon használatával. A kód kérni fogja az új kiszolgáló nevét, az új erőforráscsoport nevét és helyét, valamint a rendszergazdai fiók nevét és jelszavát.

A kód futtatásához Azure Cloud Shell bármelyik  kódblokk felső sarkában válassza a Kipróbálom lehetőséget.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Kövesse az alábbi lépéseket az új Azure Database for MariaDB áttekintéséhez:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **a Azure Database for MariaDB kiszolgálókat.**

2. Az adatbázislistában válassza ki az új kiszolgálót. **Megjelenik** az új Azure Database for MariaDB Áttekintés lapja.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Futtassa a következő interaktív kódot a Azure Database for MariaDB megtekintéséhez. Meg kell adnia az új kiszolgáló nevét.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Futtassa a következő interaktív kódot a Azure Database for MariaDB megtekintéséhez. Meg kell adnia az új kiszolgáló nevét és erőforráscsoportját.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza az **Erőforráscsoportok lehetőséget.**

2. Az erőforráscsoport listában válassza ki az erőforráscsoport nevét.

3. Az **erőforráscsoport Áttekintés** lapján válassza az **Erőforráscsoport törlése lehetőséget.**

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Következő lépések

Az ARM-sablonok létrehozásának folyamatát bemutató részletes oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
