---
title: VM-lemezek biztonsági mentése Azure Stack Edge Pro GPU-eszközön a PowerShell használatával
description: Ismerteti, hogyan lehet biztonsági másolatot készíteni az Azure Stack Edge Pro GPU-eszközön futó virtuálisgép-lemezekről.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: ea860f58caba25ef3027fbf7bc4728355a7ca1bc
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315439"
---
# <a name="back-up-vm-disks-on-azure-stack-edge-pro-gpu-via-azure-powershell"></a>Virtuálisgép-lemezek biztonsági mentése Azure Stack Edge Pro GPU-n keresztül Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan hozhatók létre biztonsági másolatok a Azure Stack Edge Pro GPU-eszközön futó virtuálisgép-lemezekről Azure PowerShell használatával.

> [!IMPORTANT]
> Ez az eljárás a leállított virtuális gépek esetében használatos. A futó virtuális gépek biztonsági mentéséhez ajánlott egy külső gyártótól származó biztonsági mentési eszközt használni.

## <a name="workflow"></a>Munkafolyamat

A következő lépések összefoglalják az eszközön található virtuálisgép-lemezek biztonsági mentésének magas szintű munkafolyamatát:

1. Állítsa le a virtuális gépet.
1. Készítsen pillanatképet a virtuális gép lemezéről.
1. Másolja a pillanatképet egy helyi Storage-fiókba VHD-ként.
1. Töltse fel a VHD-t egy külső célra.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gépek biztonsági mentése előtt győződjön meg az alábbiakról:

- Olyan ügyfélhez fér hozzá, amelyet az eszközhöz való kapcsolódáshoz fog használni.
    - Az ügyfél egy [támogatott operációs rendszert](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)futtat.
    - Az ügyfél az eszköz helyi Azure Resource Manager való kapcsolódásra van konfigurálva, mint a [Kapcsolódás a Azure Resource Managerhez eszközhöz](azure-stack-edge-gpu-connect-resource-manager.md)című részben leírtak szerint.

## <a name="verify-connection-to-local-azure-resource-manager"></a>Helyi Azure Resource Managerhoz való kapcsolódás ellenőrzése

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]

## <a name="back-up-a-vm-disk"></a>VM-lemez biztonsági mentése

1. Az eszközön futó virtuális gépek listájának beolvasása. Azonosítsa a leállítani kívánt virtuális gépet.

    ```powershell
    Get-AzureRMVM
    ```

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\Users\user> Get-AzureRMVM

    ResourceGroupName    Name         Location  VmSize         OsType   NIC         ProvisioningState Zone
    -----------------    ----         --------  ------         ------   ---         ----------------- ----
    MYASERG           myasewindowsvm1 dbelocal Standard_D1_v2  Linux myasewindowsvm1nic  Succeeded
    MYASERG1            myaselinuxvm1 dbelocal Standard_D1_v2  Linux   myaselinuxvm1nic  Succeeded
    MYASERG2             myasetestvm1 dbelocal Standard_D1_v2  Linux    myasetestvm1nic  Succeeded

    PS C:\Users\user>
    ```
    
1. Állítsa le a virtuális gépet.

    ```powershell
    Stop-AzureRMVM –ResourceGroupName <Resource group name> -Name <VM name>
    ```

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\Users\user> Stop-AzureRMVM -ResourceGroupName myaserg2 -Name myasetestvm1

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    OperationId :
    Status      : Succeeded
    StartTime   : 4/9/2021 8:43:47 AM
    EndTime     : 4/9/2021 8:44:27 AM
    Error       :
    
    PS C:\Users\user>
    ```
    A virtuális gépet a Azure Portal is leállíthatja.
 

