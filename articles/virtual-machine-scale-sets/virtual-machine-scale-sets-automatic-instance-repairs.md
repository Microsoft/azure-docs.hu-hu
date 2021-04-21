---
title: Példányok automatikus javítása azure-beli virtuálisgép-méretezési készletekkel
description: Ismerje meg, hogyan konfigurálhatja az automatikus javítási szabályzatot egy méretezési készletben található virtuálisgép-példányokhoz
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 733f4602e43511924783f6bc8cb1bad29edb5ea0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762910"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Példányok automatikus javítása az Azure-beli virtuális gépek méretezési csoportjaiban

Az Azure-beli virtuálisgép-méretezési készletek automatikus példányjavításainak engedélyezése segít elérni az alkalmazások magas rendelkezésre állását a kifogástalan állapotú példányok egy készletének fenntartásával. Ha a méretezési csoport egyik példánya nem megfelelőnek talál az [Application Health](./virtual-machine-scale-sets-health-extension.md) bővítmény vagy a [Terheléselosztás](../load-balancer/load-balancer-custom-probe-overview.md)állapot-mintavételei által jelentett állapotfigyelő adatokat, akkor ez a funkció automatikusan elvégzi a példány javítását a nem megfelelő példány törlésével és egy új példány létrehozásával a helyére.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Az automatikus példányjavítások használatának követelményei

**Alkalmazás állapotfigyelésének engedélyezése méretezési készlethez**

A méretezési csoportban engedélyezni kell a példányok alkalmazás állapotának figyelése funkciót. Ezt az Application [Health bővítővel](./virtual-machine-scale-sets-health-extension.md) vagy a [Terheléselosztás állapot-mintavételével lehet tenni.](../load-balancer/load-balancer-custom-probe-overview.md) Egyszerre csak az egyik engedélyezhető. Az alkalmazásállapot-bővítmény vagy a terheléselosztási mintavétel pingelte a virtuálisgép-példányon konfigurált alkalmazásvégpontot az alkalmazás állapotának megállapításához. Ezt az állapotállapotot a méretezésikészlet-vezénylési rendszer a példány állapotának figyelése és szükség esetén a javítás végrehajtása során használja.

**Végpont konfigurálása állapotra**

Az automatikus példányjavítási szabályzat engedélyezése előtt győződjön meg arról, hogy a méretezésikészlet-példányok alkalmazásvégpontja úgy van konfigurálva, hogy kibocsátsa az alkalmazás állapotát. Ha egy példány a 200-as (OK) állapotot adja vissza ezen az alkalmazásvégponton, akkor a példány "Kifogástalan" állapotúként lesz megjelölve. Minden más esetben a példány "Nem megfelelő" jelöléssel van megjelölve, beleértve a következő forgatókönyveket is:

- Ha nincs olyan alkalmazásvégpont konfigurálva a virtuálisgép-példányon belül, amely az alkalmazás állapotát szolgáltatja
- Ha az alkalmazásvégpont helytelenül van konfigurálva
- Ha az alkalmazásvégpont nem elérhető

A "Nem megfelelő" jelölésű példányok esetén a méretezési készlet aktiválja az automatikus javításokat. Az automatikus javítási szabályzat engedélyezése előtt győződjön meg arról, hogy az alkalmazásvégpont helyesen van konfigurálva, hogy elkerülje a példányok nem szándékolt javítását, miközben a végpont konfigurálása meg van állítva.

**Példányok maximális száma a méretezési készletben**

Ez a funkció jelenleg csak a legfeljebb 200 példányú méretezési készletekhez érhető el. A méretezési csoport üzembe helyezhető egyetlen elhelyezési csoportként vagy több elhelyezési csoportként, de a példányszám nem lehet 200 fölé, ha a méretezési csoporton engedélyezve van a példányok automatikus javítása.

**API-verzió**

Az automatikus javítási szabályzat a 2018-10-01-es vagy újabb számítási API-verziókhoz támogatott.

**Erőforrás- vagy előfizetés-elosztási korlátozások**

Az erőforrás- vagy előfizetés-elosztás jelenleg nem támogatott a méretezési csoportokkal, ha az automatikus javítási funkció engedélyezve van.

