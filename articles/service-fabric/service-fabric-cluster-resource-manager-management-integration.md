---
title: Fürterőforrás-kezelő – felügyelet integrációja
description: A fürterőforrás-kezelő és a Service Fabric felügyelet közötti integrációs pontok áttekintése.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92174036"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>A fürterőforrás-kezelő integrációja Service Fabric fürtszolgáltatással
A Service Fabric fürterőforrás-kezelő nem hajtja Service Fabric a frissítéseit, de az érintett. A fürt erőforrás-kezelője által a felügyelethez nyújtott első módszer a fürt és a benne lévő szolgáltatások kívánt állapotának nyomon követése. A fürterőforrás-kezelő jelentést küld, amikor nem tudja a fürtöt a kívánt konfigurációba helyezni. Ha például nincs elegendő kapacitás, a fürterőforrás-kezelő kiküldi az állapottal kapcsolatos figyelmeztetéseket, és hibákat jelez a problémával kapcsolatban. A frissítések működéséhez egy másik integrációra van szükség. A fürterőforrás-kezelő némileg megváltoztatja a viselkedését a frissítések során.  

## <a name="health-integration"></a>Állapot-integráció
A fürterőforrás-kezelő folyamatosan nyomon követi a szolgáltatások elhelyezéséhez meghatározott szabályokat. Emellett nyomon követi a csomópontok és a fürt, valamint a fürtben lévő egyes mérőszámok fennmaradó kapacitását is. Ha nem tudja kielégíteni ezeket a szabályokat, vagy ha nincs elegendő kapacitás, a rendszer kibocsátja az állapottal kapcsolatos figyelmeztetéseket és hibákat. Ha például egy csomópont meghaladja a kapacitást, és a fürterőforrás-kezelő megkísérli kijavítani a helyzetet a szolgáltatások áthelyezésével. Ha nem tudja kijavítani a helyzetet, a rendszer olyan állapotra vonatkozó figyelmeztetést bocsát ki, amely azt jelzi, hogy melyik csomóponton van a kapacitás, és mely metrikákat.

A Resource Manager állapotára vonatkozó figyelmeztetések egy másik példája az elhelyezési megkötések megsértése. Ha például meghatározta az elhelyezési korlátozást (például), `“NodeColor == Blue”` és a Resource Manager észleli a korlátozás megsértését, a rendszer állapot-figyelmeztetést bocsát ki. Ez az egyéni megkötések és az alapértelmezett megkötések (például a tartalék tartomány és a frissítési tartományok megkötései) esetében igaz.

Az alábbi példa egy ilyen állapotjelentés-jelentésre mutat. Ebben az esetben az állapotjelentés a rendszerszolgáltatások egyik partíciója. Az állapotfigyelő üzenet jelzi, hogy a partíció replikái átmenetileg túl kevés frissítési tartományba vannak csomagolva.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

A következő üzenet jelenik meg:

1. A replikák mindegyike kifogástalan állapotú: `AggregatedHealthState : Ok`
2. A frissítési tartomány terjesztési megkötése jelenleg sérül. Ez azt jelenti, hogy egy adott frissítési tartomány több replikával rendelkezik ebből a partícióból.
3. Azt a csomópontot tartalmazza, amely a replikát okozza. Ebben az esetben ez a csomópont a name *csomóponttal. 8*
4. Azt jelzi, hogy folyamatban van-e a frissítés a partícióhoz ("jelenleg verziófrissítés – hamis")
5. A szolgáltatás terjesztési házirendje: "terjesztési szabályzat – csomagolás". Ezt az `RequireDomainDistribution` [elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)szabályozza. A *csomagolás* azt jelzi, hogy ebben az esetben a DomainDistribution _nem_ volt szükséges, ezért tudjuk, hogy az elhelyezési házirend nincs megadva ehhez a szolgáltatáshoz. 
6. A jelentés végrehajtásakor – 8/10/2015 7:13:02 PM

Ezek az adatok, mint például ez az állapot riasztást küldenek az éles környezetben, és így biztos lehet benne, hogy hiba történt, és a rendszer a hibás frissítések észlelésére és leállítására is használja Ebben az esetben szeretnénk megtekinteni, hogy a Resource Managernek miért kellett a replikákat a frissítési tartományba csomagolnia. Általában a csomagolás átmeneti, mert a többi frissítési tartomány csomópontjai nem voltak ilyenek, például:.

Tegyük fel, hogy a fürterőforrás-kezelő bizonyos szolgáltatásokat próbál elhelyezni, de nincs olyan megoldás, amely működik. A szolgáltatások nem helyezhetők el, általában az alábbi okok egyike miatt:

