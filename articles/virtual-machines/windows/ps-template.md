---
title: Windows rendszerű virtuális gép létrehozása sablonból az Azure-ban
description: Egy Resource Manager-sablon és egy PowerShell használatával egyszerűen létrehozhat egy új Windowsos virtuális gépet.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe28be1d7c08c24b728de2476cb2210c6e5bd0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82098322"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Windows rendszerű virtuális gép létrehozása Resource Manager-sablonból

Megtudhatja, hogyan hozhat létre Windows rendszerű virtuális gépeket Azure Resource Manager sablonnal, és hogyan Azure PowerShell az Azure Cloud shellben. A cikkben használt sablon egyetlen alhálózattal rendelkező, Windows Server rendszert futtató virtuális gépet helyez üzembe egy új virtuális hálózaton. Linux rendszerű virtuális gépek létrehozásával kapcsolatban lásd: [Linux rendszerű virtuális gép létrehozása Azure Resource Manager-sablonokkal](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az Azure-beli virtuális gépek létrehozása általában két lépést tartalmaz:

- Hozzon létre egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni.
- Virtuális gépet hoz létre.

Az alábbi példa egy virtuális gépet hoz létre egy [Azure Gyorsindítás sablonból](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). A sablon egy másolata:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

A PowerShell-szkript futtatásához válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**parancsot:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Ha az Azure Cloud Shell helyett a PowerShell helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz a Azure PowerShell modulra van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

Az előző példában egy GitHubban tárolt sablont adott meg. Letölthet vagy létrehozhat egy sablont is, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

Néhány további információforrás:

- A Resource Manager-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/)talál további információt.
- Az Azure-beli virtuális gépek sémáinak megtekintéséhez lásd: [Azure-sablonok referenciája](/azure/templates/microsoft.compute/allversions).
- A virtuálisgép-sablonok további mintáinak megtekintéséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Az előző parancsfájl utolsó PowerShell-parancsa megjeleníti a virtuális gép nevét. A virtuális géphez való kapcsolódáshoz lásd: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](./connect-logon.md).

## <a name="next-steps"></a>Következő lépések

- Ha probléma merült fel az üzembe helyezéssel kapcsolatban, tekintse meg a [gyakori Azure-telepítési hibák elhárítását Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Megtudhatja, hogyan hozhat létre és kezelhet virtuális gépeket a [Windows rendszerű virtuális gépek létrehozása és kezelése a Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A sablonok létrehozásával kapcsolatos további tudnivalókért tekintse meg a telepített erőforrások típusának JSON-szintaxisát és tulajdonságait:

- [Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
