---
title: Azure Service Fabric önálló fürt skálázása
description: További információ a Service Fabric önálló fürtök méretezéséről vagy kicsinyítéséről, illetve fel-vagy leskálázásáról.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843031"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Önálló fürtök méretezése Service Fabric
A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A fürtök akár több ezer csomópontot is tartalmazhatnak. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt (a csomópontok számának módosítása) vagy függőlegesen (a csomópontok erőforrásainak módosítása).  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

Miért érdemes méretezni a fürtöt? Az alkalmazás iránti igények időbeli változása.  Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen az alkalmazások megnövekedett munkaterhelésének vagy a hálózati forgalomnak, vagy csökkentenie kell a fürterőforrások mennyiségét.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Be-és kiskálázás, vagy horizontális skálázás
Megváltoztatja a fürt csomópontjainak számát.  Miután az új csomópontok csatlakoznak a fürthöz, a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) áthelyezi a szolgáltatásokat, ami csökkenti a meglévő csomópontok terhelését.  Csökkentheti a csomópontok számát is, ha a fürt erőforrásait nem hatékonyan használják.  Mivel a csomópontok elhagyják a fürtöt, a szolgáltatások elmozdulnak a csomópontokon, és a többi csomóponton is növekednek.  Az Azure-ban futó fürtökben lévő csomópontok számának csökkentése révén pénzt takaríthat meg, mivel a felhasznált virtuális gépek számának és a virtuális gépek munkaterhelésének a megfizetését kell fizetnie.  

- Előnyök: végtelen skála, elméletileg.  Ha az alkalmazás méretezhetőségre van tervezve, további csomópontok hozzáadásával engedélyezheti a korlátlan növekedést.  A felhőalapú környezetekben az eszközök megkönnyítik a csomópontok hozzáadását és eltávolítását, így egyszerűen beállíthatja a kapacitást, és csak a felhasznált erőforrásokért kell fizetnie.  
- Hátrányok: az alkalmazásokat [a méretezhetőség érdekében kell tervezni](service-fabric-concepts-scalability.md).  Az alkalmazás adatbázisai és az adatmegőrzés további építészeti munkát is igényelhet a méretezéshez.  Service Fabric állapot-nyilvántartó szolgáltatások [megbízható gyűjteményei](service-fabric-reliable-services-reliable-collections.md) azonban sokkal egyszerűbbé teszik az alkalmazásadatok méretezését.

Az önálló fürtök lehetővé teszik Service Fabric-fürt üzembe helyezését a helyszínen vagy az Ön által választott felhőalapú szolgáltatón.  A csomópont-típusok fizikai gépekből vagy virtuális gépekből állnak, a telepítéstől függően. Az Azure-ban futó fürtökhöz képest egy különálló fürt méretezési folyamata valamivel nagyobb szerepet játszik.  Manuálisan módosítania kell a fürt csomópontjainak számát, majd futtatnia kell a fürt konfigurációjának frissítését.

A csomópontok eltávolítása több frissítést is kezdeményezhet. Egyes csomópontok címkével vannak megjelölve `IsSeedNode=”true”` , és a fürt jegyzékfájljának lekérdezésével azonosíthatók a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest)használatával. Az ilyen csomópontok eltávolítása több időt is igénybe vehet, mivel a vetőmag-csomópontokat ilyen helyzetekben kell áthelyezni. A fürtnek legalább három elsődleges csomópont típusú csomópontot kell fenntartania.

> [!WARNING]
> Azt javasoljuk, hogy ne csökkentse a csomópontok számát a fürt [megbízhatósági rétegének mérete](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) alá. Ez meggátolja a Service Fabric rendszerszolgáltatások replikálását a fürtön keresztül, és destabilizálja vagy esetleg elpusztítja a fürtöt.
>

Önálló fürt skálázásakor tartsa szem előtt a következő irányelveket:
- Az elsődleges csomópontok cseréjét egy másik csomópont után kell végrehajtani, a kötegek eltávolítása és hozzáadása helyett.
- A csomópont típusának eltávolítása előtt ellenőrizze, hogy vannak-e olyan csomópontok, amelyek hivatkoznak a csomópont típusára. Távolítsa el ezeket a csomópontokat, mielőtt eltávolítja a megfelelő csomópont-típust. Az összes kapcsolódó csomópont eltávolítása után eltávolíthatja a NodeType a fürt konfigurációjától, és megkezdheti a konfiguráció frissítését a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)használatával.

További információ: [önálló fürt skálázása](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Felfelé és lefelé skálázás, vagy vertikális skálázás 
Módosítja a fürt csomópontjainak erőforrásait (processzor, memória vagy tároló).
- Előnyök: a szoftverek és az alkalmazások architektúrája ugyanaz marad.
- Hátrányok: véges skála, mivel korlátozva van, hogy mennyit növelheti az erőforrások az egyes csomópontokon. A leállás miatt a fizikai vagy virtuális gépeket offline állapotba kell helyezni, hogy erőforrásokat lehessen hozzáadni vagy eltávolítani.

## <a name="next-steps"></a>Következő lépések
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.

