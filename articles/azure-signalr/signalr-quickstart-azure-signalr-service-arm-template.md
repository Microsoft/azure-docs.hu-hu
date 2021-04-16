---
title: 'Rövid útmutató: Azure SignalR Service létrehozása – ARM-sablon'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure SignalR Service egy Azure Resource Manager (ARM-sablon) használatával.
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom:
- subject-armqs
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 075915f2a06e29eb5b80425913c6e7a2c476ff99
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536519"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Rövid útmutató: Üzembe helyezés ARM-sablonnal Azure SignalR Service

Ez a rövid útmutató azt ismerteti, hogyan használható Azure Resource Manager sablon (ARM-sablon) egy Azure SignalR Service. A virtuális gép üzembe Azure SignalR Service a Azure Portal, a PowerShell vagy a parancssori felület használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon megnyílik a Azure Portal bejelentkezés után.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Gomb az Azure SignalR Service üzembe helyezéséhez az Azure-ban egy ARM-sablonnal a Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/)
* Ha helyileg szeretné futtatni [](/powershell/azure/install-az-ps)a kódot, Azure PowerShell.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/)
* Ha helyileg szeretné futtatni a kódot:
    * Bash-rendszerhéj (például a Git Bash, amely a [Git for Windows része).](https://gitforwindows.org)
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-signalr/) közül származik.

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

A sablon egy Azure-erőforrást határoz meg:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Kattintson a következő hivatkozásra a Azure SignalR Service üzembe helyezéséhez az ARM-sablonnal a Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Gomb az Azure SignalR Service üzembe helyezéséhez az Azure-ban az ARM-sablonnal a Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

A Deploy **an Azure SignalR Service** oldalon:

1. Ha szeretné, módosítsa az **előfizetést** az alapértelmezettről.

2. Az **Erőforráscsoport mezőben** válassza az **Új létrehozása** lehetőséget, adjon nevet az új erőforráscsoportnak, majd kattintson az OK **gombra.**

3. Ha új erőforráscsoportot hozott létre, válasszon ki egy **Régiót** az erőforráscsoporthoz.

4. Ha szeretné, adjon meg egy új **Nevet** és **a** Hely (például **eastus2)** nevet a Azure SignalR Service. Ha nem ad meg nevet, a rendszer automatikusan létrehoz egy nevet. Az erőforráscsoport Azure SignalR Service megegyezik az erőforráscsoport régiójával, vagy különbözik attól. Ha nem ad meg helyet, a hely az erőforráscsoporttal azonos régióra lesz beállítva.

5. Válassza ki  a tarifacsomagot **(Free_F1** vagy  **Standard_S1**), adja meg a kapacitást (a SignalR-egységek száma), és válassza az Alapértelmezett szolgáltatási módot **(központi** kiszolgáló **szükséges),** kiszolgáló nélküli (nem engedélyezi a kiszolgálókapcsolatot) vagy klasszikus (csak akkor, ha a központ kiszolgálókapcsolattal rendelkezik a központ kiszolgálókapcsolatához).   Ezután válassza ki, hogy engedélyezi-e **a kapcsolati naplókat** vagy az **üzenetküldési naplókat.**

    > [!NOTE]
    > A **Free_F1** tarifacsomag esetében a kapacitás 1 egységre van korlátozva.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Képernyőkép az ARM-sablonról a Azure SignalR Service létrehozásához Azure Portal.":::

6. Válassza az **Áttekintés + létrehozás** lehetőséget.

7. Olvassa el a használati feltételeket, majd válassza a **Létrehozás lehetőséget.**

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Ha helyileg szeretné futtatni a PowerShell-szkripteket, először adja meg a parancsot az `Connect-AzAccount` Azure-beli hitelesítő adatok beállításhoz.

A következő kóddal helyezheti üzembe a Azure SignalR Service ARM-sablonnal. A kód a következő elemeket kéri:

* Az új Azure SignalR Service
* Új erőforráscsoport neve és régiója
* Az Azure-tarifacsomag (**Free_F1** vagy **Standard_S1**)
* A SignalR-egység kapacitása (1, 2, 5, 10, 20, 50 vagy 100)
  > [!NOTE]
  > A **Free_F1** tarifacsomag esetében a kapacitás 1 egységre van korlátozva.
* A szolgáltatási mód: **Alapértelmezés** szerint egy központi **kiszolgálóra,** a kiszolgáló  nélküli kiszolgálóra nem engedélyezett kiszolgálókapcsolatra, vagy klasszikus módra van szükség a központi kiszolgálóra való útválasztáshoz, ha a központ kiszolgálói kapcsolattal rendelkezik
* Engedélyezze-e a naplók csatlakozását vagy üzenetkezelését **(igaz vagy** **hamis)**

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

A következő kóddal helyezheti üzembe Azure SignalR Service az ARM-sablonnal. A kód a következő elemeket kéri:

* Az új Azure SignalR Service
* Új erőforráscsoport neve és régiója
* Az Azure-tarifacsomag (**Free_F1** vagy **Standard_S1**)
* A SignalR-egység kapacitása (1, 2, 5, 10, 20, 50 vagy 100)
    > [!NOTE]
    > A **Free_F1** tarifacsomag esetében a kapacitás 1 egységre van korlátozva.
* A szolgáltatási mód: **Alapértelmezés** szerint egy központi **kiszolgálóra,** a kiszolgáló  nélküli kiszolgálóra nem engedélyezett kiszolgálókapcsolatra, vagy klasszikus módra van szükség a központi kiszolgálóra való útválasztáshoz, ha a központ kiszolgálói kapcsolattal rendelkezik
* Engedélyezze-e a naplók csatlakozását vagy üzenetkezelését **(igaz vagy** **hamis)**

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
> Az üzembe helyezés befejezése eltarthat néhány percig. Jegyezze fel a Azure SignalR Service erőforráscsoport nevét, amelyet később az üzembe helyezett erőforrások áttekintéséhez fog használni.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépésekkel áttekintheti az új Azure SignalR Service:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **SignalR lehetőséget.**

2. A SignalR listában válassza ki az új szolgáltatást. **Megjelenik** az új webhely Áttekintés Azure SignalR Service lapja.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Futtassa az alábbi interaktív kódot a Azure SignalR Service. Meg kell adnia az új szolgáltatás nevét és az erőforráscsoportot.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Futtassa az alábbi interaktív kódot a Azure SignalR Service. Meg kell adnia az új szolgáltatás nevét és az erőforráscsoportot.

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

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza az **Erőforráscsoportok lehetőséget.**

2. Az erőforráscsoport listában válassza ki az erőforráscsoport nevét.

3. Az **erőforráscsoport Áttekintés** lapján válassza az **Erőforráscsoport törlése lehetőséget.**

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

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

Az ARM-sablonok létrehozásának folyamatát részletesen ismertető oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
