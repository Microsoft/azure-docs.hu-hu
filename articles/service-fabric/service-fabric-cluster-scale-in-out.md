---
title: Service Fabric-fürt méretezése vagy kibontása
description: Az egyes csomópontok és virtuálisgép-méretezési csoportokra vonatkozó automatikus méretezési szabályok beállításával az igény szerint méretezheti a Service Fabric-fürtöt az igényeknek megfelelően. Csomópontok hozzáadása vagy eltávolítása Service Fabric fürthöz
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409960"
---
# <a name="scale-a-cluster-in-or-out"></a>Fürt horizontális fel- és leskálázása

> [!WARNING]
> A méretezés előtt olvassa el ezt a szakaszt

A számítási erőforrások méretezése az alkalmazás munkahelyi terhelésének forrásához szándékos tervezést igényel, és szinte mindig hosszabb időt vesz igénybe, mint egy órát az éles környezet befejezéséhez, és meg kell ismernie a munkaterhelést és az üzleti környezetet; Ha még nem tette meg ezt a tevékenységet, javasoljuk, hogy először olvassa el és ismerkedjen meg [Service Fabric a fürt kapacitásának megtervezésével kapcsolatos szempontokkal](service-fabric-cluster-capacity.md), mielőtt folytatná a dokumentum hátralévő részét. Ez a javaslat a nem szándékolt LiveSite problémák elkerülésére szolgál, és azt is javasoljuk, hogy tesztelje a nem éles környezetben elvégezhető műveleteket. A [termelési problémák bármikor bejelenthetnek, vagy az Azure-ra vonatkozó fizetett támogatást kérhetnek](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). A megfelelő környezettel rendelkező műveletek végrehajtásához kiosztott mérnökök esetében ez a cikk leírja a skálázási műveleteket, de el kell döntenie, hogy mely műveletek megfelelőek a használati esethez; ilyenek például a méretezni kívánt erőforrások (CPU, tárolás, memória), a skálázási irány (vertikálisan vagy vízszintesen), valamint a végrehajtandó műveletek (erőforrás Template deployment, portál, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Service Fabric-fürt méretezése automatikus méretezési szabályokkal vagy manuális módszerrel
A virtuálisgép-méretezési csoportok egy olyan Azure-beli számítási erőforrás, amellyel készletként telepítheti és kezelheti a virtuális gépek gyűjteményét. A Service Fabric-fürtben definiált összes csomópont-típus külön virtuálisgép-méretezési csoportként van beállítva. Ezután az egyes csomópont-típusok egymástól függetlenül méretezhetők, különböző portokat nyitnak meg, és rendelkezhetnek különböző kapacitási metrikákkal. További információ a [Service Fabric Node types](service-fabric-cluster-nodetypes.md) dokumentumban olvasható. Mivel a fürtben lévő Service Fabric csomópontok virtuálisgép-méretezési csoportokból állnak a háttérben, be kell állítania az automatikus skálázási szabályokat minden egyes csomópont típus/virtuálisgép-méretezési csoport számára.

> [!NOTE]
> Az előfizetésnek elegendő maggal kell rendelkeznie ahhoz, hogy hozzáadja a fürtöt alkotó új virtuális gépeket. Jelenleg nincs modell-érvényesítés, ezért ha a kvóta bármely korlátja teljesül, az üzembe helyezési idő sikertelen lesz. Egyetlen csomópont-típus sem lehet egyszerűen a 100 csomópontok VMSS. Előfordulhat, hogy hozzá kell adnia a VMSS-t a célként megadott skála eléréséhez, és az automatikus skálázás nem tudja automatikusan felvenni a VMSS. A VMSS egy élő fürthöz való elhelyezése egy kihívást jelentő feladat, amely általában az új fürtök kiosztását eredményezi a létrehozáskor kiépített megfelelő csomópont-típusokkal. Ennek megfelelően [tervezze meg a fürt kapacitását](./service-fabric-cluster-capacity.md) . 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Válassza ki a csomópont típusát/virtuálisgép-méretezési csoportját a skálázáshoz
Jelenleg nem lehet megadnia a virtuálisgép-méretezési csoportokra vonatkozó automatikus méretezési szabályokat a portál használatával Service Fabric-fürt létrehozásához, ezért Azure PowerShell (1.0 +) használatával listázhatja a csomópontok típusait, majd az automatikus méretezési szabályokat is megadhatja.

A fürtöt alkotó virtuálisgép-méretezési csoport listájának lekéréséhez futtassa a következő parancsmagokat:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Automatikus méretezési szabályok beállítása a csomópont típusa/virtuálisgép-méretezési csoport számára
Ha a fürt több csomópont-típussal rendelkezik, akkor ismételje meg ezt minden olyan csomópont-típus/virtuálisgép-méretezési csoport esetében, amelyet szeretne méretezni (be vagy ki). Vegye figyelembe a szükséges csomópontok számát, mielőtt beállítja az automatikus skálázást. Az elsődleges csomóponttípushoz megadott minimálisan szükséges csomópontszámot a kiválasztott megbízhatósági szint határozza meg. További információ a [megbízhatósági szintekről](service-fabric-cluster-capacity.md).

> [!NOTE]
> Az elsődleges csomópont típusának kisebbnek kell lennie, mint a minimális szám, a fürt instabillá válik, vagy akár le is állítja azt. Ez adatvesztést eredményezhet az alkalmazások és a rendszerszolgáltatások számára.
> 
> 

Jelenleg az automatikus méretezési funkciót nem az alkalmazások által az Service Fabric számára jelentett terhelések vezérlik. Ebben az esetben az automatikus méretezést kizárólag a virtuálisgép-méretezési csoport példányai által kibocsátott teljesítményszámlálók vezérlik.  

A [virtuálisgép-méretezési csoportokhoz tartozó automatikus méretezés beállításához](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)kövesse az alábbi utasításokat.

> [!NOTE]
> Abban az esetben, ha a csomópont típusa nem rendelkezik az arany vagy ezüst [tartóssági szinttel][durability] , a [Remove-ServiceFabricNodeState parancsmagot](/powershell/module/servicefabric/remove-servicefabricnodestate) a megfelelő csomópont nevével kell meghívni. A bronz tartósság esetében nem ajánlott egyszerre egynél több csomóponton méretezni a méretezést.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Virtuális gépek manuális felvétele a csomópont típusú/virtuálisgép-méretezési csoportba

A horizontális felskálázás során további virtuálisgép-példányokat ad a méretezési csoporthoz. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. 

> [!NOTE]
> A virtuális gépek hozzáadása időt vesz igénybe, ezért ne várja meg, hogy a hozzáadások azonnaliek legyenek. Tervezze meg, hogy a kapacitást előre adja hozzá, így több mint 10 percet is igénybe vehet, mielőtt a virtuális gép kapacitása elérhető legyen a replikák/szolgáltatási példányok elhelyezéséhez.
> 

### <a name="add-vms-using-a-template"></a>Virtuális gépek hozzáadása sablon használatával
A virtuális gépek számának módosításához kövesse a gyors üzembe helyezési [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) katalógusának mintáját/utasításait. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Virtuális gépek hozzáadása a PowerShell vagy a parancssori felület parancsaival
A következő kód név alapján megkeres egy méretezési csoportot, és 1-gyel növeli a **kapacitását**.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Ez a kód 6-ra állítja a kapacitást.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Virtuális gépek manuális eltávolítása csomópont típusú vagy virtuálisgép-méretezési csoportból
Ha egy csomópont típusú méretezést végez, távolítsa el a virtuálisgép-példányokat a méretezési csoportból. Ha a csomópont típusa bronz tartóssági szint, Service Fabric nem ismeri, hogy mi történt, és azt jelenti, hogy egy csomópont eltűnt. A Service Fabric ekkor a fürt hibás állapotát jelzi. A helytelen állapot megakadályozása érdekében explicit módon el kell távolítania a csomópontot a fürtből, és el kell távolítania a csomópont állapotát.

A Service Fabric rendszerszolgáltatások a fürt elsődleges csomópont-típusa alatt futnak. Az elsődleges csomópont típusának skálázásakor soha ne méretezze át a példányok számát a [megbízhatósági szinteknél](service-fabric-cluster-capacity.md) kisebb értékre. 
 
Az állapot-nyilvántartó szolgáltatások esetében bizonyos számú csomópontra van szükség, amely mindig a rendelkezésre állás fenntartása és a szolgáltatás állapotának megőrzése. Legalább a partíció/szolgáltatás célként megadott replikáinak számával megegyező csomópontok száma szükséges.

### <a name="remove-the-service-fabric-node"></a>A Service Fabric-csomópont eltávolítása

A csomópont-állapot manuális eltávolításának lépései csak a *bronz* tartóssági szintű csomópont-típusokra vonatkoznak.  Az *ezüst* és az *arany* tartóssági szinten ezeket a lépéseket a platform automatikusan végrehajtja. A tartóssággal kapcsolatos további információ: [Service Fabric-fürtök kapacitástervezése][durability].

>[!NOTE]
> Legalább öt csomópontot kell fenntartania minden olyan virtuálisgép-méretezési csoportnál, amelynél a tartóssági szint (arany vagy ezüst) engedélyezve van. Ha a küszöbérték alatt méretezi a korlátot, a fürt hibás állapotba kerül, és manuálisan kell törölnie az eltávolított csomópontokat.

Annak érdekében, hogy a fürt csomópontjainak megoszlása egyenletes legyen a frissítési és tartalék tartományokban, illetve ezáltal egyenletesen legyen a kihasználtságuk, elsőként a legutóbb létrehozott csomópontot kell eltávolítani. Más szóval a csomópontokat létrehozásuk fordított sorrendjében kell eltávolítani. A legutóbb létrehozott csomópont rendelkezik a legnagyobb `virtual machine scale set InstanceId` tulajdonságértékkel. Az alábbi kódpéldák a legutóbb létrehozott csomópontot adják vissza.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

A Service Fabric-fürtnek értesülnie kell róla, hogy ez a csomópont el lesz távolítva. Három lépést kell végrehajtania:

1. Tiltsa le a csomópontot, hogy ezentúl ne replikálja az adatokat.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Állítsa le a csomópontot, hogy a Service Fabric-futtatókörnyezet szabályszerűen álljon le, és az alkalmazás kapjon egy megszakítási kérelmet.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Távolítsa el a csomópontot a fürtről.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

A fenti három lépés alkalmazása után a csomópont eltávolítható a méretezési csoportból. A [Bronzon][durability] kívül bármely más tartóssági szint használata esetén a rendszer automatikusan végrehajtja ezeket a lépéseket a méretezési csoport adott példányának eltávolításakor.

A következő kódrészlet lekérdezi az utolsó létrehozott csomópontot, majd letiltja, leállítja, és eltávolítja a fürtről.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Az alábbi **sfctl** kódban a következő parancs kéri le a legutóbb létrehozott csomópont **node-name** értékét: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> A következő **sfctl**-lekérdezésekkel ellenőrizheti az egyes lépések állapotát
>
> **Inaktiválási állapot keresése**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Leállítás állapotának keresése**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>A méretezési csoport horizontális leskálázása

Most, hogy a Service Fabric-csomópont el lett távolítva a fürtből, a virtuálisgép-méretezési csoport leskálázható. Az alábbi példa 1-gyel csökkenti a méretezési csoport kapacitását.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Ez a kód a kapacitást 5-re állítja.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>A Service Fabric Explorerban megfigyelhető viselkedések
A fürt felskálázásakor a Service Fabric Explorer tükrözi a fürt részét képező csomópontok (virtuálisgép-méretezési csoport példányai) számát.  Ha azonban a fürt méretezését fogja látni, akkor az eltávolított csomópont-vagy virtuálisgép-példány nem Kifogástalan állapotba kerül, hacsak nem hívja a [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) fájlt a megfelelő csomópont nevével.   

Itt látható ennek a viselkedésnek a magyarázata.

A Service Fabric Explorerban felsorolt csomópontok tükrözik, hogy a Service Fabric rendszerszolgáltatások (vagyis az FM) milyen számú csomóponttal rendelkeznek a fürtben. A virtuálisgép-méretezési csoportnak a ben történő skálázásakor a rendszer törölte a virtuális gépet, de az FM rendszerszolgáltatás továbbra is azt gondolja, hogy a csomópont (amely a törölt virtuális gépre van leképezve) vissza fog térni. Így Service Fabric Explorer továbbra is megjeleníti ezt a csomópontot (bár az állapot hibás vagy ismeretlen lehet).

Annak biztosítása érdekében, hogy a rendszer eltávolítsa a csomópontot egy virtuális gép eltávolításakor, két lehetőség közül választhat:

1. Válassza ki a fürtben található csomópont-típusoknál az arany vagy ezüst tartóssági szintet, amely az infrastruktúra-integrációt biztosítja. Ekkor a rendszer automatikusan eltávolítja a csomópontokat a rendszerszolgáltatások (FM) állapotáról a méretezés során.
Tekintse [meg a tartóssági szintek részleteit itt](service-fabric-cluster-capacity.md)

> [!NOTE]
> Legalább öt csomópontot kell fenntartania minden olyan virtuálisgép-méretezési csoportnál, amelynél a tartóssági szint (arany vagy ezüst) engedélyezve van. Ha a küszöbérték alatt méretezi a korlátot, a fürt hibás állapotba kerül, és manuálisan kell törölnie az eltávolított csomópontokat.

2. A virtuálisgép-példány skálázása után meg kell hívnia a [Remove-ServiceFabricNodeState parancsmagot](/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric fürtökhöz szükség van bizonyos számú csomópontra a rendelkezésre állás fenntartása és az állapot megőrzése érdekében – a továbbiakban "a kvórum fenntartása". Így általában nem biztos, hogy leállítja a fürtben lévő összes gépet, hacsak nem végezte el az [állapot teljes biztonsági mentését](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Következő lépések
A következő cikkből megtudhatja, hogyan tervezheti meg a fürt kapacitását, hogyan frissítheti a fürtöket és particionálhatja a szolgáltatásokat:

* [A fürt kapacitásának megtervezése](service-fabric-cluster-capacity.md)
* [Fürt frissítései](service-fabric-cluster-upgrade.md)
* [Állapot-nyilvántartó szolgáltatások a maximális skálázáshoz](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
