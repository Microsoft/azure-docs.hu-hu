---
title: Csomópont-típus eltávolítása az Azure Service Fabricban | Microsoft Docs
description: Megtudhatja, hogyan távolíthat el egy csomópont-típust egy Azure-ban futó Service Fabric-fürtből.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88163577"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Service Fabric csomópont típusának eltávolítása
Ez a cikk azt ismerteti, hogyan méretezhető egy Azure Service Fabric-fürt egy meglévő csomópont-típus fürtből való eltávolításával. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A virtuálisgép-méretezési csoportok egy Azure-beli számítási erőforrás, amely készletként telepíti és felügyeli a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt egy csomópont-típus (virtuálisgép-méretezési csoport) és annak összes csomópontjának eltávolításával.  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

> [!WARNING]
> Ha ezt a módszert használja egy csomópont típusának éles fürtből való eltávolítására, nem ajánlott gyakran használni. Ez egy veszélyes parancs, mivel törli a virtuálisgép-méretezési csoport erőforrását a csomópont típusa mögött. 

## <a name="durability-characteristics"></a>Tartóssági jellemzők
A Remove-AzServiceFabricNodeType használata esetén a biztonság prioritást élvez a sebességnél. A csomópont típusának ezüst vagy arany [tartóssági szintűnek](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)kell lennie, mert:
- A bronz nem nyújt garanciát az állapotadatok mentésével kapcsolatban.
- Az ezüst és az arany tartóssága a méretezési csoport minden módosítását elvégzi.
- Az arany a méretezési csoport alatti Azure-frissítések felügyeletét is lehetővé teszi.

Service Fabric "összehangolja" a mögöttes változásokat és frissítéseket, így az adatvesztés nem vész el. Ha azonban egy bronz tartósságú csomópont-típust távolít el, az állapotadatok elvesztésével járhat. Ha eltávolít egy elsődleges csomópont-típust, és az alkalmazása állapot nélküli, a bronz elfogadható. Az állapot-nyilvántartó számítási feladatok éles környezetben való futtatásakor a minimális konfigurációnak ezüst típusúnak kell lennie. Az éles környezetekhez hasonlóan az elsődleges csomópont típusa mindig ezüst vagy arany lehet.

### <a name="more-about-bronze-durability"></a>További információ a bronz tartósságról

A bronz típusú csomópontok eltávolításakor a csomópont összes csomópontja azonnal leáll. A Service Fabric nem rendelkezik a bronz csomópontok méretezési csoportjának frissítéseivel, így az összes virtuális gép azonnal leáll. Ha a csomópontok állapota nem megfelelő, az adat elvész. Most még ha állapot nélküli is volt, a Service Fabric összes csomópontja részt vesz a gyűrűn, így a teljes környék elvész, ami a fürt destabilizálása lehet.

## <a name="remove-a-node-type"></a>Csomóponttípus eltávolítása

1. A folyamat megkezdése előtt ügyeljen erre az előfeltételekre.

    - A fürt állapota Kifogástalan.
    - A csomópont-típus eltávolítása után továbbra is elegendő kapacitás áll rendelkezésre, például:. a szükséges replikák számát elhelyező csomópontok száma.

2. Helyezze át az elhelyezési korlátozásokkal rendelkező összes szolgáltatást a csomópont típusának kikapcsolásához.

    - Módosítsa az alkalmazás/szolgáltatás jegyzékfájlját úgy, hogy a továbbiakban ne hivatkozzon a csomópont típusára.
    - Telepítse a változást.

    Ezután ellenőrizze a következőket:
    - Az összes fent módosított szolgáltatás már nem fut a csomópont-típushoz tartozó csomóponton.
    - Az összes szolgáltatás kifogástalan.

3. A csomópont típusának megjelölése nem elsődlegesként (kihagyás a nem elsődleges csomópont-típusokhoz)

    - Keresse meg az üzembe helyezéshez használt Azure Resource Manager sablont.
    - Keresse meg a csomópont típusával kapcsolatos szakaszt a Service Fabric szakaszban.
    - Módosítsa a isPrimary tulajdonságot hamis értékre. * * Ne távolítsa el az ebben a feladatban a csomópont típusával kapcsolatos szakaszt.
    - Telepítse a módosított Azure Resource Manager sablont. * * A fürt konfigurációjától függően ez a lépés eltarthat egy ideig.
    
    Ezután ellenőrizze a következőket:
    - A portál Service Fabric szakasza azt jelzi, hogy a fürt készen áll.
    - A fürt állapota Kifogástalan.
    - A csomópont-típushoz tartozó csomópontok egyike sincs megjelölve mag-csomópontként.

