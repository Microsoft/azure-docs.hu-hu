---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ebeca5ec1e3a478fdf1a62e2478cad9754c6ccd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99808543"
---
1. Győződjön meg arról, hogy telepítette a legújabb [Azure PowerShell verziót](/powershell/azure/install-az-ps), és be van jelentkezve egy Azure-fiókba a alkalmazásban Connect-AzAccount

1. Hozzon létre egy Azure Key Vault és egy titkosítási kulcs egy példányát.

    A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Hozzon létre egy DiskEncryptionSet egy példányát. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Adja meg a DiskEncryptionSet-erőforrás hozzáférését a kulcstartóhoz.

        > [!NOTE]
        > Eltarthat néhány percig, amíg az Azure létrehozza a DiskEncryptionSet identitását a Azure Active Directoryban. Ha a következő parancs futtatásakor a "nem találja a Active Directory objektumot" hibaüzenet jelenik meg, várjon néhány percet, és próbálkozzon újra.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```
