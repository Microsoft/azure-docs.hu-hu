---
title: Cloud Services és Service Fabric közötti különbségek
description: Elméleti áttekintés az alkalmazások Cloud Servicesról Service Fabricba való áttelepítéséről.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575771"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Ismerkedjen meg Cloud Services és Service Fabric közötti különbségekkel az alkalmazások áttelepítése előtt.
A Microsoft Azure Service Fabric a felhőalapú alkalmazások következő platformja, amely rugalmasan méretezhető, megbízható elosztott alkalmazások számára készült. Számos új funkciót vezet be az elosztott felhőalapú alkalmazások csomagolásához, üzembe helyezéséhez, frissítéséhez és felügyeletéhez. 

Ez egy bevezető útmutató az alkalmazások Cloud Servicesról Service Fabricba való áttelepítéséhez. Elsősorban a Cloud Services és Service Fabric közötti építészeti és tervezési különbségekre koncentrál.

## <a name="applications-and-infrastructure"></a>Alkalmazások és infrastruktúra
Cloud Services és Service Fabric közötti alapvető különbség a virtuális gépek, a munkaterhelések és az alkalmazások közötti kapcsolat. Az itt megadott számítási feladatok egy adott feladat végrehajtásához vagy szolgáltatás nyújtásához írt kódként vannak meghatározva.

* **Cloud Services az alkalmazások virtuális gépekként való üzembe helyezéséről szól.** Az írási kód szorosan össze van választva egy virtuálisgép-példányhoz, például egy webes vagy feldolgozói szerepkörhöz. A számítási feladatok Cloud Servicesban történő üzembe helyezéséhez egy vagy több, a számítási feladatot futtató virtuálisgép-példány üzembe helyezése szükséges. Az alkalmazások és a virtuális gépek elkülönítése nem történik meg, így az alkalmazásnak nincs formális meghatározása. Egy alkalmazás a webes vagy feldolgozói szerepkör példányain egy Cloud Services központi telepítésben vagy teljes Cloud Services üzemelő példányként is megtekinthető. Ebben a példában egy alkalmazás szerepkör-példányok halmaza jelenik meg.

![Alkalmazások és topológia Cloud Services][1]

* **Service Fabric az alkalmazások Windows vagy Linux rendszeren Service Fabric futtató meglévő virtuális gépekre vagy számítógépekre történő központi telepítésével kapcsolatban.** Az Ön által írt szolgáltatások teljes mértékben le vannak választva a mögöttes infrastruktúrától, amelyet a Service Fabric alkalmazás-platform eloszt, így az alkalmazások több környezetbe is üzembe helyezhetők. Service Fabric "szolgáltatásnak" nevezzük a munkaterhelést, és egy vagy több szolgáltatás a Service Fabric alkalmazási platformon futó, hivatalosan meghatározott alkalmazásban van csoportosítva. Több alkalmazás is telepíthető egyetlen Service Fabric-fürtre.

![Alkalmazások és topológia Service Fabric][2]

Service Fabric maga a Windows vagy Linux rendszeren futó alkalmazás-platform réteg, míg a Cloud Services egy olyan rendszer, amely az Azure által felügyelt virtuális gépeket a csatlakoztatott munkaterhelésekkel telepíti.
A Service Fabric alkalmazás modelljének számos előnye van:

* Gyors üzembe helyezési idő. A virtuálisgép-példányok létrehozása időigényes lehet. Service Fabric a virtuális gépeket csak egyszer kell telepíteni, hogy olyan fürtöt lehessen alkotni, amely a Service Fabric alkalmazás-platformot üzemelteti. Ettől kezdve az alkalmazás csomagjai nagyon gyorsan üzembe helyezhetők a fürtön.
* Nagy sűrűségű üzemeltetés. Cloud Services a feldolgozói szerepkörű virtuális gép egy munkaterhelést üzemeltet. Service Fabric az alkalmazások eltérhetnek az azokat futtató virtuális gépektől, ami azt jelenti, hogy nagyszámú virtuális gépre telepíthet nagy mennyiségű alkalmazást, ami csökkentheti a nagyobb üzembe helyezések teljes költségeit.
* A Service Fabric platform bárhol futtatható, amelyen Windows Server vagy Linux rendszerű gépek működnek, akár az Azure-ban, akár a helyszínen is. A platform egy absztrakt réteget biztosít a mögöttes infrastruktúrában, így az alkalmazás különböző környezetekben futhat. 
* Elosztott alkalmazások kezelése. A Service Fabric egy olyan platform, amely nem csupán az elosztott alkalmazásokat üzemelteti, hanem a virtuális gép vagy a gépi életciklustól függetlenül is segíti az életciklusuk kezelését.

