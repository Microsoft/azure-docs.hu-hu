---
title: Azure Disk Encryption a Windows rendszerű virtuális gépekhez készült minta-parancsfájlok
description: Ez a cikk a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításának függeléke.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f113a1e559798328a2ef81336e8afff02732bb90
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804954"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption – mintaszkriptek 

Ez a cikk példákat tartalmaz az előre titkosított virtuális merevlemezek és egyéb feladatok előkészítéséhez.

> [!NOTE]
> Minden parancsfájl az ADE legújabb, nem HRE verziójára hivatkozik, kivéve a következőt:.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-parancsfájlok Azure Disk Encryptionhoz 


- **Az előfizetésben található összes titkosított virtuális gép listázása**

  Az összes ADE-titkosítású virtuális gép és a bővítmény verziója megtalálható az előfizetésben található összes erőforráscsoport számára ezen a [PowerShell-parancsfájl](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1)használatával.

  Másik lehetőségként ezek a parancsmagok az összes ADE-titkosítású virtuális gépet megjelenítik (a bővítmény verziószáma nem):

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **Az előfizetéshez tartozó összes titkosított VMSS-példány listázása**
    
    Az összes ADE-titkosítású VMSS-példányt és a bővítmény verzióját a jelen [PowerShell-parancsfájl](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1)használatával az előfizetésben található összes erőforráscsoporthoz megtalálhatja.
 
- **A Key vaultban lévő virtuális gépek titkosításához használt összes lemez-titkosítási titok listázása**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Az Azure Disk Encryption előfeltételek PowerShell-parancsfájl használata

Ha már ismeri a Azure Disk Encryption előfeltételeit, használhatja az [Azure Disk Encryption előfeltételek PowerShell-szkriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). A PowerShell-szkriptek használatára példát a [virtuális gépek titkosítása](disk-encryption-powershell-quickstart.md)– gyors útmutató című témakörben talál. A parancsfájl egy szakaszának megjegyzéseit eltávolíthatja a meglévő erőforráscsoporthoz tartozó meglévő virtuális gépek összes lemezének titkosításához a 211. sorban kezdődően. 

A következő táblázat a PowerShell-parancsfájlban használható paramétereket mutatja be: 

|Paraméter|Leírás|Kötelező?|
|------|------|------|
|$resourceGroupName| Azon erőforráscsoport neve, amelyhez a kulcstartó tartozik.  Ha az egyik nem létezik, akkor létrejön egy ilyen nevű új erőforráscsoport.| Igaz|
|$keyVaultName|Annak a kulcstartónak a neve, amelyben a titkosítási kulcsokat el kell helyezni. Ha az egyik nem létezik, akkor a rendszer létrehoz egy új tárat ezzel a névvel.| Igaz|
|$location|A kulcstartó helye. Győződjön meg arról, hogy a kulcstároló és a virtuális gépek titkosítva vannak ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzLocation`.|Igaz|
|$subscriptionId|A használni kívánt Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzSubscription`.|Igaz|
|$aadAppName|Annak az Azure AD-alkalmazásnak a neve, amelyet a rendszer a kulcstartók írásához fog használni. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha az alkalmazás már létezik, adja át a aadClientSecret paramétert a parancsfájlnak.|Hamis|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás ügyfél-titka.|Hamis|
|$keyEncryptionKeyName|A kulcstartóban nem kötelező kulcs-titkosítási kulcs neve. Ha az egyik nem létezik, a rendszer létrehoz egy új kulcsot a névvel.|Hamis|

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Virtuális gépek titkosítása vagy visszafejtése Azure AD-alkalmazás nélkül

