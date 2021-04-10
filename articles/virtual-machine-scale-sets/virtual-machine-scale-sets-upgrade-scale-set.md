---
title: Azure virtuálisgép-méretezési csoport módosítása
description: Ismerje meg, hogyan módosítható és frissíthető egy Azure virtuálisgép-méretezési csoport a REST API-kkal, a Azure PowerShell és az Azure CLI-vel
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 45c52e141ed2d93f89c9d7c1426a9d7cbbbd47f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023928"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport módosítása

Az alkalmazások életciklusa során előfordulhat, hogy módosítania vagy frissítenie kell a virtuálisgép-méretezési csoportját. Ezek a frissítések magukban foglalhatják a méretezési csoport konfigurációjának frissítését vagy az alkalmazás konfigurációjának módosítását. Ez a cikk azt ismerteti, hogyan módosítható egy meglévő méretezési csoport a REST API-kkal, Azure PowerShelltel vagy az Azure CLI-vel.

## <a name="fundamental-concepts"></a>Alapfogalmak

### <a name="the-scale-set-model"></a>A méretezési csoport modellje
A méretezési csoport egy "méretezési csoport modelljével" rendelkezik, amely a méretezési csoport egy egészének *kívánt* állapotát rögzíti. A méretezési csoport modelljének lekérdezéséhez használhatja a 

