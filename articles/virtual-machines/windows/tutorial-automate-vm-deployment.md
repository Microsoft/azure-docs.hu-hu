---
title: Oktatóanyag – alkalmazások telepítése Windows rendszerű virtuális gépen az Azure-ban
description: Ennek az oktatóanyagnak a követésével elsajátíthatja, hogyan használja az szkriptbővítményt szkriptek futtatására és alkalmazások Windows rendszerű virtuális gépekre való telepítésére az Azure-ban
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c7c0fc8aaa3779390060ae283f1427f4955ed421
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555975"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Oktatóanyag – Alkalmazások telepítése Windows virtuális gépekre az Azure-ban az egyéni szkriptbővítménnyel

A virtuális gépek gyors és konzisztens konfigurálásához használhatja a [Windowshoz készült egyéni parancsfájl-bővítményt](../extensions/custom-script-windows.md). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * IIS telepítése az egyéni szkriptek bővítményével
> * Az egyéni szkriptek bővítményét használó virtuális gép létrehozása
> * Futó IIS-hely megtekintése a bővítmény alkalmazása után

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="custom-script-extension-overview"></a>Az egyéni szkriptek bővítményének áttekintése
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében.

Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Az egyéni szkriptek bővítménye Windows és Linux rendszerű virtuális gépeken is használható.


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát a [Get-hitelesítőadat használatával](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem léteznek, létrejön a *myResourceGroupAutomate* nevű erőforráscsoport és a támogató hálózati erőforrások. A webes forgalom engedélyezéséhez a parancsmag megnyitja a *80*-as portot is.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.


## <a name="automate-iis-install"></a>Az IIS-telepítés automatizálása
A [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) használatával telepítse az egyéni szkriptek bővítményét. A bővítmény a `powershell Add-WindowsFeature Web-Server` parancs futtatásával telepíti az IIS-webkiszolgálót, majd a *Default.htm* lapot frissítve megjeleníti a virtuális gép eszköznevét:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Webhely tesztelése
Szerezze be a terheléselosztó nyilvános IP-címét a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott *myPublicIPAddress* IP-címét kéri le:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![IIS-webhely futtatása](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az IIS telepítését automatizálta egy virtuális gépen. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * IIS telepítése az egyéni szkriptek bővítményével
> * Az egyéni szkriptek bővítményét használó virtuális gép létrehozása
> * Futó IIS-hely megtekintése a bővítmény alkalmazása után

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket.

> [!div class="nextstepaction"]
> [Egyéni virtuálisgép-rendszerképek létrehozása](./tutorial-custom-images.md)