## <a name="application-architecture"></a>Alkalmazásarchitektúra
Egy Cloud Services alkalmazás architektúrája általában számos külső szolgáltatási függőséget tartalmaz, például a Service Bus, az Azure Table és a Blob Storage, az SQL, a Redis és mások számára az alkalmazások állapotának és adatkezelésének kezelésére, valamint a webes és a feldolgozói szerepkörök közötti kommunikációra egy Cloud Services üzemelő példányban. A teljes Cloud Services alkalmazás például a következőhöz hasonló lehet:  

![Cloud Services architektúra][9]

Service Fabric alkalmazások is dönthetnek úgy, hogy a teljes alkalmazásban ugyanazokat a külső szolgáltatásokat használják. Ha ezt a példát Cloud Services architektúrát használja, a Cloud Servicesról Service Fabricra történő legegyszerűbb áttelepítési útvonal csak a Cloud Services telepítést helyettesíti egy Service Fabric alkalmazással, a teljes architektúra megtartásával. A webes és feldolgozói szerepkörök Service Fabric állapot nélküli szolgáltatások számára is elhelyezhetők, amelyeknek minimális a kód módosításai.

![Service Fabric architektúra az egyszerű áttelepítés után][10]

Ebben a fázisban a rendszeren továbbra is ugyanúgy kell működnie, mint korábban. Az Service Fabric állapot-nyilvántartó funkcióinak kihasználása érdekében a külső állapotú áruházak a rendszerállapot-nyilvántartó szolgáltatásokban helyezhetők el, ahol alkalmazhatók. Ez nagyobb szerepet játszik, mint a webes és feldolgozói szerepkörök egyszerű áttelepítése Service Fabric állapot nélküli szolgáltatások számára, mivel ehhez olyan egyéni szolgáltatásokat kell írnia, amelyek egyenértékű funkciókat biztosítanak az alkalmazásnak, mint a külső szolgáltatások előtt. Ennek előnyei a következők: 

* Külső függőségek eltávolítása 
* Az üzembe helyezési, felügyeleti és frissítési modellek egységesítése. 

Ennek a szolgáltatásnak a internalizálását egyik példája a következőhöz hasonló:

![Service Fabric architektúra a teljes áttelepítés után][11]

## <a name="communication-and-workflow"></a>Kommunikáció és munkafolyamat
A legtöbb Cloud Service-alkalmazás több rétegből áll. Hasonlóképpen, egy Service Fabric alkalmazás több szolgáltatásból áll (jellemzően sok szolgáltatás). Két közös kommunikációs modell közvetlen kommunikációt és külső tartós tárolást biztosít.

### <a name="direct-communication"></a>Közvetlen kommunikáció
A közvetlen kommunikáció révén a rétegek közvetlenül kommunikálhatnak az egyes rétegek által közzétett végpontokon keresztül. Állapot nélküli környezetekben, például a Cloud Servicesban ez azt jelenti, hogy kijelöl egy virtuálisgép-szerepkör egy példányát, véletlenszerűen vagy ciklikus multiplexelés használatával a terhelés elosztása érdekében, és közvetlenül csatlakozik a végponthoz.

![Közvetlen kommunikáció Cloud Services][5]

 A közvetlen kommunikáció a Service Fabric közös kommunikációs modellje. Service Fabric és Cloud Services között a legfontosabb különbség az, hogy Cloud Services egy virtuális géphez csatlakozik, míg Service Fabric egy szolgáltatáshoz csatlakozik. Ez egy fontos különbség a következő két okból:

