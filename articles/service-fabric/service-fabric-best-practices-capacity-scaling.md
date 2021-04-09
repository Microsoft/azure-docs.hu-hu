---
title: Az Azure Service Fabric kapacitástervezése és skálázása
description: Ajánlott eljárások Service Fabric fürtök és alkalmazások tervezéséhez és méretezéséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 32a9c26bb9e89cf4057cc753b02ad3c006d0bae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595081"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Az Azure Service Fabric kapacitástervezése és skálázása

Mielőtt bármilyen Azure Service Fabric-fürtöt hozna létre, vagy a fürtöt futtató számítási erőforrásokat méretezheti, fontos a kapacitás megtervezése. További információ a kapacitás megtervezéséről: [a Service Fabric-fürt kapacitásának megtervezése](./service-fabric-cluster-capacity.md). A fürtök méretezhetőségével kapcsolatos további gyakorlati útmutatásért lásd: [Service Fabric skálázhatósági megfontolások](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

A csomópont típusának és a fürt jellemzőinek figyelembevétele mellett a skálázási műveletek várhatóan hosszabb időt vesznek igénybe, amíg az éles környezetben is elvégezhető. Ez a megfontolás a hozzáadott virtuális gépek számától függetlenül igaz.

## <a name="autoscaling"></a>Automatikus skálázás
A skálázási műveleteket Azure Resource Manager sablonokon keresztül kell végrehajtania, mivel ez az ajánlott eljárás az [erőforrás-konfigurációk kódként](./service-fabric-best-practices-infrastructure-as-code.md)való kezelésére. 

A virtuálisgép-méretezési csoportokon keresztüli automatikus skálázással a verziószámmal ellátott Resource Manager-sablon pontatlanul definiálja a példányok számát a virtuálisgép-méretezési csoportokhoz. A pontatlan definíció növeli annak kockázatát, hogy a jövőbeli központi telepítések nem kívánt skálázási műveleteket okozzák. Általában az automatikus skálázást kell használnia, ha:

* A megfelelő kapacitással rendelkező Resource Manager-sablonok üzembe helyezése nem támogatja a használati esetet.
     
   A manuális skálázás mellett az Azure [DevOps-szolgáltatásokban is konfigurálhat folyamatos integrációs és kézbesítési folyamatot az Azure erőforráscsoport-telepítési projektjeivel](../azure-resource-manager/templates/add-template-to-azure-pipelines.md). Ezt a folyamatot általában egy olyan logikai alkalmazás indítja el, amely a [Azure Monitor Rest APIból](../azure-monitor/essentials/rest-api-walkthrough.md)lekérdezett virtuális gépek teljesítmény-metrikáit használja. A folyamat hatékonyan kibővíti a kívánt mérőszámok alapján, a Resource Manager-sablonok optimalizálásával.
* Egyszerre csak egy virtuálisgép-méretezési csoport csomópontot kell horizontálisan méreteznie.
   
   Ha egyszerre három vagy több csomópontot szeretne felskálázásra, egy [virtuálisgép-méretezési csoport hozzáadásával Service Fabric-fürtöt kell felskálázása](virtual-machine-scale-set-scale-node-type-scale-out.md). A virtuálisgép-méretezési csoportok horizontálisan, egyszerre egy csomóponton méretezhetők és méretezhetők.
* A Service Fabric-fürthöz Silver megbízhatósági vagy magasabb szintű, az automatikus skálázási szabályokat konfiguráló méreteken pedig ezüst tartósság vagy magasabb.
  
   Az automatikus skálázási szabályok minimális kapacitásának legalább öt virtuálisgép-példánynak kell lennie. Az elsődleges csomópont típusának legalább a megbízhatósági szintjénél nagyobbnak kell lennie.

> [!NOTE]
> A Service Fabric állapot-nyilvántartó Service Fabric:/System/InfastructureService/<NODE_TYPE_NAME> minden olyan csomópont-típuson fut, amely ezüst vagy magasabb tartóssággal rendelkezik. Ez az egyetlen olyan rendszerszolgáltatás, amely az Azure-ban a fürtök bármelyik csomópont-típusán futtatható.

> [!IMPORTANT]
> Service Fabric az automatikus skálázás támogatja a `Default` és `NewestVM` a virtuálisgép-méretezési csoport [skálázási konfigurációját](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md).

## <a name="vertical-scaling-considerations"></a>Vertikális skálázási megfontolások

Az Azure Service Fabric egy csomópont-típus [vertikális skálázása](./virtual-machine-scale-set-scale-node-type-scale-out.md) számos lépést és szempontot igényel. Például:

* A skálázás előtt a fürtnek kifogástalan állapotban kell lennie. Ellenkező esetben a fürt destabilizálása is megtörténik.
* Az állapot-nyilvántartó szolgáltatásokat futtató összes Service Fabric esetében az ezüst tartóssági szintnek vagy nagyobbnak kell lennie.

> [!NOTE]
> Az állapot-nyilvántartó Service Fabric rendszerszolgáltatásokat futtató elsődleges csomópont típusa csak ezüst tartóssági vagy magasabb szintű lehet. Miután engedélyezte az ezüst tartósságot, a fürt műveletei, például a frissítések, a csomópontok hozzáadása vagy eltávolítása, és így tovább lassabbak lesznek, mivel a rendszer az adatbiztonságot a műveletek sebessége alapján optimalizálja.

A virtuálisgép-méretezési csoport vertikális méretezése az erőforrás-SKU-ra való egyszerű módosításával roncsolásos művelet, mivel a gazdagépek újrarendszerképe eltávolítja az összes helyileg megőrzött állapotot. Ehelyett érdemes vízszintesen méretezni a fürtöt egy új méretezési csoport hozzáadásával a kívánt SKU-val, majd át kell telepítenie a szolgáltatásokat az új méretezési csoportba egy biztonságos vertikális skálázási művelet elvégzéséhez.

A fürt Service Fabric [csomópont-tulajdonságokat és elhelyezési korlátozásokat](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) használ, hogy eldöntse, hová szeretné üzemeltetni az alkalmazás szolgáltatásait. Ha függőlegesen méretezi az elsődleges csomópont típusát, üzembe helyez egy második elsődleges csomópont-típust, majd beállítja a ( `"isPrimary": false` ) értéket az eredeti elsődleges csomópont típusára, és folytatja a csomópontok letiltását és a méretezési csoport és a kapcsolódó erőforrások eltávolítását. Részletekért lásd: [Service Fabric fürt elsődleges csomópont-típusának Felskálázása](service-fabric-scale-up-primary-node-type.md).

> [!NOTE]
> Mindig ellenőrizze a tesztelési környezetekben lévő műveleteket, mielőtt megkísérli a módosításokat az éles környezetben. Alapértelmezés szerint Service Fabric a fürt rendszerszolgáltatásainak elhelyezési megkötése csak a célként megadott elsődleges csomópont típusára vonatkozik.

A csomópont tulajdonságai és az elhelyezési megkötések deklarálása esetén a következő lépésekkel egyszerre egy virtuálisgép-példányt hajthat végre. Ez lehetővé teszi, hogy a rendszerszolgáltatások (és az állapot-nyilvántartó szolgáltatások) szabályosan legyenek leállítva az eltávolítani kívánt virtuálisgép-példányon, mivel az új replikák máshol jönnek létre.

1. A PowerShellből futtassa `Disable-ServiceFabricNode` a parancsot, `RemoveNode` hogy letiltsa az eltávolítani kívánt csomópontot. Távolítsa el a legmagasabb számú csomópont-típust. Ha például hat csomópontos fürttel rendelkezik, távolítsa el a "MyNodeType_5" virtuálisgép-példányt.
2. A futtatásával `Get-ServiceFabricNode` Győződjön meg arról, hogy a csomópont le van-e tiltva. Ha nem, várja meg, amíg le nem tiltja a csomópontot. Ez eltarthat egy pár órát az egyes csomópontokhoz. Ne folytassa, amíg a csomópont nem lett letiltva.
3. Csökkentse a virtuális gépek számát egy adott csomópont-típusban. A legmagasabb virtuálisgép-példány most el lesz távolítva.
4. Szükség szerint ismételje meg az 1 – 3. lépést, de az elsődleges csomópontban lévő példányok száma nem méretezhető, mint a megbízhatósági szintet. Lásd: [a Service Fabric-fürt kapacitásának megtervezése](./service-fabric-cluster-capacity.md) az ajánlott példányok listájához.
5. Ha minden virtuális gép el lett mentve ("Down"-ként jelenik meg), a Fabric:/System/InfrastructureService/[NODE name] hibaüzenetet jelenít meg. Ezután frissítheti a fürterőforrás-t a csomópont típusának eltávolításához. Használhatja az ARM-sablon üzembe helyezését, vagy szerkesztheti a fürterőforrás-t az [Azure Resource Managerrel](https://resources.azure.com). Ezzel elindítja a fürt frissítését, amely a hibás állapotú háló:/System/InfrastructureService/[NODE type] szolgáltatást fogja eltávolítani.
 6. Ezután törölheti is a VMScaleSet, ha a csomópontokat a Service Fabric Explorer nézetből "lefelé" fogja látni. Az utolsó lépés az, hogy megtisztítsa őket a `Remove-ServiceFabricNodeState` paranccsal.

## <a name="horizontal-scaling"></a>Horizontális skálázás

A horizontális skálázást [manuálisan](./service-fabric-cluster-scale-in-out.md) vagy [programozott](./service-fabric-cluster-programmatic-scaling.md)módon is elvégezheti.

> [!NOTE]
> Ha olyan csomópont-típust használ, amely ezüst vagy arany tartósságot tartalmaz, a skálázás lassú lesz.

### <a name="scaling-out"></a>Méretezés

Service Fabric-fürt felskálázása egy adott virtuálisgép-méretezési csoport példányszámának növelésével. A kapacitás növeléséhez programozott módon, a `AzureClient` és a kívánt méretezési csoport azonosítójának használatával is méretezhető.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

A manuális méretezéshez frissítse a kapacitást a kívánt [virtuálisgép-méretezési készlet](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás SKU tulajdonságában.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skálázás folyamatban

A-ben a méretezés több szempontot igényel, mint a horizontális felskálázás. Például:

* Service Fabric rendszerszolgáltatások a fürt elsődleges csomópont-típusa alatt futnak. Soha ne állítsa le vagy méretezze át az adott csomópont típusú példányok számát, hogy kevesebb példánya legyen, mint a megbízhatósági szinten. 
* Állapot-nyilvántartó szolgáltatás esetén bizonyos számú csomópontra van szükség, amely mindig a rendelkezésre állás fenntartása és a szolgáltatás állapotának megőrzése érdekében szükséges. Legalább egy olyan csomópontra van szükség, amely megegyezik a partíció vagy szolgáltatás célként megadott replikáinak számával.

A manuális méretezéshez kövesse az alábbi lépéseket:

1. A PowerShellből futtassa `Disable-ServiceFabricNode` a parancsot, `RemoveNode` hogy letiltsa az eltávolítani kívánt csomópontot. Távolítsa el a legmagasabb számú csomópont-típust. Ha például hat csomópontos fürttel rendelkezik, távolítsa el a "MyNodeType_5" virtuálisgép-példányt.
2. A futtatásával `Get-ServiceFabricNode` Győződjön meg arról, hogy a csomópont le van-e tiltva. Ha nem, várja meg, amíg le nem tiltja a csomópontot. Ez eltarthat egy pár órát az egyes csomópontokhoz. Ne folytassa, amíg a csomópont nem lett letiltva.
3. Csökkentse a virtuális gépek számát egy adott csomópont-típusban. A legmagasabb virtuálisgép-példány most el lesz távolítva.
4. A kívánt kapacitás kiépítése előtt ismételje meg az 1 – 3. lépést. Ne méretezze át az elsődleges csomópontokban lévő példányok számát a megbízhatósági szinteknél kisebb értékre. Lásd: [a Service Fabric-fürt kapacitásának megtervezése](./service-fabric-cluster-capacity.md) az ajánlott példányok listájához.

Ha manuálisan szeretné méretezni a méretezést, frissítse a kapacitást a virtuálisgép- [méretezési csoport](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) kívánt erőforrás SKU tulajdonságában.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

A csomópont programozott módon történő skálázásához elő kell készítenie a csomópontot a Leállítás érdekében. Keresse meg az eltávolítandó csomópontot (a legmagasabb példányú csomópont). Például:

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Inaktiválja és távolítsa el a csomópontot ugyanazzal a `FabricClient` példánnyal (ebben az `client` esetben) és a csomópont-példánnyal (ebben az `instanceIdString` esetben), amelyet az előző kódban használt:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Ha egy fürtben méretezi a méretezést, az eltávolított csomópont-vagy virtuálisgép-példány nem kifogástalan állapotban jelenik meg Service Fabric Explorerban. Ennek a viselkedésnek a magyarázata: [Service Fabric Explorerban megfigyelhető viselkedések](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer). A következőket teheti:
> * Hívja meg a [Remove-ServiceFabricNodeState parancsot](/powershell/module/servicefabric/remove-servicefabricnodestate) a megfelelő csomópont-névvel.
> * Telepítse az [Service Fabric autoscale Helper alkalmazást](https://github.com/Azure/service-fabric-autoscale-helper/) a fürtön. Ez az alkalmazás biztosítja, hogy a méretezett csomópontok törlődnek Service Fabric Explorerból.

## <a name="reliability-levels"></a>Megbízhatósági szintek

A [megbízhatósági szint](./service-fabric-cluster-capacity.md) a Service Fabric fürterőforrás egyik tulajdonsága. Az egyes csomópont-típusok esetében nem konfigurálható másképpen. A fürt rendszerszolgáltatásainak replikációs tényezőjét vezérli, és a fürt erőforrás-szintjének beállítása. 

A megbízhatósági szint határozza meg, hogy az elsődleges csomópont típusa hány csomópontot tartalmazhat. A megbízhatósági szintek a következő értékeket vehetik igénybe:

* Platinum: a rendszerszolgáltatások futtatásához a célként megadott replika a hét és a kilenc mag csomópontot határozza meg.
* Gold: a rendszerszolgáltatások futtatásához a célként megadott replikák hét és hét mag csomópontot tartalmazó száma.
* Ezüst: a rendszerszolgáltatások futtatásához egy célként megadott replika-készletet öt és öt mag csomóponttal.
* Bronz: a rendszerszolgáltatások futtatásához a célként megadott replika három és három vetőmag-csomópontot tartalmazhat.

A minimálisan ajánlott megbízhatósági szint Silver.

A megbízhatósági szint a [Microsoft. ServiceFabric/Clusters erőforrás](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)tulajdonságok részében van beállítva, a következőhöz hasonlóan:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Tartóssági szintek

> [!WARNING]
> A bronz tartóssággal futó csomópont-típusok _nem kapnak jogosultságot_. Az állapot nélküli munkaterheléseket befolyásoló infrastrukturális feladatok nem lesznek leállítva vagy késleltetve, ami hatással lehet a számítási feladatokra. 
>
> A bronz tartósságot csak olyan csomópont-típusok esetében használja, amelyek állapot nélküli munkaterheléseket futtatnak. Éles számítási feladatokhoz futtassa az Silver vagy a Higher parancsot az állapot konzisztenciájának biztosítása érdekében. Válassza ki a megfelelő megbízhatóságot a [kapacitás megtervezése dokumentációjában](./service-fabric-cluster-capacity.md)található útmutatás alapján.

A tartóssági szintet két erőforrásban kell megadni. Az egyik a [virtuálisgép-méretezési csoport erőforrásának](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)bővítmény-profilja:

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

A másik erőforrás a `nodeTypes` [Microsoft. ServiceFabric/Clusters erőforrás](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)alatt található: 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Következő lépések

* Hozzon létre egy fürtöt a virtuális gépeken vagy a Windows Servert futtató számítógépeken: [Service Fabric a fürt létrehozását a Windows Server](service-fabric-cluster-creation-for-windows-server.md)rendszerhez.
* Hozzon létre egy fürtöt a virtuális gépeken vagy Linuxon futó számítógépeken: [hozzon létre egy linuxos fürtöt](service-fabric-cluster-creation-via-portal.md).
* További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
