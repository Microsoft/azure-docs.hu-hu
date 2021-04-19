---
title: Felhőszolgáltatás üzembe helyezése (kiterjesztett támogatás) – PowerShell
description: Felhőszolgáltatás üzembe helyezése (kiterjesztett támogatás) a PowerShell használatával
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717386"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Felhőszolgáltatás üzembe helyezése (kiterjesztett támogatás) a Azure PowerShell

Ez a cikk bemutatja, hogyan használhatja a PowerShell-modult olyan Cloud Services (kiterjesztett támogatás) üzembe helyezéséhez az Azure-ban, amely több szerepkörrel (WebRole és WorkerRole) és távoli asztali bővítővel `Az.CloudService` rendelkezik. 

## <a name="before-you-begin"></a>Előkészületek

Tekintse át [az üzembe helyezés előfeltételeit](deploy-prerequisite.md) Cloud Services (kiterjesztett támogatás), és hozza létre a társított erőforrásokat. 

## <a name="deploy-a-cloud-services-extended-support"></a>Virtuális gép Cloud Services (kiterjesztett támogatás)
1. Az Az.CloudService PowerShell-modul telepítése  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Új erőforráscsoport létrehozása. Ez a lépés nem kötelező, ha meglévő erőforráscsoportot használ.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Hozzon létre egy tárfiókot és egy tárolót, amely a Cloud Service package (.cspkg) és a Service Configuration (.cscfg) fájlok tárolására lesz használva. A tárfiók nevéhez egyedi nevet kell használnia. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Töltse fel a Cloud Service-csomagot (cspkg) a tárfiókba.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Töltse fel a felhőszolgáltatás konfigurációját (cscfg) a tárfiókba. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Hozzon létre egy virtuális hálózatot és egy alhálózatot. Ez a lépés nem kötelező, ha meglévő hálózatot és alhálózatot használ. Ez a példa egyetlen virtuális hálózatot és alhálózatot használ a felhőszolgáltatás szerepkörei (WebRole és WorkerRole) számára. 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Hozzon létre egy nyilvános IP-címet, és állítsa be a nyilvános IP-cím DNS-címke tulajdonságát. Cloud Services (kiterjesztett támogatás) csak az [alapszintű](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) termékváltozatú nyilvános IP-címeket támogatja. A standard termékváltozatú nyilvános IP-k nem működnek Cloud Services.
Ha statikus IP-címet használ, hivatkozni kell rá szolgáltatáskonfigurációs Fenntartott IP (.cscfg) fájlban található fájlban. 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Hozzon létre egy hálózatiprofil-objektumot, és társítsa a nyilvános IP-címet a terheléselosztás előtere számára. Az Azure platform automatikusan létrehoz egy "Klasszikus" termékváltozatú terheléselosztási erőforrást ugyanabban az előfizetésben, mint a felhőszolgáltatás-erőforrás. A terheléselosztási erőforrás egy csak olvasható erőforrás az ARM-ban. Az erőforrás minden frissítése csak a felhőszolgáltatás üzembehelyezett fájljain keresztül támogatott (.cscfg & .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Key Vault létrehozása. A Key Vault a Felhőszolgáltatás (kiterjesztett támogatás) szerepkörhöz társított tanúsítványok tárolására lesz használva. A Key Vault régiónak és előfizetésnek ugyanabban a régióban és előfizetésben kell lennie, mint a felhőszolgáltatásnak, és egyedi névvel kell rendelkezik. További információ: [Tanúsítványok használata Azure Cloud Services (kiterjesztett támogatás)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Frissítse a Key Vault hozzáférési házirendet, és adjon tanúsítványengedélyeket a felhasználói fióknak. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Másik lehetőségként állítsa be a hozzáférési szabályzatot az ObjectId segítségével (amely a futtatásával szerezhető `Get-AzADUser` be) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Ebben a példában egy önaírt tanúsítványt adunk hozzá egy Key Vault. A tanúsítvány ujjlenyomatát hozzá kell adni a Cloud Service Configuration- (.cscfg-) fájlhoz a felhőszolgáltatás-szerepkörökben való üzembe helyezéshez. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Hozzon létre egy operációsrendszer-profilt a memóriában objektum. Az operációs rendszer profilja a felhőszolgáltatás szerepköreihez társított tanúsítványokat adja meg. Ez megegyezik az előző lépésben létrehozott tanúsítvánnyal. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Hozzon létre egy szerepkörprofilt a memóriában objektum. A szerepkörprofil egy szerepkör-termékváltozat-specifikus tulajdonságokat határoz meg, például a nevet, a kapacitást és a szintet. Ebben a példában két szerepkört definiáltunk: frontendRole és backendRole. A szerepkörprofil-információknak egyezniük kell a konfigurációs (cscfg) fájl- és szolgáltatásdefiníciós (csdef-) fájlban meghatározott szerepkör-konfigurációval. 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Nem kötelező) Hozzon létre egy memóriabeli bővítményprofil-objektumot, amit hozzá szeretne adni a felhőszolgáltatáshoz. Ebben a példában RDP-bővítményt adunk hozzá. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Vegye figyelembe, hogy a configFile csak PublicConfig címkével és névteret tartalmazhat az alábbiak szerint:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Nem kötelező) Definiálja a címkéket PowerShell-kivonattáblaként, amelyet hozzá szeretne adni a felhőszolgáltatáshoz. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Felhőszolgáltatás üzembe helyezésének létrehozása profilobjektumok és & SAS URL-címek használatával.

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
- Tekintse [át a gyakori kérdéseket a](faq.md) Cloud Services (kiterjesztett támogatás).
- Felhőszolgáltatás (kiterjesztett támogatás) üzembe helyezése a [következő Azure Portal,](deploy-portal.md) [PowerShell,](deploy-powershell.md) [sablon](deploy-template.md) vagy [Visual Studio.](deploy-visual-studio.md)
- Látogasson el [Cloud Services (kiterjesztett támogatás) mintatárba](https://github.com/Azure-Samples/cloud-services-extended-support)
