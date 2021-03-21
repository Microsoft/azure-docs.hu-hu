---
title: A Azure Monitor ügynök telepítése
description: A Azure Monitor-ügynök (AMA) telepítésének lehetőségei az Azure Virtual Machines és az Azure arc-kompatibilis kiszolgálókon.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: b2f91f0036a86151588c8c138dac5421ad54e18e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586420"
---
# <a name="install-the-azure-monitor-agent-preview"></a>A Azure Monitor-ügynök telepítése (előzetes verzió)
Ez a cikk a [Azure monitor ügynöknek](azure-monitor-agent-overview.md) az Azure-beli virtuális gépeken és az Azure arc-kompatibilis kiszolgálókon való telepítésének különböző lehetőségeit ismerteti, valamint az [adatgyűjtési szabályokkal rendelkező társítások](data-collection-rule-azure-monitor-agent.md) létrehozási lehetőségeit, amelyek meghatározzák, hogy az ügynöknek milyen adatokat kell gyűjtenie.

## <a name="prerequisites"></a>Előfeltételek
A Azure Monitor ügynök telepítése előtt a következő előfeltételek szükségesek.

- A [felügyelt rendszeridentitást](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) engedélyezni kell az Azure Virtual Machines szolgáltatásban. Ez az Azure arc-kompatibilis kiszolgálók esetében nem szükséges. A rendszeridentitás automatikusan engedélyezve lesz, ha az ügynököt a [Azure Portal használatával egy adatgyűjtési szabály létrehozásához és hozzárendeléséhez](#install-with-azure-portal)szükséges folyamat részeként telepíti a rendszer.
- A virtuális gép virtuális hálózatán engedélyezni kell a [AzureResourceManager szolgáltatás címkéjét](../../virtual-network/service-tags-overview.md) .

## <a name="virtual-machine-extension-details"></a>Virtuálisgép-bővítmény részletei
A Azure Monitor ügynök Azure-beli virtuálisgép- [bővítményként](../../virtual-machines/extensions/overview.md) van megvalósítva, és a következő táblázatban található adatokat tartalmazza. A virtuálisgép-bővítmények telepítésének bármelyik módszerével telepíthető, beleértve a jelen cikkben ismertetett lépéseket is.

| Tulajdonság | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Típus      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Telepítés Azure Portal
Ha a Azure Portal használatával szeretné telepíteni a Azure Monitor ügynököt, kövesse az [adatgyűjtési szabály létrehozásához](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) szükséges eljárást a Azure Portalban. Ez lehetővé teszi az adatgyűjtési szabály hozzárendelését egy vagy több Azure-beli virtuális géppel vagy Azure arc-kompatibilis kiszolgálóval. Az ügynök minden olyan virtuális gépen telepítve lesz, amely még nem rendelkezik.


## <a name="install-with-resource-manager-template"></a>Telepítés Resource Manager-sablonnal
A Resource Manager-sablonok segítségével telepítheti az Azure Monitor ügynököt az Azure-beli virtuális gépeken és az Azure arc-kompatibilis kiszolgálókon, és létrehozhat egy adatgyűjtési szabályokkal való társítást. A társítás létrehozása előtt létre kell hoznia egy adatgyűjtési szabályt.

Példák az ügynök telepítésére és a társítás létrehozásához a következőkből: 

- [Sablon a Azure Monitor-ügynök telepítéséhez (Azure és Azure arc)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Az adatgyűjtési szabállyal való társítást létrehozó sablon](./resource-manager-data-collection-rules.md)

Telepítse a sablonokat a [Resource Manager-sablonok bármely üzembe helyezési módszerével](../../azure-resource-manager/templates/deploy-powershell.md) , például az alábbi parancsokkal.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[Parancssori felület](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Telepítés a PowerShell-lel
A virtuálisgép-bővítmény hozzáadásához a PowerShell-paranccsal telepítheti az Azure Monitor ügynököt az Azure-beli virtuális gépeken és az Azure arc-kompatibilis kiszolgálókon. 

### <a name="azure-virtual-machines"></a>Azure-beli virtuális gépek
Az alábbi PowerShell-parancsokkal telepítheti az Azure Monitor-ügynököt az Azure Virtual Machines szolgáltatásban.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc-kompatibilis kiszolgálók
A következő PowerShell-parancsokkal telepítheti a Azure Monitor Agent onAzure arc-kompatibilis kiszolgálókat.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
Az Azure-beli virtuális gépeken és az Azure arc-kompatibilis kiszolgálókon telepítheti az Azure Monitor-ügynököt a virtuálisgép-bővítmények hozzáadásához az Azure CLI parancs használatával. 

### <a name="azure-virtual-machines"></a>Azure-beli virtuális gépek
Az alábbi CLI-parancsokkal telepítheti az Azure Monitor-ügynököt az Azure Virtual Machines szolgáltatásban.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc-kompatibilis kiszolgálók
Az alábbi CLI-parancsokkal telepítheti az Azure Monitor Agent onAzure arc-kompatibilis kiszolgálókat.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy adatgyűjtési szabályt](data-collection-rule-azure-monitor-agent.md) , amely adatokat gyűjt az ügynöktől, és elküldi azt a Azure monitornak.
