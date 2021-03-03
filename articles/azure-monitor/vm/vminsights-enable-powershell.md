---
title: VIRTUÁLIS gépekkel való bepillantások engedélyezése a PowerShell használatával
description: Útmutatás az Azure-beli virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz Azure PowerShell használatával történő engedélyezéséhez.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 47ac71797684f82dfd94acff2d18bca11b2f50d1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717077"
---
# <a name="enable-vm-insights-using-powershell"></a>VIRTUÁLIS gépekkel való bepillantások engedélyezése a PowerShell használatával
Ez a cikk bemutatja, hogyan engedélyezheti a VM-információkat az Azure Virtual Machines szolgáltatásban a PowerShell használatával. Ez az eljárás a következő módon használható:

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre és konfiguráljon egy log Analytics munkaterületet](./vminsights-configure-workspace.md).
- A [támogatott operációs rendszerekkel](./vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport operációs rendszere támogatott legyen. 


## <a name="powershell-script"></a>PowerShell-parancsprogram

Ha több virtuális gépre vagy virtuálisgép-méretezési csoportra szeretné engedélyezni a virtuális gépeket, használja a [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights)PowerShell-parancsfájlt, amely a Azure PowerShell gyűjteményből érhető el. Ez a szkript a következő lépésekből áll:

- Minden virtuális gép és virtuálisgép-méretezési csoport az előfizetésben.
- A *ResourceGroup* által megadott hatókörön belüli erőforráscsoport.
- Egyetlen virtuális gép vagy virtuálisgép-méretezési csoport, amelyet a *név* határoz meg.

A parancsfájl minden egyes virtuális gép vagy virtuálisgép-méretezési csoport esetében ellenőrzi, hogy a Log Analytics ügynök és a függőségi ügynök virtuálisgép-bővítménye már telepítve van-e. Ha mindkét bővítmény telepítve van, a parancsfájl megpróbálja újratelepíteni. Ha mindkét bővítmény nincs telepítve, a parancsfájl telepíti őket.

Győződjön meg arról, hogy a Azure PowerShell-modult használja az az Version 1.0.0 vagy újabb verzióval, és a `Enable-AzureRM` kompatibilitási aliasok engedélyezve vannak. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

A parancsfájl argumentumának részleteiről és a példa használatáról a Futtatás gombra kattintva kérheti le a listát `Get-Help` .

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

Az alábbi példa azt mutatja be, hogyan használhatja a mappában található PowerShell-parancsokat a virtuális gépek bepillantást tevő és a várt kimenet megértéséhez:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Következő lépések

* A felderített alkalmazások függőségeinek megtekintéséhez lásd a virtuálisgép-információk [leképezésének használata](vminsights-maps.md) című témakört. 
* Lásd: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md) a szűk keresztmetszetek, a teljes kihasználtság és a virtuális gép teljesítményének azonosításához.