* A Service Fabric szolgáltatásai nincsenek kötve az azokat üzemeltető virtuális gépekhez. a szolgáltatások mozoghatnak a fürtben, és valójában várhatóan különböző okokat kell megadniuk: erőforrás-kiegyensúlyozás, feladatátvétel, alkalmazás-és infrastruktúra-frissítések, elhelyezési vagy terhelési kényszerek. Ez azt jelenti, hogy a szolgáltatási példányok címe bármikor megváltozhat. 
* A Service Fabricban lévő virtuális gépek több szolgáltatást is tárolhatnak, amelyek mindegyike egyedi végpontokkal rendelkezik.

A Service Fabric egy elnevezési szolgáltatás nevű szolgáltatás-felderítési mechanizmust biztosít, amely a szolgáltatások végponti címeinek feloldására használható. 

![Diagram, amely azt mutatja, hogy a Service Fabric hogyan biztosít egy szolgáltatás-felderítési mechanizmust, az úgynevezett elnevezési szolgáltatás, amely a szolgáltatások végponti címeinek feloldására használható.][6]

### <a name="queues"></a>Üzenetsorok
Az állapot nélküli környezetekben a rétegek közötti közös kommunikációs mechanizmus, például a Cloud Services egy külső tárolási várólista használata a tartósan az egyik rétegből a másikba való tárolásához. A gyakori forgatókönyv egy olyan webes szint, amely feladatokat küld egy Azure-várólistába, vagy Service Bus, ahol a feldolgozói szerepkör példányai elhelyezhetők és feldolgozhatják a feladatokat.

![Cloud Services üzenetsor-kommunikáció][7]

Ugyanez a kommunikációs modell használható Service Fabricban. Ez akkor lehet hasznos, ha egy meglévő Cloud Services alkalmazást Service Fabricba telepít át. 

![Közvetlen kommunikáció Service Fabric][8]

## <a name="parity"></a>Parity
[Cloud Services hasonló a Service Fabrichoz, mint a könnyű használat, de ez már egy örökölt szolgáltatás, és az új fejlesztéshez Service Fabric ajánlott](/azure/architecture/guide/technology-choices/compute-decision-tree). a következő egy API-összehasonlítás:


| **Cloud Service API** | **Service Fabric API** | **Megjegyzések** |
| --- | --- | --- |
| RoleInstance. GetID | FabricRuntime. GetNodeContext. NodeId vagy. Csomópontnév | Az azonosító a csomópontnév tulajdonsága |
| RoleInstance. GetFaultDomain | FabricClient. QueryManager. GetNodeList | Szűrés a csomópontnév és az FD tulajdonság használata |
| RoleInstance. GetUpgradeDomain | FabricClient. QueryManager. GetNodeList | Szűrés a csomópontnév, és a frissítési tulajdonság használata |
| RoleInstance. GetInstanceEndpoints | FabricRuntime. GetActivationContext vagy Naming (ResolveService) | CodePackageActivationContext, amelyet a FabricRuntime. GetActivationContext és a replikák a ServiceInitializationParameters. CodePackageActivationContext használatával biztosítanak. Inicializálása |
| RoleEnvironment.GetRoles | FabricClient. QueryManager. GetNodeList | Ha ugyanazokat a szűrési típust szeretné elvégezni, a FabricClient. ClusterManager. GetClusterManifest használatával lekérheti a csomópont-típusok listáját a fürt jegyzékfájljában. |
| RoleEnvironment.GetIsAvailable | Egy adott csomópontra mutató FabricRuntime Connect-WindowsFabricCluster vagy létrehozása | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext. log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext. log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext. log/Temp/Work | * |
| Role. GetInstances | FabricClient. QueryManager. GetNodeList vagy ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime. GetActivationContext vagy Naming (ResolveService) | * |

## <a name="next-steps"></a>Következő lépések
A Cloud Servicesról Service Fabricra történő legegyszerűbb áttelepítési útvonal csak az Cloud Services-telepítést helyettesíti egy Service Fabric alkalmazással, így nagyjából ugyanaz lesz az alkalmazás általános architektúrája. A következő cikk útmutatást nyújt a webes vagy feldolgozói szerepkörök Service Fabric állapot nélküli szolgáltatásba való átalakításához.

* [Egyszerű áttelepítés: webes vagy feldolgozói szerepkör átalakítása Service Fabric állapot nélküli szolgáltatásba](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png