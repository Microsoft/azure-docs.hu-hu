---
title: PowerShell-parancsfájl – tároló létrehozása a Storage-fiókhoz
description: Megtudhatja, hogyan használhatja Azure PowerShell parancsfájlt annak a Recovery Services-tárolónak a megkereséséhez, ahol a Storage-fiók regisztrálva van.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075698"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>PowerShell-parancsfájl azon Recovery Services-tároló megtalálásához, ahol a Storage-fiók regisztrálva van

Ez a szkript segít megkeresni azt a Recovery Services tárolót, ahol a Storage-fiók regisztrálva van.

## <a name="sample-script"></a>Példaszkript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>A szkript végrehajtása

1. Mentse a fenti szkriptet a gépen a választott névvel. Ebben a példában a *FindRegisteredStorageAccount.ps1ként* mentettük.
2. Futtassa a szkriptet a következő paraméterek megadásával:

    * **-ResourceGroupName** – a Storage-fiók erőforráscsoport
    * **-StorageAccountName** -Storage-fiók neve
    * **-SubscriptionID** – annak az előfizetésnek az azonosítója, amelyben a Storage-fiók megtalálható.

A következő példa megkísérli megkeresni azt a Recovery Services tárolót, ahol a *afsaccount* Storage-fiók regisztrálva van:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Kimenet

A kimenet megjeleníti annak a Recovery Services-tárolónak a teljes elérési útját, ahol a Storage-fiók regisztrálva van. Itt látható egy mintakimenet:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [készíthet biztonsági mentést az Azure-fájlmegosztás Azure Portal](../backup-afs.md)
