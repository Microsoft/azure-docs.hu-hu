---
title: Key Vault kulcs visszaállítása a titkosított virtuális gép titkos &
description: Megtudhatja, hogyan állíthatja vissza Azure Backup Key Vault kulcsát és titkos kódját a PowerShell használatával
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 456ce18f253ffa02cd6b13826a7839f18beecba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88827086"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>A titkosított virtuális gépekhez tartozó Key Vault-kulcs és titkos kulcs visszaállítása az Azure Backup használatával

Ez a cikk az Azure-beli virtuális gépek biztonsági mentésének használatáról nyújt útmutatást a titkosított Azure-beli virtuális gépek visszaállításához, ha a kulcs és a titkos kód nem létezik a Key vaultban. Ezeket a lépéseket akkor is használhatja, ha a kulcs (kulcs titkosítási kulcsa) és a titkos kulcs (BitLocker-titkosítási kulcs) külön másolatát szeretné fenntartani a visszaállított virtuális géphez.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* **Titkosított virtuális gépek biztonsági mentése** – a titkosított Azure-beli virtuális gépek biztonsági mentése a Azure Backup használatával történt. Az Azure [virtuális gépek biztonsági mentésének és visszaállításának kezelése a PowerShell használatával](backup-azure-vms-automation.md) című cikkből megtudhatja, hogyan készíthet biztonsági mentést a titkosított Azure-beli virtuális gépekről.
* **Azure Key Vault konfigurálása** – győződjön meg arról, hogy az a kulcstartó, amelybe vissza kell állítani a kulcsokat és a titkos kulcsokat, már jelen van. A Key Vault felügyeletével kapcsolatos részletekért tekintse meg a [Azure Key Vault első lépései](../key-vault/general/overview.md) című cikket.
* **Lemez visszaállítása** – ellenőrizze, hogy aktiválta-e a visszaállítási feladatot a titkosított virtuális gép lemezeit [PowerShell-lépések](backup-azure-vms-automation.md#restore-an-azure-vm)használatával. Ennek az az oka, hogy ez a művelet a titkosított virtuális gép visszaállításához szükséges kulcsokat és titkos kódokat tartalmazó JSON-fájlt hoz létre a Storage-fiókban.

## <a name="get-key-and-secret-from-azure-backup"></a>Kulcs és titok beolvasása Azure Backup

> [!NOTE]
> Miután visszaállította a lemezt a titkosított virtuális géphez, ügyeljen arra, hogy:
>
> * a $details a lemez-visszaállítási feladatok részleteivel van feltöltve, ahogy azt a [lemezek visszaállítása szakasz PowerShell-lépései részben](backup-azure-vms-automation.md#restore-an-azure-vm) említettük.
> * A virtuális gépet csak a **kulcs és a titkos kulcs visszaállítása után** lehet helyreállított lemezekről létrehozni.

A visszaállított lemez tulajdonságainak lekérdezése a feladatok részleteihez.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Állítsa be az Azure Storage-környezetet, és állítsa vissza a titkosított virtuális gép kulcs-és titkos adatait tartalmazó JSON konfigurációs fájlt.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Kulcs visszaállítása

Miután a rendszer létrehozta a JSON-fájlt a fent említett cél elérési úton, hozza létre a kulcs blob-fájlját a JSON-ből, és táplálja azt a Key parancsmag visszaállításához, hogy a kulcs (KEK) vissza legyen helyezve a kulcstartóba.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Titkos kód visszaállítása

A fent generált JSON-fájllal titkos nevet és értéket kaphat, és megadhatja a titkos parancsmagot, hogy a titkos kulcs (BEK) vissza legyen helyezve a kulcstartóba. Akkor használja ezeket a parancsmagokat, ha a **virtuális gép a BEK és a KEK használatával van titkosítva**.

**Akkor használja ezeket a parancsmagokat, ha a Windows rendszerű virtuális gép a BEK és a KEK használatával van titkosítva.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Ezek a parancsmagok akkor használhatók, ha a linuxos virtuális gép a BEK és a KEK használatával van titkosítva.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

A fent generált JSON-fájllal titkos nevet és értéket kaphat, és megadhatja a titkos parancsmagot, hogy a titkos kulcs (BEK) vissza legyen helyezve a kulcstartóba. Akkor használja ezeket a parancsmagokat **, ha a virtuális gép csak BEK használatával van titkosítva** .

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * $Secretname értékének megszerzéséhez a $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl és a titkokat követő szöveg, illetve a titkos kulcs neve, például a kimeneti titkos URL-cím, a `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` titkos név pedig B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * A DiskEncryptionKeyFileName címke értéke megegyezik a titkos névvel.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Virtuális gép létrehozása visszaállított lemezről

Ha titkosított virtuális gépet készített biztonsági mentést az Azure VM Backup használatával, a fent említett PowerShell-parancsmagok segítenek visszaállítani a kulcsot és a titkos kulcsot a kulcstartóba. A visszaállítást követően tekintse meg az [Azure-beli virtuális gépek biztonsági mentésének és visszaállításának kezelése a PowerShell használatával](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) című cikket, amely titkosított virtuális gépeket hoz létre a visszaállított lemezről, kulcsról és titkos

## <a name="legacy-approach"></a>Örökölt megközelítés

A fent említett módszer a helyreállítási pontok esetében is működni fog. A kulcs-és titkos adatok helyreállítási pontról történő lekérésének régebbi megközelítése azonban a 2017. július 11-én régebbi helyreállítási pontok esetében érvényes a BEK és a KEK használatával titkosított virtuális gépek esetében. Ha a visszaállítási lemez feladata befejeződött a titkosított virtuális gép számára a [PowerShell-lépések](backup-azure-vms-automation.md#restore-an-azure-vm)használatával, győződjön meg arról, hogy a $RP érvényes értékkel van feltöltve.

### <a name="restore-key-legacy-approach"></a>Visszaállítási kulcs (örökölt megközelítés)

A következő parancsmagok segítségével lekérheti a kulcs (KEK) adatait a helyreállítási pontról, és megtáplálhatja a kulcs-parancsmag visszaállításával, hogy vissza lehessen állítani a kulcstartóban.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret-legacy-approach"></a>Restore Secret (örökölt megközelítés)

A következő parancsmagok segítségével szerezheti be a titkos (BEK) adatokat a helyreállítási pontról, és megadhatja, hogy a titkos parancsmagot állítsa vissza a Key vaultba.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * $Secretname értékének megszerzéséhez a $rp 1 kimenetére hivatkozva lehet. KeyAndSecretDetails. SecretUrl és szöveg használata a titkok után/például a kimeneti titkos URL-cím, a `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` titkos név pedig B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * A címke DiskEncryptionKeyFileName értéke megegyezik a titkos névvel.
> * A DiskEncryptionKeyEncryptionKeyURL értékének beszerzése a Key vaultból a kulcsok visszaállítása után és a [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) parancsmag használatával
>
>

## <a name="next-steps"></a>Következő lépések

Miután visszaállította a kulcsot és a titkos kulcsot a Key vaultba, tekintse meg az Azure-beli [virtuális gépek biztonsági mentésének és visszaállításának kezelése a PowerShell használatával](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) című cikket a titkosított virtuális gépek visszaállításához a helyreállított lemez, kulcs és titkos