1. Néhány átmeneti feltétel lehetetlenné tette a szolgáltatási példány vagy a replika helyes elhelyezését
2. A szolgáltatás elhelyezési követelményei unsatisfiable.

Ezekben az esetekben a fürterőforrás-kezelőből származó állapotadatok segítenek meghatározni, hogy miért nem helyezhető el a szolgáltatás. Ezt a műveletet hívjuk a korlátozás megszüntetési sorrendjének Ebben az esetben a rendszer végigvezeti a szolgáltatást befolyásoló konfigurált korlátozásokon, és rögzíti, hogy mit törölnek. Így a szolgáltatások nem helyezhetők el, láthatja, hogy mely csomópontok lettek kizárva, és miért.

## <a name="constraint-types"></a>Korlátozási típusok
Beszéljünk a különböző megkötésekről ezekben az egészségügyi jelentésekben. Ha a replikák nem helyezhetők el, a megkötésekhez kapcsolódó rendszerállapot-üzenetek jelennek meg.

* **ReplicaExclusionStatic** és **ReplicaExclusionDynamic**: ezek a korlátozások azt jelzik, hogy a rendszer elutasította a megoldást, mert az azonos partíción lévő két szolgáltatási objektumot ugyanahhoz a csomóponthoz kell helyezni. Ez nem engedélyezett, mert az adott csomópont meghibásodása túlságosan befolyásolhatja a partíciót. A ReplicaExclusionStatic és a ReplicaExclusionDynamic szinte ugyanaz a szabály, és a különbségek nem igazán számítanak. Ha a ReplicaExclusionStatic vagy a ReplicaExclusionDynamic korlátozást tartalmazó korlátozás-megszüntetési sorozatot lát, a fürterőforrás-kezelő úgy gondolja, hogy nincs elég csomópont. Ehhez az érvénytelen elhelyezések használatához további megoldások szükségesek, amelyek nem megengedettek. A sorozatban megjelenő egyéb megkötések általában azt jelzik, hogy a csomópontok miért vannak kizárva az első helyen.
* **PlacementConstraint**: Ha ezt az üzenetet látja, az azt jelenti, hogy megoldottunk néhány csomópontot, mert nem egyeznek a szolgáltatás elhelyezési korlátaival. Az üzenet részeként nyomon követjük a jelenleg konfigurált elhelyezési korlátozásokat. Ez normális, ha van definiálva elhelyezési korlátozás. Ha azonban az elhelyezési megkötés helytelenül okoz túl sok csomópontot, akkor ez az értesítési útmutató.
* **NodeCapacity**: Ez a korlátozás azt jelenti, hogy a fürterőforrás-kezelő nem tudta elhelyezni a replikákat a jelzett csomópontokon, mert az a kapacitáson át lenne helyezve.
* **Affinitás**: Ez a korlátozás azt jelzi, hogy a replika nem helyezhető el az érintett csomópontokon, mert az affinitási megkötés megsértését okozza. Az affinitással kapcsolatos további információk [ebben a cikkben](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) olvashatók.
* **FaultDomain** és **UpgradeDomain**: Ez a korlátozás kiküszöböli a csomópontokat, ha a jelzett csomópontokra helyezve a replikát egy adott hiba vagy frissítési tartomány csomagolása okozza. A megkötést bemutató példák a [hibák és a frissítési tartományok korlátozásait és az eredményül kapott viselkedést](service-fabric-cluster-resource-manager-cluster-description.md) ismertető témakörben jelennek meg.
* **PreferredLocation**: általában nem látja ezt a korlátozást a csomópontok eltávolításával a megoldásból, mert alapértelmezés szerint optimalizálásként fut. A frissítések során az előnyben részesített hely megkötése is szerepel. A frissítés során a szolgáltatás visszahelyezi a szolgáltatásokat a frissítés elindításakor.

## <a name="blocklisting-nodes"></a>Blocklisting-csomópontok
Másik állapotjelző üzenet: a fürterőforrás-kezelő jelentései a csomópontok blocklisted. Azt is megteheti, hogy a blocklisting ideiglenes korlátozásként alkalmazza, amelyet automatikusan alkalmaz a rendszer. A csomópontok blocklisted kapnak, amikor ismétlődő hibák jelentkeznek, amikor elindítja az adott szolgáltatástípus példányait. A csomópontok blocklisted szerint vannak leképezve. Egy csomópont lehet egy blocklisted, de nem egy másik. 

