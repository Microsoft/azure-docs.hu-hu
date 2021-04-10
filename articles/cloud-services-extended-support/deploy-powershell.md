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
ms.openlocfilehash: bcf6b2f6b964a056b9d90f08c0586fcbdec5b260
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167277"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Felhőalapú szolgáltatás (kiterjesztett támogatás) üzembe helyezése Azure PowerShell használatával

Ez a cikk bemutatja, hogyan használhatja a `Az.CloudService` PowerShell-modult olyan Cloud Services (bővített támogatás) üzembe helyezéséhez az Azure-ban, amely több szerepkörrel (webrole és WorkerRole) és a távoli asztali bővítménnyel rendelkezik. 

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a Cloud Services [telepítésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás), és hozza létre a kapcsolódó erőforrásokat. 

## <a name="deploy-a-cloud-services-extended-support"></a>Cloud Services üzembe helyezése (kiterjesztett támogatás)
1. Telepítés az. felhőszolgáltatás PowerShell-modul  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Új erőforráscsoport létrehozása. Ez a lépés nem kötelező, ha meglévő erőforráscsoportot használ.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Hozzon létre egy Storage-fiókot és-tárolót, amelyet a rendszer a Cloud Service-csomag (. cspkg) és a szolgáltatás-konfigurációs (. cscfg) fájlok tárolására fog használni. A Storage-fiók nevének egyedi nevet kell használnia. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Töltse fel a Cloud Service-csomagot (cspkg) a Storage-fiókba.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Töltse fel a Cloud Service-konfigurációt (cscfg) a Storage-fiókba. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Hozzon létre egy virtuális hálózatot és egy alhálózatot. Ez a lépés nem kötelező, ha meglévő hálózatot és alhálózatot használ. Ez a példa egyetlen virtuális hálózatot és alhálózatot használ a Cloud Service-szerepkörökhöz (webrole és WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Hozzon létre egy nyilvános IP-címet, és állítsa be a nyilvános IP-cím DNS-címke tulajdonságát. A Cloud Services (kiterjesztett támogatás) csak az [alapszintű] ( https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU nyilvános IP-címek) használatát támogatja. A standard SKU nyilvános IP-címei nem működnek Cloud Services.
Ha statikus IP-címet használ, hivatkoznia kell rá a szolgáltatás konfigurációs (. cscfg) fájljában Fenntartott IP. 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Hozzon létre egy hálózati profil objektumot, és rendelje hozzá a nyilvános IP-címet a terheléselosztó felületéhez. Az Azure platform automatikusan létrehoz egy "klasszikus" SKU Load Balancer-erőforrást ugyanabban az előfizetésben, mint a Cloud Service-erőforrás. A terheléselosztó erőforrás egy írásvédett erőforrás az ARM-ben. Az erőforrás frissítései csak a Cloud Service telepítési fájljain (. cscfg &. csdef) keresztül támogatottak.

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Key Vault létrehozása. Ez a Key Vault a Cloud Service (bővített támogatás) szerepköreihez társított tanúsítványok tárolására szolgál. A Key Vaultnak ugyanabban a régióban és előfizetésben kell lennie, mint a Cloud Service, és egyedi névvel kell rendelkeznie. További információ: [tanúsítványok használata az Azure Cloud Services (bővített támogatás)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Frissítse a Key Vault hozzáférési szabályzatot, és adja meg a tanúsítvány engedélyeit a felhasználói fiókjához. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Azt is megteheti, hogy hozzáférési szabályzatot állít be a ObjectId használatával (amely a futtatásával szerezhető be `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Ebben a példában egy önaláírt tanúsítványt fogunk hozzáadni egy Key Vaulthoz. A tanúsítvány ujjlenyomatát hozzá kell adni a Cloud Service konfigurációs (. cscfg) fájlhoz a Cloud Service-szerepkörökben való üzembe helyezéshez. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Hozzon létre egy operációsrendszer-profilt a memóriában lévő objektumban. Az operációs rendszer profilja meghatározza a Cloud Service szerepköreihez társított tanúsítványokat. Ez lesz az előző lépésben létrehozott tanúsítvány. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Hozzon létre egy szerepkör-profilt a memóriában lévő objektumban. A szerepkör-profil egy adott szerepkör-specifikus tulajdonságokat definiál, például a nevet, a kapacitást és a szintet. Ebben a példában két szerepkört definiálunk: frontendRole és backendRole. A szerepkör-profil adatainak meg kell egyezniük a konfigurációs (cscfg) fájl-és szolgáltatás-definíciós (csdef) fájlban megadott szerepkör-konfigurációval. 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. Választható Hozzon létre egy olyan bővítmény-profilt a memóriában lévő objektumban, amelyet hozzá szeretne adni a felhőalapú szolgáltatáshoz. Ebben a példában az RDP-bővítményt fogjuk hozzáadni. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Vegye figyelembe, hogy a engedélybeállítások csak PublicConfig címkével kell rendelkeznie, és tartalmaznia kell egy névteret az alábbiak szerint:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. Választható Adja meg a címkéket a felhőalapú szolgáltatáshoz hozzáadni kívánt PowerShell-kivonatoló táblázatként. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Felhőalapú szolgáltatás központi telepítésének létrehozása a profil objektumaival & SAS URL-címekkel.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
- Látogasson el a [Cloud Services (bővített támogatás) minták tárházára](https://github.com/Azure-Samples/cloud-services-extended-support)
