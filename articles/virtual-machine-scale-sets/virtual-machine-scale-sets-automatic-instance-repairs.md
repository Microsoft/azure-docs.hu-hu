---
title: Azure-beli virtuálisgép-méretezési csoportokkal rendelkező automatikus példányok javítása
description: Útmutató a méretezési csoportokban lévő virtuálisgép-példányok automatikus javítási házirendjének konfigurálásához
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 080666c9857c1a3dc509ca980bc85b1dc11b5975
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214286"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Példányok automatikus javítása az Azure-beli virtuális gépek méretezési csoportjaiban

Az Azure-beli virtuálisgép-méretezési csoportokhoz tartozó automatikus példányok javításának engedélyezése lehetővé teszi, hogy az alkalmazások magas rendelkezésre állást biztosítson a megfelelő állapotok mellett. Ha a méretezési csoport egyik példánya nem kifogástalan állapotú, mint az [Application Health Extension](./virtual-machine-scale-sets-health-extension.md) vagy a [Load Balancer Health](../load-balancer/load-balancer-custom-probe-overview.md)mintavétele, akkor ez a funkció automatikusan elvégzi a példányok javítását a nem kifogástalan példány törlésével, és újat hoz létre a helyére.

## <a name="requirements-for-using-automatic-instance-repairs"></a>A példányok automatikus javításának használatára vonatkozó követelmények

**Alkalmazás állapot-figyelésének engedélyezése a méretezési csoport számára**

A méretezési csoportnak rendelkeznie kell az alkalmazás állapotának figyelésével a példányok engedélyezéséhez. Ez az [alkalmazás állapotának](./virtual-machine-scale-sets-health-extension.md) vagy a [terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md)használatával végezhető el. Egyszerre csak az egyik engedélyezhető. Az alkalmazás állapota vagy a terheléselosztó-tesztek pingelik a virtuálisgép-példányokon konfigurált alkalmazás-végpontot az alkalmazás állapotának megállapításához. Ezt az állapotot a méretezési csoport Orchestrator a példány állapotának figyelésére, valamint szükség esetén a javítások elvégzésére használja.

**Végpont beállítása állapot biztosításához**

A példányok automatikus javításának engedélyezése előtt gondoskodjon arról, hogy a méretezési csoport példányai rendelkezzenek az alkalmazás állapotának kibocsátására konfigurált alkalmazás-végponttal. Ha egy példány a 200 (OK) állapotot adja vissza ezen az alkalmazás-végponton, akkor a példány "kifogástalan" jelöléssel jelenik meg. Minden más esetben a példány "nem megfelelő" állapotúként van megjelölve, beleértve az alábbi eseteket:

- Ha a virtuálisgép-példányokban nincs konfigurálva alkalmazás-végpont az alkalmazás állapotának megadásához
- Ha az alkalmazás végpontja helytelenül van konfigurálva
- Ha az alkalmazás végpontja nem érhető el

A "nem megfelelő" állapotú példányok esetén a méretezési csoport automatikusan kijavítja az automatikus javításokat. Győződjön meg arról, hogy az alkalmazás végpontja megfelelően van konfigurálva az automatikus javítási házirend engedélyezése előtt, hogy elkerülje a nem kívánt példányok javítását, a végpontot pedig konfigurálja.

**A méretezési csoport példányainak maximális száma**

Ez a funkció jelenleg csak a legfeljebb 200 példányú méretezési csoportokhoz érhető el. A méretezési csoport egyetlen elhelyezési csoportként vagy több elhelyezési csoportként is telepíthető, azonban a példányszám nem lehet 200-nál nagyobb, ha a méretezési csoport automatikus példányának javítása engedélyezve van.

**API-verzió**

Az automatikus javítási szabályzat a 2018-10-01-es vagy újabb számítási API-verzió esetén támogatott.

**Az erőforrás-vagy előfizetés-áthelyezésre vonatkozó korlátozások**

Az erőforrás-vagy előfizetési lépések jelenleg nem támogatottak a méretezési csoportokban, ha engedélyezve van az automatikus javítás funkció.