2. Készítsen pillanatképet a VM-lemezről, és mentse a pillanatképet egy helyi erőforráscsoporthoz. Ezt az eljárást az operációs rendszer és az adatlemezek esetében is használhatja.

   1. Lekérheti az eszközön lévő lemezek listáját, vagy egy adott erőforráscsoporthoz. Jegyezze fel a lemez nevét, amelyről biztonsági másolatot szeretne készíteni.

        ```powershell
        Get-AzureRMDisk -ResourceGroupName <Resource group name>
        ```   
        Íme egy példa a kimenetre:

        ```powershell
        PS C:\Users\user> $Disk = Get-AzureRMDisk -ResourceGroupName myaserg2
        PS C:\Users\user> $Disk.Name
        myasetestdisk1
        myasetestvm1_disk1_0ed91809927f4023b7aceb6eeca51c05
        PS C:\Users\user>
        ```
   1. Hozzon létre egy helyi erőforráscsoportot a virtuális gép pillanatképének célhelyként való kiszolgálásához.

        ```powershell
        PS C:\Users\user> New-AzureRmResourceGroup -ResourceGroupName myaserg3 -Location dbelocal
    
        ResourceGroupName : myaserg3
        Location          : dbelocal
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg3
        
        PS C:\Users\user>
        ```

   1. Állítsa be a paramétereket.

      ```powershell
      $DiskResourceGroup = <Disk resource group>
      $DiskName = <Disk name>
      $SnapshotName = <Snapshot name>
      $DestinationRG = <Snapshot destination resource group>
      ```

   3. Állítsa be a pillanatkép-konfigurációt, és végezze el a pillanatképet.

        ```powershell
        $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        $Snapshot = New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        ```
        Ellenőrizze, hogy a pillanatkép a cél erőforráscsoporthoz lett-e létrehozva.

        ```powershell
        Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        ```
        Íme egy példa a kimenetre:

        ```powershell
        PS C:\Users\user> $DiskResourceGroup = "myaserg2"
        PS C:\Users\user> $DiskName = "myasetestdisk1"
        PS C:\Users\user> $SnapshotName = "myasetestdisk1_ss"
        PS C:\Users\user> $DestinationRG = "myaserg3"
        PS C:\Users\user> $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        PS C:\Users\user> $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        PS C:\Users\user> $Snapshot=New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        PS C:\Users\user> Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        
        ResourceGroupName  : myaserg3
        ManagedBy          :
        Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
        TimeCreated        : 4/9/2021 4:23:21 PM
        OsType             :
        CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
        DiskSizeGB         : 10
        EncryptionSettings :
        ProvisioningState  : Succeeded
        Id                 : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg3/providers/Microsoft.Compute/snapshots/myasetestdisk1_ss
        Name               : myasetestdisk1_ss
        Type               : Microsoft.Compute/snapshots
        Location           : DBELocal
        Tags               : {}
        
        PS C:\Users\user>
        ```

## <a name="copy-the-snapshot-into-a-local-storage-account"></a>A pillanatkép másolása helyi Storage-fiókba

   Másolja a pillanatképeket egy helyi Storage-fiókba az eszközön. 

1. Állítsa be a paramétereket. 

    ```powershell
    $StorageAccountRG = <Local storage account resource group>
    $StorageAccountName = <Storage account name>
    $StorageEndpointSuffix = <Connection string in format: DeviceName.DnsDomain.com>
    $DestStorageContainer = <Destination storage container>
    $DestFileName = <Blob file name> 
    ```

1. Hozzon létre egy helyi Storage-fiókot az eszközön. 

    ```powershell
    New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Storage account resource group> -Location DBELocal -SkuName Standard_LRS
    ```

    Íme egy példa a kimenetre: 

    ```powershell
    PS C:\Users\user> New-AzureRmStorageAccount -Name myasesa4 -ResourceGroupName myaserg4 -Location DBELocal -SkuName Standard_LRS
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime        ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------        ----------------- ---------------
    myasesa4           myaserg4          DBELocal StandardLRS Storage            4/9/2021 6:02:56 PM Succeeded         False
    
    PS C:\Users\user>
    ```

