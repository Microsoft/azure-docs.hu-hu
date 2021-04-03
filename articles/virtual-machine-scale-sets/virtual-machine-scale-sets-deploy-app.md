---
title: Alkalmazás üzembe helyezése Azure-beli virtuálisgép-méretezési csoporton
description: Megtudhatja, hogyan helyezhet üzembe alkalmazásokat Linux és Windows rendszerű virtuálisgép-példányokon a méretezési csoportokban
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079591"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokon

Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Ez a cikk bemutatja, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket egy méretezési csoport példányaihoz, vagy automatikusan futtathat telepítési parancsfájlokat a meglévő virtuálisgép-példányokon. Azt is megtudhatja, hogyan kezelheti az alkalmazás-vagy operációsrendszer-frissítéseket egy méretezési csoporton belül.


## <a name="build-a-custom-vm-image"></a>Egyéni virtuálisgép-rendszerkép létrehozása
Ha az egyik Azure platform-rendszerképet használja a méretezési csoport példányainak létrehozásához, nincs szükség további szoftver telepítésére vagy konfigurálására. Automatizálhatja ezeknek az összetevőknek a telepítését, azonban a hozzáadja a virtuálisgép-példányoknak a méretezési csoportokhoz való kiépítéséhez szükséges időt. Ha a virtuálisgép-példányok számos konfigurációs módosítását alkalmazza, akkor ezek a konfigurációs parancsfájlok és feladatok esetében is felügyeleti terheléssel rendelkeznek.

A virtuális gép üzembe helyezéséhez és az idő csökkentéséhez létrehozhat egy egyéni virtuálisgép-rendszerképet, amely készen áll az alkalmazás futtatására, amint egy példány üzembe lett állítva a méretezési csoportba. A méretezési csoportokkal rendelkező egyéni virtuálisgép-rendszerképek létrehozásáról és használatáról további információt a következő oktatóanyagokban talál:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Alkalmazás telepítése az egyéni szkriptek bővítményével
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az alkalmazások egyéni parancsfájl-Bővítménysel történő telepítésével kapcsolatos további információkért tekintse meg a következő oktatóanyagokat:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sablon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Alkalmazások telepítése Windows rendszerű virtuális gépre PowerShell DSC-vel
A [PowerShell desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) egy felügyeleti platform, amely meghatározza a célszámítógépek konfigurációját. A DSC-konfigurációk határozzák meg, hogy mit kell telepíteni a gépre, és hogyan kell konfigurálni a gazdagépet. A helyi Configuration Manager (LCD) motor minden olyan cél csomóponton fut, amely leküldéses konfigurációk alapján dolgozza fel a kért műveleteket.

A PowerShell DSC bővítmény lehetővé teszi a virtuálisgép-példányok testreszabását a méretezési csoportokban a PowerShell használatával. A következő példa:

- Arra utasítja a virtuálisgép-példányokat, hogy töltsön le egy DSC-csomagot a GitHubról *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Beállítja a bővítményt a telepítési parancsfájl futtatásához. `configure-http.ps1`
- Lekéri a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) rendelkező méretezési csoport adatait
- Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) használatával alkalmazza a bővítményt a virtuálisgép-példányokra.

A DSC-bővítmény a *myResourceGroup* nevű erőforráscsoport *myScaleSet* virtuálisgép-példányaira lesz alkalmazva. Adja meg a saját nevét a következőképpen:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Ha a méretezési csoport frissítési szabályzata *kézi*, frissítse a virtuálisgép-példányokat az [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Ez a parancsmag a frissített méretezési csoport konfigurációját alkalmazza a virtuálisgép-példányokra, és telepíti az alkalmazást.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Alkalmazás telepítése Linux rendszerű virtuális gépre a Cloud-init használatával
A [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítás során fut, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

További információ, például *cloud-init.txt* fájl: az [Azure-beli virtuális gépek testreszabása a Cloud-init használatával](../virtual-machines/linux/using-cloud-init.md).

Méretezési csoport létrehozásához és egy Cloud-init fájl használatához adja hozzá a `--custom-data` paramétert az az [vmss Create](/cli/azure/vmss) parancshoz, és adja meg a Cloud-init fájl nevét. A következő példában létrehozunk egy *myScaleSet* nevű méretezési készletet a *myResourceGroup* -ben, és egy *cloud-init.txt* nevű fájllal konfigurálja a virtuálisgép-példányokat. Adja meg a saját nevét a következőképpen:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Alkalmazások telepítése operációs rendszer frissítéseivel
Ha új operációsrendszer-kiadás érhető el, használhatja vagy felépítheti az új egyéni rendszerképeket, és [telepítheti az operációs rendszer frissítéseit](virtual-machine-scale-sets-upgrade-scale-set.md) egy méretezési csoportba. Minden virtuálisgép-példány a megadott legújabb képre frissül. Használhatja az alkalmazást előre telepített alkalmazással, az egyéni szkriptek bővítményével vagy a PowerShell DSC-vel, hogy az alkalmazás automatikusan elérhető legyen a frissítés végrehajtása során. Előfordulhat, hogy a folyamat végrehajtása során meg kell terveznie az alkalmazások karbantartását, hogy ne legyenek kompatibilitási problémák.

Ha egy egyéni virtuálisgép-rendszerképet használ előre telepített alkalmazással, az alkalmazás frissítéseit egy központi telepítési folyamattal integrálhatja az új lemezképek létrehozásához és az operációs rendszer frissítéseinek üzembe helyezéséhez a méretezési csoporton belül. Ez a megközelítés lehetővé teszi, hogy a folyamat felvegye a legújabb alkalmazás-buildeket, hozzon létre és érvényesítse a virtuálisgép-lemezképeket, majd frissítse a méretezési csoport virtuálisgép-példányait. Ha olyan központi telepítési folyamatot szeretne futtatni, amely az alkalmazások egyéni virtuálisgép-rendszerképeken való üzembe helyezését és központi telepítését végzi, [létrehozhat egy csomagoló rendszerképet, és üzembe helyezheti az Azure DevOps Services szolgáltatást](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), vagy használhat más platformot, például a [Spinnaker](https://www.spinnaker.io/) vagy a [Jenkinst](https://jenkins.io/).


## <a name="next-steps"></a>Következő lépések
Amikor alkalmazásokat hoz létre és telepít a méretezési csoportokban, tekintse át a [méretezési csoport kialakításának áttekintését](virtual-machine-scale-sets-design-overview.md). A méretezési csoport kezelésével kapcsolatos további információkért lásd: [a méretezési csoport kezelése a PowerShell használatával](./virtual-machine-scale-sets-manage-powershell.md).
