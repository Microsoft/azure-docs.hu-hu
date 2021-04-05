---
title: 'Rövid útmutató: Azure-adatbázis létrehozása MySQL-ARM-sablonhoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Database for MySQL-kiszolgálót virtuális hálózati integrációval Azure Resource Manager sablon használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.openlocfilehash: 3da3b1694a16507203d7f1f1f6cb5df58dd54423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366177"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Gyors útmutató: Azure Database for MySQL-kiszolgáló létrehozása ARM-sablonnal

A Azure Database for MySQL felügyelt szolgáltatás, amellyel a felhőben futtathatja, kezelheti és méretezheti a magasan elérhető MySQL-adatbázisokat. Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy Azure Database for MySQL-kiszolgáló virtuális hálózati integrációval való létrehozásához. A kiszolgálót a Azure Portalban, az Azure CLI-ben vagy a Azure PowerShellban hozhatja létre.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha a kódot helyileg szeretné futtatni, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha a kódot helyileg szeretné futtatni, az [Azure CLI](/cli/azure/)-t.

---

## <a name="review-the-template"></a>A sablon áttekintése

A MySQL-kiszolgálóhoz való Azure-adatbázist számítási és tárolási erőforrások egy meghatározott készletével együtt fogja létrehozni. További információ: [Azure Database for MySQL díjszabási szintek](concepts-pricing-tiers.md). A kiszolgálót egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül hozza létre.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/) közül származik.

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json":::

A sablon öt Azure-erőforrást határoz meg:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/alhálózatok**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforMySQL/kiszolgálók**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft. DBforMySQL/kiszolgálók/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft. DBforMySQL/kiszolgálók/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

További Azure Database for MySQL-sablonok a gyorsindítási [sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular)katalógusában találhatók.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő hivatkozásra kattintva telepítheti a Azure Database for MySQL-kiszolgáló sablonját a Azure Portalban:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

A **Azure Database for MySQL üzembe helyezése a VNet** oldalon:

1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, adja meg az új erőforráscsoport nevét, majd kattintson **az OK gombra**.

2. Ha létrehozott egy új erőforráscsoportot, válassza ki az erőforráscsoport és az új kiszolgáló **helyét** .

3. Adja meg a **kiszolgáló nevét**, a **rendszergazdai bejelentkezési azonosítót** és a **rendszergazdai bejelentkezési jelszót**.

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="Azure Database for MySQL üzembe helyezése a VNet ablakban, Azure-beli Gyorsindítás sablon, Azure Portal":::

4. Ha szeretné, módosítsa a többi alapértelmezett beállítást:

    * **Előfizetés**: a kiszolgálóhoz használni kívánt Azure-előfizetés.
    * **SKU-kapacitás**: a virtuális mag kapacitása, amely lehet *2* (alapértelmezett), *4*, *8*, *16*, *32* vagy *64*.
    * **SKU neve**: az SKU-rétegek előtagja, az SKU-család és az SKU-kapacitás, aláhúzások, például *B_Gen5_1*, *GP_Gen5_2* (alapértelmezett) vagy *MO_Gen5_32*.
    * **SKU mérete MB**: a Azure Database for MySQL kiszolgáló tárterületének mérete (MB) (alapértelmezett *5120*).
    * **SKU-szint**: az üzembe helyezési szint, például *alapszintű,* *GeneralPurpose* (alapértelmezett) vagy *MemoryOptimized*.
    * **SKU-család**: *Gen4* vagy *Gen5* (alapértelmezett), amely a kiszolgáló telepítésének hardveres generálását jelzi.
    * **MySQL-verzió**: a telepítendő MySQL-kiszolgáló verziója, például *5,6* vagy *5,7* (alapértelmezett).
    * **Biztonsági másolatok megőrzési napjai**: a földrajzi redundáns biztonsági másolatok megőrzésének kívánt időtartama napokban (alapértelmezett *7*).
    * **Geo redundáns biztonsági mentés**: *engedélyezve* vagy *Letiltva* (az alapértelmezett) a Geo-vész-helyreállítási (Geo-Dr) követelményektől függően.
    * **Virtual Network neve**: a virtuális hálózat neve (alapértelmezett *azure_mysql_vnet*).
    * **Alhálózat neve**: az alhálózat neve (alapértelmezett *azure_mysql_subnet*).
    * **Virtual Network szabály neve**: az alhálózatot engedélyező virtuális hálózati szabály neve (alapértelmezett *AllowSubnet*).
    * **Vnet-címek előtagja**: a virtuális hálózat címe (alapértelmezett *10.0.0.0/16*).
    * **Alhálózat-előtag**: az alhálózat 10.0.0.0 (alapértelmezett érték */16*).

5. Olvassa el a használati feltételeket, majd válassza az Elfogadom **a fenti feltételeket és** kikötéseket lehetőséget.

6. Válassza a **Beszerzés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

A sablon használatával hozzon létre egy új Azure Database for MySQL-kiszolgálót a következő interaktív kóddal. A kód az új kiszolgálónév, egy új erőforráscsoport nevének és helyének, valamint egy rendszergazdai fiók nevének és jelszavának megadását kéri.

A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

A sablon használatával hozzon létre egy új Azure Database for MySQL-kiszolgálót a következő interaktív kóddal. A kód az új kiszolgálónév, egy új erőforráscsoport nevének és helyének, valamint egy rendszergazdai fiók nevének és jelszavának megadását kéri.

A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve tekintheti meg az új Azure Database for MySQL-kiszolgáló áttekintését:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **Azure Database for MySQL kiszolgálókat**.

2. Az adatbázis listában válassza ki az új kiszolgálót. Megjelenik az új Azure Database for MySQL-kiszolgáló **Áttekintés** lapja.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Futtassa a következő interaktív kódot a Azure Database for MySQL-kiszolgáló részleteinek megtekintéséhez. Meg kell adnia az új kiszolgáló nevét.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Futtassa a következő interaktív kódot a Azure Database for MySQL-kiszolgáló részleteinek megtekintéséhez. Meg kell adnia az új kiszolgáló nevét és erőforrás-csoportját.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="exporting-arm-template-from-the-portal"></a>ARM-sablon exportálása a portálról
ARM-sablonokat a Azure Portal is [exportálhat](../azure-resource-manager/templates/export-template-portal.md) . Kétféleképpen exportálhat sablont:

- [Exportálás erőforráscsoporthoz vagy erőforrásból](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Ez a beállítás új sablont hoz létre a meglévő erőforrásokból. Az exportált sablon az erőforráscsoport aktuális állapotának "pillanatképe". A teljes erőforráscsoportot vagy az adott erőforráscsoporthoz tartozó erőforrásokat is exportálhatja.
- [Exportálás az üzembe helyezés előtt vagy az előzményekből](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Ez a beállítás az üzembe helyezéshez használt sablon pontos másolatát kérdezi le.

A sablon exportálásakor a MySQL- ```"properties":{ }``` kiszolgáló erőforrásának szakaszában megfigyelheti, hogy a ```administratorLogin``` ```administratorLoginPassword``` rendszer nem fogja tartalmazni biztonsági okokból. Ezeket a paramétereket hozzá **kell** adnia a sablonhoz a sablon telepítése előtt, vagy a sablon sikertelen lesz.

```
"resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet.

2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.

3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Következő lépések

Az ARM-sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
