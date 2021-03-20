---
title: Lemez titkosításának engedélyezése linuxos fürtök esetén
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a lemez titkosítása az Azure Service Fabric-fürtcsomópontok esetében Linux rendszeren Azure Resource Manager és Azure Key Vault használatával.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "78252813"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Lemez titkosításának engedélyezése az Azure Service Fabric-fürtcsomópontok számára Linux rendszerben 
> [!div class="op_single_selector"]
> * [Lemez titkosítása Linux rendszerhez](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows rendszerű lemez titkosítása](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a lemezek titkosítását az Azure Service Fabric-fürtcsomópontok esetében Linuxon. Ezeket a lépéseket minden egyes csomópont és virtuálisgép-méretezési csoport esetében követnie kell. A csomópontok titkosításához a Azure Disk Encryption funkciót fogjuk használni a virtuálisgép-méretezési csoportokban.

Az útmutató a következő témaköröket tartalmazza:

* Fontos fogalmakat kell figyelembe vennie, amikor engedélyezi a lemez titkosítását Service Fabric fürt virtuálisgép-méretezési csoportjaiban a Linux rendszerben.
* A lemez titkosításának engedélyezéséhez szükséges lépések a Linux rendszerű Service Fabric-fürtcsomópontok esetében.
* A lemez titkosításának engedélyezéséhez szükséges lépések a Linux rendszerű Service Fabric fürtcsomópontokon.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

 **Önálló regisztráció**

A virtuálisgép-méretezési csoport lemez-titkosítási előnézete önregisztrációt igényel. Ehhez a következő lépések szükségesek:

1. Futtassa az alábbi parancsot: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Várjon körülbelül 10 percet, amíg az állapot *beolvasása* be nem fejeződik. Az állapotot az alábbi parancs futtatásával tekintheti meg:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Hozzon létre egy Key vaultot ugyanabban az előfizetésben és régióban, mint a méretezési csoport. Ezután válassza ki a **EnabledForDiskEncryption** hozzáférési szabályzatát a Key vaulton a PowerShell-parancsmag használatával. A szabályzatot a Azure Portal Key Vault felhasználói felületének használatával is megadhatja a következő paranccsal:
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
* A titkosítási és a letiltási titkosítás is támogatott az operációs rendszer és az adatkötetek esetében a linuxos virtuálisgép-méretezési csoportokban. 
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

Ha egyéni sablont kell létrehoznia, javasoljuk, hogy használja az [Azure Service Fabric-fürt létrehozási sablonjának minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) lapján található sablonok egyikét. 

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablon mindhárom tanúsítványhoz kapcsolódó paramétere és a paraméter fájlja az alábbiak szerint van-e elnevezve. Ügyeljen arra is, hogy az értékek null értékűek legyenek a következők szerint:

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

Mivel a Linux rendszerű virtuálisgép-méretezési csoportok csak az adatlemezek titkosítását támogatják, a Resource Manager-sablonok használatával adatlemezt kell hozzáadnia. A következőképpen frissítheti a sablont az adatlemezek létrehozásához:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
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

Itt látható az egyenértékű CLI-parancs. Módosítsa a deklarált utasítások értékeit a megfelelő értékekre. A CLI támogatja az előző PowerShell-parancs által támogatott összes többi paramétert.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Adatlemez csatlakoztatása Linux-példányhoz
Mielőtt folytatná a titkosítást egy virtuálisgép-méretezési csoporton, győződjön meg arról, hogy a hozzáadott adatlemez megfelelően van csatlakoztatva. Jelentkezzen be a Linux-fürt virtuális gépre, és futtassa a **LSBLK** parancsot. A kimenetnek meg kell jelennie a felvett adatlemeznek a **csatlakoztatási pont** oszlopban.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Alkalmazás üzembe helyezése egy Service Fabric-fürtön Linuxon
Ha alkalmazást szeretne üzembe helyezni a fürtön, kövesse az alábbi lépéseket és útmutatást a gyors [üzembe helyezési útmutatóban: Linux-tárolók központi telepítése Service Fabric](service-fabric-quickstart-containers-linux.md).


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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Annak ellenőrzése, hogy engedélyezve van-e a lemez titkosítása egy virtuálisgép-méretezési csoport számára a Linux rendszerben
A teljes virtuálisgép-méretezési csoport vagy a méretezési csoport bármely példánya állapotának lekéréséhez futtassa a következő parancsokat.
Emellett bejelentkezhet a Linux-fürt virtuális gépre, és futtathatja a **LSBLK** parancsot. A kimenetnek meg kell jelennie a hozzáadott adatlemeznek a **csatlakoztatási pont** oszlopban, és a Type oszlopnak a *Crypt* **értéket** kell beolvasnia.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

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
Ezen a ponton egy biztonságos fürtnek kell lennie, és tudnia kell, hogyan lehet engedélyezni és letiltani a lemez titkosítását Service Fabric fürtcsomópontok és virtuálisgép-méretezési csoportok esetében. A Linux rendszerű fürtök Service Fabricával kapcsolatos hasonló útmutatásért lásd: a [Windows lemezes titkosítása](service-fabric-enable-azure-disk-encryption-windows.md). 
