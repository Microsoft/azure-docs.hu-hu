---
title: 'Rövid útmutató: Azure-adatbázis létrehozása PostgreSQL-ARM-sablonhoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Database for PostgreSQL egyetlen kiszolgálót egy Azure Resource Manager sablon használatával.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: fb9f12b3b31f1049cd4d9306294783e514331229
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382191"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Gyors útmutató: ARM-sablon használata Azure Database for PostgreSQL – egyetlen kiszolgáló létrehozásához

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hoz létre egy Azure Database for PostgreSQL-egyetlen kiszolgálót a Azure Portal, a PowerShell vagy az Azure CLI-ben.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

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

Létre kell hoznia egy Azure Database for PostgreSQL-kiszolgálót a számítási és tárolási erőforrások konfigurált készletével. További információ: [Azure Database for PostgreSQL-Single Server díjszabási szintjei](concepts-pricing-tiers.md). A kiszolgálót egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül hozza létre.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/) közül származik.

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

A sablon öt Azure-erőforrást határoz meg:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/alhálózatok**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforPostgreSQL/kiszolgálók**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft. DBforPostgreSQL/kiszolgálók/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft. DBforPostgreSQL/kiszolgálók/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

További Azure Database for PostgreSQL-sablonok találhatók az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő hivatkozásra kattintva telepítheti a Azure Database for PostgreSQL-kiszolgáló sablonját a Azure Portalban:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

A **Azure Database for PostgreSQL üzembe helyezése a VNet** oldalon:

1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, adja meg az új erőforráscsoport nevét, majd kattintson **az OK gombra**.

2. Ha létrehozott egy új erőforráscsoportot, válassza ki az erőforráscsoport és az új kiszolgáló **helyét** .

3. Adja meg a **kiszolgáló nevét**, a **rendszergazdai bejelentkezési azonosítót** és a **rendszergazdai bejelentkezési jelszót**.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Azure Database for PostgreSQL üzembe helyezése a VNet ablakban, Azure-beli Gyorsindítás sablon, Azure Portal":::

4. Ha szeretné, módosítsa a többi alapértelmezett beállítást:

    * **Előfizetés**: a kiszolgálóhoz használni kívánt Azure-előfizetés.
    * **SKU-kapacitás**: a virtuális mag kapacitása, amely lehet *2* (alapértelmezett), *4*, *8*, *16*, *32* vagy *64*.
    * **SKU neve**: az SKU-rétegek előtagja, az SKU-család és az SKU-kapacitás, aláhúzások, például *B_Gen5_1*, *GP_Gen5_2* (alapértelmezett) vagy *MO_Gen5_32*.
    * **SKU mérete MB**: a Azure Database for PostgreSQL kiszolgáló tárterületének mérete (MB) (alapértelmezett *51200*).
    * **SKU-szint**: az üzembe helyezési szint, például *alapszintű,* *GeneralPurpose* (alapértelmezett) vagy *MemoryOptimized*.
    * **SKU-család**: *Gen4* vagy *Gen5* (alapértelmezett), amely a kiszolgáló telepítésének hardveres generálását jelzi.
    * **PostgreSQL-verzió**: a telepített PostgreSQL-kiszolgáló verziója, például *9,5*, *9,6*, *10* vagy *11* (az alapértelmezett).
    * **Biztonsági másolatok megőrzési napjai**: a földrajzi redundáns biztonsági másolatok megőrzésének kívánt időtartama napokban (alapértelmezett *7*).
    * **Geo redundáns biztonsági mentés**: *engedélyezve* vagy *Letiltva* (az alapértelmezett) a Geo-vész-helyreállítási (Geo-Dr) követelményektől függően.
    * **Virtual Network neve**: a virtuális hálózat neve (alapértelmezett *azure_postgresql_vnet*).
    * **Alhálózat neve**: az alhálózat neve (alapértelmezett *azure_postgresql_subnet*).
    * **Virtual Network szabály neve**: az alhálózatot engedélyező virtuális hálózati szabály neve (alapértelmezett *AllowSubnet*).
    * **Vnet-címek előtagja**: a virtuális hálózat címe (alapértelmezett *10.0.0.0/16*).
    * **Alhálózat-előtag**: az alhálózat 10.0.0.0 (alapértelmezett érték */16*).

5. Olvassa el a használati feltételeket, majd válassza az Elfogadom **a fenti feltételeket és** kikötéseket lehetőséget.

6. Válassza a **Beszerzés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

A sablon használatával hozzon létre egy új Azure Database for PostgreSQL-kiszolgálót a következő interaktív kóddal. A kód az új kiszolgálónév, egy új erőforráscsoport nevének és helyének, valamint egy rendszergazdai fiók nevének és jelszavának megadását kéri.

A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

A sablon használatával hozzon létre egy új Azure Database for PostgreSQL-kiszolgálót a következő interaktív kóddal. A kód az új kiszolgálónév, egy új erőforráscsoport nevének és helyének, valamint egy rendszergazdai fiók nevének és jelszavának megadását kéri.

A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve tekintheti meg az új Azure Database for PostgreSQL-kiszolgáló áttekintését:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **Azure Database for PostgreSQL kiszolgálókat**.

2. Az adatbázis listában válassza ki az új kiszolgálót. Megjelenik az új Azure Database for PostgreSQL-kiszolgáló **Áttekintés** lapja.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Futtassa a következő interaktív kódot a Azure Database for PostgreSQL-kiszolgáló részleteinek megtekintéséhez. Meg kell adnia az új kiszolgáló nevét.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Futtassa a következő interaktív kódot a Azure Database for PostgreSQL-kiszolgáló részleteinek megtekintéséhez. Meg kell adnia az új kiszolgáló nevét és erőforrás-csoportját.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>ARM-sablon exportálása a portálról
ARM-sablonokat a Azure Portal is [exportálhat](../azure-resource-manager/templates/export-template-portal.md) . Kétféleképpen exportálhat sablont:

- [Exportálás erőforráscsoporthoz vagy erőforrásból](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Ez a beállítás új sablont hoz létre a meglévő erőforrásokból. Az exportált sablon az erőforráscsoport aktuális állapotának "pillanatképe". A teljes erőforráscsoportot vagy az adott erőforráscsoporthoz tartozó erőforrásokat is exportálhatja.
- [Exportálás az üzembe helyezés előtt vagy az előzményekből](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Ez a beállítás az üzembe helyezéshez használt sablon pontos másolatát kérdezi le.

A sablon exportálásakor a PostgreSQL- ```"properties":{ }```  kiszolgáló erőforrásának szakaszában megfigyelheti, hogy a ```administratorLogin``` ```administratorLoginPassword``` biztonsági okokból nem kerül bele a szolgáltatásba. Ezeket a paramétereket hozzá **kell** adnia a sablonhoz a sablon telepítése előtt, vagy a sablon sikertelen lesz.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
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

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
