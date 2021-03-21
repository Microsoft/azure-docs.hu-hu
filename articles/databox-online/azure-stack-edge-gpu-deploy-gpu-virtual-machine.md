---
title: GPU-beli virtuális gépek áttekintése és üzembe helyezése az Azure Stack Edge Pro-eszközön
description: Ismerteti, hogyan lehet GPU virtuális gépeket (VM-ket) létrehozni és kezelni egy Azure Stack Edge Pro-eszközön sablonok használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ff805b758dce05a66764ab1ff08e53378c946362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438182"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>GPU virtuális gépek a Azure Stack Edge Pro-eszközhöz

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ez a cikk áttekintést nyújt a GPU virtuális gépekről (VM) az Azure Stack Edge Pro-eszközön. A cikk azt ismerteti, hogyan lehet GPU-alapú virtuális gépet létrehozni, majd telepíteni a GPU illesztőprogram-bővítményt a megfelelő NVIDIA-illesztőprogramok telepítéséhez. A GPU virtuális gép létrehozásához használja a Azure Resource Manager sablonokat, és telepítse a GPU illesztőprogram-bővítményt. 

Ez a cikk Azure Stack Edge Pro GPU-ra és a Azure Stack Edge Pro R-eszközökre vonatkozik.

## <a name="about-gpu-vms"></a>Tudnivalók a GPU-beli virtuális gépekről

Az Azure Stack Edge Pro-eszközök az NVIDIA Tesla T4 GPU-val 1 vagy 2. A GPU-gyorsított virtuális gépek számítási feladatainak ezeken az eszközökön való üzembe helyezéséhez használja a GPU-ra optimalizált virtuálisgép-méreteket. Például az NC T4 v3 sorozatot kell használni a T4 GPU-t tartalmazó következtetési feladatok üzembe helyezéséhez. 

