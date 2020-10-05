---
title: SQL-készlet létrehozása Azure Resource Manager sablon használatával
description: Ismerje meg, hogyan hozhat létre Azure szinapszis Analytics SQL-készletet Azure Resource Manager sablon használatával.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 29d4e4d696b34aa493714c870ebb466f491c47fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88641874"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>Rövid útmutató: Azure szinapszis Analytics SQL-készlet létrehozása ARM-sablonnal

Ez a Azure Resource Manager sablon (ARM-sablon) egy Azure szinapszis Analytics SQL-készletet hoz létre, amelynek transzparens adattitkosítás engedélyezve van. A szinapszis SQL-készlet az Azure Szinapszisban általánosan elérhető vállalati adattárház-funkciókra utal.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/) közül származik.

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

A sablon egy erőforrást definiál:

- [Microsoft. SQL/kiszolgálók](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez és a sablon megnyitásához. Ez a sablon egy szinapszis SQL-készletet hoz létre.
   
   [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Adja meg vagy frissítse a következő értékeket:

   * **Előfizetés**: válasszon ki egy Azure-előfizetést.
   * **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK gombra**. Egy új erőforráscsoport segíti az erőforrás karbantartását.
   * **Régió**: válasszon régiót.  Például: **USA középső régiója**.
   * **SQL Server neve**: fogadja el az alapértelmezett nevet, vagy adja meg a SQL Server nevének nevét.
   * **SQL-rendszergazdai bejelentkezés**: adja meg a SQL Server rendszergazdai felhasználónevét.
   * **SQL-rendszergazda jelszava**: adja meg a SQL Server rendszergazdai jelszavát.
   * **Adatraktár neve**: adjon meg egy SQL-készlet nevét.
   * **Transzparens adattitkosítás**: fogadja el az alapértelmezett, engedélyezve értéket. 
   * **Szolgáltatási szint célkitűzése**: fogadja el az alapértelmezett DW400c.
   * **Hely**: fogadja el az erőforráscsoport alapértelmezett helyét.
   * **Áttekintés és létrehozás**: válassza a lehetőséget.
   * **Létrehozás**: válassza a lehetőséget.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Használhatja a Azure Portal az üzembe helyezett erőforrások vizsgálatához, vagy az Azure CLI vagy a Azure PowerShell parancsfájl használatával listázhatja az üzembe helyezett erőforrásokat.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot az Azure CLI vagy a Azure PowerShell használatával:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure szinapszis Analytics SQL-készletet egy ARM-sablonnal, és ellenőrizte az üzembe helyezést. Ha többet szeretne megtudni az Azure szinapszis Analyticsről és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- Az [Azure szinapszis Analytics áttekintése](sql-data-warehouse-overview-what-is.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- [Az első ARM-sablon létrehozása és üzembe helyezése](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