**Service Fabric-méretezési csoportok korlátozása**

Ez a funkció jelenleg nem támogatott a Service Fabric-méretezési csoportokban.

## <a name="how-do-automatic-instance-repairs-work"></a>Hogyan működik az automatikus példányok javítása?

Az automatikus példány-javítási funkció a méretezési csoportokban lévő egyes példányok állapotának figyelésére támaszkodik. A méretezési csoportokban lévő virtuálisgép-példányok [az alkalmazás állapotának vagy a](./virtual-machine-scale-sets-health-extension.md) [terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md)használatával állíthatók be az alkalmazás állapotának kiosztására. Ha egy példány nem kifogástalan állapotú, akkor a méretezési csoport javítási műveletet hajt végre, ha törli a nem megfelelő állapotú példányt, és létrehoz egy újat a helyére. Az új példány létrehozásához a legújabb virtuálisgép-méretezési csoport modellt használjuk. Ez a funkció a *automaticRepairsPolicy* objektum használatával engedélyezhető a virtuálisgép-méretezési csoport modelljében.

### <a name="batching"></a>Kötegelés

Az automatikus példány-javítási műveletek kötegekben lesznek elvégezve. Egy adott időpontban a méretezési csoport példányainak legfeljebb 5%-a javítása az automatikus javítási szabályzattal történik. Ez segít elkerülni a nagy számú példány egyidejű törlését és újbóli létrehozását, ha nem megfelelő állapotot észlelt.

### <a name="grace-period"></a>Türelmi időszak

Ha egy példány állapot-módosítási művelettel halad át a méretezési csoporton végrehajtott PUT, PATCH vagy POST művelet miatt (például rendszerkép, újratelepítés, frissítés stb.), akkor az adott példányon végzett javítási műveletek csak a türelmi időszakra való várakozás után történnek. A türelmi időszak azt az időtartamot adja meg, amely lehetővé teszi, hogy a példány Kifogástalan állapotba térjen vissza. A türelmi időszak az állapotváltozás befejeződése után kezdődik. Ez segít elkerülni a korai vagy véletlen javítási műveleteket. A türelmi időszakot a méretezési csoport minden újonnan létrehozott példánya esetében tiszteletben kell venni (beleértve a javítási művelet eredményeképpen létrehozott egyet is). A türelmi időszak ISO 8601 formátumú percekben van megadva, és a *automaticRepairsPolicy. gracePeriod* tulajdonsággal állítható be. A türelmi időszak 30 perc és 90 perc között lehet, és alapértelmezett értéke 30 perc.

### <a name="suspension-of-repairs"></a>Javítások felfüggesztése 

A virtuálisgép-méretezési csoportok lehetővé teszik, hogy szükség esetén ideiglenesen felfüggessze az automatikus példányok javításait. A virtuálisgép-méretezési csoport *orchestrationServices* tulajdonsága alatt az automatikus javítások *serviceState* az automatikus javítás aktuális állapotát jeleníti meg. Ha egy méretezési csoport automatikus javításokra van beállítva, a *serviceState* paraméter értéke *fut* értékre van állítva. Ha az automatikus javítás fel van függesztve egy méretezési csoportra, a *serviceState* paraméter *felfüggesztve* értékre van állítva. Ha a *automaticRepairsPolicy* definiálva van egy méretezési csoport, de az automatikus javítás funkció nincs engedélyezve, akkor a *ServiceState* paraméter *nem fut* értékre van állítva.

Ha egy méretezési csoport nem kifogástalan állapotának cseréjéhez újonnan létrehozott példányok továbbra is kifogástalan állapotban maradnak, még a javítási műveletek ismételt végrehajtása után is, akkor a platform biztonsági intézkedése frissíti a *serviceState* az automatikus javítások *felfüggesztéséhez*. Újra folytathatja az automatikus javításokat úgy, hogy a *serviceState* értékének megadásával *futtatja* az automatikus javításokat. Részletes utasítások a méretezési csoport [automatikus javítási szabályzatának megtekintési és frissítési állapotának megtekintése és frissítése](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) című szakaszban olvashatók. 

