---
title: Sap Azure Monitor megoldások üzembe helyezése Azure PowerShell
description: Sap Azure Monitor megoldások üzembe helyezése Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.date: 09/08/2020
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0af2611bada7a9aad206ce7463ef72ec930c89a2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538689"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Gyorsútmutató: az Azure Monitor for SAP Solutions üzembe helyezése az Azure PowerShell-lel

Ez a cikk azt ismerteti, hogyan hozhat létre Azure Monitor for SAP Solutions az [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell-modullal.

> [!CAUTION]
> Azure Monitor for SAP Solutions jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés. Az előzetes verzió használata NEM javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az Az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. További információ az Az PowerShell-modul telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps). Ha úgy dönt, hogy a Cloud Shell használja, további információt [a](../../../cloud-shell/overview.md) Azure Cloud Shell áttekintésében talál.

> [!IMPORTANT]
> Bár az **Az.HanaOnAzure** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Amint ez a PowerShell-modul általánosan elérhetővé válik, a későbbi Az PowerShell-modul kiadásának része lesz, és natív módon elérhető a Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat ki kell számlázni. Válasszon ki egy adott előfizetést a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../../../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

Az alábbi példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-monitor

SAP-monitor létrehozásához használja a [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) parancsmagot. Az alábbi példa létrehoz egy SAP-figyelőt a megadott előfizetéshez, erőforráscsoporthoz és erőforrásnévhez.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Egy SAP-figyelő tulajdonságainak lekérése a [Get-AzSapMonitor parancsmaggal](/powershell/module/az.hanaonazure/get-azsapmonitor) olvasható be. Az alábbi példa egy SAP-figyelő tulajdonságait lekérte a megadott előfizetéshez, erőforráscsoporthoz és erőforrásnévhez.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Szolgáltatópéldány

Szolgáltatópéldány létrehozásához használja a [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) parancsmagot. Az alábbi példa létrehoz egy szolgáltatópéldányt a megadott előfizetéshez, erőforráscsoporthoz és erőforrásnévhez.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

A szolgáltatói példány tulajdonságainak lekéréshez használja a [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) parancsmagot. Az alábbi példa lekérte a szolgáltatópéldány tulajdonságait a megadott előfizetéshez, erőforráscsoporthoz, SapMonitor-névhez és erőforrásnévhez.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a cikkben létrehozott erőforrásokra nincs szükség, az alábbi példák futtatásával törölheti őket.

### <a name="delete-the-provider-instance"></a>A szolgáltatópéldány törlése

Szolgáltatópéldány eltávolításához használja a [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) parancsmagot. Az alábbi példa egy szolgáltatópéldányt töröl a megadott előfizetéshez, erőforráscsoporthoz, SapMonitor-névhez és erőforrásnévhez.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Az SAP-figyelő törlése

Sap-figyelő eltávolításához használja a [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) parancsmagot. Az alábbi példa egy SAP-figyelőt töröl a megadott előfizetéshez, erőforráscsoporthoz és figyelőnévhez.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> Az alábbi példa törli a megadott erőforráscsoportot és a benne lévő összes erőforrást.
> Ha a cikk hatókörében kívüli erőforrások a megadott erőforráscsoportban vannak, akkor azok is törlődnek.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Monitor for SAP Solutions.](azure-monitor-overview.md)