**A Service Fabric-méretezési készletek korlátozásai**

Ez a szolgáltatás a Service Fabric-méretezési készletekben jelenleg nem támogatott.

## <a name="how-do-automatic-instance-repairs-work"></a>Hogyan működik az automatikus példányjavítás?

Az automatikus példányjavítási funkció a méretezési készlet egyes példányainak állapotfigyelésére támaszkodik. A méretezési készletben lévő virtuálisgép-példányok úgy konfigurálhatóak, hogy alkalmazásállapot-állapotot adjanak ki az [Application Health](./virtual-machine-scale-sets-health-extension.md) bővítmény vagy a [Terheléselosztás állapot-mintavételei segítségével.](../load-balancer/load-balancer-custom-probe-overview.md) Ha egy példány nem megfelelőnek talál, akkor a méretezési csoport javítási műveletet hajt végre a nem megfelelő példány törlésével és egy új példány létrehozásával a helyére. Az új példány létrehozásához a legújabb virtuálisgép-méretezési csoport modell használható. Ez a funkció az *automaticRepairsPolicy* objektummal engedélyezhető a virtuálisgép-méretezési csoport modelljében.

### <a name="batching"></a>Kötegelés

Az automatikus példányjavítási műveletek kötegekként vannak hajtva végre. Egy adott időpontban a méretezési készletben a példányok nem több mint 5%-a javítható ki az automatikus javítási szabályzat segítségével. Így elkerülhető a nagy számú példány egyidejű törlése és újra létrehozása, ha egyszerre nem megfelelőnek talál megfelelőt.

### <a name="grace-period"></a>Türelmi időszak

Ha egy példány állapotváltozási műveleten megy keresztül a méretezési készleten végrehajtott PUT, PATCH vagy POST művelet miatt (például rendszerállapot-újratelepítés, frissítés stb.), akkor az adott példányon végrehajtott javítási műveletek csak a türelmi időszakra való várakozás után hajtják végre. A türelmi időszak az az időtartam, amely alatt a példány kifogástalan állapotúra térhet vissza. A türelmi időszak az állapotváltozás befejezése után kezdődik. Ez segít elkerülni a korai vagy véletlen javítási műveleteket. A rendszer a türelmi időszakot a méretezési készletben újonnan létrehozott példányok esetén (beleértve a javítási művelet eredményeként létrehozottat is) be lesz állítva. A türelmi időszak ISO 8601 formátumban, percben van megadva, és az *automaticRepairsPolicy.gracePeriod tulajdonság használatával lehet beállítani.* A türelmi időszak 30 perc és 90 perc között lehet, és az alapértelmezett értéke 30 perc.

### <a name="suspension-of-repairs"></a>Javítások felfüggesztése 

A virtuálisgép-méretezési készletek lehetővé teszi a példányok automatikus javításának ideiglenes felfüggesztését, ha szükséges. A *serviceState* az automatikus javításokhoz a virtuálisgép-méretezési készlet példánynézetében található *orchestrationServices* tulajdonság alatt az automatikus javítások aktuális állapotát jeleníti meg. Ha egy méretezési készlet automatikus javítást választ, a *serviceState* paraméter értéke Fut értékre *lesz állítva.* Ha egy méretezési készlet automatikus javításai fel vannak függesztve, a *serviceState* paraméter értéke *Felfüggesztve* lesz. Ha *az automaticRepairsPolicy* egy méretezési csoportban van definiálva, de az automatikus javítási funkció nincs engedélyezve, akkor a *serviceState* paraméter értéke *Nem fut.*

Ha az újonnan létrehozott példányok a méretezési készletben a nem megfelelőek cseréjére továbbra is nem megfelelőek maradnak, még a javítási műveletek ismételt végrehajtása után is, akkor a platform biztonsági intézkedésként frissíti a *serviceState-t* az automatikus javításokhoz a *Felfüggesztve* beállításra. Az automatikus javításokat ismét folytathatja, ha a *serviceState* értéket az automatikus javításokhoz a Fut értékre *állítsa.* A részletes utasításokat a [](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) méretezési csoport automatikus javítási szabályzatának megtekintését és frissítését részletező szakaszban található. 