További információ: [NC T4 v3 sorozatú virtuális gépek](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Támogatott operációs rendszerek és GPU-illesztőprogramok 

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat. 

Az NVIDIA GPU illesztőprogram-bővítmény telepíti a megfelelő NVIDIA CUDA-vagy GRID-illesztőprogramokat. A bővítményt a Azure Resource Manager sablonok használatával telepítheti vagy kezelheti.

### <a name="supported-os-for-gpu-extension-for-windows"></a>A Windowshoz készült GPU bővítmény támogatott operációs rendszere

Ez a bővítmény a következő operációs rendszereket támogatja: (OSs). Más verziók működhetnek, de nem teszteltek házon Azure Stack Edge Pro-eszközökön futó GPU-beli virtuális gépeken.

| Disztribúció | Verzió |
|---|---|
| Windows Server 2019 | Mag |
| Windows Server 2016 | Mag |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Támogatott operációs rendszer a Linux GPU-bővítményhez

Ez a bővítmény a következő operációsrendszer-disztribúciókat támogatja, attól függően, hogy az adott operációsrendszer-verzióhoz milyen illesztőprogram-támogatás vonatkozik. Más verziók működhetnek, de nem teszteltek házon Azure Stack Edge Pro-eszközökön futó GPU-beli virtuális gépeken.


| Disztribúció | Verzió |
|---|---|
| Ubuntu | 18,04 LTS |
| Red Hat Enterprise Linux | 7,4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU-beli virtuális gépek és Kubernetes

A GPU virtuális gépek eszközön való üzembe helyezése előtt tekintse át a következő szempontokat, ha az Kubernetes konfigurálva van az eszközön.

#### <a name="for-1-gpu-device"></a>1 – GPU-eszköz esetén: 

- **Hozzon létre egy GPU virtuális gépet, amelyet Kubernetes-konfiguráció követ az eszközön**: ebben a forgatókönyvben a GPU virtuális gép létrehozása és a Kubernetes konfigurálása is sikeres lesz. Ebben az esetben a Kubernetes nem fog hozzáférni a GPU-hoz.

- A **Kubernetes konfigurálása az eszközön, amelyet egy GPU virtuális gép létrehozása követ**: ebben a forgatókönyvben a KUBERNETES a GPU-t igényli az eszközön, és a virtuális gép létrehozása sikertelen lesz, mivel nem érhető el GPU-erőforrás.

#### <a name="for-2-gpu-device"></a>2 GPU-eszköz esetén

- **Hozzon létre egy GPU virtuális gépet, amelyet Kubernetes-konfiguráció követ az eszközön**: ebben a forgatókönyvben a létrehozott GPU virtuális gép egy GPU-t igényel az eszközön, és a Kubernetes-konfiguráció is sikeres lesz, és a fennmaradó egy GPU-t igényli. 

- **Hozzon létre két GPU virtuális gépet, majd a Kubernetes-konfigurációt az eszközön**: ebben a forgatókönyvben a két GPU virtuális gép igényli a két GPU-t az eszközön, és a Kubernetes sikeresen konfigurálva van GPU nélkül. 

- **Kubernetes konfigurálása az eszközön, majd egy GPU virtuális gép létrehozása**: ebben a forgatókönyvben a Kubernetes az eszközön található GPU-k és a virtuális gép létrehozása sikertelen lesz, mivel nem állnak rendelkezésre GPU-erőforrások.

Ha az eszközön és a Kubernetes futó GPU-beli virtuális gépek is konfigurálva vannak, akkor a virtuális gép bármikor felszabadítható (ha a Stop-AzureRmVM vagy a Remove-AzureRmVM használatával leállítja vagy eltávolítja a virtuális gépet), fennáll a kockázata annak, hogy a Kubernetes-fürt az eszközön elérhető összes GPU-t igényli. Ilyen esetben nem fogja tudni újraindítani az eszközön üzembe helyezett GPU-beli virtuális gépeket, vagy GPU-alapú virtuális gépeket létrehozni.


## <a name="create-gpu-vms"></a>GPU virtuális gépek létrehozása

A GPU virtuális gépek eszközön való üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Annak megállapítása, hogy az eszköz a Kubernetes-t is futtatja-e. Ha az eszköz Kubernetes fog futni, először létre kell hoznia a GPU virtuális gépet, majd konfigurálnia kell a Kubernetes. Ha a Kubernetes először van konfigurálva, akkor az összes rendelkezésre álló GPU-erőforrást igényli, és a GPU virtuális gép létrehozása sikertelen lesz.

1. [Töltse le a virtuálisgép-sablonokat és a paraméterek fájljait](https://aka.ms/ase-vm-templates) az ügyfélszámítógépre. Bontsa ki a munkakönyvtárként használni kívánt könyvtárba.

1. GPU-alapú virtuális gépek létrehozásához kövesse a [virtuális gép üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) -ban című témakör lépéseit a sablonok használatával, kivéve a következő különbségeket: 

    1. A számítási hálózat konfigurálása közben engedélyezze az internethez csatlakozó portot a számítási feladatokhoz. Ez lehetővé teszi a GPU-beli virtuális gépek GPU-bővítményeihez szükséges GPU-illesztőprogramok letöltését.

        Íme egy példa arra, hogy a 2. port csatlakozik az internethez, és a rendszer a számítási hálózat engedélyezésére használta. Ha azonosította, hogy a Kubernetes nincs szükség az előző lépésben, kihagyhatja a Kubernetes csomópont IP-címét és a külső szolgáltatás IP-hozzárendelését.

        ![Az internethez csatlakozó port számítási beállításainak engedélyezése](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Hozzon létre egy virtuális gépet a sablonok használatával. A GPU-s virtuálisgép-méretek megadásakor ügyeljen arra, hogy a NCasT4-v3 sorozatot használja, mivel ezek a GPU-beli virtuális `CreateVM.parameters.json` gépek esetében támogatottak. További információ: támogatott virtuálisgép- [méretek a GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)-beli virtuális gépekhez.

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. A GPU virtuális gép sikeres létrehozása után megtekintheti ezt a virtuális gépet a Azure Stack peremhálózati erőforrásban található virtuális gépek listájában a Azure Portalban.

        ![GPU virtuális gép a virtuális gépek listájában Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Válassza ki a virtuális gépet, és részletezse a részleteket. Másolja ki a virtuális géphez lefoglalt IP-címet.

    ![A Azure Portal GPU virtuális géphez lefoglalt IP-cím](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. A virtuális gép létrehozása után telepítse a GPU bővítményt a bővítmény sablonnal. Linux rendszerű virtuális gépek esetén lásd: a [GPU-bővítmény telepítése Linuxra](#gpu-extension-for-linux) és Windows rendszerű virtuális gépekre: [a GPU-bővítmény telepítése Windows](#gpu-extension-for-windows)rendszerre.

1. A GPU-bővítmény telepítésének ellenőrzéséhez kapcsolódjon a GPU virtuális géphez:
    1. Windows rendszerű virtuális gép használata esetén kövesse a [Kapcsolódás Windows rendszerű virtuális géphez](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm)című témakör lépéseit. [Ellenőrizze a telepítést](#verify-windows-driver-installation).
    1. Ha Linux rendszerű virtuális gépet használ, kövesse a [Kapcsolódás Linux rendszerű virtuális géphez](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)című témakör lépéseit. [Ellenőrizze a telepítést](#verify-linux-driver-installation).

1. Ha szükséges, a számítási hálózatot visszakapcsolhatja, amire szüksége van. 


> [!NOTE]
> Amikor az eszköz szoftverét a 2012-es verziójáról később frissíti, manuálisan le kell állítania a GPU virtuális gépeket.


## <a name="install-gpu-extension"></a>A GPU bővítmény telepítése

A virtuális gép operációs rendszerének függvényében a GPU-bővítményt a Windowshoz vagy Linuxra is telepítheti.

> [!NOTE]
> A GPU bővítmény telepítése előtt győződjön meg arról, hogy az eszközön a számítási hálózat számára engedélyezett port csatlakozik az internethez, és rendelkezik hozzáféréssel. A GPU-illesztőprogramok az Internet-hozzáférésen keresztül tölthetők le.

### <a name="gpu-extension-for-windows"></a>GPU-bővítmény a Windowshoz

Egy meglévő virtuális gép NVIDIA GPU-illesztőprogramjainak telepítéséhez szerkessze a `addGPUExtWindowsVM.parameters.json` paramétereket tartalmazó fájlt, majd telepítse a sablont `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Paraméterek szerkesztése fájl

A fájl `addGPUExtWindowsVM.parameters.json` a következő paramétereket veszi figyelembe:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Itt látható egy példa a cikkben használt paraméterre:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezése `addGPUextensiontoVM.json` . Ez a sablon egy meglévő virtuális gépre telepíti a bővítményt. Futtassa az alábbi parancsot:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> A bővítmény üzembe helyezése hosszú ideig futó feladatot jelent, és körülbelül 10 percet vesz igénybe.

Itt látható egy mintakimenet:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Üzembe helyezés nyomon követése

Egy adott virtuális gép bővítményeinek központi telepítési állapotának vizsgálatához futtassa a következő parancsot: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Itt látható egy mintakimenet:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

A bővítmény végrehajtásának kimenete a következő fájlba lesz naplózva. `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`A telepítés állapotának nyomon követéséhez tekintse meg ezt a fájlt. 


A sikeres telepítést a és a érték `message` jelzi `Enable Extension` `status` `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>A Windows-illesztőprogram telepítésének ellenőrzése

Jelentkezzen be a virtuális gépre, és futtassa az illesztőprogrammal telepített NVIDIA-SMI parancssori segédprogramot. A a `nvidia-smi.exe` következő helyen található:  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Ha nem látja a fájlt, lehetséges, hogy az illesztőprogram telepítése továbbra is fut a háttérben. Várjon 10 percet, és próbálkozzon újra.

Ha az illesztőprogram telepítve van, az alábbi példához hasonló kimenet jelenik meg: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

További információ: [az NVIDIA GPU illesztőprogram-bővítmény a Windowshoz](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>GPU-bővítmény Linux rendszerhez

Egy meglévő virtuális gép NVIDIA GPU-illesztőprogramjainak telepítéséhez szerkessze a paramétereket tartalmazó fájlt, majd telepítse a sablont `addGPUextensiontoVM.json` . Az Ubuntu és a Red Hat Enterprise Linux (RHEL) paraméterek fájljai az alábbi szakaszokban leírtak szerint vannak.

#### <a name="edit-parameters-file"></a>Paraméterek szerkesztése fájl

Ubuntu használata esetén a `addGPUExtLinuxVM.parameters.json` fájl a következő paramétereket veszi figyelembe:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Red Hat Enterprise Linux (RHEL) használata esetén a `addGPUExtensionRHELVM.parameters.json` fájl a következő paramétereket veszi figyelembe:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Az alábbi példa egy Ubuntu-paramétert tartalmazó fájlt használ a cikkben:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezése `addGPUextensiontoVM.json` . Ez a sablon egy meglévő virtuális gépre telepíti a bővítményt. Futtassa az alábbi parancsot:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> A bővítmény üzembe helyezése hosszú ideig futó feladatot jelent, és körülbelül 10 percet vesz igénybe.

Itt látható egy mintakimenet:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Központi telepítés állapotának nyomon követése    
    
A Template deployment hosszú ideig futó feladatok. Egy adott virtuális gép bővítményeinek központi telepítési állapotának vizsgálatához nyisson meg egy másik PowerShell-munkamenetet (Futtatás rendszergazdaként). Futtassa az alábbi parancsot: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Itt látható egy mintakimenet: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Ha a telepítés befejeződött, a `ProvisioningState` módosításai `Succeeded` .

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>A Linux-illesztőprogram telepítésének ellenőrzése

Az illesztőprogram telepítésének ellenőrzéséhez kövesse az alábbi lépéseket:

1. Kapcsolódjon a GPU virtuális géphez. Kövesse a [Kapcsolódás Linux rendszerű virtuális géphez](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)című témakör utasításait. 

    Itt látható egy mintakimenet:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Futtassa az illesztőprogrammal telepített NVIDIA-SMI parancssori segédprogramot. Ha az illesztőprogram sikeresen telepítve van, akkor futtathatja a segédprogramot, és megtekintheti a következő kimenetet:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

További információ: [az NVIDIA GPU illesztőprogram-bővítmény a Linux rendszerhez](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>GPU-bővítmény eltávolítása

A GPU bővítmény eltávolításához használja a következő parancsot:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Itt látható egy mintakimenet:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager-parancsmagok](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)