- [Lemez titkosításának engedélyezése meglévő vagy Windows rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Titkosítás letiltása egy futó Windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Virtuális gépek titkosítása vagy visszafejtése Azure AD-alkalmazással (korábbi kiadás) 
 
- [Lemez titkosításának engedélyezése meglévő vagy Windows rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Titkosítás letiltása egy futó Windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Új titkosított felügyelt lemez létrehozása egy előre titkosított VHD/Storage-blobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új titkosított felügyelt lemezt, amely egy előre titkosított VHD-t és a hozzá tartozó titkosítási beállításokat biztosított.

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Előre titkosított Windows virtuális merevlemez előkészítése
Az alábbi szakaszoknak az Azure IaaS-ben titkosított virtuális merevlemezként való üzembe helyezéséhez egy előre titkosított Windows virtuális merevlemez előkészítéséhez van szükségük. Az információk segítségével új Windowsos virtuális gépet (VHD-t) készíthet a Azure Site Recovery vagy az Azure-ban. A virtuális merevlemezek előkészítésével és feltöltésével kapcsolatos további információkért lásd: [általánosított virtuális merevlemez feltöltése és használata új virtuális gépek létrehozásához az Azure-ban](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Csoportházirend frissítése, hogy az operációs rendszer védelmét nem TPM-t engedélyezze
Konfigurálja a BitLocker csoportházirend beállítást **BitLocker meghajtótitkosítás**, amelyet a **helyi számítógép-házirend**  >  **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők** szakaszban talál. Ha módosítani szeretné az **operációs rendszer meghajtóit**  >  ,**további hitelesítésre van szükség az indításkor**  >  ,**kompatibilis TPM nélkül**, az alábbi ábrán látható módon:

![Microsoft Antimalware szolgáltatás az Azure-ban](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>A BitLocker szolgáltatás összetevőinek telepítése
A Windows Server 2012-es és újabb verzióiban használja a következő parancsot:

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Windows Server 2008 R2 esetén használja a következő parancsot:

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>A BitLocker operációsrendszer-kötetének előkészítése a használatával `bdehdcfg`
Az operációsrendszer-partíció tömörítéséhez és a számítógép BitLockerhez való előkészítéséhez hajtsa végre a [bdehdcfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) , ha szükséges:

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Az operációs rendszer kötetének biztosítása a BitLocker használatával
A [`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)) parancs használatával engedélyezze a titkosítást a rendszerindító köteten egy külső kulcstartó használatával. Helyezze a külső kulcsot (. BEK fájlt) a külső meghajtóra vagy kötetre. A következő újraindítás után a titkosítás engedélyezve van a rendszer/rendszerindító köteten.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> Készítse elő a virtuális gépet egy külön adat/erőforrás virtuális merevlemezen a külső kulcs BitLocker használatával történő beolvasásához.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális merevlemez feltöltése Azure Storage-fiókba
DM-Crypt titkosítás engedélyezése után a helyi titkosított virtuális merevlemezt fel kell tölteni a Storage-fiókjába.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel az előre titkosított virtuális gép titkos kulcsát a kulcstartóba
A korábban beszerzett titkosítási titkot titkos kulcsként kell feltölteni a kulcstartóba.  Ehhez meg kell adni a titkos kulcs beállítása engedélyt és a wrapkey engedélyt ahhoz a fiókhoz, amely fel fogja tölteni a titkot.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>A lemez titkosítási titka nem titkosított KEK-sel
A titkos kulcs a Key vaultban történő beállításához használja a [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A jelszó Base64 karakterláncként van kódolva, majd feltöltve a kulcstartóba. Továbbá győződjön meg arról, hogy a következő címkék vannak beállítva, amikor létrehozza a titkos kulcsot a kulcstartóban.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Használja a `$secretUrl` következő lépésben az operációsrendszer- [lemez csatlakoztatását a KEK használata nélkül](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>A lemez titkosítási titka egy KEK-sel titkosítva
Mielőtt feltölti a titkos kulcsot a kulcstartóba, lehetősége van arra, hogy titkosítsa azt egy kulcs titkosítási kulcs használatával. A wrap [API](/rest/api/keyvault/wrapkey) használatával először Titkosítsa a titkos kulcsot a kulcs titkosítási kulcsával. Ennek a körbefuttatási műveletnek a kimenete Base64 URL-kódolású karakterlánc, amelyet ezután titkos kulcsként tölthet fel a [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

A `$KeyEncryptionKey` `$secretUrl` következő lépésben használja az operációsrendszer- [lemez csatlakoztatását a KEK használatával](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Titkos URL-cím megadása operációsrendszer-lemez csatlakoztatásakor

###  <a name="without-using-a-kek"></a>KEK használata nélkül
Az operációsrendszer-lemez csatlakoztatása közben át kell adnia a következőt: `$secretUrl` . Az URL-cím a "lemez-titkosítási titok, amely nem titkosított KEK-sel" című szakaszban lett létrehozva.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK használata
Az operációsrendszer-lemez csatlakoztatásakor adja meg a és a kapcsolót `$KeyEncryptionKey` `$secretUrl` . Az URL-címet a "lemez titkosítási titka egy KEK-lel titkosítva" szakasz hozta létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