A blocklisting a fejlesztés során gyakran jelenik meg: néhány hiba miatt a Service Host összeomlik az indításkor, Service Fabric néhányszor megpróbálja létrehozni a szolgáltatást, és a hiba továbbra is fennáll. Néhány próbálkozás után a csomópont blocklisted válik, és a fürterőforrás-kezelő máshol is megpróbálja létrehozni a szolgáltatást. Ha ez a hiba több csomóponton is tart, lehetséges, hogy a fürt összes érvényes csomópontja le van tiltva. A Blocklisting olyan sok csomópontot is eltávolíthat, amely nem elegendő ahhoz, hogy sikeresen elindítsa a szolgáltatást a kívánt lépték kielégítése érdekében. Általában további hibák vagy figyelmeztetések jelennek meg a fürterőforrás-kezelőben, ami azt jelzi, hogy a szolgáltatás nem éri el a kívánt replika-vagy példányszámot, valamint azokat az állapotjelző üzeneteket, amelyek azt jelzik, hogy a blocklisting milyen hibát eredményezett az első helyen.

A Blocklisting nem állandó feltétel. Néhány perc elteltével a rendszer eltávolítja a csomópontot a Blocklist, és Service Fabric is aktiválhatja a csomóponton lévő szolgáltatásokat. Ha a szolgáltatások továbbra is sikertelenek, a csomópontot újra blocklisted az adott szolgáltatástípus számára. 

### <a name="constraint-priorities"></a>Megkötés prioritásai

> [!WARNING]
> A korlátozási prioritások módosítása nem ajánlott, és jelentős negatív hatással lehet a fürtre. Az alábbi információk az alapértelmezett korlátozási prioritások és azok viselkedésének hivatkozását adják meg. 
>

A fenti megkötések esetében előfordulhat, hogy a "Hey – úgy gondolom, hogy a tartalék tartományra vonatkozó megkötések a legfontosabb dolgok a rendszeren. Annak érdekében, hogy a tartalék tartományra vonatkozó korlátozás ne sértse meg, hajlandó vagyok megsérteni más korlátozásokat. "

A megkötéseket különböző prioritási szintekkel lehet konfigurálni. Ezek a következők:

   - "Hard" (0)
   - "Soft" (1)
   - "optimalizálás" (2)
   - "off" (-1). 
   
A megkötések többsége alapértelmezés szerint rögzített korlátozásként van konfigurálva.

A megkötések prioritásának módosítása nem gyakori. Vannak olyan idők, amikor a korlátozás prioritásainak módosítására van szükség, általában a környezetet befolyásoló egyéb hibák vagy viselkedések megkerülésére. Általánosságban a korlátozás prioritási infrastruktúrájának rugalmassága nagyon jól működött, de gyakran nem szükséges. Az idő nagy részében minden az alapértelmezett prioritások szerint ülésezik. 

A prioritási szintek nem azt jelentik, hogy egy _adott megkötés nem_ sérül, és nem mindig teljesülnek. A korlátozási prioritások határozzák meg a megkötések kényszerített sorrendjét. A prioritások határozzák meg a kompromisszumokat, ha az összes korlátozást nem lehet kielégíteni. Általában minden megkötés teljesül, kivéve, ha valami más is a környezetben. Néhány példa olyan forgatókönyvekre, amelyek a korlátozás megsértését okozzák, vagy nagy számú egyidejű hibát okoznak.

Speciális helyzetekben módosíthatja a korlátozási prioritásokat. Tegyük fel például, hogy azt szeretné, hogy a rendszer mindig megsértse a csomópont-kapacitással kapcsolatos problémák megoldásához szükséges affinitást. Ennek eléréséhez beállíthatja az affinitási megkötés prioritását "Soft" (1) értékre, és a kapacitás korlátozását állítsa "Hard" (0) értékre.

A különböző megkötések alapértelmezett prioritási értékei a következő konfigurációban vannak megadva:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Tartalék tartomány és frissítési tartományok korlátai
A fürterőforrás-kezelő szeretné megőrizni a szolgáltatásokat a hibák és a frissítési tartományok között. Ezt megkötésként modellezi a fürterőforrás-kezelő motorján belül. További információ a használatáról és az adott viselkedésről: a [fürt konfigurációjának](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)cikke.