Az automatikus példányjavítási folyamat a következőképpen működik:

1. [Az Alkalmazásállapot bővítmény vagy](./virtual-machine-scale-sets-health-extension.md) [a Terheléselosztás állapot-mintavételei](../load-balancer/load-balancer-custom-probe-overview.md) pingelik az alkalmazásvégpontot a méretezési készletben lévő egyes virtuális gépeken az egyes példányok alkalmazásállapotának lekérése érdekében.
2. Ha a végpont 200 (OK) állapottal válaszol, akkor a példány "Kifogástalan" állapotúként lesz megjelölve. A többi esetben (beleértve azt is, ha a végpont nem érhető el) a példány "Nem megfelelő" jelöléssel van megjelölve.
3. Ha egy példány nem megfelelőnek talál, a méretezési csoport egy javítási műveletet indít el a nem megfelelő példány törlésével és egy új példány létrehozásával a helyére.
4. A példányok javítása kötegben történik. Egy adott időpontban a méretezési készletben található összes példánynak nem több mint 5%-a javítható. Ha egy méretezési készletben kevesebb mint 20 példány van, a javítás egyszerre egy nem megfelelő példányon történik.
5. A fenti folyamat addig folytatódik, amíg meg nem javítják a méretezési készlet összes nem megfelelő példányát.

## <a name="instance-protection-and-automatic-repairs"></a>Példányvédelem és automatikus javítások

Ha egy méretezési készletben egy példány védelmét az egyik védelmi szabályzat [védi,](./virtual-machine-scale-sets-instance-protection.md)akkor a rendszer nem hajt végre automatikus javításokat a példányon. Ez mindkét védelmi szabályzatra vonatkozik: *Védelem a leskálástól* és *Védelem a méretezésihalmaz-műveletektől.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Lemondhatja az értesítéseket és az automatikus javításokat

Ha a [leállítási](./virtual-machine-scale-sets-terminate-notification.md) értesítési funkció engedélyezve van egy méretezési csoporton, akkor az automatikus javítási művelet során a nem megfelelő példány törlése a leállítási értesítési konfigurációt követi. A leállítja értesítést a rendszer az Azure metaadat-szolgáltatásán (ütemezett eseményeken) keresztül küldi el, és a példány törlése a konfigurált késési időkorlát időtartamára késik. A nem megfelelőt lecserélő új példány létrehozása azonban nem várja meg a késési időkorlát befejezését.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatikus javítási szabályzat engedélyezése új méretezési készlet létrehozásakor

