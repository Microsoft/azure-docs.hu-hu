---
title: Azure Diagnostics bővítmény a Windowshoz | Microsoft Docs
description: Azure-beli Windows rendszerű virtuális gépek figyelése az Azure Diagnostics bővítmény használatával
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 4230e2aac8d386c759a403b9008029d68049569c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749394"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure Diagnostics bővítmény a Windows rendszerű virtuális gépekhez

## <a name="overview"></a>Áttekintés

A Azure Diagnostics virtuálisgép-bővítmény lehetővé teszi a figyelési adatok, például a teljesítményszámlálók és az eseménynaplók összegyűjtését a Windows rendszerű virtuális gépről. Részletesen megadhatja, hogy milyen adatokat szeretne összegyűjteni, illetve hogy hová szeretné tenni az adatokat, például egy Azure Storage-fiók vagy egy Azure Event hub számára. Ezen adatok használatával diagramokat építhet ki a Azure Portal, vagy metrikai riasztásokat hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Azure Diagnostics bővítmény a Windows 10-ügyfél, a Windows Server 2008 R2, a 2012, az 2012 R2 és az 2016 használatával futtatható.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Azure Diagnostics bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

[A jelen dokumentum a Azure Diagnostics-bővítmény sémáját és a tulajdonság értékeit írja le.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-séma Azure Resource Manager sablonban használható a Azure Diagnostics bővítmény futtatásához Azure Resource Manager sablon központi telepítésekor. Lásd: [figyelés és diagnosztika használata Windows rendszerű virtuális gépekkel és Azure Resource Manager-sablonokkal](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti az Azure Diagnostics-bővítményt egy meglévő virtuális gépre. Cserélje le a védett beállítások és beállítások tulajdonságokat érvényes JSON-ra a fenti kiterjesztési sémából. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMDiagnosticsExtension` parancs használatával adhatja hozzá a Azure Diagnostics bővítményt egy meglévő virtuális géphez. Lásd még: a [PowerShell használata a Azure Diagnostics Windows rendszerű virtuális gépeken való engedélyezéséhez](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Tekintse meg ezt a cikket](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) a Azure Diagnostics bővítmény részletes hibaelhárítási útmutatójában.

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
* [További információ a Azure Diagnostics bővítménnyel kapcsolatban](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [A bővítmény sémájának és verzióinak áttekintése](../../azure-monitor/platform/diagnostics-extension-schema.md)
