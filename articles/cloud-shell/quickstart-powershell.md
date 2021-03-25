---
title: Azure Cloud Shell rövid útmutató – PowerShell
description: Megtudhatja, hogyan használhatja a PowerShellt a böngészőben a Azure Cloud Shell használatával.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 7ff58c4e463b4ad47680b9140403e9ae5e22b057
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045280"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>A PowerShell rövid útmutatója Azure Cloud Shell

Ez a dokumentum részletesen ismerteti, hogyan használható a PowerShell a [Azure Portal](https://portal.azure.com/)Cloud Shellban.

> [!NOTE]
> Azure Cloud Shell rövid útmutatóban is elérhető [bash](quickstart.md) .

## <a name="start-cloud-shell"></a>Cloud Shell indítása

1. Kattintson **Cloud Shell** gombra a Azure Portal felső navigációs sávján.

   ![A Azure Portal Azure Cloud Shell indítását bemutató képernyőkép.](media/quickstart-powershell/shell-icon.png)

2. Válassza ki a PowerShell-környezetet a legördülő listából, és az Azure Drive-ban lesz `(Azure:)`

   ![A Azure Cloud Shell PowerShell-környezetének kijelölését bemutató képernyőkép.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell-parancsok futtatása

Futtasson rendszeres PowerShell-parancsokat a Cloud Shellban, például:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>Kommunikáció a virtuális gépekkel

A jelenlegi előfizetéshez tartozó összes virtuális gépet megtalálhatja a `VirtualMachines` címtár használatával.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell-parancsfájl meghívása távoli virtuális gépek között

 > [!WARNING]
 > Tekintse meg az Azure-beli [virtuális gépek távoli felügyeletének hibaelhárítását ismertető témakört](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Feltételezve, hogy rendelkezik egy virtuális géppel, a MyVM1 a `Invoke-AzVMCommand` távoli gépen egy PowerShell-szkriptet is meghívhat.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  A VirtualMachines címtárat először is megnyithatja, és a következőképpen futtathatja `Invoke-AzVMCommand` .

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktív bejelentkezés egy távoli virtuális gépre

Az használatával `Enter-AzVM` interaktív módon bejelentkezhet az Azure-ban futó virtuális gépekre.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

A könyvtárat a következőképpen is megnyithatja `VirtualMachines` és futtathatja `Enter-AzVM`

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>WebApps felderítése

A címtárba való belépéssel `WebApps` könnyedén megtekintheti a webalkalmazások erőforrásait

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Ha SSH-val szeretne hitelesítést végezni a kiszolgálókon vagy virtuális gépeken, hozza a nyilvános titkos kulcspár Cloud Shell, és tegye közzé a nyilvános kulcsot a `authorized_keys` távoli gépen, például: `/home/user/.ssh/authorized_keys` .

> [!NOTE]
> Létrehozhat egy nyilvános SSH-kulcsot, `ssh-keygen` és közzéteheti őket a `$env:USERPROFILE\.ssh` Cloud Shellban.

### <a name="using-ssh"></a>Az SSH használata

Az új [virtuálisgép-konfiguráció](../virtual-machines/linux/quick-create-powershell.md) Azure PowerShell parancsmagokkal történő létrehozásához kövesse az alábbi utasításokat.
Mielőtt `New-AzVM` megkezdi az üzembe helyezést, adjon hozzá egy nyilvános SSH-kulcsot a virtuális gép konfigurációjához.
Az újonnan létrehozott virtuális gép tartalmazni fogja a nyilvános kulcsot a `~\.ssh\authorized_keys` helyen, így lehetővé teszi a hitelesítő adatokkal való ingyenes SSH-munkamenetet a virtuális géphez.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Elérhető parancsok listázása

`Azure`A meghajtó területen adja `Get-AzCommand` meg a környezetfüggő Azure-parancsok beolvasását.

Azt is megteheti, `Get-Command *az* -Module Az.*` hogy bármikor megkeresi az elérhető Azure-parancsokat.

## <a name="install-custom-modules"></a>Egyéni modulok telepítése

A futtatásával `Install-Module` modulokat telepíthet a [PowerShell-galériaból][gallery].

## <a name="get-help"></a>Get-Help

Írja be `Get-Help` a Azure Cloud Shell PowerShell-lel kapcsolatos információkat.

```azurepowershell-interactive
Get-Help
```

Egy adott parancs esetében továbbra is `Get-Help` egy parancsmag követhető.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Az adattároláshoz használjon Azure Files

Létrehozhat egy parancsfájlt, megadhatja `helloworld.ps1` , és mentheti a `clouddrive` alkalmazásba a rendszerhéj-munkamenetek között.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Amikor legközelebb a PowerShellt használja Cloud Shellban, a `helloworld.ps1` fájl a `$HOME\clouddrive` Azure Files-megosztást csatlakoztató könyvtár alatt fog létezni.

## <a name="use-custom-profile"></a>Egyéni profil használata

A PowerShell-környezet testreszabható a PowerShell-profil (ok) létrehozásával `profile.ps1` (vagy `Microsoft.PowerShell_profile.ps1` ).
Mentse azt a `$profile.CurrentUserAllHosts` (vagy `$profile.CurrentUserAllHosts` ) alatt, hogy az Cloud Shell-munkamenetben lévő összes PowerShellben betölthető legyen.

A profilok létrehozásával kapcsolatban tekintse meg a [About Profiles című témakört][profile].

## <a name="use-git"></a>A git használata

Git-tárház klónozásához a Cloud Shell létre kell hoznia egy [személyes hozzáférési jogkivonatot][githubtoken] , és azt felhasználónévként kell használni. Ha rendelkezik a jogkivonattal, a következő módon klónozott a tárházat:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Kilépés a rendszerhéjból

`exit`A munkamenet megszakításához írja be a következőt:.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
