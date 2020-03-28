---
title: Virtuálisgép- és tárfiók létrehozása méretezhető alkalmazáshoz az Azure-ban
description: Ismerkedjen meg a skálázható alkalmazások futtatására szolgáló virtuális gépek üzembe helyezésével az Azure Blob Storage használatával
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 60bb227af7cc886bb35cbd045cc8a27cf82ae903
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75372244"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Virtuális gép és tárfiók létrehozása egy méretezhető alkalmazás számára

Ez az oktatóanyag egy sorozat első része. Ez az oktatóanyag egy olyan alkalmazás üzembe helyezését mutatja be, amely nagy mennyiségű véletlenszerű adat fel- és letöltését végzi egy Azure Storage-fiók használatával. Az oktatóanyagban egy virtuális gépen futó konzolalkalmazást hoz majd létre, amely nagy mennyiségű adatot tölt fel és le egy tárfiókba vagy tárfiókból.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Create a storage account
> * Virtuális gép létrehozása
> * Egyéni szkriptbővítmény konfigurálása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az Azure PowerShell-modul, az Az 0.7-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Create a storage account
 
A minta 50 nagy méretű fájlt tölt fel egy Azure Storage-fiókban lévő blobtárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Hozzon létre egy tárfiókot a [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) paranccsal létrehozott erőforráscsoportban.

A következő parancsban a `<blob_storage_account>` helyőrző helyett írja be a saját globálisan egyedi Blob Storage-fióknevét.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

Hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm)segítségével.

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Az üzembe helyezés konfigurálása

Ebben az oktatóanyagban bizonyos előfeltételeket kell telepíteni a virtuális gépen. Az egyéni szkriptbővítmény olyan PowerShell-szkriptek futtatására szolgál, amelyek a következő feladatokat hajtják végre:

> [!div class="checklist"]
> * A .NET Core 2.0 telepítése
> * A Chocolatey telepítése
> * A GIT telepítése
> * A mintatár klónozása
> * NuGet-csomagok visszaállítása
> * 50 1 GB-os véletlenszerű adatokkal feltöltött fájlt hoz létre

Futtassa az alábbi parancsmagot a virtuális gép konfigurációjának véglegesítéséhez. Ez a lépés 5–15 percet is igénybe vehet.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>További lépések

A sorozat első részében megismerkedett a tárfiókok létrehozásával, a virtuális gépek üzembe helyezésével, illetve a virtuális gépnek az előírt előfeltételek szerinti konfigurálásával, például a következőkkel:

> [!div class="checklist"]
> * Create a storage account
> * Virtuális gép létrehozása
> * Egyéni szkriptbővítmény konfigurálása

A sorozat második részében nagy mennyiségű adatot fog feltölteni egy tárfiókba exponenciális újrapróbálkozás és párhuzamosság alkalmazásával.

> [!div class="nextstepaction"]
> [Nagy mennyiségű nagyméretű fájl párhuzamos feltöltése egy tárfiókba](storage-blob-scalable-app-upload-files.md)
