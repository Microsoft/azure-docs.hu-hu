---
title: WinRM-hozzáférés beállítása Azure-beli virtuális gépekhez
description: A WinRM-hozzáférés beállítása a Resource Manager-alapú üzemi modellben létrehozott Azure-beli virtuális gépekkel való használatra.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: 75fa2071f2ad54292e1cff6856de2091b74d3187
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101535"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>WinRM-hozzáférés beállítása Virtual Machineshoz Azure Resource Manager

A virtuális gépek WinRM-kapcsolattal való beállításához szükséges lépéseket itt találja.

1. Kulcstartó létrehozása
2. Önaláírt tanúsítvány létrehozása
3. Saját aláírású tanúsítvány feltöltése Key Vault
4. Az önaláírt tanúsítvány URL-címének lekérése a Key Vault
5. A virtuális gép létrehozásakor az önaláírt tanúsítványok URL-címére való hivatkozás

 

## <a name="step-1-create-a-key-vault"></a>1. lépés: Key Vault létrehozása
A Key Vault létrehozásához az alábbi parancsot használhatja.

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>2. lépés: önaláírt tanúsítvány létrehozása
Létrehozhat egy önaláírt tanúsítványt a PowerShell-parancsfájl használatával

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>3. lépés: az önaláírt tanúsítvány feltöltése a Key Vaultba
Mielőtt feltölti a tanúsítványt az 1. lépésben létrehozott Key Vaultba, konvertálnia kell a Microsoft. számítási erőforrás-szolgáltatójának formátumára. Az alábbi PowerShell-parancsfájl lehetővé teszi, hogy

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>4. lépés: az önaláírt tanúsítvány URL-címének lekérése a Key Vault
A Microsoft. számítási erőforrás-szolgáltatónak a virtuális gép üzembe helyezése során a Key Vaulton belüli titok URL-címét kell tartalmaznia. Ez lehetővé teszi, hogy a Microsoft. számítási erőforrás-szolgáltató letöltse a titkot, és létrehozza a megfelelő tanúsítványt a virtuális gépen.

> [!NOTE]
> A titok URL-címének is szerepelnie kell a verzióban. Egy példaként szolgáló URL-cím a\/következőhöz hasonlít: https:/contosovault.Vault.Azure.net:443/Secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Sablonok
A sablon URL-címére mutató hivatkozást az alábbi kód használatával érheti el

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Ezt az URL-címet az alábbi PowerShell-parancs használatával szerezheti be

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>5. lépés: az önaláírt tanúsítványok URL-címére való hivatkozás a virtuális gép létrehozásakor
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager sablonok
A virtuális gépek sablonokon keresztüli létrehozása közben a tanúsítvány a Secrets (titkok) szakaszban, a winRM szakaszban pedig az alábbi módon lesz hivatkozva:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

A fenti minta sablon itt található: [201-VM-WinRM-kulcstartó-Windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

A sablon forráskódja a [githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows) érhető el

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>6. lépés: csatlakozás a virtuális géphez
Ahhoz, hogy csatlakozhasson a virtuális géphez, meg kell győződnie arról, hogy a számítógép konfigurálva van a WinRM távoli felügyeletéhez. Indítsa el a PowerShellt rendszergazdaként, és hajtsa végre az alábbi parancsot, és győződjön meg róla, hogy be van állítva.

    Enable-PSRemoting -Force

> [!NOTE]
> Előfordulhat, hogy a fentiekben leírtak szerint meg kell győződnie arról, hogy a WinRM szolgáltatás fut. Ezt a használatával végezheti el`Get-Service WinRM`
> 
> 

A telepítés befejezése után a következő paranccsal csatlakozhat a virtuális géphez.

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
