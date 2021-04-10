---
title: Service Fabric a fürt kapacitásának tervezési szempontjait
description: A csomópontok típusai, tartóssága, megbízhatósága és egyéb, a Service Fabric-fürt tervezésekor megfontolandó dolgok.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: 9268dfef15d8302eb31cc1b649c7fd713aab6721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732584"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric a fürt kapacitásának tervezési szempontjait

A fürt kapacitásának megtervezése minden Service Fabric éles környezetben fontos. A legfontosabb szempontok a következők:

* **A fürtcsomópontok *típusainak* kezdeti száma és tulajdonságai**

* **Az egyes csomópont-típusok *tartóssági* szintje**, amely az Azure-infrastruktúrában Service Fabric virtuális gépekre vonatkozó jogosultságokat határoz meg

* **A fürt *megbízhatósági* szintje**, amely meghatározza a Service Fabric rendszerszolgáltatások és a fürt általános funkciójának stabilitását

Ez a cikk végigvezeti az egyes területek jelentős döntési pontjain.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>A fürtcsomópontok típusainak kezdeti száma és tulajdonságai

A *csomópont típusa* a fürtben lévő csomópontok (virtuális gépek) méretét, számát és tulajdonságait határozza meg. Minden Service Fabric fürtben definiált csomópont-típus egy [virtuálisgép-méretezési csoportra](../virtual-machine-scale-sets/overview.md)mutat.

Mivel az egyes csomópont-típusok külön méretezési csoportok, az egymástól függetlenül méretezhető, a portok különböző készletei nyitottak, és különböző kapacitási metrikákkal rendelkeznek. További információ a csomópontok típusai és a virtuálisgép-méretezési csoportok közötti kapcsolatról: [Service Fabric fürtcsomópontok típusai](service-fabric-cluster-nodetypes.md).

Minden fürthöz egy **elsődleges csomópont-típus** szükséges, amely Service Fabric platform-képességeket biztosító kritikus rendszerszolgáltatásokat futtat. Bár az alkalmazások futtatására is lehetséges az elsődleges csomópont-típusok használata, ajánlott kizárólag a rendszerszolgáltatások futtatására használni őket.

A **nem elsődleges csomópontok típusai** az alkalmazás szerepköreinek (például *előtér-* és *háttér-* szolgáltatások) meghatározására és a fürtön belüli szolgáltatások fizikai elkülönítésére használhatók. Service Fabric fürtökhöz nulla vagy több nem elsődleges csomópont típusú típus tartozhat.

Az elsődleges csomópont típusa a `isPrimary` Azure Resource Manager telepítési sablon csomópont típusa definíciójában található attribútummal van konfigurálva. Tekintse meg a csomópont típusú tulajdonságok teljes listáját a [NodeTypeDescription objektumban](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) . Ha például a használatot, nyissa meg a [Service Fabric-fürtökön](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) található fájlon *AzureDeploy.jst* , és *keressen* rá az `nodeTypes` objektumra a lapon.

### <a name="node-type-planning-considerations"></a>Csomópont típusú tervezési szempontok

A kezdeti csomópontok típusának száma a fürt, valamint a rajta futó alkalmazások és szolgáltatások alapján függ. A következő kérdéseket kell figyelembe venni:

* ***Az alkalmazása több szolgáltatással rendelkezik, és ezek közül bármelyiknek nyilvánosnak vagy internetkapcsolatnak kell lennie?***

    A tipikus alkalmazások olyan előtér-átjáró szolgáltatást tartalmaznak, amely egy ügyféltől érkező adatokat fogad, valamint egy vagy több háttér-szolgáltatást, amelyek az előtér-szolgáltatásokkal kommunikálnak, és amelyek az előtér-és a háttér-szolgáltatások közötti külön hálózatkezeléssel rendelkeznek. Ezek az esetek általában három csomópont-típust igényelnek: egy elsődleges csomópont-típust és két nem elsődleges csomópont-típust (egyet az előtér-és háttér-szolgáltatáshoz).

