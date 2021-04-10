---
title: A Windows-függőség virtuálisgép-bővítményének Azure Monitor
description: Telepítse a Azure Monitor függőségi ügynököt a Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: windows
ms.date: 03/29/2019
ms.openlocfilehash: 429cc01f466c55283985729c3395bb2137e38fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566301"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>A Windows-függőség virtuálisgép-bővítményének Azure Monitor

A Azure Monitor for VMs Map szolgáltatás a Microsoft függőségi ügynöktől kapja meg az adatait. Az Azure-beli virtuális gép függőségi ügynökének a Windows rendszerhez készült virtuálisgép-bővítményét a Microsoft közzétette és támogatja. A bővítmény telepíti a függőségi ügynököt az Azure Virtual Machines szolgáltatásban. Ez a dokumentum részletesen ismerteti a Windows rendszerhez készült Azure-beli virtuális gép függőségi ügynökének támogatott platformokat, konfigurációkat és telepítési lehetőségeit.

## <a name="operating-system"></a>Operációs rendszer

A Windows rendszerhez készült Azure-beli virtuálisgép-függőségi ügynök bővítmény a Azure Monitor for VMs telepítési cikk [támogatott operációs rendszerek](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) szakaszában felsorolt támogatott operációs rendszereken is futtatható.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az Azure-beli virtuális gép függőségi ügynökének sémáját mutatja egy Azure Windows rendszerű virtuális gépen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| közzétevő | Microsoft. Azure. monitoring. DependencyAgent |
| típus | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítményeket Azure Resource Manager-sablonokkal is üzembe helyezheti. Az Azure Resource Manager sablon előző szakaszában részletes JSON-sémát használva futtathatja az Azure-beli virtuális gép függőségi ügynökének bővítményét egy Azure Resource Manager-sablon központi telepítése során.

A virtuálisgép-bővítmények JSON-je beágyazható a virtuális gép erőforrásaiba. Azt is megteheti, hogy egy Resource Manager JSON-sablon gyökerére vagy legfelső szintjére helyezi. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md).

Az alábbi példa azt feltételezi, hogy a függőségi ügynök bővítménye a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a rendszer a JSON-t a `"resources": []` virtuális gép objektumában helyezi el.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Ha a bővítmény JSON-fájlját a sablon gyökerében helyezi el, az erőforrás neve a szülő virtuális gépre mutató hivatkozást tartalmaz. A típus a beágyazott konfigurációt tükrözi.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>A PowerShell telepítése

A `Set-AzVMExtension` parancs használatával telepítheti a függőségi ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és a privát konfigurációkat egy PowerShell-kivonatoló táblában kell tárolni.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portal és az Azure PowerShell modul használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot a Azure PowerShell modul használatával:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Vagy egy Azure-támogatási incidenst is betölthet. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
