---
title: Windows-fürtök lemezes titkosításának engedélyezése
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a lemezek titkosítása az Azure Service Fabric-fürtcsomópontok számára a Azure Resource Manager Azure Key Vault használatával.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78251819"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Lemez titkosításának engedélyezése az Azure Service Fabric-fürtcsomópontok számára a Windows rendszerben 
> [!div class="op_single_selector"]
> * [Windows rendszerű lemez titkosítása](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Lemez titkosítása Linux rendszerhez](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a lemezek titkosítását Service Fabric fürtcsomópontokon a Windowsban. Ezeket a lépéseket minden egyes csomópont és virtuálisgép-méretezési csoport esetében követnie kell. A csomópontok titkosításához a Azure Disk Encryption funkciót fogjuk használni a virtuálisgép-méretezési csoportokban.

Az útmutató a következő témaköröket tartalmazza:

* Fontos fogalmakat kell figyelembe vennie, amikor engedélyezi a lemez titkosítását Service Fabric fürtözött virtuálisgép-méretezési csoportokban a Windows rendszerben.
* A lemez titkosításának engedélyezése előtt elvégzendő lépések a Windows rendszerben Service Fabric fürtcsomópontokon.
* A lemez titkosításának engedélyezéséhez szükséges lépések a Windows rendszerben Service Fabric fürtcsomópontokon.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

**Önálló regisztráció** 

A virtuálisgép-méretezési csoport lemez-titkosítási előnézete önregisztrációt igényel. Ehhez a következő lépések szükségesek: 

1. Először futtassa a következő parancsot:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Várjon körülbelül 10 percet, amíg az állapot *beolvasása* be nem fejeződik. Az állapotot az alábbi parancs futtatásával tekintheti meg: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Hozzon létre egy Key vaultot ugyanabban az előfizetésben és régióban, mint a méretezési csoport, majd válassza ki a **EnabledForDiskEncryption** hozzáférési szabályzatát a kulcstartón a PowerShell-parancsmag használatával. A szabályzatot a Azure Portal Key Vault felhasználói felületének használatával is megadhatja a következő paranccsal:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Telepítse az [Azure CLI](/cli/azure/install-azure-cli)legújabb verzióját, amely az új titkosítási parancsokkal rendelkezik.
3. Telepítse az [Azure SDK](https://github.com/Azure/azure-powershell/releases) legújabb verzióját Azure PowerShell kiadásból. Az alábbiakban a virtuálisgép-méretezési csoport Azure Disk Encryption parancsmagokkal engedélyezheti ([beállíthatja](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) a titkosítást,[lekérheti](/powershell/module/az.compute/get-azvmssvmdiskencryption)a titkosítási állapotot, és eltávolíthatja ([letilthatja](/powershell/module/az.compute/disable-azvmssdiskencryption)) a titkosítást a méretezési csoport példányán.

| Parancs | Verzió |  Forrás  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 vagy újabb | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>A lemez titkosításának támogatott forgatókönyvei
* A virtuálisgép-méretezési csoportok titkosítása csak a felügyelt lemezekkel létrehozott méretezési csoportok esetében támogatott. Natív (vagy nem felügyelt) lemezek méretezési csoportjai esetén nem támogatott.
* A Windowsban a virtuálisgép-méretezési csoportokban a titkosítás támogatott az operációs rendszer és az adatkötetek esetében. A titkosítás letiltása az operációs rendszer és az adatkötetek esetében is támogatott a Windowsban futó virtuálisgép-méretezési csoportokban.
* A virtuálisgép-méretezési csoportokhoz tartozó virtuális gépek rendszerkép-és frissítési műveletei nem támogatottak az aktuális előzetes verzióban.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Új fürt létrehozása és a lemez titkosításának engedélyezése

A következő parancsokkal hozhat létre fürtöt, és engedélyezheti a lemezek titkosítását egy Azure Resource Manager sablon és egy önaláírt tanúsítvány használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be az alábbi parancsokkal:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>A már meglévő egyéni sablon használata 

Ha egyéni sablont kell létrehoznia az igényeinek megfelelően, javasoljuk, hogy kezdje az [Azure Service Fabric-fürt létrehozási sablonjának minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) lapján elérhető sablonok egyikével. A [fürt sablonjának testreszabásához][customize-your-cluster-template] tekintse meg az alábbi útmutatást.

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablon mindhárom tanúsítványhoz kapcsolódó paramétere és a paraméter fájl neve a következő, és hogy az értékek null értékűek-e a következőképpen:

```Json
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


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Alkalmazás üzembe helyezése Service Fabric-fürtön a Windowsban
Ha alkalmazást szeretne üzembe helyezni a fürtön, kövesse az [alkalmazások telepítése és eltávolítása a PowerShell használatával](service-fabric-deploy-remove-applications.md)című lépéseket és útmutatást.


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Lemez titkosításának engedélyezése a korábban létrehozott virtuálisgép-méretezési csoportokhoz

A következő parancsok futtatásával engedélyezheti a lemez titkosítását az előző lépésekben létrehozott virtuálisgép-méretezési csoportokhoz:
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Annak ellenőrzése, hogy engedélyezve van-e a lemez titkosítása egy virtuálisgép-méretezési csoport számára a Windows rendszerben
A következő parancsok futtatásával lekérheti a teljes virtuálisgép-méretezési csoport vagy a méretezési csoport bármely példányának állapotát.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Emellett bejelentkezhet a virtuálisgép-méretezési csoportba, és gondoskodhat arról, hogy a meghajtók titkosítva legyenek.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Virtuálisgép-méretezési csoport lemezes titkosításának letiltása Service Fabric-fürtben 
A következő parancsok futtatásával tiltsa le a virtuálisgép-méretezési csoport lemezes titkosítását. Vegye figyelembe, hogy a lemez titkosításának letiltása a teljes virtuálisgép-méretezési csoportra vonatkozik, nem egyedi példányra.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Következő lépések
Ezen a ponton egy biztonságos fürtnek kell lennie, és tudnia kell, hogyan lehet engedélyezni és letiltani a lemez titkosítását Service Fabric fürtcsomópontok és virtuálisgép-méretezési csoportok esetében. A Linux rendszerű fürt csomópontjaival kapcsolatos hasonló Service Fabric útmutatásért lásd: [lemez titkosítása Linux rendszeren](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
