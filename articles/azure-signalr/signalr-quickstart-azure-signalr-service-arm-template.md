---
title: 'Rövid útmutató: Azure Signaler szolgáltatás-ARM-sablon létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Signal Service-szolgáltatást egy Azure Resource Manager sablonnal (ARM-sablon).
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841615"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Gyors útmutató: ARM-sablon használata az Azure Signaler szolgáltatás üzembe helyezéséhez

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy Azure Signaler szolgáltatás létrehozásához. Az Azure Signaler szolgáltatást a Azure Portal, a PowerShell vagy a CLI használatával helyezheti üzembe.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon ekkor megnyílik a Azure Portal a bejelentkezés után.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Gomb az Azure Signaler szolgáltatás az Azure-ba való üzembe helyezéséhez az Azure Portal ARM-sablon használatával.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha a kódot helyileg szeretné futtatni, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha helyileg szeretné futtatni a kódot:
    * Egy bash-rendszerhéj (például git bash, amely a [git for Windows](https://gitforwindows.org)részét képezi).
    * [Azure CLI](/cli/azure/install-azure-cli)-vel.

---

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-signalr/) közül származik.

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

A sablon egy Azure-erőforrást definiál:

* [**Microsoft. SignalRService/szignáló**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő hivatkozásra kattintva telepítheti az Azure Signaler szolgáltatást a Azure Portal ARM sablonjának használatával:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Gomb az Azure Signaler szolgáltatás az Azure-ban való üzembe helyezéséhez a Azure Portal ARM-sablonjának használatával.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Az **Azure signaler szolgáltatás üzembe helyezése** oldalon:

1. Ha szeretné, módosítsa az **előfizetést** az alapértelmezett értékre.

2. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, adja meg az új erőforráscsoport nevét, majd kattintson **az OK gombra**.

3. Ha létrehozott egy új erőforráscsoportot, válasszon ki egy **régiót** az erőforráscsoport számára.

4. Ha szeretné, adja meg az Azure Signaler szolgáltatás új **nevét** és **helyét** (például **eastus2**). Ha nem ad meg nevet, az automatikusan létrejön. Az Azure Signaler szolgáltatás helye megegyezik az erőforráscsoport régiójával, vagy eltérő lehet. Ha nem ad meg helyet, az az erőforráscsoport számára azonos régióra van beállítva.

5. Válassza ki **az árképzési szintet** (**Free_F1** vagy **Standard_S1**), adja meg a **kapacitást** (a jelző egységek számát), és válassza ki az **alapértelmezett** **szolgáltatási módot** (a szükséges központi kiszolgáló), a **kiszolgáló** nélküli (nem engedélyezi a kiszolgálók kapcsolatát) vagy a **klasszikus** (csak akkor, ha a hub kiszolgálói kapcsolattal rendelkezik). Ezután adja meg, hogy engedélyezi-e a **kapcsolati naplókat** , vagy **engedélyezze az üzenetkezelési naplókat**.

    > [!NOTE]
    > A **Free_F1** díjszabási szintje esetében a kapacitás 1 egységre van korlátozva.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Képernyőkép a Azure Portal Azure Signaler szolgáltatás létrehozásához használt ARM-sablonról.":::

6. Válassza az **Áttekintés + létrehozás** lehetőséget.

7. Olvassa el a használati feltételeket, majd válassza a **Létrehozás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Ha helyileg szeretné futtatni a PowerShell-parancsfájlokat, első lépésként adja meg az `Connect-AzAccount` Azure-beli hitelesítő adatait.

Az alábbi kód használatával helyezheti üzembe az Azure Signaler szolgáltatást az ARM-sablonnal. A kód a következő elemekre kéri:

* Az új Azure Signaler szolgáltatás neve és régiója
* Egy új erőforráscsoport neve és régiója
* Az Azure díjszabási szintje (**Free_F1** vagy **Standard_S1**)
* A jelző egység kapacitása (1, 2, 5, 10, 20, 50 vagy 100)
  > [!NOTE]
  > A **Free_F1** díjszabási szintje esetében a kapacitás 1 egységre van korlátozva.
* A szolgáltatási mód: **alapértelmezés** szerint a központi kiszolgáló megkövetelése, **kiszolgáló** nélküli, hogy ne engedélyezze a kiszolgálói kapcsolatokat vagy a **klasszikus** útvonalat a központi kiszolgálókhoz való továbbításhoz, ha a hub kiszolgálói kapcsolatban van
* A kapcsolatok vagy üzenetküldési naplók engedélyezése (**igaz** vagy **hamis**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Az alábbi kód használatával helyezheti üzembe az Azure Signaler szolgáltatást az ARM-sablonnal. A kód a következő elemekre kéri:

* Az új Azure Signaler szolgáltatás neve és régiója
* Egy új erőforráscsoport neve és régiója
* Az Azure díjszabási szintje (**Free_F1** vagy **Standard_S1**)
* A jelző egység kapacitása (1, 2, 5, 10, 20, 50 vagy 100)
    > [!NOTE]
    > A **Free_F1** díjszabási szintje esetében a kapacitás 1 egységre van korlátozva.
* A szolgáltatási mód: **alapértelmezés** szerint a központi kiszolgáló megkövetelése, **kiszolgáló** nélküli, hogy ne engedélyezze a kiszolgálói kapcsolatokat vagy a **klasszikus** útvonalat a központi kiszolgálókhoz való továbbításhoz, ha a hub kiszolgálói kapcsolatban van
* A kapcsolatok vagy üzenetküldési naplók engedélyezése (**igaz** vagy **hamis**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Az üzembe helyezés befejezése eltarthat néhány percig. Jegyezze fel az Azure Signaler szolgáltatás és az erőforráscsoport nevét, amelyet később az üzembe helyezett erőforrások áttekintésére használ.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve megtekintheti az új Azure Signaler szolgáltatás áttekintését:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a **jelzőt**.

2. A szignáló listából válassza ki az új szolgáltatást. Megjelenik az új Azure Signaler szolgáltatás **áttekintő** lapja.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

A következő interaktív kód futtatásával tekintheti meg az Azure Signaler szolgáltatás részleteit. Meg kell adnia az új szolgáltatás nevét és az erőforráscsoportot.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

A következő interaktív kód futtatásával tekintheti meg az Azure Signaler szolgáltatás részleteit. Meg kell adnia az új szolgáltatás nevét és az erőforráscsoportot.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet.

2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.

3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Következő lépések

Az ARM-sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
