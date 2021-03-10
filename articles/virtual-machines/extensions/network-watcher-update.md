---
title: A Network Watcher-bővítmény frissítése a legújabb verzióra
description: Ismerje meg, hogyan frissítheti az Azure Network Watcher bővítményt a legújabb verzióra.
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 42efe2927b4d711f7fa66a96ebd25f1a62bf654a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563608"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>A Network Watcher-bővítmény frissítése a legújabb verzióra

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely az Azure-hálózatokat figyeli. A Network Watcher ügynök virtuálisgép-bővítménye követelmény a hálózati forgalom igény szerinti rögzítése, valamint az Azure-beli virtuális gépeken futó egyéb speciális funkciók használata. A Network Watcher bővítményt olyan szolgáltatások használják, mint a kapcsolat figyelője, a kapcsolat figyelője (előzetes verzió), a kapcsolati hibák és a csomagok rögzítése.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy a Network Watcher bővítmény telepítve van a virtuális gépen.

## <a name="latest-version"></a>Legújabb verzió

Jelenleg a Network Watcher bővítmény legújabb verziója érhető el `1.4.1693.1` .

## <a name="update-your-extension-using-a-powershell-script"></a>Bővítmény frissítése PowerShell-parancsfájl használatával
Olyan nagyméretű központi telepítéseket használó ügyfelek, akiknek egyszerre több virtuális gépet kell frissíteniük. Ha manuálisan szeretné frissíteni a virtuális gépeket, tekintse meg a következő szakaszt. 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1654.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>A bővítmény manuális frissítése

A bővítmény frissítéséhez ismernie kell a bővítmény verzióját.

### <a name="check-your-extension-version"></a>A bővítmény verziójának keresése

A bővítmény verzióját a Azure Portal, az Azure CLI vagy a PowerShell használatával tekintheti meg.

#### <a name="usetheazureportal"></a>A Azure Portal használata

1. Lépjen a virtuális gép **Extensions (bővítmények** ) paneljére a Azure Portal.
1. A részletek ablaktábla megjelenítéséhez válassza a **AzureNetworkWatcher** bővítményt.
1. Keresse meg a verziószámot a **verzió** mezőben.  

#### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Futtassa az alábbi parancsot egy Azure CLI-parancssorból:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Keresse meg a **"AzureNetworkWatcherExtension"** elemet a kimenetben, és azonosítsa a verziószámot a kimenet *"TypeHandlerVersion"* mezőjében.  Megjegyzés: a bővítményre vonatkozó információk többször is megjelennek a JSON-kimenetben. Tekintse meg a "bővítmények" blokkot, és látnia kell a bővítmény teljes verziószámát. 

Az alábbihoz hasonlónak kell megjelennie: ![ Azure CLI képernyőkép](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>A PowerShell használata

Futtassa a következő parancsokat egy PowerShell-parancssorból:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Keresse meg az Azure Network Watcher-bővítményt a kimenetben, és azonosítsa a verziószámot a kimenet *"TypeHandlerVersion"* mezőjében.   

A következőhöz hasonlónak kell megjelennie: ![ PowerShell képernyőkép](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Bővítmény frissítése

Ha a verzió a fent említett legújabb verzió alatt van, frissítse a bővítményt az alábbi lehetőségek bármelyikével.

#### <a name="option-1-use-powershell"></a>1. lehetőség: a PowerShell használata

Futtassa a következő parancsokat:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Ha ez nem működik. Távolítsa el, majd telepítse újra a bővítményt az alábbi lépések segítségével. Ekkor a rendszer automatikusan hozzáadja a legújabb verziót.

A bővítmény eltávolítása 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

A bővítmény újbóli telepítése

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>2. lehetőség: az Azure CLI használata

A frissítés kényszerítése.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Ha ez nem működik, távolítsa el, majd telepítse újra a bővítményt, és kövesse az alábbi lépéseket a legújabb verzió automatikus hozzáadásához.

Távolítsa el a bővítményt.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Telepítse újra a bővítményt.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>3. lehetőség: a virtuális gépek újraindítása

Ha az automatikus frissítés értéke TRUE (igaz) értékre van állítva a Network Watcher-bővítmény esetében, indítsa újra a virtuális gép telepítését a legújabb bővítményre.

## <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg a [Linux](./network-watcher-linux.md) vagy [Windows rendszerhez](./network-watcher-windows.md)készült Network Watcher-bővítmény dokumentációját. Az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/)is kapcsolatba léphet az Azure-szakértőkkel. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
