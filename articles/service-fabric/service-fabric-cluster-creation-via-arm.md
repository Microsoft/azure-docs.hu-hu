---
title: Azure Service Fabric-fürt létrehozása
description: Ismerje meg, hogyan állíthat be biztonságos Service Fabric-fürtöt az Azure-ban Azure Resource Manager használatával.  Létrehozhat egy fürtöt egy alapértelmezett sablonnal, vagy használhatja a saját fürtözött sablonját.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: d4daa75fd9383bc19da1b09104ebddff5712c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791811"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Service Fabric-fürt létrehozása Azure Resource Manager használatával 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Az [Azure Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait.  Az Azure-ban futó Service Fabric fürtök egy Azure-erőforrás, és a Azure Resource Manager használatával települnek. Ez a cikk azt ismerteti, hogyan helyezhet üzembe biztonságos Service Fabric-fürtöt az Azure-ban a Resource Manager használatával. Használhat alapértelmezett fürtöt vagy egyéni sablont is.  Ha még nem rendelkezik egyéni sablonnal, [megtudhatja, hogyan hozhat létre egyet](service-fabric-cluster-creation-create-template.md).

Meg kell adni a fürt védelmére kiválasztott biztonsági típust (pl.: Windows Identity, X509 stb.) a fürt kezdeti létrehozásához, és ezt követően nem módosítható. Fürt beállítása előtt olvassa el [Service Fabric fürt biztonsági forgatókönyveit][service-fabric-cluster-security]. Az Azure-ban a Service Fabric x509-tanúsítvánnyal védi a fürtöt és a végpontokat, hitelesíti az ügyfeleket és titkosítja az adatait. Azure Active Directory ajánlott a felügyeleti végpontokhoz való hozzáférés biztonságossá tétele is. További információért olvassa el az [Azure ad beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md)című témakört.