Az automatikus példány javításának folyamata a következőképpen működik:

1. Az [alkalmazás állapotának vagy a](./virtual-machine-scale-sets-health-extension.md) [terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md) vizsgálatával pingelheti az alkalmazás végpontját a méretezési csoport minden egyes virtuális gépén az egyes példányok állapotának lekéréséhez.
2. Ha a végpont 200-as (OK) állapottal válaszol, akkor a példány "kifogástalan" állapotúként lesz megjelölve. Az összes többi esetben (ha a végpont nem érhető el), a példány "nem megfelelő" állapotúként van megjelölve.
3. Ha egy példány sérült állapotba kerül, a méretezési csoport kivált egy javítási műveletet a nem kifogástalan példány törlésével, és újat hoz létre a helyére.
4. A példányok javítása kötegekben történik. Egy adott időpontban a méretezési csoport összes példányának legfeljebb 5%-a javításra kerül. Ha a méretezési csoport kevesebb, mint 20 példánya van, a javításokat egyszerre egy nem kifogástalan állapotú példányhoz kell elvégezni.
5. A fenti folyamat addig folytatódik, amíg a méretezési csoport összes nem kifogástalan állapotú példánya ki van javítva.

## <a name="instance-protection-and-automatic-repairs"></a>A példányok védelme és az automatikus javítás

Ha a méretezési csoport egy példánya védett egy [védelmi házirend](./virtual-machine-scale-sets-instance-protection.md)alkalmazásával, akkor a rendszer nem hajtja végre az automatikus javításokat az adott példányon. Ez a védelmi házirendekre is vonatkozik: *védelem a méretezési* és a védelem között a *méretezési* műveleteknél. 

## <a name="terminatenotificationandautomaticrepairs"></a>Értesítés és automatikus javítás leállítása

Ha a leállítási [értesítési](./virtual-machine-scale-sets-terminate-notification.md) funkció engedélyezve van egy méretezési csoporton, akkor az automatikus javítási művelet során a nem megfelelő állapotú példányok törlése a megszakítási értesítés konfigurációját követi. Az Azure metaadat-szolgáltatás – ütemezett események –, a példányok törlése pedig késleltetve küldi el a lemondási értesítést a beállított késleltetési időkorlát időtartamára. Egy új példány létrehozásakor azonban a sérült állapot cseréje nem várja meg, amíg a késleltetési időkorlát be nem fejeződik.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatikus javítási szabályzat engedélyezése új méretezési csoport létrehozásakor