* ***Az alkalmazást alkotó szolgáltatások eltérő infrastrukturális igényekkel rendelkeznek, például nagyobb RAM-mal vagy magasabb CPU-ciklusokkal?***

    Az előtér-szolgáltatás gyakran kisebb virtuális gépeken (például D2) is futhat, amelyeken a portok nyitva vannak az interneten.  Előfordulhat, hogy a számítási célból intenzív háttér-szolgáltatásokat nagyobb virtuális gépeken kell futtatni (olyan virtuálisgép-méretekkel, mint a D4, a D6, a D15), amelyek nem internetre néznek. Ezeknek a szolgáltatásoknak a különböző csomópontjainak meghatározása lehetővé teszi az alapul szolgáló Service Fabric virtuális gépek hatékonyabb és biztonságos használatát, és lehetővé teszi, hogy egymástól függetlenül méretezhetők legyenek. Ha többet szeretne megtudni a szükséges erőforrások mennyiségének becsléséről, tekintse meg az [Service Fabric alkalmazások kapacitásának megtervezése](service-fabric-capacity-planning.md) című témakört.

* ***Szükség van-e az alkalmazás szolgáltatásainak az 100 csomóponton túli skálázásra?***

    Az egycsomópontos típusok nem tudják megbízhatóan méretezni a virtuálisgép-méretezési csoporton túli 100-csomópontot Service Fabric alkalmazásokhoz. A több mint 100 csomópont futtatásához további virtuálisgép-méretezési csoportok szükségesek (és így további csomópont-típusok is).

