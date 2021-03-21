---
title: Az Azure Network Watcher ügynök Windows rendszerhez készült virtuális gépi bővítménye
description: Telepítse a Network Watcher Agent ügynököt a Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 02/14/2017
ms.openlocfilehash: d336a39714712e5436086e22ad24fc942a7d850a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563540"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Network Watcher Agent virtuálisgép-bővítmény a Windowshoz

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher ügynök virtuálisgép-bővítményének követelménye a hálózati forgalom igény szerinti rögzítése, valamint az Azure-beli virtuális gépeken futó egyéb speciális funkciók.


Ez a dokumentum részletesen ismerteti a Windows rendszerhez készült Network Watcher ügynök virtuálisgép-bővítményének támogatott platformokat és telepítési lehetőségeit. Az ügynök telepítése nem zavarja vagy nem igényli a virtuális gép újraindítását. A bővítményt a telepített virtuális gépekre is telepítheti. Ha a virtuális gépet egy Azure-szolgáltatás telepíti, a szolgáltatás dokumentációjában ellenőrizze, hogy engedélyezi-e a bővítmények telepítését a virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Windows rendszerhez készült Network Watcher ügynök bővítmény a Windows Server 2008 R2, 2012, 2012 R2, 2016 és 2019 kiadásokon futtatható. A nano Server jelenleg nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher ügynök egyes funkciói megkövetelik, hogy a célként megadott virtuális gép csatlakozni lehessen az internethez. A kimenő kapcsolatok létrehozásának lehetősége nélkül a Network Watcher ügynök nem fogja tudni feltölteni a csomagokat a Storage-fiókjába. További részletekért tekintse meg a [Network Watcher dokumentációját](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher ügynök bővítmény sémáját jeleníti meg. A bővítmény nem igényli, és nem támogatja a felhasználó által megadott beállításokat, és az alapértelmezett konfigurációra támaszkodik.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| közzétevő | Microsoft. Azure. NetworkWatcher |
| típus | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal helyezhetők üzembe. A Azure Resource Manager sablon előző szakaszában részletes JSON-sémát használva futtathatja az Network Watcher Agent bővítményt egy Azure Resource Manager sablon központi telepítése során.

## <a name="powershell-deployment"></a>A PowerShell telepítése

A `Set-AzVMExtension` parancs használatával telepítse a Network Watcher Agent virtuálisgép-bővítményt egy meglévő virtuális gépre:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

A bővítmények telepítésének állapotáról a Azure Portal és a PowerShellből kérhet le információkat. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell modul használatával:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg a Network Watcher felhasználói útmutató dokumentációját, vagy lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
