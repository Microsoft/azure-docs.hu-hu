---
title: Gépek elindítása a Azure DevTest Labs Automation runbookok használatával
description: Megtudhatja, hogyan indíthatja el a virtuális gépeket Azure DevTest Labs tesztkörnyezetben Azure Automation runbookok használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282262"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Virtuális gépek indítása tesztkörnyezetben Azure Automation runbookok használatával
A DevTest Labs automatikus [indítási](devtest-lab-set-lab-policy.md#set-autostart) funkciója lehetővé teszi, hogy a virtuális gépek egy adott időpontban automatikusan induljon el. Ez a funkció azonban nem támogatja, hogy a gépek meghatározott sorrendben induljon el. Több esetben is hasznos lehet az ilyen típusú automatizálás.  Az egyik esetben, ha a laboron belül egy Jumpbox virtuális gépet először kell elindítani, a többi virtuális gép előtt, mivel a Jumpbox a többi virtuális géphez való hozzáférési pontként használják.  Ez a cikk bemutatja, hogyan állíthat be egy Azure Automation fiókot egy olyan PowerShell-runbook, amely egy parancsfájlt futtat. A szkript címkéket használ a virtuális gépeken a laborban, így lehetővé teszi az indítási sorrend szabályozását anélkül, hogy módosítani kellene a parancsfájlt.

## <a name="setup"></a>Beállítás
Ebben a példában a laborban lévő virtuális gépeknek meg kell adni a címkét a megfelelő értékkel (0, 1, 2 stb.) a **StartupOrder** . Jelölje ki azokat a gépeket, amelyek nem szükségesek az-1-es indításhoz.

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása
Hozzon létre egy Azure Automation fiókot a [cikk](../automation/automation-create-standalone-account.md)utasításait követve. A fiók létrehozásakor válassza a **futtató fiókok** lehetőséget. Az Automation-fiók létrehozása után nyissa meg a **modulok** lapot, és válassza az **Azure-modulok frissítése** elemet a menüsávon. Az alapértelmezett modulok több régebbi verziójúak, a frissítés nélkül pedig előfordulhat, hogy a parancsfájl nem működik.

## <a name="add-a-runbook"></a>Runbook hozzáadása
Most, ha runbook szeretne hozzáadni az Automation-fiókhoz, válassza a bal oldali menü **runbookok** elemét. Válassza a **Runbook hozzáadása** lehetőséget a menüben, majd kövesse az utasításokat a [PowerShell-runbook létrehozásához](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-parancsprogram
A következő szkript az előfizetés nevét, a labor nevét adja meg paraméterként. A szkript folyamata a laborban lévő összes virtuális gép lekérése, majd a címke adatainak elemzése a virtuális gépek nevének és indítási sorrendjének a létrehozásához. A szkript végigvezeti a virtuális gépeken, és elindítja a virtuális gépeket. Ha egy adott sorszám több virtuális gépet használ, a rendszer aszinkron módon indítja el a PowerShell-feladatokat. Azoknak a virtuális gépeknek, amelyek nem rendelkeznek címkével, állítsa be az indítási értéket az utolsó (10) értékre, alapértelmezés szerint a rendszer az utolsót fogja elindítani.  Ha a labor nem szeretné, hogy a virtuális gép automatikusan induljon el, állítsa a címke értéket 11-re, és figyelmen kívül hagyja a rendszer.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Ütemterv létrehozása
Ahhoz, hogy ezt a szkriptet naponta végrehajtsa, [hozzon létre egy ütemtervet](../automation/shared-resources/schedules.md#create-a-schedule) az Automation-fiókban. Az ütemterv létrehozása után [csatolja azt a runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

Nagyméretű helyzetekben, ahol több előfizetés is található több laborral, a paramétereket a különböző Labs-fájlokban tárolhatja, és az egyes paraméterek helyett át kell adni a fájlt a parancsfájlba. A parancsfájlt módosítani kell, de az alapszintű végrehajtás ugyanaz lenne. Habár ez a példa a Azure Automation használja a PowerShell-parancsfájl végrehajtásához, más lehetőségek is vannak, például a feladatok felépítése egy Build/Release folyamatba.

## <a name="next-steps"></a>Következő lépések
A következő cikkben további információt talál a Azure Automationról: [Bevezetés a Azure Automationba](../automation/automation-intro.md).
