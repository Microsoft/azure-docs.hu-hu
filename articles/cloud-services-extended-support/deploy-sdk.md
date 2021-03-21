---
title: Cloud Services üzembe helyezése (bővített támogatás) – SDK
description: Cloud Services (bővített támogatás) üzembe helyezése az Azure SDK használatával
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: b63f42ccc0a9d8d138e38a262db528fd36ea701a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123037"
---
# <a name="deploy-cloud-services-extended-support-by-using-the-azure-sdk"></a>Cloud Services (bővített támogatás) üzembe helyezése az Azure SDK használatával

Ez a cikk bemutatja, hogyan használható az [Azure SDK](https://azure.microsoft.com/downloads/) egy olyan Cloud Services (bővített támogatás) példány üzembe helyezéséhez, amely több szerepkörrel (webes szerepkörrel és feldolgozói szerepkörrel) és a távoli asztali bővítménnyel is rendelkezik. A Cloud Services (bővített támogatás) az Azure Cloud Services üzembe helyezési modellje, amely Azure Resource Manageron alapul.

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a Cloud Services [telepítésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás), és hozzon létre kapcsolódó erőforrásokat.

## <a name="deploy-cloud-services-extended-support"></a>Cloud Services üzembe helyezése (kiterjesztett támogatás)
1. Telepítse az [Azure számítási SDK NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview) , és inicializálja az ügyfelet egy szabványos hitelesítési mechanizmus használatával.

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
    {
        private string AuthenticationToken { get; set; }
        public override void InitializeServiceClient<T>(ServiceClient<T> client)
           {
               var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
               var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
               var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
               if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
               AuthenticationToken = result.Result.AccessToken;
           }
        public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
           {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Hozzon létre egy új erőforráscsoportot a Azure Resource Manager NuGet csomag telepítésével.

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. Hozzon létre egy Storage-fiókot és egy tárolót, ahol a szolgáltatáscsomag (. cspkg) és a szolgáltatás-konfigurációs (. cscfg) fájlokat fogja tárolni. Telepítse az [Azure Storage NuGet-csomagot](https://www.nuget.org/packages/Azure.Storage.Common/). Ez a lépés nem kötelező, ha meglévő Storage-fiókot használ. A Storage-fiók nevének egyedinek kell lennie.

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. Töltse fel a szolgáltatáscsomag (. cspkg) fájlját a Storage-fiókba. A csomag URL-címe lehet megosztott hozzáférés-aláírási (SAS) URI bármely Storage-fiókból.

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. Töltse fel a szolgáltatás konfigurációs (. cscfg) fájlját a Storage-fiókba. Adjon meg a szolgáltatás konfigurációját karakterlánc XML vagy URL-formátumként.

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. Hozzon létre egy virtuális hálózatot és egy alhálózatot. Telepítse az [Azure hálózati NuGet csomagot](https://www.nuget.org/packages/Azure.ResourceManager.Network/). Ez a lépés nem kötelező, ha meglévő hálózatot és alhálózatot használ.

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. Hozzon létre egy nyilvános IP-címet, és (opcionálisan) állítsa be a nyilvános IP-cím DNS-címke tulajdonságát. Ha statikus IP-címet használ, azt a szolgáltatás konfigurációs fájljában foglalt IP-címekre kell hivatkoznia.

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. Hozzon létre egy hálózati profil objektumot, és rendeljen hozzá egy nyilvános IP-címet a platform által létrehozott terheléselosztó elülső végéhez.

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. Kulcstartó létrehozása. Ezt a kulcstartót fogja használni a Cloud Services (bővített támogatás) szerepkörökhöz társított tanúsítványok tárolására. A Key vaultnak ugyanabban a régióban és előfizetésben kell lennie, mint a Cloud Services (kiterjesztett támogatás) példánynak, és egyedi névvel kell rendelkeznie. További információ: [tanúsítványok használata az Azure Cloud Services (bővített támogatás)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. Frissítse a Key Vault hozzáférési házirendjét, és adja meg a tanúsítvány engedélyeit a felhasználói fiókjának.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    Azt is megteheti, hogy a hozzáférési házirendet objektumazonosító alapján állítja be (amelyet a futtatásával érhet el `Get-AzADUser` ).

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. Ebben a példában egy önaláírt tanúsítványt fogunk felvenni egy kulcstartóba. A tanúsítvány ujjlenyomatát hozzá kell adni a szolgáltatás konfigurációs (. cscfg) fájljához a Cloud Services (bővített támogatás) szerepkörökön való üzembe helyezéshez.

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. Hozzon létre egy operációsrendszer-profil objektumot. Az operációs rendszer profilja a Cloud Services (bővített támogatás) szerepkörökhöz társított tanúsítványokat határozza meg. Itt ugyanaz a tanúsítvány, amelyet az előző lépésben hozott létre.

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. Hozzon létre egy szerepkör-profil objektumot. A szerepkör-profil definiálja az SKU szerepkör-specifikus tulajdonságait, például a nevet, a kapacitást és a szintet. 

    Ebben a példában két szerepkört definiálunk: ContosoFrontend és ContosoBackend. A szerepkör-profil adatainak meg kell egyezniük a szolgáltatás konfigurációs (. cscfg) fájljában és a Service Definition (. csdef) fájlban meghatározott szerepkör-konfigurációval.

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. Választható Hozzon létre egy bővítmény-profil objektumot, amelyet hozzá szeretne adni a Cloud Services (kiterjesztett támogatás) példányhoz. Ebben a példában egy RDP-bővítményt adunk hozzá.

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. Hozza létre a Cloud Services (kiterjesztett támogatás) példány központi telepítését.

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add your configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>Következő lépések
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- Cloud Services (bővített támogatás) üzembe helyezése a [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md)vagy a [Visual Studio](deploy-visual-studio.md)használatával.
- Látogasson el a [Cloud Serviceshoz készült Samples adattárra (bővített támogatás)](https://github.com/Azure-Samples/cloud-services-extended-support)