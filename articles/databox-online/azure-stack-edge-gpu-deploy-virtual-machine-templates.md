---
title: Virtuális gépek üzembe helyezése a Azure Stack Edge Pro-eszközön sablonok használatával
description: Ismerteti, hogyan lehet virtuális gépeket (VM-ket) létrehozni és felügyelni egy Azure Stack Edge Pro-eszközön sablonok használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2922e9620f558e072cfc1264f7e661efd11a90fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437008"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Virtuális gépek üzembe helyezése a Azure Stack Edge Pro GPU-eszközön sablonok használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez az oktatóanyag leírja, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack Edge Pro-eszközön sablonok használatával. Ezek a sablonok JavaScript Object Notation (JSON) fájlok, amelyek meghatározzák a virtuális gép infrastruktúráját és konfigurációját. Ezekben a sablonokban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

A sablonok különböző környezetekben rugalmasak, mivel a paramétereket bemenetként, fájlból is elvégezhetik. A szabványos elnevezési struktúra a `TemplateName.json` sablonhoz és `TemplateName.parameters.json` a Parameters fájlhoz szükséges. Az ARM-sablonokkal kapcsolatos további információkért [tekintse meg a mi a Azure Resource Manager-sablonok?](../azure-resource-manager/templates/overview.md)című témakört.

Ebben az oktatóanyagban az előre megírt minta sablonokat fogjuk használni az erőforrások létrehozásához. Nem kell szerkesztenie a sablonfájlt, és csak a `.parameters.json` fájlokat módosíthatja, hogy testreszabja a központi telepítést a gépen. 

## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

Azure Stack Edge Pro virtuális gépek számos eszközön való üzembe helyezéséhez egyetlen Sysprep használatával létrehozott virtuális merevlemezt használhat a teljes flottához, ugyanezt a sablont az üzembe helyezéshez, és csak kisebb módosításokat hajthat végre a sablonban az egyes telepítési helyekhez 

A sablonok használatával történő üzembe helyezési munkafolyamat magas szintű összefoglalása a következő:

1. **Előfeltételek konfigurálása** – az előfeltételek három típusa létezik: eszköz, ügyfél és a virtuális gép.

    1. **Eszköz előfeltételei**

        1. Azure Resource Manager csatlakoztatása az eszközön.
        2. A számítás engedélyezése a helyi felhasználói felületen keresztül.

    2. **Ügyfél előfeltételei**

        1. Töltse le a virtuálisgép-sablonokat és a kapcsolódó fájlokat az ügyfélen.
        1. A TLS 1,2 opcionálisan konfigurálható az ügyfélen.
        1. [Töltse le és telepítse a Microsoft Azure Storage Explorert](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) az ügyfélen.

    3. **A virtuális gépek előfeltételei**

        1. Hozzon létre egy erőforráscsoportot az eszköz helyén, amely az összes virtuálisgép-erőforrást tartalmazni fogja.
        1. Hozzon létre egy Storage-fiókot a virtuálisgép-rendszerkép létrehozásához használt virtuális merevlemez feltöltéséhez.
        1. Adja hozzá a helyi Storage-fiók URI-JÁT a DNS-hez vagy a Hosts fájlhoz az eszközt elérő ügyfélen.
        1. Telepítse a blob Storage-tanúsítványt az eszközre és az eszközt elérő helyi ügyfélre. Szükség esetén telepítse a blob Storage-tanúsítványt a Storage Explorer.
        1. Hozzon létre és töltsön fel egy virtuális merevlemezt a korábban létrehozott Storage-fiókba.

2. **Virtuális gép létrehozása sablonokból**

    1. Hozzon létre egy virtuálisgép-rendszerképet a `CreateImage.parameters.json` Parameters fájl és a `CreateImage.json` központi telepítési sablon használatával.
    1. Hozzon létre egy virtuális gépet a korábban létrehozott erőforrásokkal `CreateVM.parameters.json` Paraméterek fájl és  `CreateVM.json` központi telepítési sablon használatával.

## <a name="device-prerequisites"></a>Eszköz előfeltételei

Konfigurálja ezeket az előfeltételeket az Azure Stack Edge Pro-eszközön.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Ügyfél előfeltételei