Ha új méretezési csoport létrehozásakor szeretné engedélyezni az automatikus javítási szabályzatot, ellenőrizze, hogy teljesülnek-e a szolgáltatásba való bejelentkezéshez [szükséges összes követelmény](#requirements-for-using-automatic-instance-repairs) . Az alkalmazás-végpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, így elkerülhető a nem szándékolt javítások elindítása a végpont konfigurálása közben. Az újonnan létrehozott méretezési csoportok esetében minden példány javítása csak a türelmi időszak időtartamára való várakozás után történik. A méretezési csoportokban az automatikus példányok javításának engedélyezéséhez használja a *automaticRepairsPolicy* objektumot a virtuálisgép-méretezési csoport modelljében.

Ezzel a rövid útmutató [sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) üzembe helyezhet egy virtuálisgép-méretezési készletet a terheléselosztó állapotának mintavételével és az automatikus példányok javításával, amely 30 perces türelmi időszakra van beállítva.

### <a name="azure-portal"></a>Azure Portal
 
Az alábbi lépésekkel engedélyezheti az automatikus javítási szabályzatot új méretezési csoport létrehozásakor.
 
1. Nyissa meg a **virtuálisgép-méretezési csoportokat**.
1. Válassza a **+ Hozzáadás** lehetőséget egy új méretezési csoport létrehozásához.
1. Lépjen a **Health (állapot** ) lapra. 
1. Keresse meg az **állapot** szakaszt.
1. Az **alkalmazás állapotának figyelése** lehetőség engedélyezése.
1. Keresse meg az **automatikus javítási szabályzat** szakaszt.
1. Kapcsolja **be** az **automatikus javítási** lehetőséget.
1. A **türelmi időszak (perc)** mezőben határozza meg a türelmi időszakot percben, a megengedett értékek 30 és 90 perc között vannak. 
1. Ha elkészült az új méretezési csoport létrehozásával, válassza a **felülvizsgálat + létrehozás** gombot.

### <a name="rest-api"></a>REST API

Az alábbi példa bemutatja, hogyan engedélyezheti az automatikus példányok javítását egy méretezési csoport modelljében. Az API 2018-10-01-es vagy újabb verzióját használja.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Az automatikus példány-javítási funkció engedélyezhető egy új méretezési csoport létrehozásakor a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag használatával. Ez a minta parancsfájl végigvezeti a méretezési csoport és a kapcsolódó erőforrások létrehozásán a konfigurációs fájl használatával: [hozzon létre egy teljes virtuálisgép-méretezési készletet](./scripts/powershell-sample-create-complete-scale-set.md). A *EnableAutomaticRepair* és a *AutomaticRepairGracePeriod* paramétereknek a méretezési csoport létrehozásához való hozzáadásával konfigurálhatja az automatikus példányok javítási házirendjét. A következő példa egy 30 perces türelmi időszakot engedélyez a szolgáltatás számára.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az alábbi példa lehetővé teszi az automatikus javítási szabályzatot az új méretezési csoport létrehozásakor az *[az vmss Create](/cli/azure/vmss#az-vmss-create)* paranccsal. Először hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot, amely az automatikus javítási házirend türelmi időszaka 30 percre van beállítva.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

A fenti példa egy meglévő Load balancert és egy állapot-mintavételt használ az alkalmazások állapotának figyeléséhez. Ha inkább egy alkalmazás-állapotfigyelő bővítményt szeretne használni a figyeléshez, létrehozhat egy méretezési készletet, beállíthatja az alkalmazás állapotát, majd engedélyezheti az automatikus példány javítása házirendet az az *vmss Update* használatával, a következő szakaszban leírtak szerint.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatikus javítási szabályzat engedélyezése egy meglévő méretezési csoport frissítésekor

Mielőtt engedélyezné az automatikus javítási szabályzatot egy meglévő méretezési csoporton belül, győződjön meg arról, hogy a szolgáltatásba való bejelentkezéshez [szükséges összes követelmény](#requirements-for-using-automatic-instance-repairs) teljesül. Az alkalmazás-végpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, így elkerülhető a nem szándékolt javítások elindítása a végpont konfigurálása közben. A méretezési csoportokban az automatikus példányok javításának engedélyezéséhez használja a *automaticRepairsPolicy* objektumot a virtuálisgép-méretezési csoport modelljében.

Egy meglévő méretezési csoport modelljének frissítése után győződjön meg arról, hogy a legújabb modellt alkalmazza a skála összes példányára. Tekintse át a [virtuális gépek naprakész állapotba helyezésének utasításait a legújabb méretezési csoport modelljével](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Azure Portal

Egy meglévő méretezési csoport automatikus javítási szabályzatát módosíthatja a Azure Portalon keresztül. 
 
1. Váltson egy meglévő virtuálisgép-méretezési csoportra.
1. A bal oldali menü **Beállítások** területén válassza az **állapot és javítás** lehetőséget.
1. Az **alkalmazás állapotának figyelése** lehetőség engedélyezése.
1. Keresse meg az **automatikus javítási szabályzat** szakaszt.
1. Kapcsolja **be** az **automatikus javítási** lehetőséget.
1. A **türelmi időszak (perc)** mezőben határozza meg a türelmi időszakot percben, a megengedett értékek 30 és 90 perc között vannak. 
1. Amikor elkészült, válassza a **Mentés** gombot. 

### <a name="rest-api"></a>REST API

Az alábbi példa a 40 perces türelmi idővel engedélyezi a szabályzatot. Az API 2018-10-01-es vagy újabb verzióját használja.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag segítségével módosíthatja egy meglévő méretezési csoport automatikus példány-javítási funkciójának konfigurációját. A következő példa a türelmi időszakot 40 percre frissíti.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az alábbi példa egy meglévő méretezési csoport automatikus példányának javítási szabályzatának frissítését szemlélteti az *[az vmss Update](/cli/azure/vmss#az-vmss-update)* használatával.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Az automatikus példányok javítási szabályzatának szolgáltatási állapotának megtekintése és frissítése

### <a name="rest-api"></a>REST API 

A virtuálisgép-méretezési csoporthoz tartozó, 2019-12-01 [-es vagy](/rest/api/compute/virtualmachinescalesets/getinstanceview) újabb API-s verziójú, a *serviceState* az automatikus javításokhoz a *orchestrationServices* tulajdonság alatt megtekintheti. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Az automatikus javítás állapotának beállításához használja a *setOrchestrationServiceState* API-t a 2019-12-01-es vagy újabb API-verzióval a virtuálisgép-méretezési csoportokban. Ha a méretezési csoport az automatikus javítás funkcióval van elindítva, ezzel az API-val felfüggesztheti vagy folytathatja a méretezési csoport automatikus javításait. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

A [Get-instance-View](/cli/azure/vmss#az-vmss-get-instance-view) parancsmag használatával megtekintheti az automatikus példányok javításának *serviceState* . 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

A [set-vezényel-Service-State](/cli/azure/vmss#az-vmss-set-orchestration-service-state) parancsmag használatával frissítse a *serviceState* az automatikus példányok javításához. Ha a méretezési csoport az automatikus javítási szolgáltatásban van, akkor ezzel a parancsmaggal felfüggesztheti vagy folytathatja a méretezési csoport automatikus javításait. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

A [Get-AzVmss](/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) parancsmag és a *InstanceView* paraméter használatával megtekintheti az automatikus példányok javításának *ServiceState* .

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Az automatikus példányok javításához használja a Set-AzVmssOrchestrationServiceState parancsmagot a *serviceState* frissítéséhez. Ha a méretezési csoport az automatikus javítási szolgáltatásba van állítva, ezzel a parancsmaggal felfüggesztheti vagy folytathatja a méretezési csoport automatikus javításait.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Hibaelhárítás

**Nem sikerült engedélyezni az automatikus javítási szabályzatot**

Ha "BadRequest" hibaüzenet jelenik meg, amely azt jelzi, hogy "a" nem található a "Properties" típusú objektum "automaticRepairsPolicy" tagja, akkor ellenőrizze a virtuálisgép-méretezési csoporthoz használt API-verziót. Ehhez a szolgáltatáshoz a 2018-10-01-es vagy újabb API-verzió szükséges.

**A példány nem kerül javításra, még akkor is, ha a szabályzat engedélyezve van**

A példány lehet türelmi időszak. Ez az az időtartam, ameddig a rendszer az állapot megváltozása után várakozik a példányon a javítások végrehajtása előtt. Ezzel elkerülhető az idő előtti vagy véletlen javítás. A javítási műveletnek akkor kell történnie, ha a türelmi időszak befejeződött a példányhoz.

**A méretezési csoport példányainak alkalmazás-állapotának megtekintése**

A virtuálisgép-méretezési csoport példányaihoz tartozó példányok [megtekintése API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) használatával megtekintheti az alkalmazás állapotát. A Azure PowerShell a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) parancsmagot használhatja a *-InstanceView* jelzővel. Az alkalmazás állapotának állapotát a *vmHealth* tulajdonság alatt kell megadnia.

A Azure Portal az állapotot is megtekintheti. Váltson egy meglévő méretezési csoportra, válassza a bal oldali menüben a **példányok** lehetőséget, és tekintse **meg az Állapot oszlopban az** egyes méretezési csoportokhoz tartozó állapotok állapotát. 

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan konfigurálhatja a méretezési csoportokhoz az [alkalmazás állapotának kiterjesztését](./virtual-machine-scale-sets-health-extension.md) vagy a [terheléselosztó állapotát](../load-balancer/load-balancer-custom-probe-overview.md) .
