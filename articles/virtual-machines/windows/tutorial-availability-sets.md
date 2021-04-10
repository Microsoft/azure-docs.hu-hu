---
title: Virtuális gépek üzembe helyezése rendelkezésre állási csoportokban Azure PowerShell használatával
description: Ismerje meg, hogy miként használhatók a Azure PowerShell a magasan elérhető virtuális gépek rendelkezésre állási csoportokban való üzembe helyezéséhez
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: mvc
ms.openlocfilehash: 178a29ea37195ddd2013ca5220663a75132beb24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555907"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-powershell"></a>Virtuális gépek létrehozása és üzembe helyezése rendelkezésre állási csoportokban Azure PowerShell használatával

Ebből az oktatóanyagból megtudhatja, hogyan növelheti a Virtual Machines (VM-EK) rendelkezésre állását és megbízhatóságát a rendelkezésre állási csoportok használatával. A rendelkezésre állási csoportok gondoskodnak arról, hogy az Azure-ban üzembe helyezett virtuális gépek több, elkülönített hardverkonfiguráció között legyenek elosztva a fürtben. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Az egy adott helyen lévő hardver több frissítési és a tartalék tartományra van osztva. A **frissítési tartomány** virtuális gépek és mögöttes fizikai hardverelemek csoportja, amelyek egyszerre indíthatók újra. Az egyazon **tartalék tartományba** tartozó virtuális gépek közös tárolóval, valamint közös áramforrással és hálózati kapcsolóval rendelkeznek.  

A [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)használatával létrehozhat egy rendelkezésre állási készletet. Ebben a példában a frissítési és a tartalék tartományok száma *2* , a rendelkezésre állási csoport neve pedig *myAvailabilitySet*.

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Hozzon létre egy felügyelt rendelkezésre állási készletet a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) és a `-sku aligned` paraméter használatával.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Virtuális gépek létrehozása rendelkezésre állási csoportban
A virtuális gépeket a rendelkezésre állási csoporton belül kell létrehozni annak biztosításához, hogy megfelelően legyenek elosztva a hardveren. A létrehozás után nem adhat hozzá meglévő virtuális gépet egy rendelkezésre állási csoporthoz. 


Amikor [új AzVM](/powershell/module/az.compute/new-azvm)hoz létre egy virtuális gépet, a `-AvailabilitySetName` paraméter használatával megadhatja a rendelkezésre állási csoport nevét.

Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre két virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm) a rendelkezésre állási csoporton belül.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

A két virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe. Ha befejeződött, két virtuális géppel rendelkezik majd elosztva a mögöttes hardveren. 

Ha megtekinti a rendelkezésre állási csoportot a portálon, az **erőforráscsoportok**  >  **myResourceGroupAvailability**  >  **myAvailabilitySet**, látnia kell, hogyan oszlanak meg a virtuális gépek a két hiba és a frissítési tartomány között.

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése 

Amikor létrehoz egy virtuális gépet egy rendelkezésre állási csoporton belül, tudnia kell, hogy milyen virtuálisgép-méretek érhetők el a hardveren. Használja a [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) parancsot a rendelkezésre állási csoportba központilag telepíthető virtuális gépek összes elérhető méretének lekéréséhez.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Az Azure Advisor ellenőrzése 

Azure Advisor segítségével további információkat kaphat a virtuális gépek rendelkezésre állásának javításáról. Azure Advisor elemzi a konfigurációt és a használat telemetria, majd az Azure-erőforrások költséghatékonyságának, teljesítményének, rendelkezésre állásának és biztonságának javítását segítő megoldásokat ajánl fel.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), válassza a **Minden szolgáltatás** lehetőséget, és írja be az **Advisor** kifejezést. Az Advisor-irányítópult személyre szabott javaslatokat jelenít meg a kiválasztott előfizetéshez. További információért lásd [az Azure Advisor használatának első lépéseit](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-méretezési csoportokat ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport létrehozása](tutorial-create-vmss.md)
