---
title: Hibaelhárítás rendszerállapot-jelentések segítségével
description: Az Azure Service Fabric Components által elküldett, a fürthöz vagy az alkalmazásokkal kapcsolatos problémák elhárítására szolgáló állapotjelentést ismerteti
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.openlocfilehash: 8e60ac5065c2f9543a641daf4f62299c00c61fc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000657"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Rendszerállapot-jelentések használata a hibaelhárítás során
Az Azure Service Fabric-összetevők rendszerállapot-jelentéseket biztosítanak a fürtben lévő összes entitáshoz, közvetlenül a jelölőnégyzetből. A [Health Store](service-fabric-health-introduction.md#health-store) a rendszerjelentések alapján hozza létre és törli az entitásokat. Azt is megszervezi egy hierarchiában, amely rögzíti az entitások interakcióit.

> [!NOTE]
> Az állapottal kapcsolatos fogalmak megismeréséhez tekintse meg a [Service Fabric Health Model](service-fabric-health-introduction.md)című témakört.
> 
> 

A rendszerállapot-jelentések a fürt és az alkalmazás funkcióinak láthatóságát, valamint a problémák megjelölését teszik lehetővé. Alkalmazások és szolgáltatások esetén a rendszerállapot-jelentések ellenőrzik, hogy az entitások implementálva vannak-e, és megfelelően viselkednek-e a Service Fabric perspektívában. A jelentések nem biztosítják a szolgáltatás üzleti logikájának vagy a nem válaszoló folyamatok észlelésének állapotát. A felhasználói szolgáltatások az állapotadatokat az adott logikával kapcsolatos adatokkal gazdagítják.

> [!NOTE]
> A felhasználói watchdogok által elküldett állapotjelentés csak akkor láthatók, *Ha* a rendszerösszetevők létrehoznak egy entitást. Ha töröl egy entitást, az állapotfigyelő szolgáltatás automatikusan törli az összes hozzá tartozó állapotjelentést. Ugyanez érvényes az entitás új példányának létrehozásakor. Ilyen például, amikor létrejön egy új állapot-nyilvántartó megőrzött szolgáltatás másodpéldány-példánya. A régi példányhoz társított összes jelentés törlődik, és a rendszer törli az áruházból.
> 
> 

A rendszerösszetevő-jelentéseket a forrás azonosítja, amely a "rendszer" kifejezéssel kezdődik **.** előtagot. A watchdog nem használhatja ugyanazt az előtagot a forrásaihoz, mert a rendszer visszautasítja az érvénytelen paraméterekkel rendelkező jelentéseket.

Tekintse át a rendszerjelentéseket, amelyekből megtudhatja, hogy mi váltja ki, és hogy miként lehet kijavítani az általuk képviselt lehetséges problémákat.

> [!NOTE]
> Service Fabric folytatja a jelentések hozzáadását olyan feltételekkel, amelyek segítségével megtekintheti, hogy mi történik a fürtben és az alkalmazásokban. A meglévő jelentések további részletekkel is kijavíthatók, hogy gyorsabban lehessen elhárítani a problémát.
> 
> 

## <a name="cluster-system-health-reports"></a>Fürt rendszerállapot-jelentései
A rendszer automatikusan létrehozza a fürt állapota entitást az állapotfigyelő tárolóban. Ha minden megfelelően működik, nem rendelkezik rendszerjelentéssel.

### <a name="neighborhood-loss"></a>Környék elvesztése
A **System. Federation** hibát jelez, amikor a környék elvesztését észleli. A jelentés az egyes csomópontokból származik, és a csomópont-azonosítót a tulajdonság neve tartalmazza. Ha az egyik környék elvész a teljes Service Fabric ringben, akkor általában két olyan eseményt várhat, amely a Gap-jelentés mindkét oldalát képviseli. Ha több környék is elvész, több esemény is van.

A jelentés az élettartam (TTL) globális címbérleti időtúllépését határozza meg. A jelentést a rendszer a TTL időtartamának minden felében újra elküldi, amíg a feltétel aktív marad. A rendszer automatikusan eltávolítja az eseményt, amikor lejár. A Remove-when-lejárt viselkedés biztosítja, hogy a jelentés megfelelően legyen megtisztítva az állapotfigyelő tárolóból, még akkor is, ha a jelentéskészítési csomópont nem működik.

* **SourceId forrásazonosító**: System. Federation
* **Tulajdonság**: a **szomszédsággal** kezdődik, és csomópont-információkat tartalmaz.
* **Következő lépések**: vizsgálja meg, miért elveszett a környék. Például vizsgálja meg a fürtcsomópontok közötti kommunikációt.

### <a name="rebuild"></a>Újraépítés

A Feladatátvételi felügyelő (FM) szolgáltatás kezeli a fürtcsomópontok információit. Ha az FM elveszti az adatait, és adatvesztéssel jár, nem tudja garantálni, hogy a fürt csomópontjaival kapcsolatos legnaprakészebb információval rendelkezik. Ebben az esetben a rendszer egy Újraépítés útján halad át, a System.FM pedig a fürt összes csomópontjának adatait gyűjti az állapotának újbóli létrehozásához. A hálózati vagy csomóponti problémák miatt előfordulhat, hogy az Újraépítés elakad, vagy leáll. Ugyanez a Feladatátvételi felügyelő Master (FMM) szolgáltatással is történhet. A FMM olyan állapot nélküli rendszerszolgáltatás, amely nyomon követi, hogy az összes FMs a fürtben legyen. A FMM elsődleges értéke mindig a 0 értékhez legközelebbi AZONOSÍTÓJÚ csomópont. Ha a csomópont eldobásra kerül, egy Újraépítés aktiválódik.
Ha az előző feltételek egyike történik, a **System.FM** vagy a **System. FMM** egy hibajelentésen keresztül jelzőt jelez. Az Újraépítés a két fázis egyikében elakadhat:

* **Várakozás a szórásra**: az FM/FMM megvárja a szórási üzenet válaszát a többi csomóponttól.

  * **Következő lépések**: vizsgálja meg, hogy van-e hálózati kapcsolati probléma a csomópontok között.
* **Várakozás a csomópontokra**: az FM/FMM már fogadott egy szórási választ a többi csomóponttól, és az adott csomópontok válaszára vár. Az állapotjelentés felsorolja azokat a csomópontokat, amelyekhez az FM/FMM választ vár.
   * **Következő lépések**: vizsgálja meg az FM/FMM és a felsorolt csomópontok közötti hálózati kapcsolatot. Vizsgálja meg az egyes felsorolt csomópontokat a lehetséges problémákkal kapcsolatban.

* **SourceId forrásazonosító**: System.FM vagy System. FMM
* **Tulajdonság**: Újraépítés.
* **Következő lépések**: vizsgálja meg a csomópontok közötti hálózati kapcsolatot, valamint az állapotjelentés leírásában felsorolt adott csomópontok állapotát.

### <a name="seed-node-status"></a>Mag csomópontjának állapota
A **System.FM** figyelmeztetést küld, ha egyes magok csomópontja nem kifogástalan állapotú. A magok csomópontjai a mögöttes fürt rendelkezésre állását fenntartó csomópontok. Ezek a csomópontok biztosítják, hogy a fürt a többi csomóponttal való bérletek létrehozásával, valamint bizonyos hálózati meghibásodások során tiebreakers szolgáljon. Ha a vetőmag-csomópontok többsége le van állítva a fürtben, és nem hozzák vissza, a fürt automatikusan leáll. 

A magok csomópontja nem kifogástalan állapotú, ha a csomópont állapota leállt, el lett távolítva vagy ismeretlen.
A vetőmag-csomópont állapotára vonatkozó figyelmeztetési jelentés felsorolja az összes nem kifogástalan állapotú vetőmag-csomópontot, részletes információkkal.

* **SourceId forrásazonosító**: System.FM
* **Tulajdonság**: SeedNodeStatus
* **Következő lépések**: Ha ez a figyelmeztetés a fürtben jelenik meg, kövesse az alábbi utasításokat a kijavításához: Service Fabric 6,5-es vagy újabb verzióját futtató fürt esetén: az Azure-beli Service Fabric-fürtön a magok csomópontjának leállása után a Service Fabric megkísérli a nem magot futtató csomópontok automatikus módosítását. Ennek elvégzéséhez győződjön meg arról, hogy az elsődleges csomópont típusában lévő nem Seed csomópontok száma nagyobb vagy egyenlő a lefelé irányuló mag csomópontjainak számával. Ha szükséges, vegyen fel további csomópontokat az elsődleges csomópont-típusba ennek eléréséhez.
A fürt állapotától függően eltarthat egy ideig a probléma megoldásában. Ha ez megtörtént, a rendszer automatikusan törli a figyelmeztetési jelentést.

Service Fabric önálló fürt esetében a figyelmeztetési jelentés törléséhez az összes vetőmag-csomópontnak Kifogástalan állapotba kell állítania. Attól függően, hogy a magok miért nem kifogástalanok, különböző műveleteket kell végrehajtania: Ha a mag csomópontja nem működik, a felhasználóknak be kell állítania a mag csomópontot; Ha a vetőmag-csomópont el van távolítva vagy ismeretlen, akkor ezt a vetőmag-csomópontot [el kell távolítani a fürtből](./service-fabric-cluster-windows-server-add-remove-nodes.md).
A figyelmeztetési jelentés automatikusan törlődik, amikor az összes vetőmag-csomópont Kifogástalan állapotba kerül.

A 6,5-nál régebbi Service Fabric verziót futtató fürtök esetében: ebben az esetben a figyelmeztetési jelentést manuálisan kell törölni. **A jelentés törlése előtt győződjön meg arról, hogy az összes mag-csomópont kifogástalan** állapotba kerül: Ha a magok csomópontja nem működik, a felhasználóknak el kell érniük a mag csomópontot; ha a vetőmag-csomópontot eltávolítja vagy ismeretlen, a vetőmag-csomópontot el kell távolítani a fürtből.
Miután az összes vetőmag-csomópont Kifogástalan állapotba került, a Powershellből a következő parancs használatával [törölje a figyelmeztetési jelentést](/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Tanúsítvány lejárata
A **System. FabricNode** figyelmeztetést küld, ha a csomópont által használt tanúsítványok közelében lejárnak. Egy csomóponton három tanúsítvány van: **Certificate_cluster**, **Certificate_server** és **Certificate_default_client**. Ha a lejárat legalább két hét múlva lejár, a jelentés állapota OK. Ha a lejárat két héten belül van, a jelentés típusa figyelmeztetés. Az események ÉLETTARTAMa végtelen, és akkor törlődnek, ha egy csomópont elhagyja a fürtöt.

* **SourceId forrásazonosító**: System. FabricNode
* **Tulajdonság**: a **tanúsítvánnyal** kezdődik, és további információkat tartalmaz a tanúsítvány típusáról.
* **Következő lépések**: frissítse a tanúsítványokat, ha a közeljövőben lejárnak.

### <a name="load-capacity-violation"></a>Teherbírás megsértése
A Service Fabric Load Balancer figyelmeztetést küld, ha a csomópont kapacitása megsértését észleli.

* **SourceId forrásazonosító**: System. PLB
* **Tulajdonság**: a **kapacitással** kezdődik.
* **Következő lépések**: Tekintse át a megadott metrikákat, és tekintse meg az aktuális kapacitást a csomóponton.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Csomópont-kapacitás nem egyezik az erőforrás-irányítási mérőszámok esetében
A System. hosting figyelmeztetést küld, ha a fürt jegyzékfájljában definiált csomópont-kapacitások nagyobbak, mint az erőforrás-irányítási metrikák (memória és CPU-magok) valódi csomópont-kapacitása. Egy állapotjelentés jelenik meg, ha az első olyan szolgáltatáscsomag, amely egy adott csomóponton [erőforrás-irányítási](service-fabric-resource-governance.md) regisztereket használ.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: **ResourceGovernance**.
* **Következő lépések**: Ez a probléma problémát jelenthet, mert a szolgáltatási csomagok szabályozása nem kényszerített a várt módon, és az [erőforrás-szabályozás](service-fabric-resource-governance.md) nem működik megfelelően. Frissítse a fürt jegyzékfájlját a metrikák megfelelő csomópont-kapacitásával, vagy ne határozza meg, és hagyja Service Fabric az elérhető erőforrások automatikus észlelését.

## <a name="application-system-health-reports"></a>Alkalmazásrendszer állapotáról készült jelentések
A Fürtfelügyelő szolgáltatást jelképező System.CM az alkalmazással kapcsolatos információkat kezelő szolgáltató.

### <a name="state"></a>Állapot
A System.CM-jelentések az alkalmazás létrehozásakor vagy frissítésekor OKként jelennek meg. A szolgáltatás törli az állapot-tárolót az alkalmazás törlésekor, hogy az eltávolítható legyen az áruházból.

* **SourceId forrásazonosító**: System.cm
* **Tulajdonság**: állapot.
* **Következő lépések**: Ha az alkalmazást létrehozták vagy frissítették, tartalmaznia kell a Fürtfelügyelő állapotjelentést. Ellenkező esetben egy lekérdezés kiadásával vizsgálja meg az alkalmazás állapotát. Használja például a **Get-ServiceFabricApplication-ApplicationName** *ApplicationName* PowerShell-parancsmagot.

Az alábbi példa a **Fabric:/WordCount** alkalmazás állapotjelző eseményét mutatja be:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Szolgáltatási rendszerállapot-jelentések
A Feladatátvételi felügyelő szolgáltatást jelképező System.FM a szolgáltatásokkal kapcsolatos információkat kezelő szolgáltató.

### <a name="state"></a>Állapot
A System.FM jelentések a szolgáltatás létrehozásakor rendben vannak. A szolgáltatás törlése után törli az entitást az állapotfigyelő tárolóból.

* **SourceId forrásazonosító**: System.FM
* **Tulajdonság**: állapot.

Az alábbi példa a Service **Fabric:/WordCount/wordcountwebservice szolgáltatásé viszont**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Szolgáltatás korrelációs hibája
A **System. PLB** hibát jelez, amikor azt észleli, hogy egy szolgáltatás frissítése összefügg egy másik szolgáltatással, amely affinitási láncot hoz létre. A jelentés törlődik, amikor sikeres frissítés történik.

* **SourceId forrásazonosító**: System. PLB
* **Tulajdonság**: **ServiceDescription leírásban**.
* **Következő lépések**: Tekintse meg a korrelált szolgáltatás leírását.

## <a name="partition-system-health-reports"></a>Partíció rendszerállapot-jelentései
A Feladatátvételi felügyelő szolgáltatást jelölő System.FM az a szolgáltató, amely a szolgáltatási partíciókkal kapcsolatos információkat kezeli.

### <a name="state"></a>Állapot
A System.FM-jelentések a partíció létrehozásakor és kifogástalan állapotában is rendben vannak. A partíció törlése után törli az entitást az állapotfigyelő tárolóból.

Ha a partíció a replikák minimális száma alatt van, hibát jelez. Ha a partíció nem a replika minimális száma alá esik, de a cél replikák száma alá esik, a rendszer figyelmeztetést jelenít meg. Ha a partíció kvórum elvesztése miatt következik be, a System.FM hibát jelez.

További jelentős események esetén figyelmeztetés jelenik meg, ha az újrakonfigurálás a vártnál hosszabb időt vesz igénybe, és ha a Build a vártnál tovább tart. A build és az újrakonfigurálás várható időpontja a szolgáltatási forgatókönyvek alapján konfigurálható. Ha például egy szolgáltatás egy terabájtos állapottal rendelkezik (például Azure SQL Database), akkor a Build hosszabb időt vesz igénybe, mint egy kis mennyiségű szolgáltatásnál.

* **SourceId forrásazonosító**: System.FM
* **Tulajdonság**: állapot.
* **Következő lépések**: Ha az állapot nem megfelelő, lehetséges, hogy egyes replikák nem lettek létrehozva, megnyitva vagy előléptetve az elsődleges vagy a másodlagos helyesen. 

Ha a Leírás leírja a kvórum elvesztését, és megvizsgálta, hogy a replikák milyen részletesen jelennek meg, és biztonsági másolatot készít róluk, a partíció ismét online állapotba kerül.

Ha a leírás egy olyan partíciót ír le, amely az [újrakonfigurálás](service-fabric-concepts-reconfiguration.md)során beragadt, akkor az elsődleges replikán található állapotjelentés további információkat tartalmaz.

Más System.FM-jelentések esetén a replikák, illetve a partíció vagy a szolgáltatás más rendszerösszetevőkből is készíthető jelentés. 

Az alábbi példák a jelentések némelyikét írják le. 

Az alábbi példa egy kifogástalan partíciót mutat be:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

A következő példa egy olyan partíció állapotát jeleníti meg, amely a cél replikáinak száma alatt van. A következő lépés a partíció leírásának beolvasása, amely a konfigurálásának módját mutatja: a **MinReplicaSetSize** három, a **TargetReplicaSetSize** pedig hét. Ezután szerezze be a fürtben lévő csomópontok számát, ami ebben az esetben öt. Így ebben az esetben két replika nem helyezhető el, mert a replikák megcélzott száma nagyobb, mint a rendelkezésre álló csomópontok száma.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Az alábbi példa egy olyan partíció állapotát mutatja, amely az újrakonfigurálás során beragadt, mivel a felhasználó nem tartja tiszteletben a **RunAsync** metódusban található lemondási tokent. Az elsődleges (P) jelölésű replika állapotáról szóló jelentés kivizsgálása segíthet a probléma további részletezésében.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Ez az állapotjelentés az újrakonfigurálás alatt álló partíció replikáinak állapotát jeleníti meg: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Az állapotjelentés az egyes replikák esetében az alábbiakat tartalmazza:
- Előző konfigurációs szerepkör
- Aktuális konfigurációs szerepkör
- [Replika állapota](service-fabric-concepts-replica-lifecycle.md)
- A csomópont, amelyen a replika fut
- Replika azonosítója

A példához hasonló esetben további vizsgálatra van szükség. Vizsgálja meg az egyes replikák állapotát az `Primary` `Secondary` előző példában (131482789658160654 és 131482789688598467) jelölt replikákkal kezdődően.

### <a name="replica-constraint-violation"></a>Replika megkötésének megsértése
A **System. PLB** figyelmeztetést küld, ha a replika korlátozásának megsértését észleli, és nem helyezi el az összes partíció replikáját. A jelentés részletei megmutatják, hogy mely megkötések és tulajdonságok akadályozzák meg a replika elhelyezését.

* **SourceId forrásazonosító**: System. PLB
* **Tulajdonság**: a **ReplicaConstraintViolation**-vel kezdődik.

## <a name="replica-system-health-reports"></a>Replika rendszerállapot-jelentései
Az újrakonfigurálási ügynök összetevőjét képviselő **System. ra**, a replika állapotának szolgáltatója.

### <a name="state"></a>Állapot
A System. RA-jelentések a replika létrehozásakor rendben vannak.

* **SourceId forrásazonosító**: System. ra
* **Tulajdonság**: állapot.

Az alábbi példa egy kifogástalan replikát mutat be:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Ez a tulajdonság a replikák megnyitása, a replika lezárása vagy egy replika egyik szerepkörből egy másikba való átváltásakor a figyelmeztetések és hibák jelzésére szolgál. További információ: [replika életciklusa](service-fabric-concepts-replica-lifecycle.md). Ebben az időszakban a meghibásodások az API-hívásoktól vagy a szolgáltatási gazdagép összeomlásával kapcsolatos kivételek lehetnek. A C#-kódból származó API-hívások miatti hibák esetén Service Fabric hozzáadja a kivételt és a verem nyomkövetését az állapotjelentés-jelentéshez.

Ezek az állapot-figyelmeztetések akkor következnek be, amikor a művelet a házirendtől függően bizonyos számú alkalommal újra helyire próbálkozik. Service Fabric újrapróbálkozik a művelettel a maximális küszöbértékig. A maximális küszöbérték elérésekor előfordulhat, hogy a rendszer megpróbálta kijavítani a helyzetet. Ez a kísérlet azt eredményezheti, hogy ezek a figyelmeztetések törölve lettek, mivel a művelet ezen a csomóponton is elérhető. Ha például egy replika nem tud megnyitni egy csomóponton, a Service Fabric állapot figyelmeztetést vált ki. Ha a replika továbbra sem fog megnyílni, Service Fabric önálló javítást hajt végre. Előfordulhat, hogy ez a művelet egy másik csomóponton próbálkozik. Ez a kísérlet azt eredményezi, hogy a rendszer törli a replika figyelmeztetését. 

* **SourceId forrásazonosító**: System. ra
* **Tulajdonság**: **ReplicaOpenStatus**, **ReplicaCloseStatus** és **ReplicaChangeRoleStatus**.
* **Következő lépések**: vizsgálja meg a szolgáltatási kódot vagy az összeomlási memóriaképeket, hogy megtudja, miért sikertelen a művelet.

Az alábbi példa egy olyan replika állapotát mutatja, amely a `TargetInvocationException` nyitott metódusból származik. A leírás tartalmazza a meghibásodási pontot, a **IStatefulServiceReplica. Open**, a kivétel típusát **TargetInvocationException** és a verem nyomkövetését.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Az alábbi példa egy olyan replikát mutat be, amely állandóan összeomlik a bezáráskor:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguration
Ezzel a tulajdonsággal jelezhető, hogy egy [újrakonfigurálást](service-fabric-concepts-reconfiguration.md) végző replika esetén az újrakonfigurálás megrekedt vagy megakadt. Ez az állapotjelentés azon a replikán lehet, amelynek a jelenlegi szerepköre elsődleges, kivéve a swap elsődleges újrakonfigurálásának eseteit, ahol a replika az elsődlegesről az aktív másodlagosra van lefokozni.

Az újrakonfigurálás a következő okok egyike miatt elakadhat:

- A helyi replika egyik művelete, az újrakonfigurálást végző replika, nem fejeződik be. Ebben az esetben az ezen a replikán lévő, más összetevőkből, a System. RAP-ból vagy a System.RE származó állapotjelentés kivizsgálása további információkat is biztosíthat.

- Egy művelet nem fejeződik be távoli replikán. A függőben lévő műveletek replikái a Health jelentésben vannak felsorolva. Ezen távoli replikák esetében további vizsgálatot kell végezni. A csomópont és a távoli csomópont között kommunikációs problémák is előfordulhatnak.

Ritka esetekben az újrakonfigurálás az adott csomópont és a Feladatátvételi felügyelő szolgáltatás közötti kommunikáció vagy egyéb problémák miatt beragadható.

* **SourceId forrásazonosító**: System. ra
* **Tulajdonság**: újrakonfigurálás.
* **Következő lépések**: vizsgálja meg a helyi vagy a távoli replikákat az állapotjelentés leírásának függvényében.

Az alábbi példa egy olyan állapotjelentést mutat be, amelyben az újrakonfigurálás megakad a helyi replikán. Ebben a példában egy olyan szolgáltatás okozza, amely nem tartja tiszteletben a lemondási jogkivonatot.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Az alábbi példa egy olyan állapotjelentést mutat be, amelyben az újrakonfigurálás megakad a két távoli replika válaszára való várakozáskor. Ebben a példában három replika van a partícióban, beleértve a jelenlegi elsődlegest is. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Ez az állapotjelentés azt mutatja, hogy az újrakonfigurálás megakadt a két replika válaszára való várakozáskor: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Az egyes replikák esetében az alábbi információk szerepelnek:
- Előző konfigurációs szerepkör
- Aktuális konfigurációs szerepkör
- [Replika állapota](service-fabric-concepts-replica-lifecycle.md)
- Csomópont-azonosító
- Replika azonosítója

Az újrakonfigurálás feloldása:
- A **lefelé** replikált replikákat fel kell vinni. 
- A **kiépített** replikáknak készen kell állnia a build és az áttérés befejezésére.

### <a name="slow-service-api-call"></a>Lassú szolgáltatás API-hívása
A **System. rap** és a **System. replikátor** figyelmeztetést küld, ha a felhasználói szolgáltatás kódjának meghívása a beállított időpontnál hosszabb időt vesz igénybe. A rendszer törli a figyelmeztetést, ha a hívás befejeződik.

* **SourceId forrásazonosító**: System. rap vagy System. replikátor
* **Tulajdonság**: a lassú API neve. A Leírás további részleteket tartalmaz arról, hogy az API Mikor van függőben.
* **Következő lépések**: vizsgálja meg, hogy a hívás miért tart a vártnál tovább.

Az alábbi példa a System. RAP rendszerállapot-eseményét mutatja be egy megbízható szolgáltatáshoz, amely nem tartja tiszteletben a lemondási tokent a **RunAsync**-ben:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

A tulajdonság és a szöveg jelzi, hogy melyik API ragadt. A különböző beragadt API-kkal kapcsolatos következő lépések eltérnek. A *IStatefulServiceReplica* vagy *IStatelessServiceInstance* lévő API-k általában egy hiba a szolgáltatás kódjában. A következő szakasz ismerteti, hogyan fordítja le ezeket a [Reliable Services modellre](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica. Open**: Ez a figyelmeztetés azt jelzi, hogy a (z), `CreateServiceInstanceListeners` `ICommunicationListener.OpenAsync` , vagy felülbírált hívása `OnOpenAsync` beragadt.

- **IStatefulServiceReplica. Bezárás** és **IStatefulServiceReplica. megszakítás**: a leggyakoribb eset egy olyan szolgáltatás, amely nem tartja tiszteletben az átadott visszavonási tokent `RunAsync` . Az is előfordulhat, hogy a ( `ICommunicationListener.CloseAsync` vagy felülbírált `OnCloseAsync` ) megakadt.

- **IStatefulServiceReplica. changerole művelet (S)** és **IStatefulServiceReplica. changerole művelet (N)**: a leggyakoribb eset egy olyan szolgáltatás, amely nem tartja tiszteletben az átadott visszavonási tokent `RunAsync` . Ebben az esetben a legjobb megoldás a replika újraindítása.

- **IStatefulServiceReplica. changerole művelet (P)**: a leggyakoribb eset az, hogy a szolgáltatás nem adott vissza feladatot a alkalmazásból `RunAsync` .

A beragadható egyéb API-hívások a **IReplicator** felületen találhatók. Például:

- **IReplicator. CatchupReplicaSet**: Ez a figyelmeztetés két dolog egyikét jelzi. Nincsenek elegendő replikák. Ha meg szeretné tekinteni, hogy ez a helyzet-e, tekintse meg a partíció replikáinak replika állapotát, vagy a System.FM állapotáról szóló jelentést egy beragadt újrakonfiguráláshoz. Vagy a replikák nem ismerik fel a műveleteket. A PowerShell-parancsmag segítségével `Get-ServiceFabricDeployedReplicaDetail` meghatározható az összes replika állapota. A probléma olyan replikákkal rendelkezik, amelyek `LastAppliedReplicationSequenceNumber` értéke az elsődleges érték mögött van `CommittedSequenceNumber` .

- **IReplicator. BuildReplica ( \<Remote ReplicaId> )**: Ez a figyelmeztetés problémát jelez a fordítási folyamat során. További információ: [replika életciklusa](service-fabric-concepts-replica-lifecycle.md). Ennek oka lehet a replikátor-címek helytelen konfigurációja. További információ: állapot- [nyilvántartó Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md) és [erőforrások megadása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md). A távoli csomóponton is lehet probléma.

### <a name="replicator-system-health-reports"></a>Replikátor rendszerállapot-jelentései
A **replikációs várólista megtelt:** 
 A **System. replikátor** figyelmeztetést küld, ha a replikációs várólista megtelt. Az elsődlegesen a replikációs várólista általában megtelik, mert egy vagy több másodlagos replika lassú a művelet elfogadásához. A másodlagosnál ez általában akkor fordul elő, ha a szolgáltatás lassan alkalmazza a műveleteket. A figyelmeztetés törlődik, ha a várólista már nem teljes.

* **SourceId forrásazonosító**: System. replikátor
* **Tulajdonság**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, a replika szerepkörtől függően.
* **Következő lépések**: Ha a jelentés az elsődleges, ellenőrizze a fürt csomópontjai közötti kapcsolatot. Ha az összes kapcsolat kifogástalan állapotú, akkor lehet, hogy legalább egy lassú másodlagos értékkel rendelkezik, és nagy lemezterületet alkalmaz a műveletekre. Ha a jelentés a másodlagos oldalon található, először ellenőrizze a lemez használatát és teljesítményét a csomóponton. Ezután jelölje be a lassú csomópontról az elsődlegesre irányuló kimenő kapcsolatokat.

**RemoteReplicatorConnectionStatus:** 
 Az elsődleges replika **System. replikátora** figyelmeztetést küld, ha a másodlagos (távoli) replikátorhoz való kapcsolódás nem kifogástalan állapotú. A távoli replikátor címe a jelentés üzeneteiben jelenik meg, így könnyebben észlelhető, ha a hibás konfigurációt átadták, vagy ha hálózati problémák vannak a replikálók között.

* **SourceId forrásazonosító**: System. replikátor
* **Tulajdonság**: **RemoteReplicatorConnectionStatus**.
* **Következő lépések**: Ellenőrizze a hibaüzenetet, és győződjön meg arról, hogy a távoli replikátor címe megfelelően van-e konfigurálva. Ha például a távoli replikátor a "localhost" figyelési címhez van megnyitva, az nem érhető el a kívülről. Ha a címnek helyesnek tűnik, ellenőrizze az elsődleges csomópont és a távoli címe közötti kapcsolatot, hogy megtalálja a lehetséges hálózati problémákat.

### <a name="replication-queue-full"></a>A replikációs várólista megtelt
A **System. replikátor** figyelmeztetést küld, ha a replikációs várólista megtelt. Az elsődlegesen a replikációs várólista általában megtelik, mert egy vagy több másodlagos replika lassú a művelet elfogadásához. A másodlagosnál ez általában akkor fordul elő, ha a szolgáltatás lassan alkalmazza a műveleteket. A figyelmeztetés törlődik, ha a várólista már nem teljes.

* **SourceId forrásazonosító**: System. replikátor
* **Tulajdonság**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, a replika szerepkörtől függően.

### <a name="slow-naming-operations"></a>Lassú elnevezési műveletek
A **System. NamingService** az elsődleges replikájának állapotát jelzi, ha az elnevezési művelet hosszabb időt vesz igénybe. Példák az elnevezési műveletekre: [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) vagy [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). További módszerek találhatók a FabricClient alatt. Ezek lehetnek például a [Service Management metódusok](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) vagy a [Tulajdonságok felügyeleti módszerei](/dotnet/api/system.fabric.fabricclient.propertymanagementclient)alatt.

> [!NOTE]
> Az elnevezési szolgáltatás a fürt egyik helyére oldja fel a szolgáltatás nevét. A felhasználók a szolgáltatás nevét és tulajdonságait kezelhetik. Ez egy Service Fabric particionált megőrzött szolgáltatás. Az egyik partíció a *hatóság tulajdonosát* jelöli, amely a Service Fabric nevekkel és szolgáltatásokkal kapcsolatos metaadatokat tartalmaz. A Service Fabric neveket a rendszer a különböző partíciók, *nevük tulajdonosa* partíciók néven rendeli hozzá, így a szolgáltatás bővíthető. További információ az [elnevezési szolgáltatásról](service-fabric-architecture.md).
> 
> 

Ha egy elnevezési művelet a vártnál hosszabb időt vesz igénybe, a művelet egy figyelmeztetési jelentéssel van megjelölve a műveletet kiszolgáló névhasználati szolgáltatás partíciójának elsődleges replikáján. Ha a művelet sikeresen befejeződik, a figyelmeztetés törlődik. Ha a művelet hibával fejeződött be, az állapotjelentés a hibával kapcsolatos részleteket tartalmaz.

* **SourceId forrásazonosító**: System. NamingService
* **Tulajdonság**: a (z) "**Duration_**" előtaggal kezdődik, és azonosítja a lassú műveletet, valamint azt a Service Fabric-nevet, amelyen a művelet alkalmazva van. Ha például a name **Fabric:/SajátPr/MyService** nevű szolgáltatás létrehozása túl hosszú időt vesz igénybe, a tulajdonság **Duration_AOCreateService. Fabric:/SajátPr/MyService**. Az "AO" a név és a művelet elnevezési partíciójának szerepkörére mutat.
* **További lépések**: Ellenőrizze, hogy az elnevezési művelet miért sikertelen. Minden művelethez különböző kiváltó okok tartozhatnak. Előfordulhat például, hogy a DELETE szolgáltatás beragadt. Lehet, hogy a szolgáltatás el van ragadva, mert az alkalmazás-gazdagép a szolgáltatási kódban szereplő felhasználói hiba miatt összeomlik a csomóponton.

A következő példa egy szolgáltatás-létrehozási műveletet mutat be. A művelet a beállított időtartamnál hosszabb ideig tartott. Az "AO" újrapróbálkozik, és a "nem" értékre küldi a munkát. "Nem" történt az utolsó művelet IDŐTÚLLÉPÉSsel. Ebben az esetben ugyanaz a replika elsődleges az "AO" és a "NO" szerepkörök esetében is.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication rendszerállapot-jelentések
A **System. hosting** a központilag telepített entitások felügyelete.

### <a name="activation"></a>Aktiválás
A System. hosting jelentések OK, amikor egy alkalmazás sikeresen aktiválva lett a csomóponton. Ellenkező esetben hibát jelez.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: **aktiválás**, beleértve a bevezetési verziót.
* **Következő lépések**: Ha az alkalmazás nem kifogástalan állapotú, vizsgálja meg, miért nem sikerült az aktiválás.

A következő példa egy sikeres aktiválást mutat be:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Letöltés
A System. hosting hibát jelez, ha az alkalmazáscsomag letöltése sikertelen.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: **Letöltés**, beleértve a bevezetés verzióját is.
* **Következő lépések**: vizsgálja meg, miért nem sikerült a letöltés a csomóponton.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage rendszerállapot-jelentések
A **System. hosting** a központilag telepített entitások felügyelete.

### <a name="service-package-activation"></a>Szolgáltatáscsomag aktiválása
A System. hosting jelentések OK, ha a szolgáltatási csomag aktiválása sikeres a csomóponton. Ellenkező esetben hibát jelez.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: aktiválás.
* **Következő lépések**: vizsgálja meg, miért nem sikerült az aktiválás.

### <a name="code-package-activation"></a>Csomag aktiválása
A rendszer. az aktiválás sikeressége esetén az egyes kódokhoz a System. hosting jelentéseket kell megtekintenie. Ha az aktiválás sikertelen, a rendszer a konfigurált figyelmeztetést jeleníti meg. Ha a **CodePackage** nem tudja aktiválni vagy leállítani a konfigurált **CodePackageHealthErrorThreshold** meghaladó hibát, akkor a szolgáltató hibát jelez. Ha egy szolgáltatáscsomag több csomagot tartalmaz, a rendszer aktiválási jelentést hoz létre mindegyikhez.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: a **CodePackageActivation** előtagot használja, és a kód és a belépési pont nevét tartalmazza *CodePackageActivation: CodePackageName: SetupEntryPoint/BelépésiPont*. Például **CodePackageActivation: code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Szolgáltatás típusának regisztrálása
A System. hosting jelentések OK, ha a szolgáltatástípus regisztrálása sikeresen megtörtént. Hibát jelez, ha a regisztráció nem történt meg időben, ahogy a **ServiceTypeRegistrationTimeout** használatával konfigurálták. Ha a futtatókörnyezet be van zárva, a szolgáltatás nem regisztrálja a csomópontot, és egy figyelmeztetést küld a jelentésekről.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: a **ServiceTypeRegistration** előtagot használja, és a szolgáltatástípus nevét tartalmazza. Például **ServiceTypeRegistration: FileStoreServiceType**.

A következő példa egy kifogástalan állapotú üzembe helyezési csomagot mutat be:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Letöltés
A System. hosting hibát jelez, ha a szervizcsomag letöltése sikertelen.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: **Letöltés**, beleértve a bevezetés verzióját is.
* **Következő lépések**: vizsgálja meg, miért nem sikerült a letöltés a csomóponton.

### <a name="upgrade-validation"></a>Frissítés ellenőrzése
A System. hosting hibát jelez, ha az érvényesítés a frissítés során meghiúsul, vagy ha a frissítés sikertelen a csomóponton.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: a **FabricUpgradeValidation** előtagot használja, és a frissítési verziót tartalmazza.
* **Leírás**: a hibára mutat.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nem definiált csomópont-kapacitás erőforrás-irányítási mérőszámokhoz
A System. hosting figyelmeztetést küld, ha a fürt jegyzékfájljában nincs definiálva csomópont-kapacitás, és az automatikus észlelés konfigurációja ki van kapcsolva. A Service Fabric állapottal kapcsolatos figyelmeztetést jelenít meg, ha az [erőforrás-szabályozást](service-fabric-resource-governance.md) használó szervizcsomag egy adott csomóponton regisztrálja a szolgáltatást.

* **SourceId forrásazonosító**: System. hosting
* **Tulajdonság**: **ResourceGovernance**.
* **Következő lépések**: a probléma megoldásának előnyben részesített módja a fürt jegyzékfájljának módosítása az elérhető erőforrások automatikus észlelésének engedélyezéséhez. Egy másik lehetőség a fürt jegyzékfájljának frissítése a metrikák megfelelően megadott csomópont-kapacitása alapján.

## <a name="next-steps"></a>Következő lépések
* [Service Fabric állapottal kapcsolatos jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

* [A szolgáltatás állapotának jelentése és ellenõrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md)