4. Tiltsa le a csomópont típusának minden csomópontját.

    Kapcsolódjon a fürthöz a PowerShell használatával, majd futtassa a következő lépést.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - A bronz tartósság érdekében várjon, amíg az összes csomópont letiltott állapotba kerül
    - Az ezüst és az arany tartóssága érdekében egyes csomópontok le lesznek tiltva, a REST pedig letiltási állapotba kerül. Ellenőrizze, hogy a csomópontok részletek lapján letiltott állapotban van-e, ha mind beragadt az infrastruktúra-szolgáltatási partíciók Kvórumának biztosítására, akkor biztonságos a folytatás.

5. Állítsa le a csomópont típusának adatait.

    Kapcsolódjon a fürthöz a PowerShell használatával, majd futtassa a következő lépést.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Várjon, amíg a csomópontok minden csomópontja meg van jelölve.

6. Csomópontok felszabadítása az eredeti virtuálisgép-méretezési csoportból
    
    Jelentkezzen be az Azure-előfizetésbe, ahol a méretezési csoport üzembe lett állítva, és távolítsa el a virtuálisgép-méretezési csoportját. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Távolítsa el a csomópont típusának adatait.

    Kapcsolódjon a fürthöz a PowerShell használatával, majd futtassa a következő lépést.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Várjon, amíg a rendszer eltávolítja az összes csomópontot a fürtből. A csomópontok nem jelennek meg az SFX-ben.

8. Csomópont típusának eltávolítása Service Fabric szakaszból.

    - Keresse meg az üzembe helyezéshez használt Azure Resource Manager sablont.
    - Keresse meg a csomópont típusával kapcsolatos szakaszt a Service Fabric szakaszban.
    - Távolítsa el a csomópont típusának megfelelő szakaszt.
    - Csak ezüst és magasabb tartósságú fürtök esetén frissítse a sablonban lévő fürterőforrás-erőforrást, és konfigurálja az állapot-szabályzatokat a háló:/rendszeralkalmazási állapot mellőzéséhez a `applicationDeltaHealthPolicies` fürterőforrás területen az `properties` alább megadott módon. Az alábbi házirend figyelmen kívül hagyja a meglévő hibákat, de nem engedélyezi az új állapotú hibákat. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Telepítse a módosított Azure Resource Manager sablont. * * Ez a lépés eltarthat egy ideig, általában akár két óráig is. Ez a frissítés megváltoztatja a beállításokat a InfrastructureService, ezért szükség van a csomópontok újraindítására. Ebben az esetben a `forceRestart` rendszer figyelmen kívül hagyja a következőt:. 
    A paraméter `upgradeReplicaSetCheckTimeout` Megadja azt a maximális időtartamot, ameddig Service Fabric a partíció biztonságos állapotba kerül, ha még nem biztonságos állapotban van. Miután a biztonsági ellenőrzés egy csomóponton lévő összes partícióra kiterjed, Service Fabric folytatja a frissítést a csomóponton.
    A paraméter értéke `upgradeTimeout` 6 órára csökkenthető, de a maximális biztonság érdekében 12 órát kell használni.

    Ezután ellenőrizze a következőket:
    - A portálon Service Fabric erőforrás készen áll.

9. Távolítsa el az ARM-sablon csomópont-típusára vonatkozó összes hivatkozást.

    - Keresse meg az üzembe helyezéshez használt Azure Resource Manager sablont.
    - Távolítsa el a virtuálisgép-méretezési csoport és a csomópont típusához kapcsolódó egyéb erőforrásokat a sablonból.
    - Telepítse a módosításokat.

    Ezután:
    - Várjon, amíg a telepítés befejeződik.
    
10. Távolítsa el a már nem használt csomópont-típushoz kapcsolódó erőforrásokat. Példa Load Balancer és nyilvános IP-címekre. 

    - Az erőforrások eltávolításához használhatja ugyanazt a PowerShell-parancsot, amelyet a 6. lépésben az adott erőforrástípus és API-verzió megadásakor használt. 

> [!Note]
> Ez a lépés nem kötelező, ha ugyanaz a Load Balancer, és az IP-cím a csomópontok típusai között újra használatban van.

## <a name="next-steps"></a>Következő lépések
- További információ a fürt [tartóssági jellemzőiről](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster).
- További információ a [csomópontok típusairól és Virtual Machine Scale sets](service-fabric-cluster-nodetypes.md).
- További információ a [Service Fabric-fürt skálázásáról](service-fabric-cluster-scaling.md).
