---
title: Dedikált SQL-készlet (korábban SQL DW) létrehozása egy Azure Resource Manager használatával
description: Megtudhatja, hogyan hozhat létre Azure Synapse Analytics SQL-készletet egy Azure Resource Manager használatával.
services: azure-resource-manager
author: julieMSFT
ms.author: jrasnick
ms.date: 06/09/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3e05fda65e4383f66cf9553ee7e083c18367b937
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534285"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Rövid útmutató: Dedikált AZURE SYNAPSE ANALYTICS SQL-készlet (korábban SQL DW) létrehozása ARM-sablon használatával

Ez Azure Resource Manager sablon (ARM-sablon) létrehoz egy dedikált SQL-készletet (korábban SQL DW), amely transzparens adattitkosítás engedélyezett. A dedikált SQL-készlet (korábban SQL DW) a vállalati adatraktározási funkciókra utal, amelyek általánosan elérhetők a Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/) közül származik.

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

A sablon egy erőforrást határoz meg:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az alábbi kép kiválasztásával jelentkezzen be az Azure-ba, és nyissa meg a sablont. Ez a sablon létrehoz egy dedikált SQL-készletet (korábban SQL DW).
   
   [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Adja meg vagy frissítse a következő értékeket:

   * **Előfizetés:** Válasszon ki egy Azure-előfizetést.
   * **Erőforráscsoport:** Válassza az **Új létrehozása lehetőséget,** adjon meg egy egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.** Egy új erőforráscsoport megkönnyíti az erőforrások tisztítását.
   * **Régió:** Válasszon ki egy régiót.  Például: **USA középső régiója**.
   * **SQL Server neve:** Fogadja el az alapértelmezett nevet, vagy adja meg a SQL Server nevét.
   * **SQL-rendszergazdai bejelentkezés:** Adja meg a rendszergazda felhasználónevét a SQL Server.
   * **SQL-rendszergazda jelszava:** Adja meg a rendszergazdai jelszót a SQL Server.
   * **Data Warehouse Név:** Adja meg a dedikált SQL-készlet nevét.
   * **transzparens adattitkosítás:** Fogadja el az alapértelmezett, engedélyezett beállítást. 
   * **Szolgáltatási szint célkitűzése:** Fogadja el az alapértelmezett DW400c értéket.
   * **Hely:** Fogadja el az erőforráscsoport alapértelmezett helyét.
   * **Áttekintés és létrehozás:** Válassza ki.
   * **Létrehozás:** Válassza a lehetőséget.

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

Használhatja a Azure Portal az üzembe helyezett erőforrások ellenőrzéséhez, vagy használhatja az Azure CLI-t vagy Azure PowerShell szkriptet az üzembe helyezett erőforrások listához.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot az Azure CLI vagy a Azure PowerShell:

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

Ebben a rövid útmutatóban létrehozott egy dedikált SQL-készletet (korábban SQL DW-t) egy ARM-sablonnal, és érvényesítette az üzembe helyezést. Ha többet szeretne megtudni a Azure Synapse Analytics és Azure Resource Manager, tekintse meg az alábbi cikkeket.

- Olvassa el [a Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- [Az első ARM-sablon létrehozása és üzembe helyezése](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
