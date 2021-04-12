---
title: Azure Network Watcher Agent virtuálisgép-bővítmény Linux rendszerhez
description: Telepítse a Network Watcher-ügynököt a Linux rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/14/2017
ms.openlocfilehash: bc252e560df782625d795b30c6688a34f5c2bd79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563591"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>A Network Watcher ügynök Linux rendszerhez készült virtuális gépi bővítménye

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](../../network-watcher/index.yml) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. Az Network Watcher Agent virtuálisgép-bővítmény az Azure-beli virtuális gépek egyes Network Watcher funkcióinak, például az igény szerinti hálózati forgalom rögzítésének és más speciális funkcióknak a követelménye.

Ez a cikk a Linux rendszerhez készült Network Watcher Agent virtuálisgép-bővítmény támogatott platformait és telepítési lehetőségeit ismerteti. Az ügynök telepítése nem zavarja vagy nem igényli a virtuális gép újraindítását. A bővítményt a telepített virtuális gépekre is telepítheti. Ha a virtuális gépet egy Azure-szolgáltatás telepíti, a szolgáltatás dokumentációjában ellenőrizze, hogy engedélyezi-e a bővítmények telepítését a virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Network Watcher ügynök bővítmény a következő Linux-disztribúciók esetében konfigurálható:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 és 8 |
| Red Hat | 6 és 7 |
| Oracle Linux | 6.8 + és 7 |
| SUSE Linux Enterprise Server | 11 és 12 |
| OpenSUSE-ugrás | 42.3 + |
| CentOS | 6.5 + és 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher-ügynök néhány funkciója megköveteli, hogy a virtuális gép csatlakoztatva legyen az internethez. A kimenő kapcsolatok létrehozása nélkül előfordulhat, hogy a Network Watcher ügynök egyes funkciói meghibásodnak vagy elérhetetlenné válnak. Az ügynököt igénylő Network Watcher funkcióival kapcsolatos további információkért tekintse meg a[Network Watcher dokumentációját](../../network-watcher/index.yml).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher ügynök bővítmény sémáját jeleníti meg. A bővítmény nem igényli vagy nem támogatja a felhasználó által megadott beállításokat. A bővítmény az alapértelmezett konfigurációra támaszkodik.

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
        "type": "NetworkWatcherAgentLinux",
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
| típus | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítményeket Azure Resource Manager sablonnal is üzembe helyezheti. A Network Watcher Agent bővítmény üzembe helyezéséhez használja a sablon korábbi JSON-sémáját.

## <a name="azure-classic-cli-deployment"></a>Klasszikus Azure CLI üzembe helyezés

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az alábbi példa telepíti a Network Watcher Agent virtuálisgép-bővítményt a klasszikus üzemi modellel telepített meglévő virtuális gépre:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az alábbi példa telepíti a Network Watcher Agent virtuálisgép-bővítményt a Resource Managerrel üzembe helyezett meglévő virtuális gépre:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos információkat a Azure Portal vagy az Azure CLI használatával kérheti le.

Az alábbi példa a Resource Managerrel üzembe helyezett virtuális gépek NetworkWatcherAgentLinux-bővítményének telepítési állapotát mutatja be az Azure CLI használatával:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg az [Network Watcher dokumentációját](../../network-watcher/index.yml), vagy lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