Konfigurálja ezeket az előfeltételeket az ügyfélen, amely az Azure Stack Edge Pro-eszköz elérésére szolgál majd.

1. [Töltse le Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) , ha a virtuális merevlemez feltöltésére használja. Azt is megteheti, hogy letölti a AzCopy a virtuális merevlemez feltöltéséhez. Előfordulhat, hogy konfigurálnia kell a TLS 1,2-et az ügyfélszámítógépen, ha a AzCopy régebbi verzióját futtatja. 
1. [Töltse le a virtuálisgép-sablonokat és a paraméterek fájljait](https://aka.ms/ase-vm-templates) az ügyfélszámítógépre. Bontsa ki a munkakönyvtárként használni kívánt könyvtárba.


## <a name="vm-prerequisites"></a>A virtuális gépek előfeltételei

Konfigurálja ezeket az előfeltételeket a virtuális gépek létrehozásához szükséges erőforrások létrehozásához. 

    
### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat, például a Storage-fiókot, a lemezt, a felügyelt lemezt.

> [!IMPORTANT]
> Az összes erőforrás ugyanabban a helyen jön létre, mint az eszköz, és a hely a **DBELocal** értékre van állítva.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Az alábbiakban egy példa látható a kimenetre.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy új Storage-fiókot az előző lépésben létrehozott erőforráscsoport használatával. Ez a fiók egy **helyi Storage-fiók** , amelyet a rendszer a virtuális gép virtuális lemezének lemezképének feltöltésére fog használni.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> A Azure Resource Manager használatával csak a helyi Storage-fiókok, például a helyileg redundáns tárolás (Standard_LRS vagy Premium_LRS) hozhatók létre. A többplatformos tárolási fiókok létrehozásához tekintse [meg a Hozzáadás, kapcsolódás a Storage-fiókokhoz a Azure stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md)-ban című témakör lépéseit.

Az alábbiakban egy példa látható a kimenetre.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

A Storage-fiók kulcsainak lekéréséhez futtassa a `Get-AzureRmStorageAccountKey` parancsot. Itt látható a parancs mintájának kimenete.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>BLOB URI hozzáadása a Hosts fájlhoz

Győződjön meg arról, hogy már hozzáadta a blob URI-t a Hosts fájlban a blob Storage-hoz való kapcsolódáshoz használt ügyfélhez. **Futtassa rendszergazdaként a Jegyzettömböt** , és adja hozzá a következő bejegyzést a blob URI-hoz `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

Egy tipikus környezetben a DNS-t úgy kell konfigurálni, hogy az összes Storage-fiók az Azure Stack Edge Pro-eszközre mutasson egy `*.blob.devicename.domainname.com` bejegyzéssel.

### <a name="optional-install-certificates"></a>Választható Tanúsítványok telepítése

Ugorja át ezt a lépést, ha Storage Explorer *http*-n keresztül fog kapcsolatot létesíteni. Ha *https*-t használ, akkor telepítenie kell a megfelelő tanúsítványokat a Storage Explorer. Ebben az esetben telepítse a blob Endpoint-tanúsítványt. További információ: tanúsítványok létrehozása és feltöltése a [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)szolgáltatásban. 

### <a name="create-and-upload-a-vhd"></a>Virtuális merevlemez létrehozása és feltöltése

Győződjön meg arról, hogy rendelkezik egy virtuális lemezzel, amelyet a későbbi lépésben való feltöltéshez használhat. Kövesse a [virtuális gép rendszerképének létrehozása](azure-stack-edge-gpu-create-virtual-machine-image.md)című témakör lépéseit. 

Másolja a korábbi lépésekben létrehozott helyi Storage-fiókban a lapok blobba felhasználható lemezes lemezképeit. A [virtuális merevlemezt a](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) korábbi lépések során létrehozott Storage-fiókba a [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) vagy a AzCopy eszközzel töltheti fel. 

### <a name="use-storage-explorer-for-upload"></a>Feltöltés Storage Explorer használata

1. Nyissa meg a Storage Explorert. Válassza a **Szerkesztés** lehetőséget, és győződjön meg arról, hogy az alkalmazás a **Target Azure stack API**-kra van beállítva.

    ![Cél beállítása Azure Stack API-kra](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Telepítse az ügyféltanúsítványt PEM formátumban. Válassza az **> SSL-tanúsítványok szerkesztése > tanúsítványok importálása** lehetőséget. Mutasson az ügyféltanúsítványt.

    ![BLOB Storage-végpont tanúsítványának importálása](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Ha eszköz által generált tanúsítványokat használ, töltse le és alakítsa át a blob Storage-végpont `.cer` tanúsítványát `.pem` formátumra. Futtassa az alábbi parancsot. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Ha saját tanúsítványt hoz, használja az aláíró lánc főtanúsítványát `.pem` formátumban.

3. A tanúsítvány importálása után indítsa újra Storage Explorer a módosítások érvénybe léptetéséhez.

    ![Újraindítás Storage Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. A bal oldali ablaktáblán kattintson a jobb gombbal a **Storage-fiókok** elemre, és válassza a **Kapcsolódás az Azure Storage-hoz** lehetőséget. 

    ![Kapcsolódás az Azure Storage-hoz 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Válassza a **Tárfióknév és -kulcs használata** lehetőséget. Kattintson a **Tovább** gombra.

    ![Kapcsolódás az Azure Storage-hoz 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. A **kapcsolat neve és kulcsa mezőben** adja **meg a megjelenítendő nevet**, a **Storage-fiók nevét**, az Azure Storage- **fiók kulcsát**. Válassza a **másik** tárolási tartomány lehetőséget, majd adja meg a `<device name>.<DNS domain>` kapcsolatok karakterláncát. Ha nem telepített tanúsítványt a Storage Explorerban, ellenőrizze a **use http** beállítást. Kattintson a **Tovább** gombra.

    ![Kapcsolat a névvel és a kulccsal](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Tekintse át a **kapcsolat összegzését** , és válassza a **Csatlakoztatás** lehetőséget.

8. A Storage-fiók megjelenik a bal oldali ablaktáblán. Válassza ki és bontsa ki a Storage-fiókot. Válassza a **blob-tárolók** lehetőséget, kattintson a jobb gombbal, majd válassza a **blob-tároló létrehozása** lehetőséget. Adja meg a blob-tároló nevét.

9. Válassza ki az imént létrehozott tárolót, és a jobb oldali ablaktáblában válassza a **feltöltés > a fájlok feltöltése** lehetőséget. 

    ![1. VHD-fájl feltöltése](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Tallózással keresse meg a fájlt, és mutasson arra a virtuális merevlemezre, amelyet fel szeretne tölteni a **kiválasztott fájlokban**. Válassza ki a **blob típusát** **lap blobként** , és válassza a **feltöltés** lehetőséget.

    ![A 2. VHD-fájl feltöltése](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Miután betöltötte a VHD-t a blob-tárolóba, válassza ki a virtuális merevlemezt, kattintson a jobb gombbal, majd válassza a **Tulajdonságok parancsot**. 

    ![3. VHD-fájl feltöltése](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Másolja ki és mentse el az **URI**-t, amelyet a későbbi lépésekben fog használni.

    ![URI másolása](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Rendszerkép létrehozása a virtuális géphez

Ha lemezképet szeretne létrehozni a virtuális géphez, szerkessze a `CreateImage.parameters.json` paramétereket tartalmazó fájlt, majd telepítse a `CreateImage.json` paramétert használó sablont.


### <a name="edit-parameters-file"></a>Paraméterek szerkesztése fájl

A fájl `CreateImage.parameters.json` a következő paramétereket veszi figyelembe: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Szerkessze a fájlt `CreateImage.parameters.json` , hogy tartalmazza a Azure stack Edge Pro-eszköz következő értékeit:

1. Adja meg a feltölteni kívánt virtuális merevlemezhez tartozó operációsrendszer-típust. Az operációs rendszer típusa Windows vagy Linux lehet.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Módosítsa a rendszerkép URI-ját az előző lépésben feltöltött rendszerkép URI-azonosítójához:

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   Ha a *http* -t a Storage Explorer használatával használja, módosítsa az URI-t egy *http* URI-ra.

3. Adjon meg egy egyedi rendszerkép-nevet. Ezt a rendszerképet a virtuális gép létrehozásához használhatja a későbbi lépésekben. 

   Itt látható a cikkben használt JSON-minta.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Mentse a paramétereket tartalmazó fájlt.


### <a name="deploy-template"></a>Sablon üzembe helyezése 

A sablon üzembe helyezése `CreateImage.json` . Ez a sablon telepíti a lemezkép-erőforrásokat, amelyeket a rendszer a későbbi lépésben a virtuális gépek létrehozásához használ majd.

> [!NOTE]
> Ha hitelesítési hibaüzenetet kap a sablon telepítésekor, előfordulhat, hogy a munkamenet Azure-beli hitelesítő adatai lejártak. Futtassa `login-AzureRM` újra a parancsot az Azure stack Edge Pro-eszköz Azure Resource Managerhoz való kapcsolódáshoz.

1. Futtassa az alábbi parancsot: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Ez a parancs lemezkép-erőforrást helyez üzembe. Az erőforrás lekérdezéséhez futtassa a következő parancsot:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Itt látható egy sikeres létrehozott rendszerkép kimenete.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>Virtuális gép létrehozása

### <a name="edit-parameters-file-to-create-vm"></a>Paraméterek szerkesztése virtuális gép létrehozásához
 
Virtuális gép létrehozásához használja a `CreateVM.parameters.json` paraméterfájlt. A következő paramétereket veszi igénybe.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Rendelje hozzá a megfelelő paramétereket a `CreateVM.parameters.json` Azure stack Edge Pro-eszközhöz.

1. Adjon meg egy egyedi nevet, a hálózati csatoló nevét és az ipconfig nevet. 
1. Adja meg a felhasználónevet, a jelszót és a virtuális gép támogatott méretét.
1. Ha engedélyezte a hálózati adaptert a számítási feladatokhoz, a rendszer automatikusan létrehoz egy virtuális kapcsolót és egy virtuális hálózatot az adott hálózati adapteren. Lekérdezheti a meglévő virtuális hálózatot, hogy beolvassa a vnet nevét, az alhálózat nevét és a vnet-erőforráscsoport nevét.

    Futtassa az alábbi parancsot:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Itt látható a minta kimenete:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    A ASEVNET használata a vnet neve, a ASEVNETsubNet az alhálózat neveként, valamint a ASERG a vnet erőforráscsoport neve.
    
1. Most szüksége lesz egy statikus IP-címére, amelyet a fent definiált alhálózati hálózaton lévő virtuális géphez szeretne rendelni. Cserélje le a **privateipaddress tulajdonságot** erre a címnek a paraméter fájljában. Ha azt szeretné, hogy a virtuális gép IP-címet kapjon a helyi DHCP-kiszolgálóról, hagyja `privateIPAddress` üresen az értéket.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Mentse a paramétereket tartalmazó fájlt.

    Itt látható a cikkben használt JSON-minta.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Sablon üzembe helyezése virtuális gép létrehozásához

Telepítse a virtuális gép létrehozási sablonját `CreateVM.json` . Ez a sablon egy hálózati adaptert hoz létre a meglévő VNet, és virtuális gépet hoz létre a központilag telepített rendszerképből.

1. Futtassa az alábbi parancsot: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    A virtuális gép létrehozása 15-20 percet is igénybe vehet. Az alábbi példa egy sikeresen létrehozott virtuális gép kimenetét jeleníti meg.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    A parancsot aszinkron módon is futtathatja a `New-AzureRmResourceGroupDeployment` `–AsJob` paraméterrel. Íme egy minta kimenet, amikor a parancsmag a háttérben fut. Ezután lekérdezheti a parancsmag használatával létrehozott feladatok állapotát `Get-Job` .

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Ellenőrizze, hogy a virtuális gép üzembe helyezése sikeres volt-e. Futtassa az alábbi parancsot:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Csatlakozás virtuális géphez

Attól függően, hogy létrehozott-e Windows vagy Linux rendszerű virtuális gépet, a kapcsolódás lépései különbözőek lehetnek.

### <a name="connect-to-windows-vm"></a>Kapcsolódás Windows rendszerű virtuális géphez

A Windows rendszerű virtuális gépekhez való kapcsolódáshoz kövesse az alábbi lépéseket.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Kapcsolódás Linux rendszerű virtuális géphez

A Linux rendszerű virtuális gépekhez való kapcsolódáshoz kövesse az alábbi lépéseket.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager-parancsmagok](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)