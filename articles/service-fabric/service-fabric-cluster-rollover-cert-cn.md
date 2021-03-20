---
title: Azure Service Fabric-fürt tanúsítványának átadása
description: Ismerje meg, hogyan lehet áttekinteni a tanúsítvány köznapi nevével azonosított Service Fabric-fürt tanúsítványát.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88853351"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Service Fabric-fürt tanúsítványának manuális átadása
Ha egy Service Fabric-fürt tanúsítványa le van zárva, frissítenie kell a tanúsítványt.  A tanúsítvány-átváltási egyszerű, ha a fürtöt [úgy állították be, hogy a tanúsítványokat köznapi név](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (ujjlenyomat helyett) alapján használja.  Új tanúsítvány beszerzése egy új lejárati dátummal rendelkező hitelesítésszolgáltatótól.  Az önaláírt tanúsítványok nem támogatják az éles Service Fabric fürtöket, így Azure Portal fürt létrehozásakor létrehozott tanúsítványokat is tartalmazhatnak. Az új tanúsítványnak ugyanazzal a köznapi névvel kell rendelkeznie, mint a régebbi tanúsítványnak. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric fürt automatikusan a deklarált tanúsítványt fogja használni a jövőbeli lejárati dátummal együtt. Ha egynél több érvényesítő tanúsítvány van telepítve a gazdagépen. Az ajánlott eljárás egy Resource Manager-sablon használata az Azure-erőforrások kiépítéséhez. Nem éles környezet esetén a következő parancsfájl használatával tölthet fel egy új tanúsítványt egy kulcstartóba, majd telepítheti a tanúsítványt a virtuálisgép-méretezési csoportba: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> A számítási műveletek virtuálisgép-méretezési csoportjának titkai nem támogatják ugyanazt az erőforrás-azonosítót két külön titok esetében, mivel minden egyes titok egy verzióval ellátott egyedi erőforrás. 

## <a name="next-steps"></a>Következő lépések

* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* [Fürt tanúsítványainak frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)