Előfordulhat, hogy a fürterőforrás-kezelőnek néhány replikát kell becsomagolnia egy frissítési tartományba, hogy kezelni tudja a frissítéseket, a hibákat vagy a korlátozás megsértését. A hibás vagy frissítési tartományokba való becsomagolás általában csak akkor történik meg, ha a rendszer több hibát vagy más adatváltozást okoz a megfelelő elhelyezés megakadályozása érdekében. Ha meg szeretné akadályozni a csomagolást ezen esetekben is, használhatja az `RequireDomainDistribution` [elhelyezési házirendet](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Vegye figyelembe, hogy ennek hatására a szolgáltatás rendelkezésre állását és megbízhatóságát is érintheti, ezért alaposan gondolja át.

Ha a környezet megfelelően van konfigurálva, az összes korlátozás teljes mértékben figyelembe lesz véve még a frissítések során is. A legfontosabb dolog, hogy a fürterőforrás-kezelő figyeli a megkötéseket. Ha a rendszer megsértést észlel, azonnal jelentést küld, és megkísérli kijavítani a problémát.

## <a name="the-preferred-location-constraint"></a>Az előnyben részesített hely megkötése
A PreferredLocation megkötés egy kicsit eltérő, mert két különböző felhasználási felhasználható. A megkötés egyik használata az alkalmazások frissítése során történik. A fürterőforrás-kezelő automatikusan kezeli ezt a korlátozást a frissítések során. Annak biztosítására szolgál, hogy amikor a frissítések befejeződik, a replikák a kezdeti helyükre térjenek vissza. Az PreferredLocation korlátozás más használata az [ `PreferredPrimaryDomain` elhelyezési házirendhez](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)szükséges. Mindkettő optimalizálás, ezért a PreferredLocation korlátozás az egyetlen megkötés, amely alapértelmezés szerint az "optimalizálás" értékre van állítva.

## <a name="upgrades"></a>Frissítések
A fürterőforrás-kezelő az alkalmazások és a fürtök frissítései során is segít, amikor két feladattal rendelkezik:

* Győződjön meg arról, hogy a fürt szabályait nem veszélyezteti a rendszer
* próbálja ki a frissítést zökkenőmentesen

### <a name="keep-enforcing-the-rules"></a>A szabályok betartatása
A legfontosabb, hogy tisztában legyen azzal, hogy a szabályok – a szigorú megkötések, például a elhelyezési korlátozások és a kapacitások – továbbra is érvényben vannak a frissítések során. Az elhelyezési megkötések biztosítják, hogy a munkaterhelések csak akkor fussanak, ha azok a frissítés során is engedélyezve vannak. Ha a szolgáltatások nagymértékben korlátozottak, a frissítések hosszabb időt vehetnek igénybe. Ha a szolgáltatás vagy a csomópontja egy frissítéshez van levezetve, előfordulhat, hogy néhány lehetőség is rendelkezésre áll, ahol mehet.

### <a name="smart-replacements"></a>Intelligens cserék
A frissítés indításakor a Resource Manager pillanatképet készít a fürt aktuális elrendezéséről. Ahogy minden frissítési tartomány befejeződik, megkísérli visszaadni az adott frissítési tartományba tartozó szolgáltatásokat az eredeti megkötésnek. Így a frissítés során a szolgáltatás legfeljebb két átmenettel rendelkezik. Van egy kilépés az érintett csomópontból, és egy vissza. A fürt vagy szolgáltatás visszaküldése a frissítés előtt azt is biztosítja, hogy a frissítés ne befolyásolja a fürt elrendezését. 

### <a name="reduced-churn"></a>Csökkentett forgalom
A frissítés során megjelenő másik dolog, hogy a fürterőforrás-kezelő kikapcsolja az egyensúlyt. A kiegyensúlyozás megakadályozása megakadályozza a szükségtelen reakciókat, például a szolgáltatások olyan csomópontokra való áthelyezését, amelyek ki lettek ürítve a frissítéshez. Ha a kérdéses frissítés egy fürt frissítése, akkor a teljes fürt nem kiegyensúlyozott a frissítés során. A korlátozási ellenőrzések aktívak maradnak, csak a metrikák proaktív kiegyensúlyozásán alapuló mozgás le van tiltva.

### <a name="buffered-capacity--upgrade"></a>Pufferelt kapacitás & verziófrissítés
Általában azt szeretné, hogy a frissítés befejeződjön, még akkor is, ha a fürt korlátozott vagy megtelt. A fürt kapacitásának kezelése még fontosabb a szokásosnál nagyobb frissítések esetén. A frissítési tartományok számától függően a kapacitás 5 és 20 százalékát át kell telepíteni a fürtön keresztüli verziófrissítési tekercsként. Ennek a munkának valahol el kell mennie. Itt hasznos a [pufferelt kapacitás](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) fogalma. A rendszer a normál működés során betartja a pufferelt kapacitást. Ha szükséges, a fürterőforrás-kezelő a frissítés során a teljes kapacitással (a puffer használatával) tölti ki a csomópontokat.

## <a name="next-steps"></a>Következő lépések
* Kezdje a kezdetektől, és [Ismerkedjen meg a Service Fabric fürterőforrás-kezelővel](service-fabric-cluster-resource-manager-introduction.md)
