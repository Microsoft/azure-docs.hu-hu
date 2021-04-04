---
title: Storage-Blobok törlése a hálózati biztonsági csoport folyamatábrái az Azure Network Watcherban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet törölni a hálózati biztonsági csoport folyamatábrájának tárolási blobját, amely kívül esik az Azure Network Watcher adatmegőrzési házirend időszakán.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 1b73b8fa14271619d07fca63b01e4197f4f7c8cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017850"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Hálózati biztonsági csoport folyamatábrájának tárolási blobjának törlése Network Watcher

Ha valaha is manuálisan kell törölnie a flow-naplókat a Storage-fiókból, az alábbi PowerShell-szkriptet használhatja.
Ez a parancsfájl csak olyan tárolási blobokat töröl, amelyek régebbiek, mint a felhasználó által megadott meglévő adatmegőrzési szabály.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>PowerShell-szkript futtatása a NSG-naplók törléséhez
 
Másolja és mentse a következő parancsfájlt egy helyre, például az aktuális munkakönyvtárra. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. Szükség szerint adja meg a következő paramétereket a parancsfájlban:
   - **SubscriptionId** [kötelező]: az előfizetés azonosítója, amelyből törölni szeretné a NSG flow log-blobokat.
   - **Hely** [kötelező]: annak a NSG a régiójának a _helye_ , amelyhez törölni szeretné a NSG flow log blobokat. Ezeket az információkat a Azure Portal vagy a [githubon](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)tekintheti meg.
   - **Erősítse meg** a [nem kötelező]: adja meg a megerősítés jelölőjét, ha manuálisan szeretné megerősíteni az egyes tárolási Blobok törlését.

1. Futtassa a mentett parancsfájlt az alábbi példában látható módon, ahol a parancsfájlt **Delete-NsgFlowLogsBlobs.ps1ként** mentette:
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>Következő lépések
- Az ügyfelek [Azure Logic apps](../logic-apps/logic-apps-overview.md) vagy [Azure Automation](https://azure.microsoft.com/services/automation/) használatával automatizálják a szkript futtatását
- A NSG-naplózással kapcsolatos további tudnivalókért tekintse meg [a hálózati biztonsági csoportok (NSG) naplóinak Azure monitor](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