Az automatikus javítási szabályzat új méretezési készlet létrehozásakor [](#requirements-for-using-automatic-instance-repairs) való engedélyezéséhez győződjön meg arról, hogy a szolgáltatásra való feliratkozás összes követelménye teljesül. Az alkalmazásvégpontot megfelelően kell konfigurálni a méretezésikészlet-példányokhoz, hogy ne indítsunk nem szándékolt javításokat a végpont konfigurálása közben. Újonnan létrehozott méretezési készletek esetén a példányok javítása csak a türelmi időszakra való várakozás után történik. Ha engedélyezni szeretné az automatikus példányjavítást egy méretezési készletben, használja az *automaticRepairsPolicy* objektumot a virtuálisgép-méretezésihalmaz-modellben.

Ezzel a gyorsindítási sablonnal egy virtuálisgép-méretezési csoport is üzembe helyezhető a terheléselelosztás állapot-mintavételével és az automatikus példányjavításokkal, 30 perces türelmi időszakkal. [](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe)

### <a name="azure-portal"></a>Azure Portal
 
Új méretezési készlet létrehozásakor az alábbi lépésekkel lehet engedélyezni az automatikus javítási szabályzatot.
 
1. Ugrás a **Virtuálisgép-méretezési készletekre.**
1. Új **méretezési csoport létrehozásához** válassza a + Hozzáadás lehetőséget.
1. Ugrás az Állapot **lapra.** 
1. Keresse meg **az Állapot** szakaszt.
1. Engedélyezze az **Alkalmazás állapotának figyelése** beállítást.
1. Keresse meg **az Automatikus javítási szabályzat szakaszt.**
1. Kapcsolja **be** az **Automatikus javítás beállítást.**
1. A **Türelmi időszak (min.)** mezőben adja meg a türelmi időszakot percben, az engedélyezett értékek 30 és 90 perc között vannak. 
1. Ha végzett az új méretezési csoport létrehozásával, válassza az **Áttekintés + létrehozás gombot.**

### <a name="rest-api"></a>REST API

Az alábbi példa bemutatja, hogyan engedélyezheti az automatikus példányjavítást egy méretezésikészlet-modellben. Az API 2018-10-01-es vagy újabb verzióját használja.

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

Az automatikus példányjavítási funkció új méretezési csoport létrehozásakor engedélyezhető a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmaggal. Ez a példaszk szkript végigvezeti a méretezési csoport és a társított erőforrások konfigurációs fájllal való létrehozásának folyamatán: [Teljes virtuálisgép-méretezési csoport létrehozása.](./scripts/powershell-sample-create-complete-scale-set.md) Az automatikus példányjavítási szabályzat konfigurálható úgy, hogy hozzáadja az *EnableAutomaticRepair* és az *AutomaticRepairGracePeriod* paramétereket a konfigurációs objektumhoz a méretezési készlet létrehozásához. Az alábbi példa 30 perces türelmi időszakkal engedélyezi a funkciót.

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

Az alábbi példa engedélyezi az automatikus javítási szabályzatot egy új méretezési csoport létrehozása során *[az az vmss create használatával.](/cli/azure/vmss#az_vmss_create)* Először hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot, amelynél az automatikus javítási szabályzat türelmi időszaka 30 percre van beállítva.

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

A fenti példa egy meglévő terheléselosztási és állapotfigyelőt használ a példányok alkalmazásállapotának figyelése érdekében. Ha inkább egy alkalmazás állapotfigyelő bővítményét szeretné használni a monitorozáshoz, létrehozhat egy méretezési készletet, konfigurálhatja az alkalmazás állapotbővítményét, majd engedélyezheti az automatikus példányjavítási szabályzatot az *az vmss update* parancs használatával, a következő szakaszban leírtak szerint.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatikus javítási szabályzat engedélyezése meglévő méretezési készlet frissítésekkor

Az automatikus javítási szabályzat meglévő méretezési készletben [](#requirements-for-using-automatic-instance-repairs) való engedélyezése előtt győződjön meg arról, hogy a funkcióra való feliratkozás összes követelménye teljesül. Az alkalmazásvégpontot megfelelően kell konfigurálni a méretezésikészlet-példányokhoz, hogy ne indítsunk nem szándékolt javításokat a végpont konfigurálása közben. Ha engedélyezni szeretné az automatikus példányjavítást egy méretezési készletben, használja az *automaticRepairsPolicy* objektumot a virtuálisgép-méretezésihalmaz-modellben.

Egy meglévő méretezési készlet modelljének frissítése után győződjön meg arról, hogy a rendszer a legújabb modellt alkalmazza a méretezés összes példányára. Tekintse meg a virtuális gépek a legújabb méretezésikészlet-modellel való naprakészen [való beállítását.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Azure Portal

Egy meglévő méretezési készlet automatikus javítási szabályzatát a következőn keresztül módosíthatja: Azure Portal. 
 
1. Ugrás egy meglévő virtuálisgép-méretezési készletre.
1. A **bal oldali** menü Beállítások menüjében válassza az Állapot és javítás **lehetőséget.**
1. Engedélyezze az **Alkalmazás állapotának figyelése** beállítást.
1. Keresse meg **az Automatikus javítási szabályzat szakaszt.**
1. Kapcsolja **be** az **Automatikus javítás lehetőséget.**
1. A **Türelmi időszak (min.)** mezőben adja meg a türelmi időszakot percben, az engedélyezett értékek 30 és 90 perc között vannak. 
1. Amikor elkészült, válassza a **Mentés** gombot. 

### <a name="rest-api"></a>REST API

Az alábbi példa engedélyezi a szabályzatot 40 perces türelmi időszakkal. Az API 2018-10-01-es vagy újabb verzióját használja.

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

Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag használatával módosíthatja egy meglévő méretezési készlet automatikus példányjavítási funkciójának konfigurációját. Az alábbi példa 40 percre frissíti a türelmi időszakot.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az alábbi példa egy meglévő méretezési készlet automatikus példányjavítási szabályzatának frissítésére mutat példát *[az az vmss update használatával.](/cli/azure/vmss#az_vmss_update)*

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Az automatikus példányjavítási szabályzat szolgáltatásállapotának megtekintése és frissítése

### <a name="rest-api"></a>REST API 

A [Példánynézet](/rest/api/compute/virtualmachinescalesets/getinstanceview) lekért verziója a 2019-12-01-es vagy újabb API-verzióval használható a virtuálisgép-méretezési készlethez, hogy megtekintse a *serviceState* tulajdonságot az *orchestrationServices* tulajdonság alatti automatikus javításhoz. 

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

Használja a *setOrchestrationServiceState* API-t a 2019-12-01-es vagy újabb API-verzióval egy virtuálisgép-méretezési készleten az automatikus javítások állapotának beállításához. Miután a méretezési készletet bekapcsolta az automatikus javítás funkcióba, ezzel az API-val felfüggesztheti vagy folytathatja a méretezési készlet automatikus javításait. 

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

Használja [a get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) parancsmagot a *serviceState* megtekintéséhez az automatikus példányjavítások megtekintéséhez. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Használja [a set-orchestration-service-state parancsmagot](/cli/azure/vmss#az_vmss_set_orchestration_service_state) a *serviceState* frissítéséhez az automatikus példányjavításhoz. Miután a méretezési készletet bekapcsolta az automatikus javítási funkcióba, ezzel a parancsmag használatával felfüggesztheti vagy folytathatja az automatikus javításokat a méretezési készletben. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Használja [a Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmagot az *InstanceView* paraméterrel a *ServiceState* megtekintéséhez az automatikus példányjavítások megtekintéséhez.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Használja Set-AzVmssOrchestrationServiceState parancsmagot a *serviceState* frissítéséhez a példányok automatikus javításához. Miután a méretezési készletet bekapcsolta az automatikus javítási funkcióba, ezzel a parancsmag használatával felfüggesztheti vagy folytathatja az automatikus javításokat a méretezési készletben.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Hibaelhárítás

**Nem sikerült engedélyezni az automatikus javítási szabályzatot**

Ha "BadRequest" hibaüzenetet kap a "Nem található automaticRepairsPolicy" tag a "properties" típusú objektumon, ellenőrizze a virtuálisgép-méretezési készlethez használt API-verziót. Ehhez a funkcióhoz az API 2018-10-01-es vagy újabb verziója szükséges.

**A példány nem javítható még akkor sem, ha a szabályzat engedélyezve van**

A példány türelmi időszakban lehet. Ez az az idő, amely után a példány állapotváltozása után várni kell a javítások végrehajtása előtt. Ezzel elkerülhetők a korai vagy véletlen javítások. A javítási műveletre akkor van szükség, amikor a türelmi időszak befejeződik a példányon.

**Méretezésikészlet-példányok alkalmazásállapotának megtekintése**

Az alkalmazás [](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) állapotának megtekintéséhez használhatja egy virtuálisgép-méretezési készlet példányainak Példánynézet API-ját. A Azure PowerShell a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) parancsmagot használhatja az *-InstanceView jelzővel.* Az alkalmazás állapota a *vmHealth* tulajdonság alatt található.

A Azure Portal láthatja az állapotukat is. Ugrás egy meglévő méretezési  készletre, válassza a bal oldali  menü Példányok menüpontját, és nézze meg az egyes méretezésikészlet-példányok állapotoszlopát. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan konfigurálhatja [az Application Health bővítményt](./virtual-machine-scale-sets-health-extension.md) vagy a [terheléselosztás](../load-balancer/load-balancer-custom-probe-overview.md) állapot-mintavételét a méretezési készletekhez.