* ***A fürt a Availability Zones között fog terjedni?***

    Service Fabric támogatja a különböző [Availability Zonesokra](../availability-zones/az-overview.md) kiterjedő fürtöket, ha olyan csomópont-típusokat telepít, amelyek meghatározott zónákra vannak rögzítve, így biztosítva az alkalmazások magas rendelkezésre állását. Availability Zones további típusú csomópont-tervezési és minimális követelményeket kell megkövetelni. Részletekért lásd: [ajánlott topológia a Availability Zones-ra kiterjedő Service Fabric fürtök elsődleges csomópont-típusaihoz](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

A fürt kezdeti létrehozásához szükséges csomópontok számának és tulajdonságainak meghatározásakor vegye figyelembe, hogy a fürt üzembe helyezésekor bármikor hozzáadhat, módosíthat vagy eltávolíthat (nem elsődleges) csomópont-típusokat. Az [elsődleges csomópontok típusa](service-fabric-scale-up-primary-node-type.md) a futó fürtökben is módosítható (bár az ilyen műveletek nagy mennyiségű tervezést és óvatosságot igényelnek az éles környezetekben).

A csomópont típusú tulajdonságok további megfontolása tartóssági szint, amely meghatározza, hogy a csomópont típusú virtuális gépek milyen jogosultságokat biztosítanak az Azure-infrastruktúrán belül. Használja a fürthöz választott virtuális gépek méretét, valamint az egyes csomópontokhoz hozzárendelt példányok számát, hogy az egyes csomópont-típusok megfelelő tartóssági szintjét a következő módon határozza meg.

## <a name="durability-characteristics-of-the-cluster"></a>A fürt tartóssági jellemzői

A *tartóssági szint* kijelöli a Service Fabric virtuális gépekhez tartozó jogosultságokat az alapul szolgáló Azure-infrastruktúrával. Ez a jogosultság lehetővé teszi, hogy a Service Fabric felfüggessze a rendszerszolgáltatások és az állapot-nyilvántartó szolgáltatások Kvórumának Service Fabric követelményeit érintő virtuálisgép-szintű infrastruktúra-kérelmeket (például újraindítás, rendszerkép vagy áttelepítés).

> [!IMPORTANT]
> A tartóssági szint a csomópont típusa szerint van beállítva. Ha nincs megadva, a rendszer a *bronz* szintet fogja használni, azonban nem biztosít automatikus operációsrendszer-frissítéseket. Éles számítási feladatokhoz az *ezüst* vagy az *arany* tartóssága ajánlott.

Az alábbi táblázat a Service Fabric tartóssági szintjeire, a rájuk vonatkozó követelményekre és a AFFORDANCES sorolja fel.

| Tartóssági szintek  | A virtuális gépek minimálisan szükséges száma | Támogatott VM-méretek                                                                  | A virtuálisgép-méretezési csoporton elérhető frissítések                               | Az Azure által kezdeményezett frissítések és karbantartás                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Arany             | 5                              | Egyetlen ügyfélhez dedikált teljes csomópontos méretek (például L32s, GS5, G5, DS15_v2, D15_v2) | A Service Fabric-fürt általi jóváhagyásig késleltethető | A frissítési tartomány 2 órájában szüneteltethető, így a replikák a korábbi hibákból való helyreállításának további idejét is lehetővé teszik. |
| Ezüst           | 5                              | Egymagos vagy annál újabb virtuális gépek legalább 50 GB helyi SSD-vel                      | A Service Fabric-fürt általi jóváhagyásig késleltethető | Nem késleltethető jelentős időszakra                                                    |
| Bronz          | 1                              | Legalább 50 GB helyi SSD-vel rendelkező virtuális gépek                                              | Nem késlelteti a Service Fabric-fürt           | Nem késleltethető jelentős időszakra                                                    |

> [!NOTE]
> A fent említett virtuális gépek minimális száma minden tartóssági szinthez szükséges követelmény. Érvényesek a helyszínen, ami megakadályozza a meglévő virtuális gépek scalesets létrehozását és módosítását, amelyek nem felelnek meg a követelményeknek.

> [!WARNING]
> A bronz tartóssággal az operációs rendszer rendszerképének automatikus frissítése nem érhető el. A [patch](service-fabric-patch-orchestration-application.md) -összehangoló alkalmazás (csak a nem Azure-beli üzemeltetett fürtök esetében) *nem ajánlott* ezüst vagy nagyobb tartóssági szint esetén, ezért az egyetlen lehetőség a Windows-frissítések automatizálására Service Fabric frissítési tartományok tekintetében.

> [!IMPORTANT]
> A tartóssági szinttől függetlenül a [kiosztási](/rest/api/compute/virtualmachinescalesets/deallocate) művelet futtatása a virtuálisgép-méretezési csoportokban megsemmisíti a fürtöt.

### <a name="bronze"></a>Bronz

A bronz tartóssággal futó csomópont-típusok nem kapnak jogosultságot. Ez azt jelenti, hogy az állapot-nyilvántartó számítási feladatokat befolyásoló infrastrukturális feladatok nem állnak le vagy késleltetve. A bronz tartósságot olyan csomópont-típusokhoz használhatja, amelyek csak állapot nélküli munkaterheléseket futtatnak. Az éles számítási feladatokhoz a Silver vagy a Above futtatása ajánlott.

### <a name="silver-and-gold"></a>Ezüst és arany

A gyakran használt állapot-nyilvántartó szolgáltatásokat futtató összes csomóponthoz ezüst vagy arany tartósságot használhat, és a folyamat leegyszerűsítése érdekében az üzembe helyezési műveletek késleltetése és a kapacitás csökkentése is lehetséges. A vertikális Felskálázási forgatókönyvek nem érinthetik a tartóssági szintet.

#### <a name="advantages"></a>Előnyök

* Csökkenti a skálázási műveletekhez szükséges lépések számát (a csomópont inaktiválását és a Remove-ServiceFabricNodeState automatikusan nevezik).
* Csökkenti az adatvesztés kockázatát a helyi virtuálisgép-méret változási műveletei és az Azure-infrastruktúra műveletei miatt.

#### <a name="disadvantages"></a>Hátrányok

* A virtuálisgép-méretezési csoportok és az egyéb kapcsolódó Azure-erőforrások üzembe helyezése időtúllépést okozhat, késleltethető, vagy teljes mértékben letiltható a fürt vagy az infrastruktúra szintjén felmerülő problémák miatt.
* Növeli a replika- [életciklus eseményeinek](service-fabric-reliable-services-lifecycle.md) (például az elsődleges swapok) számát az Azure-infrastruktúra műveletei során felmerülő automatizált csomópont-inaktiválások miatt.
* Az Azure platform szoftverfrissítések vagy a hardveres karbantartási tevékenységek ideje alatt a csomópontokon kívüli szolgáltatások is igénybe vesznek. Ezen tevékenységek során előfordulhat, hogy a letiltási/letiltási állapotú csomópontok láthatók. Ez csökkenti a fürt kapacitását átmenetileg, de nem befolyásolja a fürt vagy az alkalmazások rendelkezésre állását.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Ajánlott eljárások az ezüst és az arany tartóssági csomópontok típusaihoz

Kövesse ezeket a javaslatokat a csomópont-típusok ezüst vagy arany tartóssággal való kezeléséhez:

* Mindig kifogástalan állapotban tarthatja a fürtjét és az alkalmazásait, és gondoskodhat arról, hogy az alkalmazások az összes [szolgáltatás-replika életciklus-eseményre](service-fabric-reliable-services-lifecycle.md) válaszoljanak (például a buildben található replika beragadva).
* A virtuális gépek méretének megváltoztatásához (vertikális felskálázás/lefelé) való biztonságos módszerek bevezetése. A virtuálisgép-méretezési csoport VM-méretének megváltoztatásához körültekintő tervezésre és óvatosságra van szükség. Részletekért lásd: [Service Fabric csomópont-típus Felskálázása](service-fabric-scale-up-primary-node-type.md)
* Legalább öt csomópontot kell fenntartania minden olyan virtuálisgép-méretezési csoportnál, amelynél a tartóssági szint (arany vagy ezüst) engedélyezve van. Ha a küszöbérték alatt méretezi a korlátot, a fürt hibás állapotba kerül, és manuálisan kell törölnie a () állapotot `Remove-ServiceFabricNodeState` az eltávolított csomópontokhoz.
* Az ezüst vagy arany tartóssági szinttel rendelkező virtuálisgép-méretezési csoportoknak a Service Fabric fürtben lévő saját csomópont-típusra kell leképezniük. Több virtuálisgép-méretezési csoport egyetlen csomópontos típusra való leképezése megakadályozza a Service Fabric-fürt és az Azure-infrastruktúra megfelelő működésének összehangolását.
* Ne törölje a véletlenszerű virtuálisgép-példányokat, mindig használja a virtuálisgép-méretezési csoport méretezését a szolgáltatásban. A véletlenszerűen kiválasztott VM-példányok törlése lehetséges, hogy a virtuálisgép-példányok a [frissítési tartományok](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) és a tartalék [tartományok](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)között oszlanak meg. Ez az egyensúlyhiány hátrányosan befolyásolhatja, hogy a rendszer képes legyen a szolgáltatás példányainak vagy a szolgáltatás replikáinak megfelelő terheléselosztásra.
* Ha autoskálázást használ, állítsa be a szabályokat úgy, hogy a (virtuálisgép-példányok eltávolítása) művelet csak egy csomóponton legyen végrehajtva. Egyszerre több példány skálázása nem biztonságos.
* Ha a virtuális gépeket az elsődleges csomópont típusa szerint törli vagy felszabadítja, soha ne csökkentse a kiosztott virtuális gépek számát, amely alatt a megbízhatósági réteg szükséges. Ezeket a műveleteket a rendszer határozatlan ideig blokkolja egy olyan méretezési csoportnál, amelynek tartóssági szintje ezüst vagy arany.

### <a name="changing-durability-levels"></a>Tartóssági szintek módosítása

Bizonyos korlátokon belül a csomópont típusa tartóssági szintje módosítható:

* Az ezüst vagy arany tartóssági szinttel rendelkező csomópont-típusok nem állíthatók vissza a Bronzra.
* A bronzról ezüstre vagy arannyal való frissítés néhány órát is igénybe vehet.
* A tartóssági szint módosításakor ügyeljen arra, hogy a virtuálisgép-méretezési csoport erőforrásának Service Fabric-bővítményi konfigurációjában és a Service Fabric fürterőforrás-definíciójában is frissítse azt. Ezeknek az értékeknek egyezniük kell.

Egy másik szempont, ha a kapacitás megtervezése a fürt megbízhatósági szintje, amely meghatározza a rendszerszolgáltatások és a teljes fürt stabilitását a következő szakaszban leírtak szerint.

## <a name="reliability-characteristics-of-the-cluster"></a>A fürt megbízhatósági jellemzői

A fürt *megbízhatósági szintje* határozza meg, hogy a rendszer hány rendszerszolgáltatási replikát futtat a fürt elsődleges csomópont-típusán. Minél több replika van, annál megbízhatóbb a rendszerszolgáltatások (és így a fürt teljes).

> [!IMPORTANT]
> A megbízhatósági szint a fürt szintjén van beállítva, és meghatározza az elsődleges csomópont típusú csomópontok minimális számát. Az éles számítási feladatokhoz az ezüst (nagyobb vagy egyenlő) vagy újabb (legfeljebb öt csomópontos) megbízhatósági szint szükséges.  

A megbízhatósági szintek a következő értékeket vehetik igénybe:

* **Platinum** – a rendszerszolgáltatások a célként megadott replikák számának kilenc
* Az **arany** -rendszerszolgáltatások a célként megadott replika-készletekkel futnak hét
* Az **ezüst** -rendszerszolgáltatások a célként megadott replikák számának ötszörösét futtatják
* A **bronz** rendszerszolgáltatások a célként megadott replika három értékével futnak

A megbízhatósági szintet a következő javaslat alapján választhatja ki. A magok csomópontjainak számát a megbízhatósági szint csomópontjainak minimális száma is beállítja.


| **Csomópontok száma** | **Megbízhatósági szintek** |
| --- | --- |
| 1 | *Ne határozza meg a `reliabilityLevel` paramétert: a System kiszámítja.* |
| 3 | Bronz |
| 5 vagy 6| Ezüst |
| 7 vagy 8 | Arany |
| 9 és fel | Platina |

A fürt méretének növelése vagy csökkentése (a virtuálisgép-példányok száma az összes csomópontban) esetében érdemes lehet frissíteni a fürt megbízhatóságát az egyik rétegről a másikra. Ennek hatására a rendszer elindítja a fürt frissítéseit a rendszerszolgáltatások replikáinak számának módosításához. Várjon, amíg a frissítés befejeződik, mielőtt bármilyen más módosítást hajt végre a fürtön, például csomópontok hozzáadását.  A frissítés előrehaladását Service Fabric Explorer vagy a [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) futtatásával figyelheti.

### <a name="capacity-planning-for-reliability"></a>Kapacitás megtervezése megbízhatósághoz

A fürt kapacitásának szükségleteit az adott munkaterhelés és megbízhatósági követelmények határozzák meg. Ez a szakasz általános útmutatást nyújt a kapacitás megtervezésének megkezdéséhez.

#### <a name="virtual-machine-sizing"></a>Virtuális gépek méretezése

**Éles számítási feladatokhoz az ajánlott virtuálisgép-méret (SKU) [Standard D2_V2](../virtual-machines/dv2-dsv2-series.md) (vagy ezzel egyenértékű), amely legalább 50 GB helyi SSD-vel, 2 maggal és 4 GIB memóriával rendelkezik.** Legalább 50 GB helyi SSD ajánlott, azonban bizonyos munkaterhelések (például Windows-tárolók) esetén nagyobb lemezek szükségesek. Ha más virtuálisgép- [méreteket](../virtual-machines/sizes-general.md) választ az éles számítási feladatokhoz, vegye figyelembe a következő korlátozásokat:

- A részleges Core virtuálisgép-méretek (például a standard a0) nem támogatottak.
- *A sorozat* A virtuálisgép-méretek teljesítménybeli okokból nem támogatottak.
- Az alacsony prioritású virtuális gépek nem támogatottak.

#### <a name="primary-node-type"></a>Elsődleges csomópont típusa

Az Azure-beli **éles** számítási feladatokhoz legalább öt elsődleges csomópontnak (VM-példánynak) és az ezüst megbízhatósági szintnek kell lennie. Javasoljuk, hogy a fürt elsődleges csomópontjának típusát a rendszerszolgáltatásokhoz használja, és elhelyezési korlátozásokkal telepítse az alkalmazást a másodlagos csomópont-típusokra.

Az Azure-ban futó számítási **feladatok** legalább egy vagy három elsődleges csomópont futtatására használhatók. Egy csomópontos fürt konfigurálásához ellenőrizze, hogy a `reliabilityLevel` beállítás teljesen ki van-e hagyva a Resource Manager-sablonban (az üres karakterlánc-érték megadása `reliabilityLevel` nem elegendő). Ha a Azure Portaltel beállított egyetlen csomópont-fürtöt állítja be, akkor ez a konfiguráció automatikusan megtörténik.

> [!WARNING]
> Az egycsomópontos fürtök megbízhatóság nélkül, speciális konfigurációval futnak, és a vertikális felskálázás nem támogatott.

#### <a name="non-primary-node-types"></a>Nem elsődleges csomópontok típusai

A nem elsődleges csomópont típusú csomópontok minimális száma a csomópont típusának adott [tartóssági szintjétől](#durability-characteristics-of-the-cluster) függ. Meg kell terveznie a csomópontok számát (és a tartóssági szintet) azon alkalmazások vagy szolgáltatások replikáinak száma alapján, amelyeket futtatni szeretne a csomópont típusához, és attól függően, hogy a munkaterhelés állapota vagy állapota nem megfelelő-e. Ne feledje, hogy a fürt üzembe helyezése után bármikor növelheti vagy csökkentheti a virtuális gépek számát egy csomópont-típusban.

##### <a name="stateful-workloads"></a>Állapot-nyilvántartó számítási feladatok

Service Fabric [megbízható gyűjtemények vagy megbízható szereplők](service-fabric-choose-framework.md)használatával állapot-nyilvántartó üzemi számítási feladatokhoz a minimális és a célként megadott replika öt ajánlott. Ezzel a stabil állapotban az egyes tartalék tartományokban és a frissítési tartományokban egy replikával (egy replikakészlet-készletből) fejeződik be. Általánosságban elmondható, hogy a rendszerszolgáltatások számára beállított megbízhatósági szintet útmutatóként használja az állapot-nyilvántartó szolgáltatásokhoz használt replikák számához.

##### <a name="stateless-workloads"></a>Állapot nélküli számítási feladatok

Az állapot nélküli éles számítási feladatokhoz a minimálisan támogatott nem elsődleges csomópont típusának háromnak kell lennie a kvórum megőrzése érdekében, azonban a csomópont típusának mérete öt ajánlott.

## <a name="next-steps"></a>Következő lépések

A fürt konfigurálása előtt tekintse át a `Not Allowed` [fürt frissítési szabályzatait](service-fabric-cluster-fabric-settings.md) , hogy csökkentse a fürt ismételt létrehozását, mert a rendszer nem módosítható a rendszerkonfigurációs beállítások miatt.

További információ a fürtök tervezéséről:

* [Számítási teljesítmény tervezése és méretezése](service-fabric-best-practices-capacity-scaling.md)
* [Service Fabric alkalmazások kapacitásának megtervezése](service-fabric-capacity-planning.md)
* [Vész-helyreállítási tervezés](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
