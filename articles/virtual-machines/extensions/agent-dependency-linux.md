---
title: A Linux rendszerhez készült virtuálisgép-bővítmény Azure Monitor
description: Telepítse a Azure Monitor függőségi ügynököt Linux rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 03/29/2019
ms.openlocfilehash: d28f0a34f47942bba8776a0acd0bfe3aaf25df12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566302"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>A Linux rendszerhez készült virtuálisgép-bővítmény Azure Monitor

A Azure Monitor for VMs Map szolgáltatás a Microsoft függőségi ügynöktől kapja meg az adatait. Az Azure-beli virtuális gép függőségi ügynökének a Linux rendszerhez készült virtuálisgép-bővítményét a Microsoft közzétette és támogatja. A bővítmény telepíti a függőségi ügynököt az Azure Virtual Machines szolgáltatásban. Ez a dokumentum részletesen ismerteti az Azure-beli virtuális gép függőségi ügynökének támogatott platformokat, konfigurációkat és üzembe helyezési lehetőségeit Linuxra.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Linux rendszerhez készült Azure-beli virtuálisgép-függőségi ügynök bővítmény a Azure Monitor for VMs telepítési cikk [támogatott operációs rendszerek](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) szakaszában felsorolt támogatott operációs rendszereken is futtatható.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az Azure-beli virtuális gép függőségi ügynökének sémáját mutatja be egy Azure Linux rendszerű virtuális gépen. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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
| típus | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal helyezhetők üzembe. Az Azure Resource Manager sablon előző szakaszában részletes JSON-sémát használva futtathatja az Azure-beli virtuális gép függőségi ügynökének bővítményét egy Azure Resource Manager-sablon központi telepítése során.

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
        "type": "DependencyAgentLinux",
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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti a függőségi ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portal és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva:

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási incidenst is betölthet. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
