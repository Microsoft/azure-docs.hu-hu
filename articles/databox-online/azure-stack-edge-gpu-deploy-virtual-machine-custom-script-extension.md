---
title: Egyéni parancsfájl-bővítmények használata a Azure Stack Edge Pro-eszközön futó virtuális gépekhez
description: Ismerteti, hogyan telepíthet egyéni parancsfájl-bővítményeket egy Azure Stack Edge Pro-eszközön futó virtuális gépeken sablonok használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2d2e7d403ab3e9cc7e8e17de53b6e821ec24caa1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438012"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Egyéni parancsfájl-bővítmény üzembe helyezése az Azure Stack Edge Pro-eszközön futó virtuális gépeken

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Az egyéni szkriptek bővítménye parancsfájlokat vagy parancsokat futtat a Azure Stack Edge Pro-eszközökön futó virtuális gépeken. Ez a cikk részletesen ismerteti, hogyan telepítheti és futtathatja az egyéni parancsfájl-bővítményt Azure Resource Manager sablon használatával. 

Ez a cikk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkozik.

## <a name="about-custom-script-extension"></a>Az egyéni szkriptek bővítményének ismertetése

Az egyéni szkriptek bővítménye az üzembe helyezés utáni konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs/felügyeleti feladat esetében hasznos. A szkripteket letöltheti az Azure Storage-ból vagy más elérhető Internet-helyről, vagy parancsfájlokat vagy parancsokat is megadhat a bővítmény futtatókörnyezetének.

Az egyéni szkriptek bővítménye Azure Resource Manager-sablonokkal integrálódik. Azt is futtathatja az Azure CLI, a PowerShell vagy az Azure Virtual Machines REST API használatával.

## <a name="os-for-custom-script-extension"></a>Operációs rendszer egyéni parancsfájl-kiterjesztéshez

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Támogatott operációs rendszer a Windows egyéni parancsfájl-bővítményéhez

A Windowshoz készült egyéni parancsfájl-bővítmény a következő OSs-ben fog futni. Más verziók működhetnek, de nem teszteltek házon Azure Stack Edge Pro-eszközökön futó virtuális gépeken.

| Disztribúció | Verzió |
|---|---|
| Windows Server 2019 | Mag |
| Windows Server 2016 | Mag |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Támogatott operációs rendszer a Linux egyéni parancsfájl-bővítményéhez

A Linux rendszerhez készült egyéni parancsfájl-bővítmény a következő OSs-ben fog futni. Más verziók működhetnek, de nem teszteltek házon Azure Stack Edge Pro-eszközökön futó virtuális gépeken.

| Disztribúció | Verzió |
|---|---|
| Linux: Ubuntu | 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Előfeltételek

1. [Töltse le a virtuálisgép-sablonokat és a paraméterek fájljait](https://aka.ms/ase-vm-templates) az ügyfélszámítógépre. Bontsa ki a letöltést egy olyan könyvtárba, amelyet munkakönyvtárként fog használni.

1. Az eszközön létre kell hoznia és telepítenie kell egy virtuális gépet. Virtuális gépek létrehozásához kövesse a [virtuális gép üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)-ban című témakör összes lépését sablonok használatával.

    Ha le kell töltenie egy parancsfájlt, például a GitHubról vagy az Azure Storage-ból kívülről, a számítási hálózat konfigurálása közben engedélyezze az internethez csatlakozó portot a számítási feladatokhoz. Ez lehetővé teszi a szkript letöltését.

    A következő példában a 2-es port csatlakozik az internethez, és a rendszer a számítási hálózat engedélyezésére használta. Ha azt állapította meg, hogy a Kubernetes nincs szükség az előző lépésben, kihagyhatja a Kubernetes csomópont IP-címét és a külső szolgáltatás IP-hozzárendelését.

    ![Az internethez csatlakozó port számítási beállításainak engedélyezése](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Egyéni szkriptek bővítményének telepítése

A virtuális gép operációs rendszerének függvényében a Windows vagy Linux rendszerhez készült egyéni parancsfájl-bővítményt is telepíthet.
 

### <a name="custom-script-extension-for-windows"></a>Egyéniszkript-bővítmény Windows rendszerre

Ha az eszközön futó virtuális gép esetében szeretné telepíteni a Windowshoz készült egyéni parancsfájl-bővítményt, szerkessze a `addCSExtWindowsVM.parameters.json` paramétereket tartalmazó fájlt, majd telepítse a sablont `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Paraméterek szerkesztése fájl

A fájl `addCSExtWindowsVM.parameters.json` a következő paramétereket veszi figyelembe:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Adja meg a virtuális gép nevét, a bővítmény nevét és a végrehajtani kívánt parancsot.

Itt látható a cikkben használt minta-paraméter.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezése `addCSextensiontoVM.json` . Ez a sablon egy meglévő virtuális gépre telepíti a bővítményt. Futtassa az alábbi parancsot:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> A bővítmény üzembe helyezése hosszú ideig futó feladatot jelent, és körülbelül 10 percet vesz igénybe.

Íme egy példa kimenet:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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
Íme egy példa kimenet:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

A bővítmény kimenete a cél virtuális gép következő mappájában található fájlokra van naplózva. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok a cél virtuális gép következő mappájába lesznek letöltve.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
ahol a <n> decimális egész szám, amely változhat a bővítmény végrehajtásai között. Az 1. * érték megegyezik a bővítmény tényleges, aktuális `typeHandlerVersion` értékével. Például a példány tényleges könyvtára volt `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


Ebben az esetben az egyéni bővítményre végrehajtandó parancs a következő volt: `md C:\\Users\\Public\\Documents\\test` . A bővítmény sikeres telepítésekor ellenőrizheti, hogy a könyvtár a virtuális gépen lett-e létrehozva a parancsban megadott elérési úton. 


### <a name="custom-script-extension-for-linux"></a>Egyéni parancsfájl-kiterjesztés Linux rendszerhez

Ha az eszközön futó virtuális gép esetében szeretné telepíteni a Windowshoz készült egyéni parancsfájl-bővítményt, szerkessze a `addCSExtLinuxVM.parameters.json` paramétereket tartalmazó fájlt, majd telepítse a sablont `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Paraméterek szerkesztése fájl

A fájl `addCSExtLinuxVM.parameters.json` a következő paramétereket veszi figyelembe:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Adja meg a virtuális gép nevét, a bővítmény nevét és a végrehajtani kívánt parancsot.

Ebben a cikkben egy példaként használt paramétert tartalmazó fájlt talál:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> A bővítmény üzembe helyezése hosszú ideig futó feladatot jelent, és körülbelül 10 percet vesz igénybe.

Íme egy példa kimenet:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

A `commandToExecute` be lett állítva egy fájl létrehozására `file2.txt` a `/home/Administrator` címtárban, és a fájl tartalma a következő: `some text` . Ebben az esetben ellenőrizheti, hogy a fájl a megadott elérési úton lett-e létrehozva.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Központi telepítés állapotának nyomon követése    
    
A Template deployment hosszú ideig futó feladatok. Egy adott virtuális gép bővítményeinek központi telepítési állapotának vizsgálatához nyisson meg egy másik PowerShell-munkamenetet (Futtatás rendszergazdaként). Futtassa az alábbi parancsot: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Íme egy példa kimenet: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Egyéni szkriptek bővítményének eltávolítása

Az egyéni szkriptek bővítményének eltávolításához használja a következő parancsot:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Íme egy példa kimenet:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager-parancsmagok](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