1. Hozzon létre egy tárolót a létrehozott helyi Storage-fiókban. 

    ```powershell
    $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    $keyValue = $keys[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;
    $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;    
    ```
    Íme egy példa a kimenetre:

    ```powershell
    PS C:\Users\user> $StorageAccountName = "myasesa4"                                                              
    PS C:\Users\user> $StorageAccountRG = "myaserg4"
    PS C:\Users\user> $DestStorageContainer = "myasecont2"
    PS C:\Users\user> $StorageEndpointSuffix = "myasegpudev.wdshcsso.com"
    PS C:\Users\user> $DestFileName = "testfile1"

    PS C:\Users\user> $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    PS C:\Users\user> $keyValue = $keys[0].Value
    PS C:\Users\user> $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;                                                                                   
    PS C:\Users\user> $storagecontext 
    StorageAccountName : myasesa4                                                                                      
    BlobEndPoint       : http://myasesa4.blob.myasegpudev.wdshcsso.com/                                                
    TableEndPoint      : http://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : http://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : http://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[ke
                         y hidden]
    EndPointSuffix     : myasegpudev.wdshcsso.com/
    ConnectionString   : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=GSK
                         FuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}
    
    PS C:\Users\user> $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;
    PS C:\Users\user> $container
    Blob End Point: http://myasesa4.blob.myasegpudev.wdshcsso.com/
    
    Name                 PublicAccess         LastModified
    ----                 ------------         ------------
    myasecont2           Container            4/12/2021 4:46:03 PM +00:00
    
    PS C:\Users\user>
    ```    

    A Azure Storage Explorer használatával [helyi Storage-fiókot is létrehozhat](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#create-a-storage-account) , majd [létrehozhat egy tárolót a helyi Storage-fiókban](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload) az eszközön. 



1. Töltse le a pillanatképet a helyi Storage-fiókba.

   ```powershell
   $sassnapshot = Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
   $destContext = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $keyValue 
   Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
   ```

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\Users\user> $sassnapshot= Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
    PS C:\Users\user> $sassnapshot
       
    AccessSAS : https://md-2.blob.myasegpudev.wdshcsso.com/3d95ae10d9924e6fb84de408d071f22d/abcd.vhd?sv=2017-04-17&sr=
    b&si=2535bf98-f87f-4738-9142-594e3c1150fc&sk=system-1&sig=4wrtYzWg6ePWBdrXlodrVgT76q7PIueCbw3bbShKCGs%3D   
    
    PS C:\Users\user> $destContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue
    PS C:\Users\user> $destContext
        
    StorageAccountName : myasesa4
    BlobEndPoint       : https://myasesa4.blob.myasegpudev.wdshcsso.com/
    TableEndPoint      : https://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : https://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : https://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[key hidden]                                  EndPointSuffix     : myasegpudev.wdshcsso.com/                                                                     ConnectionString   : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/
                         ;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey
                         =GSKFuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}

    PS C:\Users\user> Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
    
    
       Container Uri: https://myasesa4.blob.myasegpudev.wdshcsso.com/myasecont2
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier Snapshot Time
    ----                 --------  ------          -----------                    ------------         ---------- ----
    testfile1            BlockBlob -1                                             2021-04-12 17:01:58Z
 
    PS C:\Users\user>
    ```

## <a name="download-vhd-to-external-target"></a>VHD letöltése külső célra

Ha külső helyre szeretné áthelyezni a biztonsági másolatokat, Azure Storage Explorer vagy AzCopy is használhat.

- A következő AzCopy parancs használatával töltse le a VHD-t egy külső célra.

    ```powershell
    azcopy copy "https://<local storage account name>.blob.<device name>.<DNS domain>/<container name>/<filename><SAS query string>" <destination target>
    ```

- A Azure Storage Explorer Azure Stack Edge használatával történő beállításához és használatához tekintse meg a [használati Storage Explorer a feltöltéshez](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)című részben található utasításokat.

## <a name="next-steps"></a>Következő lépések

[Virtuális gépek üzembe helyezése a Azure stack Edge Pro GPU-eszközön sablonok használatával](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).