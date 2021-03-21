---
title: Azure PowerShell – a kettős titkosítás engedélyezése a REST által felügyelt lemezeken
description: A felügyelt lemez adataihoz Azure PowerShell használatával engedélyezze a kettős titkosítást.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cfef3925152c0b94a59c662443cf202724dadc0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549549"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>A Azure PowerShell modul használata a felügyelt lemezeken a kettős titkosítás engedélyezéséhez

Azure Disk Storage támogatja a kettős titkosítást a felügyelt lemezeken. A kettős titkosítással kapcsolatos fogalmakról, valamint a felügyelt lemezes titkosítási típusokról további információt a lemezes titkosításról szóló cikkben, a [Rest-titkosítást](../disk-encryption.md#double-encryption-at-rest) ismertető szakaszban talál.

## <a name="prerequisites"></a>Előfeltételek

Telepítse a legújabb [Azure PowerShell verziót](/powershell/azure/install-az-ps), és jelentkezzen be egy Azure-fiókba a [AzAccount](/powershell/module/az.accounts/connect-azaccount)használatával.

## <a name="getting-started"></a>Első lépések

1. Hozzon létre egy Azure Key Vault és egy titkosítási kulcs egy példányát.

    A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Hozzon létre egy DiskEncryptionSet a encryptionType set EncryptionAtRestWithPlatformAndCustomerKeys néven. Használja az **2020-05-01** -es API-verziót a Azure Resource Manager-(ARM-) sablonban. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. Adja meg a DiskEncryptionSet-erőforrás hozzáférését a kulcstartóhoz.

    > [!NOTE]
    > Eltarthat néhány percig, amíg az Azure létrehozza a DiskEncryptionSet identitását a Azure Active Directoryban. Ha a következő parancs futtatásakor a "nem találja a Active Directory objektumot" hibaüzenet jelenik meg, várjon néhány percet, és próbálkozzon újra.

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta és konfigurálta ezeket az erőforrásokat, a segítségével biztonságossá teheti a felügyelt lemezeket. Az alábbi hivatkozások olyan parancsfájlokat tartalmaznak, amelyek mindegyike megfelelő forgatókönyvekkel rendelkezik, amelyek segítségével biztonságossá teheti a felügyelt lemezeket.

- [Azure PowerShell – ügyfél által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással felügyelt lemezekkel](disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