Ha éles munkaterhelést futtató üzemi fürtöt hoz létre, javasoljuk, hogy először olvassa el az [üzemi készültségi ellenőrzőlistát](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek 
Ebben a cikkben a fürt üzembe helyezéséhez használja a Service Fabric RM PowerShell vagy az Azure CLI-modult:

* [Azure PowerShell 4,1 és újabb verziók][azure-powershell]
* [Az Azure CLI 2,0-es vagy újabb verziója][azure-CLI]

A Service Fabric modulok dokumentációját itt találja:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [az SF CLI modul](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A cikkben szereplő parancsok futtatása előtt először jelentkezzen be az Azure-ba.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Új fürt létrehozása rendszer által generált önaláírt tanúsítvány használatával

A következő parancsokkal hozhat létre egy, a rendszer által létrehozott, önaláírt tanúsítványt használó fürtöt. Ez a parancs egy elsődleges fürtöt állít be, amely a fürt biztonságához használatos, és rendszergazdai hozzáférést állít be a tanúsítvány használatával végzett felügyeleti műveletekhez.  Az önaláírt tanúsítványok a tesztelési fürtök biztonságossá tételéhez hasznosak.  Az üzemi fürtöket egy hitelesítésszolgáltatótól (CA) származó tanúsítvánnyal kell védeni.

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>A modulban lévő alapértelmezett fürtözött sablon használata

A következő paranccsal gyorsan hozhat létre fürtöt a minimális paraméterek megadásával az alapértelmezett sablon használatával.

A használt sablon elérhető az [Azure Service Fabric sablonban minták: Windows-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és Ubuntu- [sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

A következő parancs Windows-vagy Linux-fürtöket is létrehozhat, ennek megfelelően meg kell adnia az operációs rendszert. A PowerShell/CLI-parancsok a megadott *CertificateOutputFolder* is kimutatják a tanúsítványt. Azonban győződjön meg arról, hogy a tanúsítvány mappája már létre van hozva. A parancs más paramétereket is igénybe vesz, például a VM SKU-t.

> [!NOTE]
> A következő PowerShell-parancs csak a Azure PowerShell `Az` modullal működik. Azure Resource Manager PowerShell-verzió aktuális verziójának vizsgálatához futtassa a következő PowerShell-parancsot: "Get-Module az". A Azure Resource Manager PowerShell verziójának frissítéséhez kövesse [ezt a hivatkozást](/powershell/azure/install-Az-ps) . 
>
>

A fürt üzembe helyezése a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

A fürt üzembe helyezése az Azure CLI használatával:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Saját egyéni sablon használata

Ha egyéni sablont kell létrehoznia az igényeinek megfelelően, javasoljuk, hogy az [Azure Service Fabric sablonjának mintáján](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)elérhető sablonok egyikével kezdjen. Megtudhatja, hogyan [szabhatja testre a fürt sablonját][customize-your-cluster-template].

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablon mindhárom tanúsítványhoz kapcsolódó paramétere és a paraméter fájl neve a következő, az értékek értéke pedig null a következő módon:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

A fürt üzembe helyezése a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

A fürt üzembe helyezése az Azure CLI használatával:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Hozzon létre egy új fürtöt saját X. 509 tanúsítvány használatával

A következő parancs használatával hozzon létre fürtöt, ha rendelkezik olyan tanúsítvánnyal, amelyet a fürt biztonságossá tételéhez szeretne használni.

Ha ez egy HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány, amelyet más célra szeretne használni, akkor azt javasoljuk, hogy adjon meg egy külön erőforráscsoportot, amely kifejezetten a kulcstartóhoz szükséges. Javasoljuk, hogy a Key vaultot a saját erőforráscsoporthoz helyezze. Ez a művelet lehetővé teszi a számítási és tárolási erőforráscsoportok eltávolítását, beleértve a Service Fabric fürtöt tartalmazó erőforráscsoportot is, a kulcsok és a titkos kódok elvesztése nélkül. **A kulcstárolót tartalmazó erőforráscsoportot az azt használó fürttel *azonos régióban kell lennie* .**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Használja az alapértelmezett öt csomópontot, amely egy, a modulban szállított csomópont típusú sablon
A használt sablon az [Azure-mintákon érhető el: Windows-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

A fürt üzembe helyezése a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

A fürt üzembe helyezése az Azure CLI használatával:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Saját egyéni fürt sablonjának használata
Ha egyéni sablont kell létrehoznia az igényeinek megfelelően, javasoljuk, hogy az [Azure Service Fabric sablonjának mintáján](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)elérhető sablonok egyikével kezdjen. Megtudhatja, hogyan [szabhatja testre a fürt sablonját][customize-your-cluster-template].

Ha már rendelkezik egyéni sablonnal, akkor győződjön meg arról, hogy a sablon mindhárom tanúsítványhoz kapcsolódó paramétere és a paraméter fájljának neve a következő, az értékek pedig null értékűek.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

A fürt üzembe helyezése a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

A fürt üzembe helyezése az Azure CLI használatával:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>A Key vaultba feltöltött titkos kulcs mutatójának használata

Meglévő kulcstartó használatához engedélyezni kell a kulcstárolót a [központi telepítéshez](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) , hogy a számítási erőforrás-szolgáltató tanúsítványokat kapjon tőle, és telepítse azt a fürtcsomópontokon.

A fürt üzembe helyezése a PowerShell használatával:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

A fürt üzembe helyezése az Azure CLI használatával:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Következő lépések
Ezen a ponton egy biztonságos fürt fut az Azure-ban. Ezután [kapcsolódjon a fürthöz](service-fabric-connect-to-secure-cluster.md) , és Ismerje meg, hogyan [kezelheti az alkalmazási titkokat](service-fabric-application-secret-management.md).

A JSON-szintaxis és a tulajdonságok sablon használatára a következő témakörben talál további információt: [Microsoft. ServiceFabric/fürtök sablonjának leírása](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
