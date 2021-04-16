---
title: Alkalmazás üzembe helyezése Azure-beli virtuálisgép-méretezési készletben
description: Megtudhatja, hogyan helyezhet üzembe alkalmazásokat Linux- és Windows-alapú virtuálisgép-példányon egy méretezési készletben
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 078c78f9fe9e52ee2a71784d5c5ae5c2a478fbe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484255"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokon

Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Ez a cikk bemutatja, hogyan hozhat létre egyéni virtuálisgép-rendszerképet a méretezési csoportban lévő példányokhoz, vagy automatikusan futtathat telepítési szkripteket a meglévő virtuálisgép-példányon. Azt is megtudhatja, hogyan kezelheti az alkalmazás- vagy operációsrendszer-frissítéseket egy méretezési készletben.


## <a name="build-a-custom-vm-image"></a>Egyéni virtuálisgép-rendszerkép létrehozása
Ha az Azure platform egyik rendszerképét használja a példányok létrehozásához a méretezési csoportban, nem lesz további szoftver telepítve vagy konfigurálva. Automatizálhatja ezeknek az összetevőknek a telepítését, azonban ez növeli a virtuálisgép-példányok méretezési készletekben való építéséhez szükséges időt. Ha sok konfigurációs módosítást alkalmaz a virtuálisgép-példányon, akkor a konfigurációs szkriptek és feladatok felügyeleti többletterheléssel jár.

A konfigurációkezelés és a virtuális gép üzembehelyéhez szükséges idő csökkentése érdekében létrehozhat egy egyéni virtuálisgép-rendszerképet, amely azonnal készen áll az alkalmazás futtatására, amint üzembe van állítva egy példány a méretezési csoportban. Az egyéni virtuálisgép-rendszerkép méretezési csoportokkal való létrehozásáról és használatával kapcsolatos további információkért tekintse meg a következő oktatóanyagokat:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Alkalmazás telepítése az egyéni szkriptbővítmény használatával
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az alkalmazások egyéni szkriptbővítményekkel való telepítésével kapcsolatos további információkért tekintse meg a következő oktatóanyagokat:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sablon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Alkalmazás telepítése Windows rendszerű virtuális gépre a PowerShell DSC használatával
[A PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) egy felügyeleti platform, amely meghatározza a célgépek konfigurációját. A DSC-konfigurációk határozzák meg, hogy mit kell telepíteni a gépre, és hogyan kell konfigurálni a gazdagépet. A helyi Konfigurációkezelő (LCM) motor minden célcsomóponton fut, amely leküldett konfigurációk alapján dolgozza fel a kért műveleteket.

A PowerShell DSC-bővítmény lehetővé teszi a méretezési készletben lévő virtuálisgép-példányok testreszabását a PowerShell használatával. Az alábbi példa:

- Arra utasítja a virtuálisgép-példányokat, hogy töltsenek le egy DSC-csomagot a GitHubról – *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Beállítja a bővítményt egy telepítési szkript futtatásához – `configure-http.ps1`
- Lekért információk egy méretezési készletről a [Get-AzVmss-sel](/powershell/module/az.compute/get-azvmss)
- Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) bővítményt alkalmazza a virtuálisgép-példányra

A DSC-bővítmény a myResourceGroup nevű erőforráscsoportban lévő *myScaleSet* *virtuálisgép-példányra lesz alkalmazva.* Adja meg a saját nevét az alábbiak szerint:

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

Ha a méretezési készlet frissítési szabályzata *manuális,* frissítse a virtuálisgép-példányokat az [Update-AzVmssInstance segítségével.](/powershell/module/az.compute/update-azvmssinstance) Ez a parancsmag alkalmazza a frissített méretezésikészlet-konfigurációt a virtuálisgép-példányokhoz, és telepíti az alkalmazást.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Alkalmazás telepítése Linux rendszerű virtuális gépre a cloud-init használatával
A [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítás során fut, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

További információ, például egy példacloud-init.txt: Azure-beli virtuális gépek testreszabása *a* [cloud-init használatával.](../virtual-machines/linux/using-cloud-init.md)

Méretezési csoport létrehozásához és cloud-init fájl használathoz adja hozzá a paramétert az az vmss create parancshoz, és adja meg `--custom-data` a cloud-init fájl nevét. [](/cli/azure/vmss) Az alábbi példa létrehoz egy *myScaleSet* nevű méretezési csoportot a *myResourceGroup csoportban,* és konfigurálja a virtuálisgép-példányokat egy *nevűcloud-init.txt.* Adja meg a saját nevét az alábbiak szerint:

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


### <a name="install-applications-with-os-updates"></a>Alkalmazások telepítése operációsrendszer-frissítésekkel
Ha új operációsrendszer-kiadások érhetők el, használhat vagy [](virtual-machine-scale-sets-upgrade-scale-set.md) létrehozhat egy új egyéni rendszerképet, és üzembe helyezheti az operációs rendszer frissítését egy méretezési csoportban. Minden virtuálisgép-példány a megadott legújabb rendszerképre van frissítve. Egyéni rendszerképet használhat az előre telepített alkalmazással, az egyéni szkriptbővítménysel vagy a PowerShell DSC-val, hogy alkalmazása automatikusan elérhető legyen a frissítés végrehajtása során. Előfordulhat, hogy a folyamat végrehajtása során meg kell tervezni az alkalmazás karbantartását, hogy ne legyen verziókompatibilitási probléma.

Ha egyéni virtuálisgép-rendszerképet használ az előre telepített alkalmazással, integrálhatja az alkalmazásfrissítéseket egy üzembe helyezési folyamatba az új rendszerképek létrehozásához és az operációs rendszer frissítésének üzembe helyezéséhez a méretezési csoportban. Ez a megközelítés lehetővé teszi, hogy a folyamat felvegye a legújabb alkalmazás-buildeket, létrehoz és érvényesítsen egy virtuálisgép-rendszerképet, majd frissítse a méretezési csoportban található virtuálisgép-példányokat. Ha olyan üzembe helyezési folyamatot futtat, amely alkalmazásfrissítéseket hoz létre és helyez üzembe az egyéni virtuálisgép-rendszerképeken, létrehozhat egy Packer-rendszerképet, és üzembe helyezheti az [Azure DevOps Services használatával,](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)vagy használhat egy másik platformot, például a [Spinnakert](https://www.spinnaker.io/) vagy a [Jenkinst.](https://jenkins.io/)


## <a name="next-steps"></a>Következő lépések
Amikor alkalmazásokat hoz létre és helyez üzembe a méretezési készletekben, tekintse át a méretezési készletek [tervezésének áttekintését.](virtual-machine-scale-sets-design-overview.md) További információ a méretezési készlet kezeléséhez: A méretezési készlet kezelése a [PowerShell-rel.](./virtual-machine-scale-sets-manage-powershell.md)
