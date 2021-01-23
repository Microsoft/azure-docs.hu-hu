---
title: Cloud Service üzembe helyezése (kiterjesztett támogatás) – PowerShell
description: Cloud Service (kiterjesztett támogatás) üzembe helyezése a PowerShell használatával
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 325e9123dd3f121b88df6d03518cfd9300fe3be9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745061"
---
# <a name="create-a-cloud-service-extended-support-using-azure-powershell"></a>Felhőalapú szolgáltatás (kiterjesztett támogatás) létrehozása Azure PowerShell használatával

Ez a cikk bemutatja, hogyan használhatja a `Az.CloudService` PowerShell-modult olyan Cloud Services (bővített támogatás) üzembe helyezéséhez az Azure-ban, amely több szerepkörrel (webrole és WorkerRole) és a távoli asztali bővítménnyel rendelkezik. 

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Tekintse át a Cloud Services [telepítésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás), és hozza létre a kapcsolódó erőforrásokat. 

3. Telepítés az. felhőszolgáltatás PowerShell-modul  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

4. Új erőforráscsoport létrehozása. Ez a lépés nem kötelező, ha meglévő erőforráscsoportot használ.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

5. Hozzon létre egy Storage-fiókot és-tárolót, amelyet a rendszer a Cloud Service-csomag (. cspkg) és a szolgáltatás-konfigurációs (. cscfg) fájlok tárolására fog használni. A Storage-fiók nevének egyedi nevet kell használnia. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “ContosoContainer” -Context $storageAccount.Context -Permission Blob 
    ```

6. Töltse fel a Cloud Service-csomagot (cspkg) a Storage-fiókba.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “ContosoContainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

7.  Töltse fel a Cloud Service-konfigurációt (cscfg) a Storage-fiókba. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container ContosoContainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

8. Hozzon létre egy virtuális hálózatot és egy alhálózatot. Ez a lépés nem kötelező, ha meglévő hálózatot és alhálózatot használ. Ez a példa egyetlen virtuális hálózatot és alhálózatot használ a Cloud Service-szerepkörökhöz (webrole és WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
9. Hozzon létre egy nyilvános IP-címet, és (opcionálisan) állítsa be a nyilvános IP-cím DNS-címke tulajdonságát. Ha statikus IP-címet használ, akkor a szolgáltatás konfigurációs fájljában Fenntartott IPra kell hivatkoznia.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

10. Hozzon létre egy hálózati profil objektumot, és rendeljen nyilvános IP-címet a platform létrehozott Load Balancer felületéhez.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
11. Key Vault létrehozása. Ez a Key Vault a Cloud Service (bővített támogatás) szerepköreihez társított tanúsítványok tárolására szolgál. A Key Vaultnak ugyanabban a régióban és előfizetésben kell lennie, mint a Cloud Service, és egyedi névvel kell rendelkeznie. További információ: [tanúsítványok használata az Azure Cloud Services (bővített támogatás)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US” 
    ```

13. Frissítse a Key Vault hozzáférési szabályzatot, és adja meg a tanúsítvány engedélyeit a felhasználói fiókjához. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Azt is megteheti, hogy hozzáférési szabályzatot állít be a ObjectId használatával (amely a futtatásával szerezhető be `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

14. Ebben a példában egy önaláírt tanúsítványt fogunk hozzáadni egy Key Vaulthoz. A tanúsítvány ujjlenyomatát hozzá kell adni a Cloud Service konfigurációs (. cscfg) fájlhoz a Cloud Service-szerepkörökben való üzembe helyezéshez. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
15. Hozzon létre egy operációsrendszer-profilt a memóriában lévő objektumban. Az operációs rendszer profilja meghatározza a Cloud Service szerepköreihez társított tanúsítványokat. Ez lesz az előző lépésben létrehozott tanúsítvány. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

16. Hozzon létre egy szerepkör-profilt a memóriában lévő objektumban. A szerepkör-profil egy adott szerepkör-specifikus tulajdonságokat definiál, például a nevet, a kapacitást és a szintet. Ebben a példában két szerepkört definiálunk: frontendRole és backendRole. A szerepkör-profil adatainak meg kell egyezniük a konfigurációs (cscfg) fájl-és szolgáltatás-definíciós (csdef) fájlban megadott szerepkör-konfigurációval. 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

17. Választható Hozzon létre egy olyan bővítmény-profilt a memóriában lévő objektumban, amelyet hozzá szeretne adni a felhőalapú szolgáltatáshoz. Ebben a példában az RDP-bővítményt fogjuk hozzáadni. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
18. Választható Adja meg a címkéket a felhőalapú szolgáltatáshoz hozzáadni kívánt PowerShell-kivonatoló táblázatként. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

19. Felhőalapú szolgáltatás központi telepítésének létrehozása a profil objektumaival & SAS URL-címekkel.

    ```powershell
    $cloudService = New-AzCloudService                                                  ` 
    -Name “ContosoCS”                                           ` 
    -ResourceGroupName “ContosOrg”                     ` 
    -Location “East US”                                           ` 
    -PackageUrl $cspkgUrl                       ` 
    -ConfigurationUrl $cscfgUrl                                         ` 
    -UpgradeMode 'Auto'                                           ` 
    -RoleProfile $roleProfile                                       ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile  
    -Tag $tag 
    ```

## <a name="next-steps"></a>További lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).