- REST API a következővel: [számítás/virtualmachinescalesets/Get](/rest/api/compute/virtualmachinescalesets/get) a következőképpen:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell a [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI az [az vmss show](/cli/azure/vmss)használatával:

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- A [Resources.Azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK](https://azure.microsoft.com/downloads/)-kat is használhatja.

A kimenet pontos bemutatása a parancs által megadott beállításoktól függ. Az alábbi példa az Azure CLI-ből kitömörített minta kimenetét mutatja be:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Ezek a tulajdonságok a méretezési csoport egészére érvényesek.


### <a name="the-scale-set-instance-view"></a>A méretezési csoport példányának nézete
A méretezési csoporthoz tartozik egy "méretezési csoportbeli példány nézet" is, amely a méretezési csoport aktuális *futtatókörnyezeti* állapotát rögzíti egészként. A méretezési csoport példány nézetének lekérdezéséhez a következőket használhatja:

- REST API a [számítási/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) a következőképpen:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell a [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI az [az vmss Get-instance-View](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Használhatja a [Resources.Azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK](https://azure.microsoft.com/downloads/) -kat is

A kimenet pontos bemutatása a parancs által megadott beállításoktól függ. Az alábbi példa az Azure CLI-ből kitömörített minta kimenetét mutatja be:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Ezek a tulajdonságok a méretezési csoportba tartozó virtuális gépek aktuális futtatókörnyezeti állapotának összegzését tartalmazzák, például a méretezési csoportra alkalmazott bővítmények állapotát.


### <a name="the-scale-set-vm-model-view"></a>A méretezési csoport virtuálisgép-modell nézete
A méretezési csoport modell nézetéhez hasonlóan a méretezési csoport minden virtuálisgép-példányának saját modell nézete van. A méretezési csoportokban egy adott virtuálisgép-példány modell nézetének lekérdezéséhez a következőket használhatja:

- REST API a következővel: [számítás/virtualmachinescalesetvms/Get](/rest/api/compute/virtualmachinescalesetvms/get) a következőképpen:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell a [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI az [az vmss show](/cli/azure/vmss)használatával:

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Használhatja a [Resources.Azure.com](https://resources.azure.com) -t vagy az [Azure SDK](https://azure.microsoft.com/downloads/)-kat is.

A kimenet pontos bemutatása a parancs által megadott beállításoktól függ. Az alábbi példa az Azure CLI-ből kitömörített minta kimenetét mutatja be:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Ezek a tulajdonságok leírják a virtuálisgép-példányok konfigurációját egy méretezési csoporton belül, nem a méretezési csoport egészének konfigurációját. Például a méretezési csoport modelljének `overprovision` tulajdonsága van, míg a méretezési csoporton belüli virtuálisgép-példány modellje nem. Ez a különbség azért van így, mert a túlzott kiépítés a méretezési csoport egészére, nem pedig a méretezési csoportba tartozó egyes virtuálisgép-példányokra (a kiépítéssel kapcsolatos további információkért lásd a [méretezési csoportok kialakításával kapcsolatos szempontokat](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>A méretezési csoport virtuálisgép-példányának nézete
A méretezési csoport egy példány nézetéhez hasonlóan a méretezési csoport minden virtuálisgép-példányának saját példány nézete van. Egy méretezési csoporton belül egy adott virtuálisgép-példány példány nézetének lekérdezéséhez a következőket használhatja:

- REST API a [számítási/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) a következőképpen:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell a [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI az [az vmss Get-instance-View](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Használhatja a [Resources.Azure.com](https://resources.azure.com) -t vagy az [Azure SDK](https://azure.microsoft.com/downloads/) -kat is

A kimenet pontos bemutatása a parancs által megadott beállításoktól függ. Az alábbi példa az Azure CLI-ből kitömörített minta kimenetét mutatja be:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Ezek a tulajdonságok leírják egy virtuálisgép-példány aktuális futtatókörnyezeti állapotát egy méretezési csoporton belül, beleértve a méretezési csoportra alkalmazott kiterjesztéseket is.


## <a name="how-to-update-global-scale-set-properties"></a>A globális méretezési csoport tulajdonságainak frissítése
A globális méretezési csoport tulajdonságának frissítéséhez frissítenie kell a tulajdonságot a méretezési csoport modelljében. Ezt a frissítést a következővel végezheti el:

- REST API a [számítási/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) a következőképpen:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- A globális méretezési csoport tulajdonságainak frissítéséhez üzembe helyezhet egy Resource Manager-sablont a REST API tulajdonságokkal.

- Azure PowerShell [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI az [az vmss Update paranccsal](/cli/azure/vmss):
    - Tulajdonság módosítása:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Objektum hozzáadása egy méretezési csoport List tulajdonságához: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Objektum eltávolítása egy méretezési csoport List tulajdonságáról: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Ha korábban telepítette a méretezési csoportját a `az vmss create` paranccsal, akkor a `az vmss create` méretezési csoport frissítéséhez futtassa újra a parancsot. Győződjön meg arról, hogy a parancs összes tulajdonsága `az vmss create` megegyezik az előzővel, kivéve a módosítani kívánt tulajdonságokat.

- Használhatja a [Resources.Azure.com](https://resources.azure.com) -t vagy az [Azure SDK](https://azure.microsoft.com/downloads/)-kat is.

A méretezési csoport modelljének frissítése után az új konfiguráció a méretezési csoporton belül létrehozott összes új virtuális gépre érvényes lesz. A méretezési csoport meglévő virtuális gépei modelljeinek azonban továbbra is naprakésznek kell lennie a legújabb teljes méretezési csoport modelljével. Az egyes virtuális gépek modellje egy nevű logikai tulajdonság `latestModelApplied` , amely azt jelzi, hogy a virtuális gép naprakész-e a legújabb teljes méretezési csoport modelljével ( `true` azt jelenti, hogy a virtuális gép naprakész a legújabb modellel).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Virtuális gépek naprakészen tartása a legújabb méretezési csoport modelljével
A méretezési csoportok "frissítési szabályzattal" rendelkeznek, amely meghatározza, hogy a virtuális gépek hogyan legyenek naprakészek a legújabb méretezési csoport modelljével. A frissítési szabályzat három módja a következő:

- **Automatikus** – ebben a módban a méretezési csoport nem vállal garanciát a leállított virtuális gépek sorrendjére. A méretezési csoport egyszerre minden virtuális gépet igénybe vehet. 
- **Gördülő** – ebben a módban a méretezési csoport a kötegek közötti, opcionális szüneteltetési időt tartalmazó kötegekben összesíti a frissítést.
- **Manuális** – ebben a módban a méretezési csoport modelljének frissítésekor semmi nem történik a meglévő virtuális gépekkel.
 
A meglévő virtuális gépek frissítéséhez minden létező virtuális gép "manuális frissítését" kell végrehajtania. Ezt a manuális frissítést a következővel végezheti el:

- REST API a [számítási/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) a következőképpen:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI az [az vmss Update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Használhatja a nyelvspecifikus [Azure SDK](https://azure.microsoft.com/downloads/)-kat is.

>[!NOTE]
> Service Fabric-fürtök csak az *automatikus* módot használhatják, de a frissítést másképp kezelik. További információ: [Service Fabric alkalmazások frissítései](../service-fabric/service-fabric-application-upgrade.md).

A globális méretezési csoport tulajdonságainak egyik típusa nem követi a frissítési szabályzatot. A méretezési csoport operációs rendszerének és az adatlemez-profil (például a rendszergazdai Felhasználónév és a jelszó) módosításai csak az API *2017-12-01* -es vagy újabb verzióiban módosíthatók. Ezek a módosítások csak a méretezési csoport modelljének módosítása után létrehozott virtuális gépekre vonatkoznak. Ahhoz, hogy a meglévő virtuális gépek naprakészek legyenek, minden meglévő virtuális gép "rendszerképét" kell végrehajtania. Ezt a rendszerképet a következő paranccsal teheti meg:

- REST API a [számítás/virtualmachinescalesets/rendszerkép](/rest/api/compute/virtualmachinescalesets/reimage) a következőképpen:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell a [set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI az [az vmss reképhez](/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Használhatja a nyelvspecifikus [Azure SDK](https://azure.microsoft.com/downloads/)-kat is.


## <a name="properties-with-restrictions-on-modification"></a>Módosításra vonatkozó korlátozásokkal rendelkező tulajdonságok

### <a name="create-time-properties"></a>Létrehozási idő tulajdonságai
Bizonyos tulajdonságok csak a méretezési csoport létrehozásakor adhatók meg. Ezek a tulajdonságok a következők:

- Rendelkezésre állási zónák
- Képhivatkozás közzétevője
- Rendszerkép-hivatkozás ajánlat
- Felügyelt operációsrendszer-lemez Storage-fiókjának típusa

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>A csak az aktuális érték alapján módosítható tulajdonságok
Bizonyos tulajdonságok megváltoztathatók, és az aktuális értéktől függően kivételek lehetnek. Ezek a tulajdonságok a következők:

- **singlePlacementGroup** – ha a singlePlacementGroup értéke TRUE (igaz), akkor előfordulhat, hogy hamis értékre módosul. Ha azonban a singlePlacementGroup hamis, akkor előfordulhat, hogy **nem** módosítható igaz értékre.
- **alhálózat** – a méretezési csoport alhálózata akkor módosítható, ha az eredeti alhálózat és az új alhálózat ugyanabban a virtuális hálózatban van.
- **imageReferenceSku** – a képhivatkozási SKU a támogatott Linux- [disztribúciók](../virtual-machines/linux/endorsed-distros.md), a Windows Server-/ügyfél-lemezképek és a [megtervezési információk](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information)nélkül is frissíthető. 

### <a name="properties-that-require-deallocation-to-change"></a>A módosítás felszabadítását igénylő tulajdonságok
Néhány tulajdonság csak akkor módosítható bizonyos értékekre, ha a méretezési csoportba tartozó virtuális gépek fel vannak szabadítva. Ezek a tulajdonságok a következők:

- **SKU neve**– ha az új VIRTUÁLISGÉP-SKU nem támogatott a méretezési csoport által használt hardveren, akkor az SKU nevének módosítása előtt fel kell szabadítania a virtuális gépeket a méretezési csoportból. További információ: Azure-beli [virtuális gép átméretezése](../virtual-machines/windows/resize-vm.md). 

## <a name="vm-specific-updates"></a>VM-specifikus frissítések
Bizonyos módosítások a globális méretezési csoport tulajdonságai helyett meghatározott virtuális gépekre is alkalmazhatók. Jelenleg az egyetlen támogatott virtuálisgép-frissítés a méretezési csoportba tartozó virtuális gépekhez kapcsolódó adatlemezek csatlakoztatása/leválasztása. Ez a funkció előzetes verzióban érhető el. További információkért tekintse meg az [előzetes verzió dokumentációját](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Forgatókönyvek

### <a name="application-updates"></a>Alkalmazások frissítései
Ha egy alkalmazás egy méretezési csoportra van telepítve kiterjesztéseken keresztül, a bővítmény konfigurációjának frissítése az alkalmazás frissítését eredményezi a frissítési szabályzatnak megfelelően. Ha például egy parancsfájl új verziója fut egy egyéni parancsfájl-bővítményben, a *fileUris* tulajdonság frissítésével az új parancsfájlra mutathat. Bizonyos esetekben előfordulhat, hogy egy frissítést is kényszeríteni kíván, bár a bővítmény konfigurációja nem változik (például a parancsfájl URI-azonosítójának módosítása nélkül frissítette a szkriptet). Ezekben az esetekben a *forceUpdateTag* módosításával kényszerítheti a frissítést. Az Azure platform nem értelmezi ezt a tulajdonságot. Ha megváltoztatja az értéket, nincs hatással a bővítmény futtatására. A módosítás egyszerűen kényszeríti a bővítmény újrafuttatását. A *forceUpdateTag* kapcsolatos további információkért tekintse meg a [bővítmények REST API dokumentációját](/rest/api/compute/virtualmachineextensions/createorupdate). Vegye figyelembe, hogy a *forceUpdateTag* minden bővítménnyel használható, nem csak az egyéni szkriptek bővítménye.

Emellett gyakori, hogy az alkalmazások egyéni rendszerkép használatával legyenek üzembe helyezhetők. Ezt a forgatókönyvet a következő szakasz ismerteti.

### <a name="os-updates"></a>Operációs rendszer frissítései
Ha Azure platform-rendszerképeket használ, a *imageReference* módosításával frissítheti a lemezképet (További információ: [REST API dokumentáció](/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> A platformmal készített lemezképek esetében gyakori, hogy a "legfrissebb" érték legyen megadva a Képhivatkozás verziójában. A létrehozás, a Kibővítés és az Alaphelyzetbe állítás során a virtuális gépek a legújabb elérhető verzióval jönnek létre. Ez azonban **nem** jelenti azt, hogy az operációsrendszer-rendszerkép automatikusan frissül az új rendszerkép-verziók megjelenésekor. Egy különálló szolgáltatás jelenleg előzetes verzióban érhető el, amely az operációs rendszer automatikus frissítését teszi lehetővé. További információ: az [operációs rendszer verziófrissítésének automatikus dokumentációja](virtual-machine-scale-sets-automatic-upgrade.md).

Ha egyéni lemezképeket használ, a lemezkép frissítéséhez frissítse a *imageReference* azonosítóját (További információ: [REST API dokumentáció](/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Példák

### <a name="update-the-os-image-for-your-scale-set"></a>A méretezési csoport operációsrendszer-rendszerképének frissítése
Lehet, hogy van egy méretezési csoport, amely az Ubuntu LTS 16,04 régi verzióját futtatja. Az Ubuntu LTS 16,04 újabb verziójára szeretne frissíteni, például a *16.04.201801090* verzióra. A Képhivatkozás verziója tulajdonság nem egy lista részét képezi, ezért ezeket a tulajdonságokat közvetlenül módosíthatja az alábbi parancsok egyikével:

- A Azure PowerShell [Update-AzVmss](/powershell/module/az.compute/update-azvmss) a következőképpen történik:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI az [az vmss Update paranccsal](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Azt is megteheti, hogy módosítani szeretné a méretezési csoport által használt képet. Előfordulhat például, hogy frissíteni vagy módosítani szeretné a méretezési csoport által használt egyéni rendszerképet. A méretezési csoport által használt képet módosíthatja a Képhivatkozás azonosító tulajdonságának frissítésével. A Képhivatkozás azonosítója tulajdonság nem egy lista részét képezi, így közvetlenül módosíthatja ezt a tulajdonságot az alábbi parancsok egyikével:

- A Azure PowerShell [Update-AzVmss](/powershell/module/az.compute/update-azvmss) a következőképpen történik:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI az [az vmss Update paranccsal](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>A méretezési csoport terheléselosztó frissítése
Tegyük fel, hogy rendelkezik egy méretezési csoporttal Azure Load Balancerkal, és az Azure Load Balancert egy Azure-Application Gateway szeretné cserélni. A méretezési csoport terheléselosztó és Application Gateway tulajdonságai egy lista részét képezik, így közvetlenül a tulajdonságok módosítása helyett eltávolíthat vagy hozzáadhat listaelemeket:

- Azure PowerShell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Ezek a parancsok feltételezik, hogy a méretezési csoport csak egyetlen IP-konfigurációval és terheléselosztó-vel rendelkezik. Ha több is van, előfordulhat, hogy a *nullától* eltérő listát kell használnia.


## <a name="next-steps"></a>Következő lépések
A méretezési csoportokban általános felügyeleti feladatokat is elvégezhet az [Azure CLI](virtual-machine-scale-sets-manage-cli.md) vagy a [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md)használatával.