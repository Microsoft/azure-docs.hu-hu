---
title: Virtuális gépek üzembe helyezése az Azure Stack Edge-eszközön Azure PowerShell használatával
description: Ismerteti, hogyan hozhat létre és kezelhet virtuális gépeket egy Azure Stack peremhálózati eszközön Azure PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 28988af0c1b3b5e4e5ce359abb617a66af816d69
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439816"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Virtuális gépek üzembe helyezése az Azure Stack Edge-eszközön Azure PowerShell használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack peremhálózati eszközön Azure PowerShell használatával. Az információ a Azure Stack Edge Pro és a GPU (grafikus feldolgozó egység), a Azure Stack Edge Pro R és a Azure Stack Edge mini R-eszközökre vonatkozik.

## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

Az üzembe helyezési munkafolyamat a következő ábrán látható:

![A virtuális gép üzembe helyezési munkafolyamatának ábrája.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Beépített előfizetés lekérdezése az eszközön

Azure Resource Manager esetében csak egyetlen, felhasználó által látható rögzített előfizetés támogatott. Ez az előfizetés eszközönként egyedi, és az előfizetés neve és az előfizetés azonosítója nem módosítható.

Az előfizetés tartalmazza a virtuális gépek létrehozásához szükséges összes erőforrást. 

> [!IMPORTANT]
> Az előfizetés akkor jön létre, amikor engedélyezi a virtuális gépeket a Azure Portalból, és helyileg él az eszközön.

Az előfizetés a virtuális gépek üzembe helyezésére szolgál.

1.  Az előfizetés listázásához futtassa a következő parancsot:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Íme néhány példa a kimenetre:

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Az eszközön futó regisztrált erőforrás-szolgáltatók listájának beolvasása. A lista általában a számítás, a hálózat és a tárolás részét képezi.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Az erőforrás-szolgáltatók előre regisztrálva vannak, és nem módosíthatók és nem módosíthatók.
    
    Íme néhány példa a kimenetre:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat, például a Storage-fiókot, a lemezt és a felügyelt lemezt.

> [!IMPORTANT]
> Az összes erőforrás ugyanabban a helyen jön létre, mint az eszköz, és a hely a **DBELocal** értékre van állítva.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Íme néhány példa a kimenetre:

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy új Storage-fiókot az előző lépésben létrehozott erőforráscsoport használatával. Ez egy helyi Storage-fiók, amelyet a virtuális gép virtuális lemezének lemezképének feltöltésére használhat.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager használatával csak helyi Storage-fiókokat hozhat létre, például a helyileg redundáns tárolást (standard vagy prémium). A többplatformos tárolási fiókok létrehozásával kapcsolatban lásd [: oktatóanyag: adatok átvitele a Storage-fiókokkal Azure stack Edge Pro és GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md)használatával.

Íme néhány példa a kimenetre:

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

A Storage-fiók kulcsainak lekéréséhez futtassa a `Get-AzureRmStorageAccountKey` parancsot. Íme néhány példa a kimenetre:

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Adja hozzá a blob URI-JÁT a gazdagép fájlhoz

Már hozzáadta a blob URI-t a Hosts fájlban ahhoz az ügyfélhez, amelyet az Azure-Blob Storagehoz való kapcsolódáshoz használ az [Azure stack Edge-beli virtuális gépek Azure PowerShell használatával történő üzembe helyezéséhez](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). 5. lépés: a végpont neve feloldása Ez a bejegyzés a blob URI-azonosító hozzáadására szolgál:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>

## <a name="install-certificates"></a>Tanúsítványok telepítése

Ha HTTPS-t használ, telepítenie kell a megfelelő tanúsítványokat az eszközre. Itt telepítheti a blob Endpoint-tanúsítványt. További információ: [tanúsítványok használata a Azure stack Edge Pro-val GPU-eszközzel](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>VHD feltöltése

Másolja a korábban létrehozott helyi Storage-fiókba azokat a lemezképeket, amelyeket az oldal blobokban kíván használni. Egy eszköz, például a [AzCopy](../storage/common/storage-use-azcopy-v10.md) használatával feltöltheti a virtuális merevlemezt (VHD) a Storage-fiókba. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
Használja a következő parancsokat a AzCopy 10 használatával:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Íme néhány példa a kimenetre: 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>Felügyelt lemez létrehozása a VHD-ből

Felügyelt lemez létrehozásához a feltöltött VHD-fájlból futtassa a következő parancsot:

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Íme néhány példa a kimenetre: 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Íme néhány példa a kimenetre. További információ erről a parancsmagról: [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Virtuálisgép-rendszerkép létrehozása a rendszerkép által felügyelt lemezről

Ha virtuálisgép-lemezképet szeretne létrehozni a felügyelt lemezről, futtassa a következő parancsot. Cserélje le a, a *\<Disk name>* *\<OS type>* és *\<Disk size>* a értéket valós értékekre.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Íme néhány példa a kimenetre. További információ erről a parancsmagról: [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>A virtuális gép létrehozása korábban létrehozott erőforrásokkal

A virtuális gép létrehozása és telepítése előtt létre kell hoznia egy virtuális hálózatot, és hozzá kell rendelnie egy virtuális hálózati adaptert.

> [!IMPORTANT]
> A következő szabályok érvényesek:
> - Csak egyetlen virtuális hálózatot hozhat létre, még az erőforráscsoportok között is. A virtuális hálózatnak pontosan ugyanaz a címtartomány kell lennie, mint a logikai hálózatnak.
> - A virtuális hálózatnak csak egy alhálózata lehet. Az alhálózatnak pontosan ugyanazzal a címtartománykel kell rendelkeznie, mint a virtuális hálózatnak.
> - A virtuális hálózati kártya létrehozásakor csak a statikus kiosztási módszert használhatja. A felhasználónak meg kell adnia egy magánhálózati IP-címet.

### <a name="query-the-automatically-created-virtual-network"></a>Az automatikusan létrehozott virtuális hálózat lekérdezése

Ha az eszköz helyi felhasználói felületéről engedélyezi a számítást, a rendszer automatikusan létrehoz egy nevű virtuális hálózatot `ASEVNET` az erőforráscsoport alatt `ASERG` . 

A következő parancs használatával kérdezheti le a meglévő virtuális hálózatot:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Virtuális hálózati kártya létrehozása

Ha virtuális hálózati adaptert szeretne létrehozni a virtuális hálózati alhálózat AZONOSÍTÓjának használatával, futtassa a következő parancsot:

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Íme néhány példa a kimenetre:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Ha virtuális hálózati adaptert hoz létre egy virtuális GÉPHEZ, akkor a nyilvános IP-címet is átadhatja. Ebben az esetben a nyilvános IP-cím a magánhálózati IP-címet adja vissza. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Mostantól a virtuálisgép-rendszerkép használatával létrehozhat egy virtuális gépet, és csatlakoztathatja azt a korábban létrehozott virtuális hálózathoz.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

Miután létrehozta és elvégezte a virtuális gépet, a következő felhasználónevet és jelszót fogja használni a bejelentkezéshez.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Attól függően, hogy létrehozott-e Windows vagy Linux rendszerű virtuális gépet, a csatlakoztatási utasítások különbözőek lehetnek.

### <a name="connect-to-a-linux-vm"></a>Csatlakozás Linux rendszerű virtuális géphez

Linux rendszerű virtuális géphez való kapcsolódáshoz tegye a következőket:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Csatlakozás Windows rendszerű virtuális géphez

Windows rendszerű virtuális géphez való kapcsolódáshoz tegye a következőket:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>A virtuális gép kezelése

A következő szakaszok ismertetik a Azure Stack Edge Pro-eszközön létrehozható gyakori műveleteket.

### <a name="list-vms-that-are-running-on-the-device"></a>Az eszközön futó virtuális gépek listázása

A Azure Stack Edge-eszközön futó összes virtuális gép listájának visszaküldéséhez futtassa a következő parancsot:


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>A virtuális gép bekapcsolása

Az eszközön futó virtuális gépek bekapcsolásához futtassa a következő parancsmagot:

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

További információ erről a parancsmagról: [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>A virtuális gép felfüggesztése vagy leállítása

Az eszközön futó virtuális gépek leállításához vagy leállításához futtassa a következő parancsmagot:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

További információ erről a parancsmagról: [stop-AzureRmVM parancsmag](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Adatlemez hozzáadása

Ha a virtuális gép számítási feladatainak növekedése megnövekszik, lehet, hogy adatlemezt kell hozzáadnia. Ehhez futtassa az alábbi parancsot:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>A virtuális gép törlése

A virtuális gép eszközről való eltávolításához futtassa a következő parancsmagot:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

További információ erről a parancsmagról: [Remove-AzureRmVm parancsmag](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager-parancsmagok](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
