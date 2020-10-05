---
title: Azure Portal-irányítópult létrehozása a PowerShell-lel
description: Megtudhatja, hogyan hozhat létre irányítópultot a Azure Portal Azure PowerShell használatával.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: 6b7a4f6d4ad7f5e94d19b9d531992f54ff13fec0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87440774"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Rövid útmutató: Azure Portal irányítópult létrehozása a PowerShell-lel

A Azure Portal egyik irányítópultja a Felhőbeli erőforrások célzott és rendezett nézete. Ez a cikk az az. Portal PowerShell-modul használatának folyamatát ismerteti irányítópult létrehozásához.
Az irányítópulton egy virtuális gép (VM) teljesítménye, valamint néhány statikus információ és hivatkozás látható.

## <a name="requirements"></a>Követelmények

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az az **. Portal** PowerShell-modul előzetes verzióban érhető el, és a parancsmag használatával külön kell telepítenie az az PowerShell-modulból `Install-Module` . Miután a PowerShell-modul általánosan elérhetővé válik, az a PowerShell-modul kiadásainak részévé válik, és natív módon elérhető lesz a Azure Cloud Shellon belülről.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Adott Azure-előfizetés kiválasztása

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Változók meghatározása

Az adatok többször is használhatók. Hozzon létre változókat az információk tárolásához.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy erőforráscsoportot a változóban `$resourceGroupName` megadott régióban található változó alapján `$location` .

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A rövid útmutató következő részében létrehozott irányítópultnak egy meglévő virtuális gépet kell megadnia. Hozzon létre egy virtuális gépet a következő lépésekkel.

Tárolja a virtuális gép bejelentkezési hitelesítő adatait egy változóban. A jelszónak összetettnak kell lennie. Ez egy új Felhasználónév és jelszó; nem például az Azure-ba való bejelentkezéshez használt fiók. További információ: a [felhasználónévre vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) és a [jelszóra vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Hozza létre a virtuális gépet.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

A virtuális gép üzembe helyezése most elindul, és általában néhány percet vesz igénybe. Az üzembe helyezés befejezése után lépjen tovább a következő szakaszra.

## <a name="download-the-dashboard-template"></a>Az irányítópult-sablon letöltése

Mivel az Azure-irányítópultok erőforrások, JSON-ként is képviseltetik magukat. A következő kód egy minta irányítópult JSON-ábrázolását tölti le. További információ: [Az Azure-irányítópultok szerkezete](/azure/azure-portal/azure-portal-dashboards-structure).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>A sablon testreszabása

Szabja testre a letöltött sablont a következő kód futtatásával.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

További információ: a [Microsoft Portal irányítópultok sablonjának referenciája](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Az irányítópult-sablon üzembe helyezése

Az az `New-AzPortalDashboard` . Portal modulhoz tartozó parancsmag használatával közvetlenül a powershellből helyezheti üzembe a sablont.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

Győződjön meg arról, hogy az irányítópult sikeresen létrejött.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Ellenőrizze, hogy látható-e a virtuális gép adatait a Azure Portal belül.

1. Az Azure Portalon válassza az **Irányítópult** lehetőséget.

   ![Navigálás az irányítópulton Azure Portal](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Az irányítópult lapon válassza az **egyszerű VM-irányítópult**lehetőséget.

   ![Navigáljon az egyszerű VM-irányítópultra](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Tekintse át az irányítópultot. Láthatja, hogy a tartalom néhány statikus, de vannak olyan diagramok is, amelyek a virtuális gép teljesítményét mutatják.

   ![Egyszerű VM-irányítópult áttekintése](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A virtuális gép és a hozzá tartozó irányítópult eltávolításához törölje az azokat tartalmazó erőforráscsoportot.

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Az az. Portal PowerShell-modulban található parancsmagokkal kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: portál irányítópult-parancsmagok](https://docs.microsoft.com/powershell/module/Az.Portal/)
